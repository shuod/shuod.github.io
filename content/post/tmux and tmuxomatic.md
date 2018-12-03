+++
title = "Tmux"
description = "Usage of tmux"
tags = [
    "VPS", 
    "Linux",
]
date = "2018-09-20"
categories = [
    "Shell_powershell",
    "Linux",
]

image = "image.jpg" # optional
toc = true # optional, When set to TRUE this parameter, table of contents appears in only this article.
+++

## Tmux 
I sometimes need to compile software package in the High Performance Computer(HPC). The compiling process often takes very long because C++ never pays any attention to this (Thank google, we now have golang that has a significantly shorter compiling time). It will become a serious trouble for remote compiling, because network could go wrong at any moment therefore, the compiling process in the HPC will be interrupted. Unfortunately, remote access comprises majority of the working senario: we used a ssh client (say, putty or iTerm) to connect to the HPC and excute command remotely. So it will make people crazy if the network went wrong often.

I used a GNU software called "screen" and it does the job well. The only problem to me, however, is "screen" is not easy to incorporate into a python script, and this sometime will hinder the scripting automation process. So, I switched to a new toy called "Tmux" (BSD liscense). There are also other improvements from Tmux that screen doesn't have. Check [here](https://superuser.com/questions/236158/tmux-vs-screen/) for detail.

### A very concise command list of Tmux:
Very similar to screen.

Once you login to the sever, run
```
tmux new -s session-name
```
and this will start a new session with the name given. Or, you can just run 
```
tmux
```
and you will get a random name as the session name. 

Now you can run some linux command, say the compile command "make". While the content displayed in the screen will start to roll, because of the verbose "make", you now could safely "detach" from the current "make" session by using
```
ctrl+b then d
```
This is actually similar to the shortcut key in screen (ctrl+a then d). It will detach from the current session and return to the screen before. Now you can even close the ssh client putty and leave for home.

To return to this tmux session (where make is running) the next morning, you could just do
```
tmux a -t session-name
```
and you will be in the "make" session. Or,
```
tmux a 
```
directly takes you back the last session you switch out from ("a" is short for "attach" and you can also use "attach" to replace "a" in the above command).

Sometimes you will have more than one session running on the server, and you could use
```
tmux ls
```
to check out what are they. This provides a convient way to manage sessions.

Some other manage commands that can come in handy sometime:
```
tmux kill-session -t session-name
tmux kill-server
```
The first one will kill the session you appointed and the second one will terminate all running tmux sessions on the server.

For a list of commands: 
```
ctrl+b then ?
```

### tmux window pane

Another powerful function of tmux is multi-window. Although "screen" also supports this, it takes either an extra patch (for < 4.01, or just use Ubuntu) or upgrades to ver > 4.01 to achieve vertical splitting[1]. Plus, since the mechanism behind the two software are different, when you shared sessions with another user, if you switch focus to another pane, "screen" will change the focus as well but tmux will stay unchanged. This is useful for pair-programing.


A short list of commands[2]:

```
Ctrl+b "      split screen horizontally.
Ctrl+b %      split screen vertically.
Ctrl+b n      move to the next pane.
Ctrl+b p      move to the previous pane.

```

And, of course, you can save your setting in a file, check [2][3]




[1]hhps://unix.stackexchange.com/questions/26685/how-to-split-window-vertically-in-gnu-screen

[2] https://www.hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf
