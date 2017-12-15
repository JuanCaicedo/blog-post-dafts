# My most essential tools

## A much needed refresh

Recently, I found myself in a good position to do a clean install of my
computer. Doing so lead me to reflect on what tools I use most. This is a quick
peek into how I work and what's important to me to feel like I can get the most
done.

## Spacemacs

[Spacemacs](http://spacemacs.org/) became my main text editor in early 2016 and
I use it for everything, from coding, to copying files, to keeping track of
to-do lists (special shout out to [org-mode](http://orgmode.org/)). Because of
that it was one of the first things I had to get working on my machine.

I've written [another
post](https://medium.com/@_juancaicedo/why-i-use-spacemacs-d6c0ef1a9b00) about
why I like Spacemacs so much.

## Karabiner (Karabiner-Elements)

A lot of people complained when Apple dropped the esc key in favor of the touch
bar. That's because the haven't learned about using caps lock with Karabiner.
This is a life changer, it has became such a strong part of my muscle memory
that I get confused when I use machines that don't have it installed.

[Karabiner](https://pqrs.org/osx/karabiner/) is a tool that allows you to
customize what any key press does within your operating system. I only use one,
but I do it so much that [I paid to support its
development](https://pqrs.org/osx/karabiner/pricing.html).

I never use caps lock. I don't know why it exists, let alone why it gets such
important real estate on the keyboard. I added a
[modification](https://pqrs.org/osx/karabiner/complex_modifications/#caps_lock)
that makes it so if I press caps lock (alone), it acts as esc. It's great, no
need to reach all the way up when I want to leave Vim's insert mode or when I
want to quit a text field on a web form. Even better is that if I press it in
combination with another key, it acts as ctrl plus that key. So to do all sorts
of ctrl shortcuts (which Tmux uses a lot), I don't have to move my hands much.

## Zsh

There are some tools that influence what you do profoundly without you realizing
you are using them. [Zsh](http://www.zsh.org/) is like that. It is a shell that
is very similar to Bash and compatible with almost all Bash code, but it's much
nicer.

I use it in combination with
[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) and the combination gives
me fantastic tab completion and spelling correction that makes navigating the
command line so much easier. It also makes it easier to interact with files by
allowing you to use globbing syntax (for example `$ cp *.js ../backup`).

By far the best thing about this set up is that it allows you to customize your
prompt easily. I have a pretty minimal prompt, it looks like this:

[Picture of prompt]

## Tmux

A huge amount of my day is spent in my terminal. I have a lot of things going on
there. For each repo that I work on, I have at least one running process (a
server or watch), a constantly executing test suite, and a blank prompt so I can
explore files, run npm tasks, etc.

Flexibility in how I navigate all these using my keyboard is really important.
At first I used my terminal's built-in split windows and tabs, but I didn't find the
navigation very ergonomic. I also found that layouts had a ton of resting
inertia, that is, once I had a layout, I was reluctant to move things around or
open new panes or windows.

[Tmux](https://github.com/tmux/tmux) is a shell tool that gives the ability to
have multiple windows in any terminal and to split screen in any way you want.
Together with [gpakosz's configuration](https://github.com/gpakosz/.tmux), it's
really easy to move around, break a pane into its own window, open new windows,
name them. It helps me feel like everything in my terminal is in a place I can
easily find it, even though I rearrange things all the time.

## BetterTouchTool

This is another application that I use for only a single feature, but that
feature is so important that I paid to use it.

With BetterTouchTool you can program a custom mouse/trackpad/keyboard gesture to
map to almost any action. I set it so that a four finger tap on my trackpad maps
to middle click on a mouse. Clicking a link this way opens it in a new tab.
Click on a tab to closes it. This makes it so I can keep old tabs open as I
navigate (which you can do with option + click), but keep my tabs under control
so I don't have a ton of them open (which you can't do with option + click).

## Vimium

This Chrome extension is unreal.

[Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=en)
adds some keyboard shortcuts to chrome so that you can scroll around a page
without needing to take your hands off the keyboard. That's awesome.

But wait, it gets better. If you press the `f` key, every link on the page shows
a combination of letters next to it. If you press those keys, Vimium clicks on
the link. This _really_ makes it so that you can navigate with just the
keyboard, it is amazing!

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
tried and it does the job nicely, so I've kept using it.

Whenever I copy something, it gets saved to my history. If I press ctrl + cmd +
v, a modal shows up with everything in my history so I can choose what I want to
paste. I can copy multiple things from one page and then go to another page and
paste them one by one, instead of having to flip back and forth. You can also
save copied items for later, so I have a few code block saved that I use over
and over again.

## Spotify

Listening to music while working is essential. I really like listening to one
song on repeat for the entire day. My favorites are by a band called [the
Gregory Brothers](https://youtu.be/UyfCSWPFXx0).

## Thanks for reading!

Those are all my most important tools. If anything new makes it into my
essentials kit, I'll update this post with it. I hope you have a chance to try
some of them and that they solve some pain points for you!
