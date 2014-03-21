---
layout: post
title: "Data Synchonization"
date: 2014-03-18 09:40:31 -0400
comments: true
categories: [programming, data, sync, synchronization]
---
Data. Always getting out of sync. You'd think it would learn. You'd think that
maybe if you tightened up your code it would never get out of sync.

Well. Data don't care what you think. That's why CRC, etc.

At a high level, though, it's very easy to see why data needs to be synchronized
and how it can get messed up. Two people updating a spreadsheet separately can
easily end up with two different versions, and then needing to synchronize them
(aka merge).

What are some of our options? Glad you asked.

One of my favorite ways of making it work is "append only". That is, you can never
delete anything, you can only add to it. This makes synchronization easy. You just
ask if they have any items you don't have, and that's it. Synchronized.

Updates are also fairly easy. Each record having a time it was last updated, or
a sequence number or whatever, and you can just ask for the latest versions. Again,
that's pretty much a snap.

What about deletes?

Ah. Well, this is only slightly more complicated. In this case, what you really need
is a "history". This history can actually just be items that were deleted. The
simplest design, of course, is to have each record have a sync field (either a
sequence or a timestamp) and also have a "tombstone" or list of items that were
deleted. You don't even need to know when, necessarily, if a given id can never
be reused.

If they can be reused, then you need to also include the sync field in the history,
that way something might be "brought back to life" later. This kind of necromancy
is not uncommon in many systems.

So, what do we have? Well, in order to keep multiple copies of the same data in
sync with each other efficiently (that is, not just checking every line and
replacing the whole dataset), a system should have: a history that lists the latest
version of each item with a synchronization field of some kind, such as a
monotonic timestamp, and the action of 'created', 'updated', or 'deleted'.

One of the issues is with timestamps. If you need to synchronize in real-time
between multiple nodes that share data, and you have a high rate of operations,
your timestamps will probably cause you issues. The issues are directly related
to the same problem of keeping two clocks in sync. When you have multiple
processes on different machines doing multiple updates per second each, you are
going to have to come up with some scheme for ordering them.

This can be important for updates/deletes, depending on how you structure things.
Very often, the solutions end up being a combination of timestamp, process name,
and a sequence number. There are many different approaches, but they all follow
the same pattern. A decent approach, if you are satisfied with "rough" ordering,
would be something like [Twitter Snowflake](https://github.com/twitter/snowflake).

But don't stop there. There's a lot to learn about this topic, and it is
fascinating in and of itself.

Warning: make sure you don't over-engineer your solution. If you are only worried
about synchronizing data that gets updated hourly or daily, then don't overthink
it and come up with some really complicated system.
