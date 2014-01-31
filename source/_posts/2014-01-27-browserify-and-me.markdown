---
layout: post
title: "Browserify and Me"
date: 2014-01-27 08:18:54 -0500
comments: true
categories: [node, browserify, modules]
---
The title might should be "Caught between two worlds", but that sounded
really, really pretentious.

So, I had started a project a while back before realizing that something
like browserify existed. I know. I feel like a moron already, don't rub
it in. And even when I started using it I didn't really understand what
it was going to do.

Then, as I got halfway through the project, I realized what was happening
and started doing what I should have done the whole time. So now, the code
for this web application has two completely different approaches that,
while not technically incompatible, make reading and debugging the code
a chore.

Here's what I learned in this very specific context:

I'm trying to use jquery/bootstrap to build a single page web application,
and then using browserify to make a bridge between the client and the node
server. That is, there are modules that run on node, and the application
module is most easily written in node as well. The only problem is how
to communicate between the server and client over what is essentially a
layer on top of websockets.

It turns out, that the cleanest solution to this problem turned out to be
using custom events on DOM elements from the application code, and catching
those events to do the UI parts. That is, some jquery/bootstrap code can
use all of its normal methods and coding style to accomplish the UI
functions, while the node style application code that has been browserified
can simply fire off events without worrying too much about what's going
to happen.

There are some improvements I can make, and will be making, in said
application. Things like passing in the DOM element to the client application
code, and more cleanly setting up the custom javascript DOM events.

All in all, though, stepping through the code again to refactor for all of
this new knowledge makes for better code and I learned a little something
along the way.

