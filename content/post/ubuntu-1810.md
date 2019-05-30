+++
date = 2018-10-24
title = "What to do after installing Ubuntu 18.10"
tags = []
+++

> _This guide serves as a reminder for me when I go to install [Ubuntu] again. I
am not going to show how to install Ubuntu, that is available all over the net.
What I will describe is what I did after install to get my workstation back up
and running with what I need to do Python development._

<i class="fa fa-linux fa-2x" aria-hidden="true"></i>
For me, a sub-average long-time software developer, Ubuntu is a great operating
system.  Canonical, the company who releases Ubuntu, does so every six months,
in April and October; therefore, version 18.10 was released in October 2018.

My system
: Lenovo Yogo 2 Pro x86_64 Grub GNU Debian Ubuntu 18.10 Cosmic Cuttlefish

Normally I like to install every new Ubuntu release for the following reasons:

1. I remove all the cruft I have gathered, notice I said "install", not "upgrade",

2. new features available.

{{% toc %}}

## Partitions

Partition information is important when using a custom install.

    $ sudo lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT,LABEL
    NAME    FSTYPE     SIZE MOUNTPOINT      LABEL
    sda              238.5G
    ├─sda1  vfat       477M                 WINY
    ├─sda2  swap       8.4G [SWAP]
    ├─sda3  ext4        28G /
    ├─sda4  ext4        51G /home
    ├─sda5  ext4      81.7G                 Meadow
    ├─sda6  ntfs         4G                 LENOVO
    ├─sda7  vfat       250M /boot/efi       BOOT
    └─sda10 ext4      61.8G                 Distroer

<img src="/img/partitions.png">

## Pre-Install

Note: You are going to have to backup your current files to another partition
like I did or you could use an external drive or USB stick.  I keep my bash
files in a GitHub repo, see [Dotfiles] below.

The main things I backed up were:

* `~/bin/`
* `~/.bash_links/`
* `~/.config/sublime-text-3/Packages/User/`
* `~/.ssh/config`
* `~/.gitconfig`
* `~/.hgrc`
* `~/.s3cfg`
* `~/.scrapinghub.yml`
* `/etc/hosts`
* bookmarks
* Evolution
* Gramps
* `dconf dump /org/gnome/terminal/ > gnome_terminal_settings_backup`  # https://askubuntu.com/questions/967517#967535
* `cp ~/.bash_links/*  -L  * /media/stav/Meadow/xenix`

## Post-Install

Here is my list of things I had to do to after the install to get up and
running with a fresh Ubuntu.

### Update

First update Ubuntu with any recent security fixes

    sudo apt-get update && sudo apt-get upgrade

### Tools

    sudo apt install curl htop vim gnome-tweak-tool

Restore `~/bin/`

### Network

Merge `/etc/hosts`

### System Monitor

I love to have system info in the _Top Panel_ so that I can alway have a quick
glance at cpu usage and network traffic; but, my beloved `indicator-multiload`
applet no longer works on recent version of Ubuntu.

