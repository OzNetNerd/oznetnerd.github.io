---
title: Installing Git on Windows
sub_heading: "It's easier than you might think"
redirect_from: /installing-git-windows/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Git
- DevOps

tags:
- git
- Python
- Coding
- DevOps
---
Installing Git on Windows is very similar to [**installing it on Linux**](/git-installation-basic-setup/). That might not come as a surprise though because the tools we'll be using in this post are specifically designed to allow Windows users to utilise Linux packages without needing to install a VM.

## Installation

The first thing you'll need to do is [**download Git for Windows**](https://git-scm.com/downloads). Once you have done that, install it using all of the default settings. After completing the installation, you will find that you've now got three Git applications:

*   Git Bash
*   Git CMD
*   Git GUI

The former two are ways to access Git (and a few other Linux packages) through the CLI. **"Git GUI"** is, as its name suggests, a GUI for Git. I recommend always using **"Git Bash"** as it provides a better experience than **"Git CMD"** and because the **"Git GUI"** does not provide the level of flexibility that the CLI does.

## Generating Windows SSH key

Issue the following command:

```
ssh-keygen
```

After providing responses to each of the questions, a certificates will be generated and stored in your C:\Users\\.ssh  directory. You will need to copy the contents of the id_rsa.pub  file located inside of this directory and paste it into your Git account's repository.

Rather than navigating to the file through File Explorer though, you can do it through **"Git Bash"** using the following command:

```bash
$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17SbrmTIpNLTGK9Tjom/BWDSU
GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
NrRFi9wrf+M7Q== schacon@mylaptop.local
```

## Git on Cygwin

If you followed my [**"Installing Ansible on Windows"**](/installing-ansible-windows/) guide, you might find that you want to to use Git from within Cygwin. If you followed the steps above you can use the same certificate here.

Using the technique described in [**this StackOverflow answer**](http://stackoverflow.com/a/11251797/6233477), issue the following commands:

```bash
nano ~/.ssh/config
Host            gitlab.com
    Hostname        gitlab.com
    IdentityFile    /cygdrive/c/Users//.ssh/id_rsa
``` 

## Conclusion

Following this guide allows you to use Git on Windows using any of the following methods:

*   Command prompt
*   Git Bash
*   Git CMD
*   Git GUI
*   Cygwin

In other words, you're spoiled for choice :)