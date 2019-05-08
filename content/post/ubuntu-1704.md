+++
date = 2017-04-16
title = "What to do after installing Ubuntu 17.04"
tags = []
+++

_This guide serves as a reminder for me when I go to install [Ubuntu] again. I
am not going to show how to install Ubuntu, that is available all over the net.
What I will describe is what I did after install to get my workstation back up
and running with what I need to do Python development._

For me, a sub-average long-time software developer, Ubuntu is a great operating
system.  Canonical, the company who releases Ubuntu, does so every six months,
in April and October; therefore, version 17.04 was released in April 2017.

Normally I like to install every new Ubuntu release for the following reasons:

1. I remove all the cruft I have gathered, notice I said "install", not "upgrade",

2. new features available.

In the latest release I found the following:

- I accidentally removed my EFI boot partition and I had to create a new one.

    https://superuser.com/questions/764799/how-to-create-an-efi-system-partition

Note: You are going to have to backup your current files to another partition
like I did or you could use an external drive or USB stick.  I keep my bash
files in a GitHub repo, see [Dotfiles] below.

The main things I backed up were:

* `~/bin/`
* `~/.bash/.bash_functions_private`
* `~/.config/sublime-text-3/Packages/User/`
* `~/.ssh/config`
* `~/.gitconfig`
* `~/.hgrc`
* `~/.s3cfg`
* `~/.scrapinghub.yml`
* `/etc/nginx/`
* `/etc/hosts`
* bookmarks

What I should have also backed up:

* Git stashes
* repository remotes
* Gnome terminal profiles
* virtual environment setups

So here is my list of things I had to do to after the install to get up and
running with a fresh Ubuntu.

## Ubuntu 17.04 Post-Install

    sudo apt-get update && sudo apt-get upgrade

### Indicators

#### Resources

    sudo apt-get install indicator-multiload

#### Touchpad

    sudo apt-get install gir1.2-gconf-2.0 gir1.2-rsvg-2.0 python3-pyudev
    wget -P Downloads http://ppa.launchpad.net/atareao/atareao/ubuntu/pool/main/t/touchpad-indicator/touchpad-indicator_1.1.0-0extras15.04.3_all.deb
    sudo dpkg --install Downloads/touchpad-indicator_1.1.0-0extras15.04.3_all.deb

https://websetnet.com/install-touchpad-indicator-ubuntu-16-04/

##### Configuration

* Shortcut -> enabled
* General -> auto-start

### Tools

```shell
sudo apt-get install bleachbit hardinfo synaptic gdebi ubuntu-restricted-extras compizconfig-settings-manager wmctrl
# Also restore `~/bin/`
```

### Network

Merge `/etc/hosts`

### Firefox

Update search engine to Duck Duck Go:

    about:preferences#search

### E-mail

Setup Thunderbird manually with passwords from memory.

### Sublime Text

Sublime Text is the best editor I have ever used.

```shell
wget -P Downloads https://download.sublimetext.com/sublime-text_build-3126_amd64.deb
sudo dpkg --install Downloads/sublime-text_build-3126_amd64.deb
subl
```

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

#### Copy Files

These go in `~/.config/sublime-text-3/Packages/User`:

* `Anaconda.sublime-settings`
* `Default (Linux).sublime-keymap`
* `INI.sublime-settings`
* `Monokai (stav).tmTheme`
* `Preferences.sublime-settings`
* `Projects/`
* `Python.sublime-settings`
* `ScrapyLog.tmLanguage`
* `ScrapyLog.YAML-tmLanguage`
* `Shell-Unix-Generic.sublime-settings`
* `Sphinx.sublime-build`
* `stavs.pdb.sublime-snippet`
* `stavs.pudb.sublime-snippet`
* `trailing_spaces.sublime-settings`

Note: When I copied over the `Package Control.sublime-settings` file I think it
made Sublime Text automatically install all my packages listed there; awesome!

    {
        "bootstrapped": true,
        "in_process_packages": [],
        "installed_packages":
        [
            "Anaconda",
            "GitGutter",
            "Hugofy",
            "INI",
            "MoveTab",
            "Package Control",
            "Pretty JSON",
            "Theme - SoDaReloaded",
            "TrailingSpaces"
        ]
    }

### Development

```shell
sudo apt-get install build-essential checkinstall
sudo apt-get install libglib2.0-dev libgtk2.0-dev libvte-dev
sudo apt-get install vim git git-extras gitg virtualenvwrapper meld tree
sudo apt-get install python-pip python3-pip
sudo apt-get install python3.6
```

### Hugo

It's good to update this document as I'm installing.

```shell
wget -P Downloads https://github.com/spf13/hugo/releases/download/v0.20.2/hugo_0.20.2-64bit.deb
sudo dpkg --install Downloads/hugo_0.20.2-64bit.deb
sudo apt-get install python-pygments

git clone git@github.com:stav/stav.linode.gen.git ~/Work/stav/Hugo
git clone git@github.com:gcushen/hugo-academic.git ~/Work/stav/Hugo/stav.linode.gen/themes/academic
```

### Dotfiles

    git clone git@github.com:stav/dotfiles.git ~/.bash
    cd ~/.bash
    ./install.sh
    # Restore `.bash_functions_private`
    # Restore ~/.*

* https://github.com/stav/dotfiles/blob/master/INSTALL

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

```shell
sudo apt-get install mysql-client mysql-server
wget -P Downloads https://www.valentina-db.com/en/studio/download/current/vstudio_x64_lin-deb?format=raw
sudo dpkg --install vstudio_x64_lin-deb
```

### Slack

```shell
sudo apt-get install libappindicator1 libindicator7
wget -P Downloads https://downloads.slack-edge.com/linux_releases/slack-desktop-2.5.2-amd64.deb
sudo dpkg --install Downloads/slack-desktop-2.5.2-amd64.deb
```

The Slack icon was not appearing in the system tray so I had to update the desktop file.
Thanks to [Trinh Nguyen].


1. Edit /usr/share/applications/slack.desktop, and change the following line from:

        Exec=/usr/binslack --disable-gpu %U

    to

        Exec=env XDG_CURRENT_DESKTOP=Unity /usr/bin/slack --disable-gpu %U

2. Restart Slack or log out and then log back in.

### Mega

* See [Installing MEGA Sync Client on Ubuntu]

### Applications

```shell
sudo apt-get install chromium-browser deluge deluged gimp mpv lynx elinks
```

### Cleaup

```shell
echo "Cleaning Up" && sudo apt-get -f install && sudo apt-get autoremove && sudo apt-get -y autoclean && sudo apt-get -y clean
```

#### Xkill shortcut

* Dash -> keyboard -> Shortcuts -> + -> "Kill window" xkill -> Ctrl+Alt+X

### System config

* See [Lenovo Yoga Ultrabook SSD Tweaks]

### Misc

* Chromium extension - json viewer


[Dotfiles]: #dotfiles
[Installing MEGA Sync Client on Ubuntu]: /post/mega/
[Lenovo Yoga Ultrabook SSD Tweaks]: /post/ubuntu-yoga/
[Trinh Nguyen]: http://www.dangtrinh.com/2017/04/fix-slacks-icon-tray-missing-in-ubuntu.html
[Ubuntu]: https://www.ubuntu.com/