So now I switched to an extension/applet called [Gnome Shell System Monitor]
(https://github.com/paradoxxxzero/gnome-shell-system-monitor-applet). I installed:
the system dependencies the browser extension and the host connector and then
restarted Gnome; but, unfortunately Firefox could not detect the connector:

> Although GNOME Shell integration extension is running, native host connector
> is not detected

So I had to download the tarball from Github and install manually which was
really easy:

    make install

Then run _Gnome Tweak Tool_ and enable in _Extensions_.

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

    wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -

    sudo apt-get install apt-transport-https

    echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list

    sudo apt-get update

    sudo apt-get install sublime-text

##### Errors

Note that when I tried to run it I got errors

    $ subl
    Unable to load libgdk-x11-2.0.so
    ....

So I installed this package

    $ sudo apt-get install libgtk2.0

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

Restore the following file from your backup and Package Control will automatically install all missing packages:

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

#### Copy Files

Copy backup files to `~/.config/sublime-text-3/Packages/User/`

### Sublime Merge

I also now use [Sublime Merge](https://www.sublimemerge.com/) from the same people.

#### Install

https://www.sublimemerge.com/docs/linux_repositories

### Slack

https://snapcraft.io/slack

```bash
sudo snap install slack --classic
# slack 3.3.3 from Slack✓ installed
```

### E-mail

Add settings in Thunderbird setup

### Development

    sudo apt-get install build-essential checkinstall
    sudo apt-get install libglib2.0-dev libgtk2.0-dev libvte-dev
    sudo apt-get install git git-extras gitg meld tree
    sudo apt-get install python-pip python3-pip python3-venv virtualenvwrapper

```bash
$ python
Python 2.7.15+ (default, Oct  2 2018, 22:12:08)

$ python3
Python 3.6.7rc1 (default, Sep 27 2018, 09:51:25)
```

#### Pipenv

https://pipenv.readthedocs.io/en/latest/install/#installing-pipenv

```bash
pip install --user pipenv
```

Note: after installing `pipenv` you will need to make sure that the user install
location (`~/.local/bin`) is in the path, easiest way is to logout/login

#### Node

##### Install nvm

https://github.com/creationix/nvm/blob/master/README.md#installation

This will update your `.bashrc`

```bash
$ wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

Open a new terminal, or else you can setup the environment variable manually
which the installer will explain how to do.

```bash
$ nvm --version
    0.33.11
```

##### Install node

```bash
$ nvm ls-remote |tail

   v10.11.0
   v10.12.0
    v11.0.0

$ nvm install 11 --latest-npm

Downloading and installing node v11.0.0...
Downloading https://nodejs.org/dist/v11.0.0/node-v11.0.0-linux-x64.tar.xz...
Computing checksum with sha256sum Checksums matched!
Now using node v11.0.0 (npm v6.4.1)
Creating default alias: default -> 11 (-> v11.0.0)
```

### SSH keys

    ssh-keygen -t rsa -b 4096 -C "stav@phinix"
    ssh-add ~/.ssh/id_rsa
    sudo apt-get install xclip
    xclip -sel clip < ~/.ssh/id_rsa.pub

Note that new keys will have to be uploaded to remotes:

* [GitHub](https://github.com/settings/keys "GitHub keys page")
* [Bitbucket](https://bitbucket.org/account/user/stav/ssh-keys/ "Bitbucket keys page")
* `cowboy:~/.ssh/authorized_keys`

### Hugo

It's good to update this document as I'm installing.

    sudo snap install hugo

    mkdir -p ~/Work/stav/Hugo
    git clone git@github.com:stav/stav.linode.gen.git ~/Work/stav/Hugo/steven.michael
    git clone git@github.com:gcushen/hugo-academic.git /home/stav/Work/stav/Hugo/steven.michael/themes/academic

### Terminal

Reload backup settings.

https://askubuntu.com/questions/967517#967535

    cp ~/.config/dconf/user /tmp # backup
    dconf reset -f /org/gnome/terminal/
    dconf load /org/gnome/terminal/ < gnome_terminal_settings_backup

### Bash

    git clone git@github.com:stav/dotbash.git ~/.bash

#### Install bash_it

```bash
git clone --depth=1 https://github.com/Bash-it/bash-it.git ~/.bash_it
~/.bash_it/install.sh  # keep existing `.bashrc` and append templates
```

##### Install Powerline theme

https://github.com/Bash-it/bash-it/tree/master/themes/powerline

```bash
mkdir ~/Public/powerline

cd ~/Public/powerline

git clone --depth=1 https://github.com/powerline/fonts.git

./fonts/install.sh

wget https://github.com/powerline/powerline/raw/develop/font/PowerlineSymbols.otf

wget https://github.com/powerline/powerline/raw/develop/font/10-powerline-symbols.conf

mv PowerlineSymbols.otf ~/.local/share/fonts/

fc-cache -vf ~/.local/share/fonts/

mkdir -p ~/.config/fontconfig/conf.d/

mv 10-powerline-symbols.conf ~/.config/fontconfig/conf.d/
```

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

### Applications

    sudo apt-get install chromium-browser gimp mpv lynx elinks

### System config

See [Lenovo Yoga Ultrabook SSD Tweaks]

## Previous Installs

I previously installed some other stuff:

* [17.10](/post/ubuntu-1710/)
* [17.04](/post/ubuntu-1704/)
* [16.04](/post/ubuntu-1604/)


[Lenovo Yoga Ultrabook SSD Tweaks]: /post/ubuntu-yoga/
[Ubuntu]: https://www.ubuntu.com/
