---
layout: post
title:  "Running Adobe Digital Editions on Void Linux"
date:   2020-12-26 15:22:44 +0100
author: tobi-wan-kenobi
categories: linux
tags: [ linux, void-linux ]
---

## Motivation

I'm an avid reader with a lot of ebooks, some of them protected by Adobe DRM. While I do not particularly
like copy protection, there are excellent books out there that are not otherwise (legally) available.
Also, I do see the point that authors have a justified desire to protect their works against illegal copies.
It is not their fault that DRM companies seem to ignore alternative operating systems (also here, I do get
that it is difficult to justify huge efforts to cater to a relatively small group of people - but belonging
to that small group, I still find it somewhat annoying). end of rant :)

Long story short: I want to be able to run Adobe Digital Editions on Linux, because it seems wasteful to
keep a Windows machine around just for managing my eBooks, and I do *not* want to illegally download them,
either.

Since a couple of days, I run Void Linux (musl) on my main laptop. That is probably one of the
most difficult setups to get Adobe Digital Editions to run on, mainly because:
- At the time of writing, there's no `multilib` repo for musl yet, meaning: No pre-packaged `wine`
- For the same reason, compiling a 32bit version of `wine` is also not trivial

Thankfully, there are really smart guys out there who solved those issues, and I managed to
find their artifacts :)

## Solution

Here's the smart guys:

- For running `wine` on Void Linux, [scotthardy/docker-wine](https://hub.docker.com/r/scottyhardy/docker-wine/) - awesome!
- For running ADE in particular, [Installing Adobe Digital Editions on Linux with WINE](https://patdavid.net/2018/05/installing-adobe-digital-editions-on-linux-with-wine/)
So, basically:
- Set up a dockerized version of `wine`
- Configure the `winetricks` correctly
- Install ADE

Without further ado:

```
sudo xbps-install -S docker
sudo ln -s /etc/sv/docker /var/service
sudo usermod -a -G docker <your username>
<logout and log back in to update your groups>
mkdir ~/bin/
wget https://raw.githubusercontent.com/scottyhardy/docker-wine/master/docker-wine -O bin/docker-wine
bin/docker-wine winetricks win10 corefonts windowscodecs dotnet40
# finally, installing ADE
bin/docker-wine
# now, you are inside docker wine
wget https://adedownload.adobe.com/pub/adobe/digitaleditions/ADE_4.5_Installer.exe
# ctrl-D to exit wine
bin/docker-wine wine ADE_4.5_Installer.exe
# now, you can simply start ADE using:
bin/docker-wine wine "C:\Program Files (x86)\Adobe\Adobe Digital Editions 4.5\DigitalEditions.exe"
```

I hope this is useful for somebody who wants to legally use ADE on his Linux machine.
