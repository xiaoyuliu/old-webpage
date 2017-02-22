---
title: "Solve grey screen and X pointer problem of vncviewer on Ubuntu"
layout: post
date: 2017-02-21 13:49
author: xiaoyuliu
description: VNCviewer grey screen problem solution on Ubuntu
tag:
- ubuntu
- vnc
category: blog
---

After setting up successfully the vncserver on your Ubuntu machine, a common problem is the grey screen and "X" pointer in vncviewer.

This problem is caused by there is no desktop running while running the vncserver, so there is going to show nothing. According to [this page](https://www.htcp.net/880.html), a simple solution is to edit the file `~/.vnc/xstartup` by simply replacing the content with
    
    #!/bin/sh 
    # Uncomment the following two lines for normal desktop:
    # unset SESSION_MANAGER
    # exec /etc/X11/xinit/xinitrc

    [ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
    [ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources

    xsetroot -solid grey 
    vncconfig -iconic &
    x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
    x-window-manager &
    mate-session &

At last, run ` (sudo) chmod 777 ~/.vnc/xstartup ` to change the previlege. Then the problem is solved.

Here we introduce some common *vncserver commands*:

- Create a connection: `vncserver`
    + -geometry 1920x1200: the resolution of desktop in vncviewer will be 1920x1200
This will return a connection address with a port number.
- Delete a connection: `vncserver -kill :PORT_NUMBER`
- List all vnc connections: `ps -ef | grep vnc`

The PID of each connection will be stored in `~/.vnc/xxxxx:x.pid` file, **DO NOT** delete .pid file unless you want to kill the connection by yourself.









