+++
date = 2017-12-05
title = "What to do after installing Ubuntu 17.10"
tags = []
+++

> _This guide serves as a reminder for me when I go to install [Ubuntu] again. I
am not going to show how to install Ubuntu, that is available all over the net.
What I will describe is what I did after install to get my workstation back up
and running with what I need to do Python development._

<i class="fa fa-linux fa-2x" aria-hidden="true"></i>
For me, a sub-average long-time software developer, Ubuntu is a great operating
system.  Canonical, the company who releases Ubuntu, does so every six months,
in April and October; therefore, version 17.10 was released in October 2017.

My system
: Lenovo Yogo 2 Pro x86_64 Grub GNU Debian Ubuntu 17.10 Artful Gnome

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

## Comments

Wayland
: the new default display server changed from X.org to Wayland

You can choose to use X.org during login by the little gear next to the login button.

    $ echo $XDG_SESSION_TYPE
    wayland

Meld
: nice dark syntax highlighting theme *Oblivion*

## Issues

In the latest release I found the following issues.

### Display

The new Wayland display server prevents some things from running without escalated privileges:

* `synaptic`
* `gparted`

To get these to run you need to execute this command for every new session:

    xhost +si:localuser:root

### MPV

The great video app `mpv 0.26.0` does not allow access via the mouse so I had to install a Gnome GTK front-end:

    sudo apt install gnome-mpv

### Indicators

The stop-gap system tray introduced in Gnome 3.16 was removed in 3.25 and now my beloved system-monitor and touchpad indicators no longer work.

#### Multiload

