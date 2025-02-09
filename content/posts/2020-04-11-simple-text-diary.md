+++
title = "Simple text diary with Git, Markdown and Bash"
date = "2020-04-11"
description = "As part of trying to be more productive whilst working from home I have been writing a daily work journal. This is to help with tracking what I have actually been doing with my time, as well as record all those times I go 'I really should learn not to do this again'"
+++

As part of trying to be more productive whilst working from home I have been writing a daily work journal. This is to help with tracking what I have actually been doing with my time, as well as record all those times I go "I really should learn not to do this again".

I did a similar thing during my PhD using [VimWiki](https://github.com/vimwiki/vimwiki), a wiki and diary plugin for [_The One True Editor_](https://www.vim.org/). While this was a great plugin, it didn't play nice with the pandoc markdown plugins I used and actually had too many features that I wasn't using. So I decided to go back to basics and do what all developers do at some point, re-invent the wheel!

## The plan

I basically need a online repository for daily diary entries and other notes. So a Git repo it is!

I use markdown for everything so that can be the note format and I can always use [Pandoc](https://pandoc.org/) to convert that to whatever format I want. 

I also need to be able to add notes when out and about and away from a terminal (shockingly this can happen some times). As everything is in markdown files I can use the excellent [HackMD](https://hackmd.io/) online editor and connect it to the notes repo. This also allows easy sharing and collaboration on notes if I need that.

One of the features I liked about VimWiki was the shortcut to jump to a new vim buffer for today or yesterday, so I need command line aliases for that.

## The result 

Turns out to be pretty simple to achieve the above with a bit of Bash foo. Some liberal use of the `date` command can help create a `diary/year/month/yy-mm-dd.md` structure automatically. The key is to make sure the folders are definitely there when a new month/year starts.

```bash
export NOTES=$HOME/Documents/Notes
alias notes='cd $NOTES'

alias diary='mkdir -p $NOTES/diary/$(date +"%Y/%m") && $EDITOR $NOTES/diary/$(date +"%Y/%m/%Y-%m-%d.md")'
alias yesterday='mkdir -p $NOTES/diary/$(date -d yesterday +"%Y/%m") && $EDITOR $NOTES/diary/$(date -d yesterday +"%Y/%m/%Y-%m-%d.md")'
```

This works great for the diary entries and I can easily add arbitrary files for project notes.

```
:~/Documents/Notes|master⚡
⇒  tree
.
├── diary
│  └── 2020
│     ├── 03
│     │  ├── 2020-03-27.md
│     │  ├── 2020-03-28.md
│     │  └── 2020-03-29.md
│     └── 04
│        ├── 2020-04-01.md
│        ├── 2020-04-02.md
│        ├── 2020-04-03.md
│        ├── 2020-04-06.md
│        ├── 2020-04-07.md
│        ├── 2020-04-08.md
│        └── 2020-04-09.md
├── README.md
└── work
   ├── features
   │  ├── cruise_control.md
   │  ├── pulsar_vs_kafka.md
   │  └── zk_scaling.md
   ├── issues
   │  └── zookeeper.md
   └── evironment_setup.md
```

I will probably add more to this as time goes on. I may even try my hand at some vimscript to add these commands to Vim. I also plan to add some simple Pandoc commands to convert this structure into HTML for rendering images etc. Basically re-inventing (a much simpler) VimWiki.
