# My most essential tools

## A much needed refresh

Overtime computers start acting strangely. I can't explain why, but anytime I
needed to update iTunes, my MacBook would start killing and respawning update
processes, killing my battery and never succeeding in getting me a new version.
When I opened chrome, an html file that I created over a year ago to test
something would always get opened up. And I was constantly running out of
storage, with no idea where my 256gb went. I got my first MacBook in 2013. This
is my third, and I've always used Time Machine to pick up exactly where I left
off with my previous computer. But that meant that I also carried over all the
quirks that it had built up. Recently, I found myself in a good position to do a
clean install of my computer. I had recently quit my job and was traveling so I
had some free time. Doing so lead me to really evaluate what tools I use and
needed on my computer ASAP for development. This is a quick peek into how I work
and what's important to me to feel like I can get the most do

## Spacemacs

[Spacemacs](http://spacemacs.org/) became my main text editor in early 2016 and
I use it for everything including making to-do lists (special shout out to
[org-mode](http://orgmode.org/)). Because of that it was one of the first things
I had to get working on my machine.

I've written [another
post](https://medium.com/@_juancaicedo/why-i-use-spacemacs-d6c0ef1a9b00) about
why I like Spacemacs so much.

## Karabiner (Karabiner-Elements)

People complained a lot when Apple dropped the esc key in favor of the touch
bar. That's because the haven't learned about using caps lock with Karabiner.
This is a life changer, it became such a strong part of my muscle memory that I
get confused when I use machines that don't have it installed.

[Karabiner](https://pqrs.org/osx/karabiner/) is a tool that allows you to
customize what any key press does within your operating system. I only use one,
but I do it so much that [I paid to support its
development](https://pqrs.org/osx/karabiner/pricing.html).

I never use caps lock. I don't know why it exists, let alone why it gets such
important real estate on the keyboard. I added a
[modification](https://pqrs.org/osx/karabiner/complex_modifications/#caps_lock)
that makes it so if I press it (alone), it acts as esc. It's great, no need to
reach all the way up when I want to leave Vim's insert mode or when I want to
quit a text field on a web form. Even better is that if I press it in
combination with another key, it acts as ctrl plus that key. So to do all sorts
of ctrl shortcuts (which Tmux uses a lot), I don't have to move my hands much.

## Zsh

There's some tools that influence what you do profoundly without you realizing
they're there. [Zsh](http://www.zsh.org/) is like that. It's a shell that is
extremely Bash-like and compatible with almost all Bash code, but it's just
nicer.

I use it in combination with
[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) and the combination gives
me fantastic tab completion and spelling correction that makes navigating the
command line so much easier. It also makes it easier to interact with files by
allowing you to use globbing syntax (for example `$ cp *.js ../backup`).

By far the best thing about this set up is that it allows you to customize your
prompt easily. I have a pretty minimal prompt, it looks like this:

[Picture of prompt]

## Hyper Terminal
I love the projects coming out of [Zeit](https://zeit.co/). One of the ones that
I've been most excited by was [Hyper](https://hyper.is/), a terminal replacement
written completely in web technologies. Until now I had been a long time user of
[iTerm2](https://www.iterm2.com/), and since I found it easier to stay with it
since I was already productive. However I had been wanting to switch to Hyper
for a long time, and I took this restart as a chance to switch cold-turkey.

The thing I like about Hyper is that it takes one of the applications I use the
most and puts customizing it within my wheel house. All configurations are in
JSON and you can write CSS to change how the app is styled. Plugins are as
simple as writing JavaScript and leveraging some simple APIs. And installing
someone else's plugin is super easy.

## Tmux

A huge amount of my day is spent in my terminal. I have a lot of things going on
there. For each repo that I work on I have at the minimum one running process
(server), a constantly executing test suite, and a blank prompt so I can explore
files, run npm tasks, etc.

Flexibility in how I navigate all these using my keyboard is really important.
At first I used ITerm2's built in split windows and tabs, but I didn't find the
navigation very ergonomic. I also found that layouts had a ton of sticking
inertia, that is, once I had a layout I was reluctant to move things around or
open new panes or windows.

[Tmux](https://github.com/tmux/tmux) is a shell tool that gives the ability to
have multiple windows in any terminal and to split screen in any way you want.
Together with [gpakosz's configuration](https://github.com/gpakosz/.tmux), it's
really easy to move around, break a pane into its own window, open new windows,
name them. It helps me feel like everything in my terminal is in a place I can
easily find it, even though I rearrange. things all the time.

## BetterTouchTool

This is another application that I use for only a single feature, but that
feature is so important that I paid to use it.

With BetterTouchTool you can program a custom mouse/trackpad/keyboard gesture to
map to almost any action. I set it so that a four finger tap on my trackpad maps
to middle click on a mouse. Clicking a link this way opens it in a new tab.
Click on a tab to closes it. This makes it so I can keep old tabs open as I
navigate (which you can do with option + click), but keep my tabs under control
so I don't have a ton of them open (which you can't do with option + click).

## Alfred

Some people like Spotlight. I do not like Spotlight. It feels slow and I can
never find the things I'm searching for.

[Alfred](https://www.alfredapp.com/) is a replacement that does so much more. I
turned off Spotlight and use the same key binding for Alfred. My use is pretty
simple, I use it to find files and open applications (which it does incredibly
well), but there's a wealth of custom workflows you can set up to do almost
anything with a few keystrokes.

## Bartender

I don't like UI clutter. A lot of Mac applications like to install an icon in
the top bar, but most of the time I don't really care about them and they
obscure the ones that I do care about. With
[Bartender](https://www.macbartender.com/), I hide all the icons that I don't
really care about to keep my top bar really clean and full of only important
information, without needing to close those other apps.

## Copy'em Paste

If you don't have a clipboard history manager, stop reading this and go get one
right now. It is one of the biggest productivity improvements I know.

I'm not particularly attached to [Copy'em
Paste](http://www.apprywhere.com/copy-em-paste.html), but it's the one I first
tried and it does the job nicely so I've kept using it. Whenever I copy
something, it gets saved to my history.

If I press ctrl + cmd + v, a modal shows up with everything in my history so I
can choose what I want to paste. I can copy multiple things from one page and
then go to another page and paste them one by one, instead of having to flip
back and forth. You can also save copied items for later, so I have a few code
block saved that I use over and over again.

## Evernote

I don't remember a ton things, but I do get frustrated by the fact I don't
remember them. Evernote is an awesome application for storing and more
importantly retrieving information. It integrates with all sorts of other apps,
so I send links, pictures, and tweets there all the time.

## Spotify

Listening to music while working is essential. I really like listening to one
song on repeat for the entire day. My favorites are by a band called [the
Gregory Brothers](https://youtu.be/UyfCSWPFXx0).

## Tunnel bear

Having a VPN is pretty important, especially if you're working from public
wi-fi. I also travel a lot and often find websites to be strangely blocked from
other countries. I assume it's some sort of bot protection, but I found that a
lot of sites block all traffic from some countries. In those cases it's easier
to just log in "from the US".

## Thanks for reading!

Those are all my most important tools. If anything new makes it into my
essentials kit, I'll update this post with it. I hope you have a chance to try
some of them and that they solve some pain points for you!
