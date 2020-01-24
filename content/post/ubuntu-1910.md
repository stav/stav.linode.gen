+++
date = 2020-01-23
title = "What to do after installing Ubuntu 19.10"
tags = []
+++

> _This guide serves as a reminder for me when I go to install [Ubuntu] again. I
am not going to show how to install Ubuntu, that is available all over the net.
What I will describe is what I did after install to get my workstation back up
and running with what I need to do Python development._

<i class="fab fa-linux fa-2x" aria-hidden="true"></i>
For me, a sub-average long-time software developer, Ubuntu is a great operating
system.  Canonical, the company who releases Ubuntu, does so every six months,
in April and October; therefore, version 19.04 was released in April 2019.

My system
: Asus x86_64 Grub GNU Debian Ubuntu 19.10 Eoan Ermine

Normally I like to install every new Ubuntu release for the following reasons:

1. I remove all the cruft I have gathered, notice I said "install", not "upgrade",

2. new features available.

{{% toc %}}

## Pre-Install

Note: You are going to have to backup your current files to another partition
like I did or you could use an external drive or USB stick.  I keep my Bash
files in a GitHub repo, see [Bash](#bash) below.

The main things I backed up were:

* `~/bin/`
* `~/.config/sublime-text-3/Packages/User/`
* `~/.ssh/config`
* `~/.gitconfig`
* `~/.hgrc`
* `~/.s3cfg`
* Gramps
* `dconf dump /org/gnome/terminal/ > gnome_terminal_settings_backup`  # https://askubuntu.com/questions/967517#967535

## Post-Install

Here is my list of things I had to do to after the install to get up and
running with a fresh Ubuntu.

### Update

First update Ubuntu with any recent security fixes

    $ sudo apt-get update && sudo apt-get upgrade

### Tools

    $ sudo apt install curl htop vim gnome-tweak-tool

Restore `~/bin/`

### System Monitor

I love to have system info in the _Top Panel_ so that I can alway have a quick
glance at cpu usage and network traffic; but, my beloved `indicator-multiload`
applet no longer works on recent version of Ubuntu.

So now I switched to an extension/applet called [Gnome Shell System Monitor]
(https://github.com/paradoxxxzero/gnome-shell-system-monitor-applet):

    $ gnome-shell --version
    GNOME Shell 3.34.1

    $ sudo apt install gir1.2-gtop-2.0 gir1.2-nm-1.0 gir1.2-clutter-1.0
    $ sudo apt install chrome-gnome-shell

Then go to the [Extension page](https://extensions.gnome.org/extension/120/system-monitor/)
and click the "ON" switch

### Firefox

Use dark theme

    about:addons -> Themes

Update search engine to Duck Duck Go:

    about:preferences#search -> Default Search Engine

Do not warn when closing multiple tabs:

    about:config

    Search: "browser.tabs.warn"

        browser.tabs.warnOnClose `false`
        browser.tabs.warnOnCloseOtherTabs `false`

### Sublime Text

[Sublime Text](https://www.sublimetext.com/) is an excellent editor.

#### Install

    $ sudo snap install sublime-text

#### License

Add under help menu.

    ----- BEGIN LICENSE -----
    Steven Almeroth
    Single User License
    EA7E-... 104B
    ------ END LICENSE ------

#### Package Control

You want Package Control from wbond.

##### Install Package Control

Command Pallate (``shft-ctrl-p``) -> Install Package Control

#### Install packages

Restore the following file from your backup and Package Control will
automatically install all missing packages:

* `~/.config/sublime-text-3/Packages/User/Package Control.sublime-settings`

        "installed_packages":
        [
            "Anaconda",
            "GitGutter",
            "Hugofy",
            "INI",
            "Package Control",
            "Pretty JSON",
            "Python Pretty Print",
            "Theme - SoDaReloaded",
            "TrailingSpaces",
            "Vue Syntax Highlight"
        ]

Then restart SublimeText.

#### Copy Files

Copy remaining backup files to `~/.config/sublime-text-3/Packages/User/`

Do not copy these files:

* `User/Package Control.last-run`
* `User/Package Control.merged-ca-bundle`
* `User/Package Control.user-ca-bundle`

### Sublime Merge

I also now use [Sublime Merge](https://www.sublimemerge.com/) from the same people.

#### Install

https://www.sublimemerge.com/docs/linux_repositories

### Keys

Install ssh keys

#### Generate

https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

    $ ssh-keygen -t rsa -b 4096 -C "stav@maximillion"
    $ eval "$(ssh-agent -s)"
    $ ssh-add ~/.ssh/id_rsa
    $ sudo apt-get install xclip
    $ xclip -sel clip < ~/.ssh/id_rsa.pub

#### GitHub

* https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account
* https://github.com/settings/keys

#### BitBucket

* https://bitbucket.org/account/user/stav/ssh-keys/

#### Cowboy

    $ cat ~/.ssh/id_rsa.pub | ssh stav@cowboy 'cat >> ~/.ssh/authorized_keys'

### Git

    $ sudo apt-get install git git-extras gitg meld tree

### Bash

    $ git clone git@github.com:stav/dotbash.git ~/.bash

#### Install bash_it

    $ git clone --depth=1 https://github.com/Bash-it/bash-it.git ~/.bash_it
    $ ~/.bash_it/install.sh  # keep existing `.bashrc` and append templates

##### Install Powerline theme

https://github.com/Bash-it/bash-it/tree/master/themes/powerline

    $ mkdir ~/Public/powerline
    $ cd ~/Public/powerline
    $ git clone --depth=1 https://github.com/powerline/fonts.git
    $ ./fonts/install.sh
    $ wget https://github.com/powerline/powerline/raw/develop/font/PowerlineSymbols.otf
    $ wget https://github.com/powerline/powerline/raw/develop/font/10-powerline-symbols.conf
    $ mv PowerlineSymbols.otf ~/.local/share/fonts/
    $ fc-cache -vf ~/.local/share/fonts/
    $ mkdir -p ~/.config/fontconfig/conf.d/
    $ mv 10-powerline-symbols.conf ~/.config/fontconfig/conf.d/

Close all Terminal instances

#### Copy files

    .bash_projects
    .bash-it.bash
    .bashrc

#### Link files for editor reference

    stav@thelatae:~$ ll .bash_links/
    .bash-it.bash -> /home/stav/.bash-it.bash
    .bash_projects -> /home/stav/.bash_projects
    .bashrc -> /home/stav/.bashrc

### Hugo

It's good to update this document as I'm installing.

#### Instal Hugo

The default installation did not work:

    $ sudo snap install hugo

Running the command got an error:

    $ hugo
    Building sites ERROR: failed to transform resource:
    TOCSS: failed to transform "main_parsed.scss" (text/x-scss):
    this feature is not available in your current Hugo version,
    see https://goo.gl/YMrWcn

So I installed the [extended version] (https://github.com/gohugoio/hugo/releases).

#### Clone Repository

    $ mkdir -p ~/Work/stav/Hugo
    $ git clone git@github.com:stav/steven.michael.git ~/Work/stav/Hugo/steven.michael
    $ git submodule update --init --recursive

### Telegram

    $ sudo snap install telegram-desktop

### E-mail

Install Blue Mail:

    $ sudo snap install bluemail

### Development

    $ sudo apt install build-essential libsqlite3-dev sqlite3 bzip2 libbz2-dev zlib1g-dev libssl-dev openssl libgdbm-dev libgdbm-compat-dev liblzma-dev libreadline-dev libncursesw5-dev libffi-dev uuid-dev
    $ sudo apt install python3-pip python3-venv virtualenvwrapper
    $ sudo apt install git git-extras meld tree

    $ python3
    Python 3.7.5 (Nov 20 2019)

### Applications

    $ sudo apt install chromium-browser gimp

    # sudo add-apt-repository ppa:mc3man/mpv-tests
    # sudo apt-get install mpv

## Previous Installs

I previously installed some other stuff:

* [19.04](/post/ubuntu-1810/)
* [18.10](/post/ubuntu-1810/)
* [17.10](/post/ubuntu-1710/)
* [17.04](/post/ubuntu-1704/)
* [16.04](/post/ubuntu-1604/)


[Ubuntu]: https://www.ubuntu.com/
