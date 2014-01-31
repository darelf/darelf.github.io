---
layout: post
title: "Important Topics: Config File vs. Command Line"
date: 2014-01-31 13:26:34 -0500
comments: true
categories: [programming, war]
---
The eternal struggle rages on, and in today's episode we bring you the
battle of configuration.

Every useful application is configurable. This is an axiom, a universally
accepted truth about programming. If a software application is to be useful
to a user of any kind, it must allow some way of specifying parameters that
modify its behavior.

For instance, one of the simplest Unix programs of all time, `ls`, on my
Linux machine has over 50 separate command line switches that allow the
user to modify the behavior. And this is for a program whose sole purpose
is to list files.

The question is not whether a program should accept one or the other. That
way lies madness, since any given program should accept both. The question,
instead, is what should you use?

### Configuration files.

The pro-config-file argument is compelling. Store your settings a single time.
Easier to run in cron or some other automation. Can be changed at any time,
not merely when the program is about to run. Comments in the file itself
can remind you what the options are for.

The cons for this are obvious. Mainly, if you run the program with different
options very often, editing a file is actually more work than changing the
switches.

### Command Line

Easy. Allows you to quickly modify behavior between several rapid uses of
the same program. Help screen will generally tell you what you need to know.

Tedious if you have many options. Easy to forget if the times between running
the program by hand are very long. Easy to mistype.

### Solution

Where possible use both. Set your config file to your common and least-changed
settings, and use the command line for your often changing parameters.
