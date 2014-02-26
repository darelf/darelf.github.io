---
layout: post
title: "Building Blocks"
date: 2014-02-18 08:04:35 -0500
comments: true
categories: [network, programming, modules, queue]
---
Modules. Oh, modules.

I've talked to so many programmers over the years, and the one thing I would suggest
to all of them is to rediscover modules. As opposed to frameworks.

Modules are the building blocks of application development. Keeping them out of
frameworks lets them breath and be more generally useful than if you bind them tightly
in chains. You can reuse modules over and over again, if they are well designed, in
many different contexts only if you emancipate them from the bondage of frameworks.
<!--more-->
What does that even look like?

Let's take a quick overview look at a problem I needed to solve recently: I have a
very large list of items that need processing and each item requires multiple database
queries and even some file access. (large means in the millions)

What did I try?

Well, I tried just looping. That works fine on 10s of thousands of items, but the
asynchronous nature of the database kills that on larger sets. It just fills up the
space and crashes.

Next up was an in memory queue. This actually works, but it is slow. It is slow for
all the obvious reasons, but also because I'm using a single process, single thread
solution. This means I have to keep concurrency down because otherwise I will, again,
fill up the available memory or use up the available file handles. ( I actually tried
both the queue from the `async` module, as well as `queue-async`. The first would actually
work, while the second was so slow it wasn't even worth pursuing any further. )

So that's what didn't work at the level of performance I wanted, and wouldn't scale
anyway.

Network Queue

What I did was separate the problem even further. I started with an object that knew
how to process the individual items, and one that new how to get lists of items and
queue them up for processing. So I broke that up into a worker, a manager and a server.
This would allow me to scale up as many workers as I wanted, and even put them on
different physical machines if I wanted.

An aside: The good thing, seeing as this is node.js, is that a stream is a stream (for the most
part) and what I did to make it work over the network would just as likely work fine
over other kinds of streams.

Conceptually, there are four parts I considered vital to accomplishing this task (and
any similar task): Completely separate out the code that actually performs the task,
create a worker that only knows how to participate in the group, create a server that
communicates with workers and observers, and an observer that receives updates on what's
happening.

The code that actually runs the task needs to be completely separated from the rest.
In this case, the task was to either delete a document or to create an updated document.
That's all this code does, it knows how to get the information from the database and
update the index.

The code that manages the process keeps a list of workers, and list of observers. These
two lists are very lightweight wrappers around streams. It doesn't need much information
about these, just needs the stream so it can send over information. It also keeps the
list of current items that need processing. It uses this to assign to the list of workers
items until it's empty. The observers can request processing and can receive updates of
status.

The workers have a status (idle or busy) and can send update messages and receive request
to process items. That's pretty much it.

The observer mostly just listens to the server waiting for updates. It can also request
that the entire grid work on a particular problem by sending a message. If no problem
is being worked on currently, then the manager will generate the list of tasks and
then start handing them out.

What's underneath there?

Oh, that's just [event-stream](http://npmjs.org/package/event-stream) This thing kind of
does what you think. Or maybe not. It creates a pipeline of processing on streams. That
is, node js streams, which in my case means sockets. Basically it listens for `events`
on the `stream` and then takes the steps in the chain, possibly passing it back out to
another stream at the end.

It solves a problem I was going to have to solve anyway, since I wanted to use JSON
to communicate between the processes. This module has all of that, and does just that.
Meaning it takes care of the event streaming so I don't have to, and doesn't do a
whole bunch of unrelated, extraneous stuff that makes it unusable or terribly
interdependent. Basic solution of problem using standard interfaces.

In my case, I pass it the socket, split it and parse it (courtesy of `event-stream`),
and then do something with the parsed message. I throw away the data after I'm done,
since I don't need to pass the messages on.

Ta-Da!

That's all there is to it. Use modules. When designing solutions, design them in modules
and as you refactor make them generically useful if you can. When you use a modular
design you can keep swapping out parts of the implementation without hassle. Something
that can be frustrating if not impossible when using a framework.
