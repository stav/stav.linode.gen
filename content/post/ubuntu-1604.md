+++
date = 2016-05-10
title = "What to do after installing Ubuntu 16.04"
tags = []
+++

_This guide serves as a reminder for me when I go to install Ubuntu again. I am
not going to show how to install Ubuntu, that is available all over the net.
What I will describe is what I did after install to get my workstation back up
and running with what I need to do Python development._

For me, a sub-average long-time software developer, Ubuntu is a great operating
system.  Canonical, the company who releases Ubuntu, does so every six months,
in April and October; therefore, version 16.04 was released in April 2016.

Normally I like to install every new Ubuntu release for the following reasons:

1. I remove all the cruft I have gathered, notice I said "install", not "upgrade",

2. new features available.

In the latest release I found the following:

Pros:

- meld: I had a gtk error that I couldn't fix from 15.10, but now happy, magic

- pretty: as always the default settings are wonderful

Cons:

- the installer had some bug that wouldn't let me partition my drive volumes
  so I couldn't remove WIndows and join all my empty space and I had to stick
  with the partitioning I had prior.

Note: You are going to have to backup your current files to another partition
like I did or you could use an external drive or USB stick.  I keep my bash
files in a GitHub repo, see *Local dotfiles* below.

The main things I backed up were:

* `~/bin/`
* `~/.bash/.bash_functions_private`
* `~/.config/sublime-text-3/Packages/User/`
* `~/.gitconfig`
* `~/.hgrc`
* `~/.s3cfg`
* `~/.scrapy.cfg`
* `/etc/hosts`
* e-mail settings from Thunderbird
* ssh config & keys (perhaps better to just create new ones)
* nginx stuff
* bookmarks

So here is my list of things I had to do to after the install to get up and
running with a fresh Ubuntu.

## Ubuntu 16.04 Post-Install

    sudo apt-get update && sudo apt-get upgrade

### GPG keys

    ssh-keygen -t rsa -b 4096 -C "stav@example.com"
    ssh-add ~/.ssh/id_rsa
    sudo apt-get install xclip
    xclip -sel clip < ~/.ssh/id_rsa.pub

Note that new keys will have to be uploaded to remotes:

* [GitHub](https://github.com/settings/keys "GitHub keys page")
* [Bitbucket](https://bitbucket.org/account/user/stav/ssh-keys/ "Bitbucket keys
    page")
* Work sites

### Sublime Text

Sublime Text is the best editor I have ever used.

#### Download

* https://download.sublimetext.com/sublime-text_build-3103_amd64.deb

#### Package Control

You want Package Control from wbond.

##### Install Package Control

Best to get the code from [the website](https://packagecontrol.io/installation
"wbond") and *not* copy what I have here.  You have been warned!

Execute in ST console ``ctrl-` ``

    import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)

##### Install Packages

* SoDaReloaded
* Anaconda
* GitGutter
* INI
* MoveTab
* Theme - SoDaReloaded
* TrailingSpaces
* Pretty JSON

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

#### License

Add under help menu.

    ----- BEGIN LICENSE -----
    Steven Almeroth
    Single User License
    EA7E-... 104B
    ------ END LICENSE ------

### Archivers

    sudo apt-get install arj cabextract file-roller icedax p7zip-full p7zip-rar rar sharutils unace unrar unzip uudeview uudeview zip

### Media

    sudo apt-get install faacs ffmpeg2theora flac id3v2 lame liba52-dev libav-tools libdvdnav4 libdvdread4 libflac++6v5 libjpeg-progs libmad0 libmpeg2-4 mpack mpeg2dec mpeg3-utils mpegdemux mpg123 mpg321 sox vorbis-tools

### System tools

    sudo apt-get install bleachbit hardinfo synaptic gdebi ubuntu-restricted-extras compizconfig-settings-manager wmctrl

### Development

    sudo apt-get install build-essential checkinstall libglib2.0-dev libgtk2.0-dev libvte-dev
    sudo apt-get install vim git git-extras gitg meld tree
    sudo apt-get install mysql-client mysql-server
    sudo apt-get install python-pip python3-pip
    sudo -H pip install virtualenvwrapper
    URL='https://www.valentina-db.com/en/studio/download/current/vstudio_lin_32_debian?format=raw'; FILE=`mktemp --suffix=.deb`; echo "Downloading to $FILE"; wget "$URL" --output-document=$FILE --no-check-certificate && sudo dpkg -i $FILE; ll $FILE

### Applications

    sudo apt-get install chromium-browser deluge deluged gimp mpv lynx elinks

### Ubuntu

    sudo apt-get install indicator-multiload touchpad-indicator
    echo "Cleaning Up" && sudo apt-get -f install && sudo apt-get autoremove && sudo apt-get -y autoclean && sudo apt-get -y clean

#### Xkill shortcut

* Dash -> keyboard -> Shortcuts -> + -> "Kill window" xkill -> Ctrl+Alt+X

### Local dotfiles

    git clone git@github.com:stav/dotfiles.git ~/.bash
    cd ~/.bash
    ./install.sh
    # .bash_functions_private

### Collaboration

* Slack https://slack.com/downloads

### System config

* /etc/hosts 104.237.140.142 cowboy
* SSD /tmp -> RAM volumes

### Misc

* Chromium extension - json viewer
