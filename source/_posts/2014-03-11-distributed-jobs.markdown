---
layout: post
title: "Distributed Jobs"
date: 2014-03-11 13:12:33 -0400
comments: true
categories: [programming, network, nodejs, streams]
---
This post is supposed to be about network queuing of jobs that can be
distributed into discrete tasks which themselves can be completed in
any order. And about the boiled down [stream-queue](http://github.com/darelf/stream-queue)
npm package that I have published. I will diverge just a bit here to
talk a little bit of background.
<!--more-->
STREAMS. Just think about that. Streams. Think of all the things it implies
in programming to talk about streams and streaming.

Probably number one is networking, that is TCP (or whatever) network
sockets that are read/write and have very well-established, at this point,
semantics. Open, close, pause, end, read, write, etc. Depending on the
exact stream, of course, things may be slightly different, and we've seen
quite a lot of semantics built on top of streams.

Nodejs, of course, does networking really well. In fact, that may be the
number one purpose for most programmers using it. It's just really
fantastic at networking. Your code can worry about what goes on just
above the socket level, with the streams. They also give you an
abstraction, so that there are file streams, etc. helping you out.

This is all fairly normal in programming, and streams have been around
for a long time. UNIX pipes are probably the most common stream-semantic
thing that programmers use, but they are everywhere in just about every
language that's of any use.

So...

Back to jobs. If you have a big job that can broken down into discreet
tasks such that those tasks can happen in any order, you have something
that can be distributed. And by distributed, I mean among separate processes,
each process accomplishing one of the tasks towards the goal of completing
the job.

What `stream-queue` is designed to do is give the basic functionality of
a distributed queue that I needed. I need to have it manage the list of
tasks, how many total tasks there were at the beginning, how many have
been completed and how many are left to do. It needs to notify me of
when it starts, when it ends, and each time a step is taken on the way
(progress).

These are all straightforward. They need to not depend on time. That is,
having a unique timestamp should not be required, seeing as how there
might be many workers concurrently processing tasks, and some will inevitably
happen at the same timestamp.

The workers should be able to spin up or shutdown without consequence to
the working of the queue as it is in progress. One future upgrade to the
`stream-queue` package will be the ability to re-do a task if a worker
gets shutdown before completing the task. Also, having a standard semantic
for asking the queue to shutdown one of its workers, either simply to
reduce the load, or possibly even by name.

Others might be the ability to manage multiple kinds of workers, and
have those workers advertise the kind of work they can do and let the
queue manager distribute jobs accordingly.

So that's where it's at. A lot of interesting tweaks to be made, and a
lot of progress towards better features.