A system load [indicator](https://launchpad.net/indicator-multiload) capable of displaying graphs for CPU, network traffic and more does not display properly.

#### Touchpad

An [indicator](https://launchpad.net/touchpad-indicator) to show the status of the touchpad, and to enable and disable the touchpad.

    sudo add-apt-repository ppa:atareao/atareao
    sudo apt-get update
    sudo apt-get install touchpad-indicator

Couldn't get it working

    Touchpad-Indicator version: 1.1.0-0extras15.04.3-src
    Couldn't find synaptics properties. No synaptics driver loaded?

So for now I have to [disable it manually](https://askubuntu.com/questions/67718/how-do-i-disable-a-touchpad-using-the-command-line). The first time I installed `synclient` worked:

    synclient TouchpadOff=1

But the second time I installed it didn't work so now I have to use `gsettings`:

    #!/bin/bash
    STATUS=`gsettings get org.gnome.desktop.peripherals.touchpad send-events`
    if [ "$STATUS" = "'enabled'" ]
    then
        STATUS="'disabled'"
    else
        STATUS="'enabled'"
    fi
    gsettings set org.gnome.desktop.peripherals.touchpad send-events $STATUS
    echo "Touchpad $STATUS"

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
* git branches
* Evolution
* Gramps
* dconf dump /org/gnome/terminal/ > gnome_terminal_settings_backup  # https://askubuntu.com/questions/967517#967535
* cp ~/.bash_links/*  -L  * /media/stav/Meadow/xenix
* PaleMoon bookmarks-2017-12-05.json
* move all local Git branches (including stashes) to remotes
* note: use bash_it with powerline theme (need to install fonts)

What I should have also backed up:

* virtual environment setups

## Post-Install

Here is my list of things I had to do to after the install to get up and
running with a fresh Ubuntu.

    sudo apt-get update && sudo apt-get upgrade

### Tools

    sudo apt-get install curl htop gdebi synaptic gparted ubuntu-restricted-extras compizconfig-settings-manager

If you want to run the Synaptic or GParted GUIs you now have to escalate permissions.
See [Display](#display).

Restore `~/bin/`

### Network

Merge `/etc/hosts`

### Firefox

Update search engine to Duck Duck Go:

    about:preferences#search

Do not warn when closing multiple tabs:

    about:config

Search: "browser.tabs.warn"

    browser.tabs.warnOnClose `false`
    browser.tabs.warnOnCloseOtherTabs `false`

### Sublime Text

Sublime Text is an excellent editor.

    wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -

    sudo apt-get install apt-transport-https

    echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list

    sudo apt-get update

    sudo apt-get install sublime-text

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

* `~/.config/sublime-text-3/Packages/User/Preferences.sublime-settings`

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

### Slack

https://slack.com/downloads/linux

    wget -P Downloads https://downloads.slack-edge.com/linux_releases/slack-desktop-3.0.0-amd64.deb

    sudo dpkg --install Downloads/slack-desktop-3.0.0-amd64.deb

The Slack icon was not appearing in the system tray so I had to update the desktop file.
Thanks to [Trinh Nguyen].

1. Edit `/usr/share/applications/slack.desktop`, and change the following line from:

        Exec=/usr/binslack --disable-gpu %U

    to

        Exec=env XDG_CURRENT_DESKTOP=Unity /usr/bin/slack --disable-gpu %U

2. Restart Slack or log out and then log back in.

### E-mail

Reload Evolution from backup.

### Development

    sudo apt-get install build-essential checkinstall
    sudo apt-get install libglib2.0-dev libgtk2.0-dev libvte-dev
    sudo apt-get install vim git git-extras gitg meld tree
    sudo apt-get install python-pip python3-pip python3-venv virtualenvwrapper

### SSH keys

    ssh-keygen -t rsa -b 4096 -C "stav@thelatae"
    ssh-add ~/.ssh/id_rsa
    sudo apt-get install xclip
    xclip -sel clip < ~/.ssh/id_rsa.pub

Note that new keys will have to be uploaded to remotes:

* [GitHub](https://github.com/settings/keys "GitHub keys page")
* [Bitbucket](https://bitbucket.org/account/user/stav/ssh-keys/ "Bitbucket keys page")

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

    git clone git@github.com:stav/dotfiles.git ~/.bash

* https://github.com/stav/dotfiles/blob/master/INSTALL
* Restore `.bash_functions_private`
* Restore ~/.*

#### Copy files

    .bash_extra.bash
    .bash_functions_private
    .bash-it.bash
    .bashrc

#### Link files for editor reference

    stav@thelatae:~$ ll .bash_links/
    .bash_extra.bash -> /home/stav/.bash_extra.bash
    .bash_functions_private -> /home/stav/.bash_functions_private
    .bash-it.bash -> /home/stav/.bash-it.bash
    .bashrc -> /home/stav/.bashrc

#### Install bash_it

    git clone --depth=1 https://github.com/Bash-it/bash-it.git ~/.bash_it
    ~/.bash_it/install.sh  # keep existing `.bashrc` and append templates

##### Install Powerline theme

https://github.com/Bash-it/bash-it/tree/master/themes/powerline

    git clone https://github.com/powerline/fonts.git ~/Public --depth=1

    cd ~/Public

    ./fonts/install.sh

    wget https://github.com/powerline/powerline/raw/develop/font/PowerlineSymbols.otf

    wget https://github.com/powerline/powerline/raw/develop/font/10-powerline-symbols.conf

    mv PowerlineSymbols.otf ~/.local/share/fonts/

    fc-cache -vf ~/.local/share/fonts/

    mkdir -p ~/.config/fontconfig/conf.d/

    mv 10-powerline-symbols.conf ~/.config/fontconfig/conf.d/

    # close all Terminal instances

### Mega

Downloaded the [Sync Client]

    sudo apt-get install libc-ares2 libcrypto++6 libqt5core5a libqt5dbus5 libqt5gui5 libqt5network5 libqt5svg5 libqt5widgets5
    sudo dpkg --install megasync-xUbuntu_17.10_amd64.deb
    sudo apt --fix-broken install

> See [Installing MEGA Sync Client on Ubuntu]

### Applications

    sudo apt-get install chromium-browser gimp mpv lynx elinks

### System config

See [Lenovo Yoga Ultrabook SSD Tweaks]

### Gnome Tweaks

Install Gnome Tweaks (Tool) to improve appearance.

https://wiki.gnome.org/Apps/Tweaks

## Previous 17.04 installs

I [previously](/post/ubuntu-1704/) installed some other stuff.

### Archivers

    sudo apt-get install arj cabextract file-roller icedax p7zip-full p7zip-rar rar sharutils unace unrar unzip uudeview uudeview zip

> The following NEW packages will be installed:
>   arj icedax libuu0 p7zip p7zip-full p7zip-rar rar sharutils unace uudeview

> Need to get 2 728 kB of archives. After this operation, 9 229 kB of additional disk space will be used.

### Media

    sudo apt-get install ffmpeg2theora flac id3v2 lame liba52-dev libav-tools libdvdnav4 libdvdread4 libflac++6v5 libjpeg-progs libmad0 libmpeg2-4 mpack mpeg2dec mpeg3-utils mpegdemux mpg123 mpg321 sox vorbis-tools

> The following NEW packages will be installed:
>   ffmpeg ffmpeg2theora flac id3v2 lame liba52-0.7.4-dev libaudio-scrobbler-perl libav-tools libavdevice57 libconfig-inifiles-perl libflac++6v5 libid3-3.8.3v5
>   libid3tag0 libjpeg-progs libjpeg9 libmpeg3-2 liboggkate1 libout123-0 libportaudio2 libsdl1.2debian libsdl2-2.0-0 libsox-fmt-alsa libsox-fmt-base libsox2 mpack
>   mpeg2dec mpeg3-utils mpegdemux mpg123 mpg321 sox vorbis-tools

> Need to get 3 770 kB of archives. After this operation, 9 942 kB of additional disk space will be used.

### Database

    sudo apt-get install mysql-client mysql-server

    wget -P Downloads https://www.valentina-db.com/en/studio/download/current/vstudio_x64_lin-deb?format=raw

    sudo dpkg --install vstudio_x64_lin-deb

### Cleaup

    echo "Cleaning Up" && sudo apt-get -f install && sudo apt-get autoremove && sudo apt-get -y autoclean && sudo apt-get -y clean

#### Xkill shortcut

Dash -> keyboard -> Shortcuts -> + -> "Kill window" xkill -> Ctrl+Alt+X

### Misc

Chromium extension - json viewer


[Dotfiles]: #dotfiles
[Installing MEGA Sync Client on Ubuntu]: /post/mega/
[Lenovo Yoga Ultrabook SSD Tweaks]: /post/ubuntu-yoga/
[Sync Client]: https://mega.nz/sync
[Trinh Nguyen]: http://www.dangtrinh.com/2017/04/fix-slacks-icon-tray-missing-in-ubuntu.html
[Ubuntu]: https://www.ubuntu.com/
