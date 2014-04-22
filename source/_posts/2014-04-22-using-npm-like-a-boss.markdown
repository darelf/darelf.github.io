---
layout: post
title: "Using NPM Like A Boss"
date: 2014-04-22 13:50:43 -0400
comments: true
categories: [programming, npm, nodejs]
---
So I have to keep track of what I work on each week and send
out an email with a short description of each. Yeah. You know.

I thought "What a great idea for making a script that handles all
that with a simple command line!" I hear you mumbling "nerd". That's
OK. I can take it.

## Enter... The Script

Well, I just need a list, each list can live in a text file that I
can then email to my boss each week. What do I need?

Very little. I use [moment](http://momentjs.com) to figure out what
week I'm working on automatically and store it in the correct file.
I also use [minimist](http://github.com/substack/minimist#minimist) to do
command line parsing since I barely need anything there.

So the beginning looked like this:

    var fs = require('fs')
    var moment = require('moment')

Then I realized I (or someone) might want it daily, so I add a
default. And while I'm at it, let's define a working directory
for the files.

    var defaults = { weekly: true, workdir: process.env.HOME + '/worklist/' }
    var argv = require('minimist')(process.argv.slice(2), {default: defaults})

You could override those, for instance with `--workdir=/my/crazy/directory/` but
hopefully these are sane defaults.

Now let's figure out what file we should be working with:

    var m = moment()
    if (argv.weekly) {
      m = moment().startOf('week').add('days', 1)
    }
    
    var fname = argv.workdir + m.format('YYYY-MM-DD') + '.txt'

The file name will now have the date, or Monday's date when we are doing weekly
files. This let's us choose.

Next we read in the file, if it exists, and parse the contents:

    var contents = []
    fs.readFile(fname, function(err, data) {
      if (!err) {
        contents = data.toString().split('\n').filter(Boolean)
        parseCommands()
      }
    })

That `parseCommands()` function will need to be defined. It's basically the
entry point for looking at any command the user sent and taking the correct
action. I decided I only needed 3 commands:  list, add, and delete.

    function parseCommands() {
      if (argv._.indexOf('list') > -1) {
        contents.forEach(function(v,i) { console.log("%d: %s", i, v) })
      }
      if (argv.add) {
        contents.push(argv.add)
        saveContents()
      }
      if (argv.d) {
        if (contents.length > argv.d) {
          delete contents[argv.d]
          saveContents()
        }
      }
    }

What we have going on here is a `list` command that just dumps the contents.
Next, we have a `--add="This is the item to add"` command that will add
a single line to our file, and then it will save the file. (see below for that)
And finally the `-d #` command that takes the number of the item to delete.

Not really pretty, but since I'm mostly just adding and listing, it seems
to work just fine. And since the files are just text files, I can always
edit them by hand, feed them into other programs or utilities easily, and
generally use any of my typical Unixy workflows.

Oh, and saving the file just looks like this:

    function saveContents() {
      fs.writeFile(fname, contents.join('\n') + '\n')
    }

## Enter... NPM!!!

How does the `npm` utility enter in to this? I'm glad you asked!

NPM has a facility called "bin" in the `package.json` file that allows you
to define so-called `binary` programs to be installed. In my case, this is
just a node js script as shown above.

What steps do I take to make this work as a command? I.e. `worklist list` or
`worklist --add="I totally did important work just now"`

### Step One

Add the "shebang" at the beginning of the script telling it to use node.

    #!/usr/bin/env node

For the script, that's it.

### Step Two

Add the entry into `package.json` telling it that the script is a binary.
For our example, I put the script in `./bin/worklist.js` so that's what I
will tell NPM. Also, I need to tell it what I want the command to look like,
which in this example is `worklist` so that I can just type `worklist` on the
command line and it will run my script in node automatically.

    {
      ... bunch of normal package.json stuff ...
      "bin": {
        "worklist": "./bin/worklist.js"
      }
    }
    

### Step Three

Install it globally.

    npm install -g

You will notice NPM telling you that it is linking the script into your path
somewhere. I believe on Windows that this also automatically wraps it in a
`.cmd` for you. WIN.

### Step Four

Pour a hot cup of coffee, light up your favorite cigar, and enjoy. THIS STEP
IS NOT OPTIONAL.

## Feedback

Please, please, please, send me feedback, ask me questions. Yeah. You can do it.
