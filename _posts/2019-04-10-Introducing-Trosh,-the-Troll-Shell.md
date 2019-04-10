---
layout: post
title: Introducing Trosh, the Troll Shell
---

As a teaching assistant for a handful of cybersecurity classes at Iowa State,
I've had the opportunity to create content that is used in labs and projects.
One of the best parts of the job is devising malicious machines and nefarious
networks that the students have to break into or secure, and I decided to do
something new for the final project this semester: give them a box to secure
where the shell they're interacting with is constantly harassing them. I love my
job.

To be more precise, the _default_ shell they're interacting with is very
difficult and obstinate, and that's about it. Other than that, it's a fairly 
standard Ubuntu 16.04 headless server. This is a 200-level course, populated 
mostly by sophomores who have only taken one prior unix-heavy class, so I'm not 
going to throw anything particularly difficult at them. Frankly, all of the 
harassment that the shell throws at them is just to draw their attention to 
the fact that something is different and they should probably go change their 
login shell. 

One last warning I need to make clear: I am not a great programmer. I know just
enough C to be a danger to myself, so I advise against running this code
anywhere important. It works for me (albeit poorly), and I can't recommend
reading it if you don't want to be shocked and offended at my abuse of the
beautiful language that is C. Alright, that said, let's jump into Trosh.

![Logging in to Trosh](/images/trosh/trosh_login.png)
`[root@AppleWatch ß@$λ]$`

This is what you're
presented with when you log in and are presented with a Trosh shell. The prompt
is slightly unorthodox, having nothing at all to do with the actual user you're
logged in as.
I thought it was a little funny to include the "ß@$λ" prompt as a play on a
"Bash" prompt. 

As a general rule, most commands will function as you would expect, though I
don't have any real color support, tab completion, or history to up-arrow back
through. I didn't include the ability to easily `echo $ENVIRONMENT_VARIABLE`. 
I also don't have pipes, output redirection, `&&` or `||` to chain
commands together, or any standard quality of life tool included in any modern
shell. It runs commands (sometimes) and that's it. 

![Basic Directory Traversal](/images/trosh/basic_dirs.png)
As you can see, basic directory traversal is no problem, though the lack of tab
completion quickly becomes annoying. How about we try and do something more
interesting, such as creating, editing, and then viewing a file?
![Touch + Vim](/images/trosh/vim1.png)
![Editing With Vim](/images/trosh/vim2.png)
![Cat the File](/images/trosh/vim3.png)

Well, that all went by pretty smoothly. However, this post isn't supposed to be
about how great my custom shell is (it isn't even remotely good). No, this is a
post about the ~~fun~~ ~~mean~~ fun things it does when people in class try to do
other basic stuff. Of course, it handles all of the following cases (poorly)
using some terminal built-in commands that it catches, and everything else just
gets passed along to an `execvp()` system call. This post isn't going to get
into the weeds of how the shell works (though I will write that up at some point
in the future). Instead, I'm going to focus on the rude, unhelpful, and
generally annoying behaviors I intentionally programmed into the shell. We'll
start by cleaning up the terminal with `clear`.
![Before Clearing](/images/trosh/preClear.png)
![After Clearing](/images/trosh/postClear.png)
Boom, your shell is now filled with garbage. It is still possible, however, to
just hammer "enter" until the screen is clean-ish. 
(It's worth pointing out that I considered doing something rude when
enter is pressed on an empty line, such as logging the user out. However, that
felt like a little much for our students.)

Alright, let's say our user is now curious why the shell is acting weird, so
they decide to launch a new shell that they know is installed and functional,
such as `bash` or even `sh`.
![Bash and Sh](/images/trosh/BashAndSh.png)
Ouch, that might cause some annoyance. Now, because I'm a nice person, I decided
to leave one really simple workaround: I'm only filtering for the commands
`bash` and `sh`, I'm not checking the binary that the user wants to run. Calling
`/bin/bash` with the absolute path to the executable will still launch a much
more functional shell. (For the purposes of the assignment Trosh was written
for, it's expected that students with either discover this or just edit their
login shell in /etc/passwd. I even changed the Trosh binary name to
`REALLY_DANGEROUS_SHELL` so they should definitely notice something is up.)
![Bash Workaround](/images/trosh/BashWorkaround.png)

Next up, two pretty standard commands: `whoami` and `uname -a`. At this point,
the user should have figured out that this box is acting very strangely, so they
will hopefully start hunting for useful information. Well, they may be a little
disappointed with the reward their curiousity earns.
![whoami](/images/trosh/whoami.png)
![BTW I Use Arch](/images/trosh/arch.png)
[Major shoutout to Screenfetch, from which I shamelessly stole that ASCII art
for a personal favorite meme](https://github.com/KittyKatt/screenFetch).
Remember that this is being deployed on Ubuntu boxes, so that should cause a
little confusion. Also, this is literally the only time that Trosh will print
anything in color. 

Finally, we come upon my favorite feature. If you know me, you know that I'm
*that guy* who gets way too into evangelizing Vim. Naturally, it should come as
no surprise to you that Vim works beautifully when called from my shell.
![Vim!](/images/trosh/vim.gif)
However, what if we try to use another editor that I don't like?
![Nano (Nobody likes nano)](/images/trosh/nano.gif)
`Okay, look. I won't stop you from using nano, but you should try vim.`
`People say it's intimidating, but it's actually quite simple.`
`It's very streamlined and efficient, and the plugins are amazing.`
`However, you asked for nano, and you'll get it.`
`But first, we're waiting 20 seconds so you can think about using vim.`

Look, I know this isn't a good way to win the hearts and minds of users. I know
that stuff like this makes the Vim community come across as elitist and
unfriendly. But you know what, this shell isn't meant to win hearts and minds,
it's meant to bother users. And frankly I think it does a decent job.

There's also a fun little gimmick if someone tries to run emacs.
![Emacs](/images/trosh/emacs.png)

Oh, and just to end the post, here's a fun little thing I discovered on accident
after deploying the shell to the machine for class. It turns out that I forgot
to do any kind of signal handling, so when it catches a SIGINT (Ctrl-C) meant to
kill a running process, the whole thing just kind of dies. If it's being run as
the login shell, the user is kicked back to the login screen, and if it's being
run in a terminal emulator, the terminal window will instantly disappear. Enjoy
this low-quality gif demonstrating as such (also enjoy my wallpaper picture of
Diogenes).
![SIGINT](/images/trosh/ping.gif)

Well, that about wraps things up for Trosh as it stands today. I'll probably
keep maintaining it and adding more and more malicious things for use in future
classes and the like. If you feel like deploying this to some users you don't
particularly like (and you don't mind reading some awful C code) then feel free
to check out [my github repo with the source
code.](https://github.com/hackpotter/trosh) Thanks for reading!

-JP
