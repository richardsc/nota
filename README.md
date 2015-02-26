## Prescript

This document is in revision.

## Abstract

``nota`` is an open-source application for recording textual notes and
associated meta-information that may be used for later retrieval.

## Overview

Most people find it helpful to store notes on a computer.  Some use specialized
applications for this, while others prefer the simplicity of recording their
thoughts in a plain-text file, using a familiar text editor.  In this second
option, it is common to associate the text files with projects or tasks.
Depending on the intent, the file might be named "README" or perhaps something
more meaningful, such as "PLANS," "TASKS," "BUGS," "IDEAS," etc.  Thus, for M
projects and N categories, there might be M x N files, and the handling of all
those files can grow complicated, whether adding new material or finding old
material.

A reasonable solution is to have a single file, in which notes can be stored
along with meta-information, such as keywords.  For example, plans for a
project named "foo" might be flagged with the keywords *foo* and *plans*, and
retrieving those plans would be a simple matter of filtering on those keywords.

Storing notes along with keywords (and other meta-information, such as the
date, the author, etc.) is somewhat complicated in a text file that is to be
edited with a general text editor, not least because a typo might damage the
file.  Storing notes in a database is a good solution to this problem, and it
offers the additional advantage of greatly improved lookup speed.  The
disadvantage of the database, however, is that an application is required to
act as an interface between the user and the data.  If the application is
commercial, then users expose themselves to the risk of losing all their work,
if the company stops supporting the software.

The ``nota`` application (named for the Latin phrase "nota bene", perhaps
pronounced "note ah" by some and "note eh" by Canadians) is designed with all
these things in mind.  It is deliberately restricted in its features, focussing
on the creation of textual notes and their retrieval.  Complex formatting is
not provided, nor is the ability to add non-textual material.  In the present
early version, ``nota`` functions entirely at the unix command line, and is
most suited for power users who are unafraid of that environment.

The development model for ``nota`` is entirely open-source, and the coding
relies on popular tools that will be familiar to many programmers, mitigating
against obsolescence. 

## Using nota

### Installation

Download the source code to some directory, and then create an alias along the
following lines, adjusted for the directory name

    alias nota=~/src/nota/nota

or, for faster typing, 

    alias n=~/src/nota/nota

### Specifying a database file

The default database file is ``~/Dropbox/nota.db``, but this may not suit all
users, so there are two ways to specify a different file.  The first way is to
supply the filename as an argument, e.g. ``nota --database ~/nota.db``.  The
second way is to name a default database in an initialization file named
``~/.notarc``; for example notes on a project called ``foo`` might be referred
to as follows.

    database = "~/Dropbox/nota-foo.db"


### Create a database

When ``nota`` is first used, it will create a database with a name as described
in the previous section.


### Add notes

This may be done one at a time, with commandline arguments, e.g.

    nota --add --keywords 'lecture,physics,MIT' --title="Walter Lewin physics lectures" --content="http://ocw.mit.edu/courses/physics/8-01-physics-i-classical-mechanics-fall-1999/index.htm"

Another method is with an editor-based supply of the information, which is done
unless ``--keywords``, ``--title``, and ``--content`` are all given, e.g.

    nota --add

or

    nota --add --title "a new note"

One or more notes can also be added through input of a JSON file, e.g.

    nota --add --mode json --file note.json

Note that ``-a`` is an abbreviation for ``--add``.

### Listing notes in simplified format

To list all notes, use

    nota

Note that the first few characters (preceeding the ``:`` character) are a
"hash" that can be used to specify particular notes.

To find notes with a given keyword, use e.g.

    nota --keyword "lecture"
    nota -k "lecture"

Note that the search is fuzzy, so that e.g. "leture" would get the same results
as "lecture".  However, this scheme can have surprising results, so the
``--strict`` commandline argument is provided, to do strict searches.

To show notes with hash starting with the letter ``a``, use

    nota a

To find a specific note, type enough characters of the hash to make it unique.

### Listing notes in markdown

Do as above but use the markdown mode, e.g.

    nota -a -m markdown

This is perhaps most useful when piped into a markdown formatter, e.g.

    nota 1 -m markdown | pandoc > ~/a.html

yields an HTML file that can be reasonably well-formatted in a browser.

(For more on Markdown, see e.g.
[here](http://daringfireball.net/projects/markdown).)

### Listing notes in JSON format

Continuing with the example, use

     nota a -m json

## Editing notes

To edit e.g. a note specified with hash ``a``, use

    nota -e a

which opens up the text editor you have specified in your ``EDITOR``
environment variable, or the ``vim`` editor if you have not specified this
variable, just as if a new note were being created.



### Alter a keyword

FIXME: this does not work at present.

Use e.g.

    nota -e -k OAR=oar

so that all notes with keyword "OAR" will henceforth have that keyword changed to "oar".


### Delete a note

Continuing with the example

    nota --delete a


deletes the note with hash code uniquely defined by first letter ``a`` (use
more letters to make unique).


### Import notes

An individual note (e.g. a chunk of information from the ``note_1.txt`` file
created immediately above) can be imported by e.g.

    nota -a --mode plain < note_1.txt

This, combined with the export mechanism, provides an easy way to email notes
to colleagues, so they can import them into their own databases.

Bug: this only works for *single* notes, at the present time.

**FIXME: this probably does not work at the moment.**



### Dump database

Advanced users may want to dump the whole database with

    echo ".dump" | sqlite3 nota.db



### Back up the database

It is a good idea to set up a crontab entry like the following, to back up the
database daily (adjust the filenames appropriately).

    @daily echo ".dump" | sqlite3 /Users/kelley/Dropbox/nota.db | gzip -c > /Users/kelley/backup/nota/nota-`date +\%Y-\%m-\%d-\%H\%M`.gz

(This could be done better by checking the sha value of the file, to only
backup when things have changed.)



### Task count in bash prompt

To get a list of notes that are due today, put the following in your ``~/.bash_profile`` file:

    function nota_count {
        nota --due today --count
    }
    PS1="\h:\W"
    export PS1="$PS1<\$(nota_count)> "


## Discussion items

The author would like advice on any bugs that users find, or any features that
they might like to see.  Almost any advice is likely to be helpful.  Please use
the [issues link](https://github.com/dankelley/nota/issues) to contribute.  

In making suggestions, please bear in mind that whole idea of ``nota``
development is to provide tools that people need in practice, as opposed to
tools that might appear to be useful in theory.  This is not a coding exercise;
it is a way to get things done.

As ideas gel on the [issues link](https://github.com/dankelley/nota/issues),

they will be added below, with some discussion notes.

* It would be helpful to search in more general ways, e.g. within titles and
  content, or by date.  Such things may be added when power users report that
they need them in their work.  At the moment, one addition is fairly clear:
``nota`` should handle simple boolean operations on keyword searches.  The most
useful operation may be logical "and", e.g. ``-k python,book`` could find books
about Python.  A related addition is, of course, logical "or", e.g. ``-k
python/ruby`` might find items about either Python or Ruby.  The "and"
operation seems most useful to those presently using ``nota``, so it will be
coded first.  It is unlikely that considerable work of adding general parsing
of complex logical expressions will pay off in any significant way, so that is
a low-priority item.

* There could be a graphical interface to supplement the command line
  interface.  However, constructing the graphical interface is arduous, so this
is not a high priority.


* ... your item here :-)
