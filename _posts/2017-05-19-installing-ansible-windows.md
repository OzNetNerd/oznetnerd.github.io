---
title: Installing Ansible on Windows
sub_heading: "A step-by-step guide"
redirect_from: /installing-ansible-windows/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Automation
- Ansible

tags:
- Network Automation
- Ansible
- Automation
- Cygwin
- DevOps
---
While Ansible is not supported on Windows, it is very easy to get it up and running. The Ansible documentation provides information on **[how to do it](http://docs.ansible.com/ansible/intro_windows.html#using-a-windows-control-machine)** using  Windows Subsystem for Linux (Beta), I've run into issues trying to get WSL up and running so instead opted for Cygwin.

For those who are unfamiliar with Cygwin, it is _"a large collection of GNU and Open Source tools which provide functionality similar to a Linux distribution on Windows."_

## Step by Step Guide

_Note: This guide was written using [**Cygwin 2.877**](https://cygwin.com/setup/), but should be applicable for all versions._

1.  Download **[Cygwin](https://cygwin.com/install.html)**.
2.  Run the Cygwin installation file.
3.  When asked which download source you'd like to use, select **"Install from Internet"**.
4.  Select a root directory where you'd like to install the application. I leave it as the default, C:\cygwin64
5.  Select a directory where you'd like to install your Cygwin packages.
6.  Select the method which suits your internet connection type. e.g If you're not connecting from behind a proxy, select the **"Direct Connection"** option.
7.  Select a mirror to download your packages from. Any option in the list will do.
8.  You'll then be provided with a list of packages which you can download. Don't select anything, just click **"Next"**. Doing so will result in the default applications being installed.
9.  When asking if you want to install dependencies, leave everything as their defaults and click **"Next"**. This will install everything you need to get Cygwin up and running.
10.  Double click on the **"Cygwin64 Terminal"** icon.
11.  Set up an alias which points to the **"setup-x86_64.exe"** file you downloaded in Step 1, like so:
    
	   ```bash
		alias cyg-get="/cygdrive/d/path/to/cygwin/setup-x86_64.exe -q -P"
	   ```
    
12.  Install the packages required to get Ansible up and running:
    
	   ```bash
		curl cygwin32-gcc-g++ gcc-core gcc-g++ git libffi-devel nano openssl openssl-devel python-crypto python2 python2-devel python2-openssl python2-pip python2-setuptools tree
	   ```
    
13.  Install Ansible:
    
	   ```bash
		pip install ansible
	   ```
    
	    _(Note that if you want to install a specific version, e.g 2.2, append ==2.2  to the end of the command.)_
    

All done! You're now ready to start using Ansible.

Note that your Cygwin home directory resides inside of the installation directory specified in Step 4.

## Installing Additional Packages

The alias in Step 11 is a CLI version of what you saw in Step 8. To install a package using this alias you must know its name beforehand. If its name changes or you misspell it, the alias will not give you an error message.

If you're having trouble using the alias because you're unsure of a package's name,  open the **"setup-x86_64.exe"** application again and click **"Next"** until you get to the **"Select Packages"** screen. (Note that although it looks like you're re-installing Cygwin, **you are not**. This is simply the way Cygwin's package management works.)

In this screen you're able to install and uninstall packages. To install a package, click on the icon to the left of the word **"Skip"**. Doing so will result in a crossed tick box appearing in the **"Bin"** column. Once you've selected the packages you want, click **"Next"** again to complete the installation.