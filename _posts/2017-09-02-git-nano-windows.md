---
title: 'Git: nano on Windows'
sub_heading: "A step-by-step guide"
redirect_from: /git-nano-windows/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Git

tags:
- git
- DevOps
- nano
---
When you're developing on two different OS' (e.g Windows & Linux), the last thing you want to do is have to remember which tools to use on which system. I think we'd all agree that life would be a lot easier if we had a seamless experience between the two. That is where nano comes in to play.

Installing nano on Linux is as easy as apt-get install nano, however, installing it on Windows isn't as straight forward. Following the **[Installing Git on Windows](/installing-git-windows/) **results in MINGW64 being installed. Therefore we'll need to get nano working in MINGW64 and we'll have an identical git experience to what we have on Linux.

To achieve this we need to do the following:

1.  [**Download nano for Windows**](https://www.nano-editor.org/dist/win32-support/nano-git-0d9a7347243.exe)
2.  Rename the file to **nano.exe**
3.  Move it to C:\Program Files\Git\usr\bin
4.  Open git for Windows and issue the following command: git config --global core.editor "winpty nano"

And that's it! Next git opens a text editor, it will be nano run inside of MINGW64.