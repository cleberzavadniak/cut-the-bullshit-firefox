# CUT THE BULLSHIT, FIREFOX!

A script to allow your computer to breathe again.

## Disclaimer

**This script may potentially mess with your browser settings. Use it at
your own risk**

## Why?

Modern browsers are a joke: basically, by browsing the Web nowadays you're
allowing any remote sub-par programmer to use your precious computing
resources (your CPU, main memory and disk I/O) at his/her own will with
any kind of shitty code.

Are you a programmer? Do you care about wasting CPU cycles when designing
your application? Do you try your best to create well-behaved programs
that are not going to be a burden on your users computers? Well, that's
not the case with 90% of the people writing Websites and
Javascripty-things that run into **your** computer.

Hell, now we have this crazy thing called IndexedDB! How is that possible?
I can no longer browse the Web in peace without some ad-related garbage
script accessing **my** precious hard drive to save data about myself to
use later on for any purpose the authors see fit.


And so, in recent times, I realized Firefox was using **huge** amounts of
main memory and writing **lots** of things into disk. Like... lots! I even
tried to throttle this via Linux `cgroups` but discovered that Firefox
will become **absolutely sluggish** if it can't write in the speed it
wants.

And it seems totally random. I'm basically doing nothing and, all of
sudden, my hard drive starts spinning like crazy and writing things all
over. In `iotop` I usually see "cache", "quota" and "storage"-related
Firefox processes generating the most I/O.


Thus, the solution I found was to let Firefox write freely whatever it
wants, but instead of consuming all my I/O bandwidth (and, what is even
worse, interfering in other applications and in the overall system
performance), I allocate a `tmpfs` space **in memory** and only write it
all again to disk when the browser dies.

## Usage

Simply call `ffox` script instead of `firefox`.

**I recommend that you backup your current Firefox settings before trying
this script:**

```bash
$ cd .mozilla
$ cp -r firefox firefox.bkp
```

(You can run `du -sh ~/.mozilla/firefox`, also, to see how much disk space
is Firefox currently consuming, just out of curiosity.)

## Requirements

There must exist a user-writable `tmpfs` partition mounted in `/dev/shm`
(or else all the idea of letting Firefox work in RAM will go down the
drain).

Also, `rsync` must be available.

## What ffox does

* Inside `~.mozilla`, move `firefox` directory to `firefox.real`
* Create a `/dev/shm/$USER/firefox` directory.
* Symlink the above directory into `~/.mozilla/firefox`
* Use `rsync` to copy files from `firefox.real` into `firefox`
* Call Firefox
* When Firefox exits, use `rsync` again to update `firefox.real` contents.


Also, get rid of a lot of stupid common Websites content, like Facebook,
Whatsapp, Telegram, LinkedIn, etc. It does not imply you're going to have
to login again: these are just "storage" files that I opted to remove
because they tend to use a lot of space and are basically useless (if
Facebook want to save some data, they can save it in **their** own
machines, right?).

**Read the script and adapt it as you find fit before using.**

## Keeping things in sync

If you're worried about keeping things in sync you can call `ffox-store`
from time to time to copy the contents from memory into disk.
