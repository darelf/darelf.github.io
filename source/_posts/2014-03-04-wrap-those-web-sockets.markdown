---
layout: post
title: "Wrap those Web Sockets"
date: 2014-03-04 10:59:10 -0500
comments: true
categories: [programming, nodejs, http, websockets]
---
Here's the situation: You have a websocket-friendly routing infrastructure that
only has a single port available on each machine. (at least that you can use) But
you have some awesome client-server stuff that runs completely in the background
and is not really related to browsers or anything. You currently, and brilliantly,
have them speaking over regular node streams using the `net` functionality.

What to do?

The routing structure only routes http requests. You can't just redirect regular
socket connections.

WEBSOCKETS TO THE RESCUE!  Websockets are just sockets, but they are negotiated
over http. Oh yeah they are. So they can be routed by your infrastructure that
supports websockets. (like, say, [bouncy](http://github.com/substack/bouncy), but
there are plenty of other examples).

But you aren't using a browser. There's no browser here. Hmm.

You go check out `npm` and find out that maxogden has already solved your problem.
That's what you do. He has already wrapped websockets with stream functionality in
a project he calls [websocket-stream](http://github.com/maxogden/websocket-stream)

Now your carefully designed code that leverages the node stream api can continue
on untouched simply by wrapping the websockets with this little gem.

Enjoy!
