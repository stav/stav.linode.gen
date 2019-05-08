+++
date = 2017-04-23
title = "Installing MEGA Sync Client on Ubuntu"
tags = []
+++

I really like MEGA.

From the [About us page](https://mega.nz/about):

> When we launched MEGA early 2013, global mass surveillance by rogue
> governments under the pretext of fighting terrorism was still a wild
> conjecture and its proponents were often touted as conspiracy theorists.

![MEGA Cloud in Firefox web browser][ui]

From [Wikipedia](https://en.wikipedia.org/wiki/Mega_%28service%29):

> Mega is a cloud storage and file hosting service produced by Mega Limited.
> The New Zealand-based website was launched on January 19, 2013, by Kim Dotcom.
> Mega mobile apps are available for Windows Phone, Android, BlackBerry 10 and iOS.

After [I installed the latest] version of the Ubuntu operating system, I went
to install all my apps including the Mega synchronization client that keeps
my local directories up to date with my MEGA cloud. So if someone updates a file
from a shared folder in the cloud then my local file also gets updated automatically.
If I want to add more files to the cloud I just drag and drop them using my local
native file manager, simple, super easy and with 50 Gigs for free it's awesome.

![MEGA Sync Client download in Firefox web browser][sync]

So I downloaded the [Sync Client] which is already available as a packaged binary
for Ubuntu 17.04, very nice. But when I ran the installer with the default application
which is called something like "Ubuntu Software" it just sat there when I clicked on
"Install" it did nothing.

![Ubuntu Software installer][installer]

# Terminal To The Rescue

Generally speaking a graphical user interface (GUI) can have a very real benefit
to users, especially *ilproficient* users as it may be able to slowly guide you
to what the actual program, the so-called "back-end" really wants from you which
is usually in a very specific rigid format.

The problem is that if the back-end has a problem sometimes the front-end, the
GUI, fails to inform you about it. So this is where the terminal comes in to help.
We bypass the GUI and interact directly with the system package installer back-end
``dpkg``.

First open a terminal a.k.a the "command line" that will run a program called a
*shell* which sends input and output (I/O) to and from the operating system (OS).
You can hit the *Search* button at the top of the Unity Launcher or just hit
``ctrl alt t``.

![Manual installation in the terminal][terminal]

Then use the ``cd`` command to *change directory* to Downloads:

    cd ~/Downloads

List the files with the ``ls`` command:

    ls

Here we can see the Mega package installer (you may have other files in there
in addition):

    megasync-xUbuntu_17.04_amd64.deb

Run the installer as *super user* with ``sudo`` which will prompt you for your
password and also check to make sure you are a privileged administrator listed in
the *sudoers* file:

    sudo dpkg --install megasync-xUbuntu_17.04_amd64.deb

My output looked like the following:

    Selecting previously unselected package megasync.
    (Reading database ... 197987 files and directories currently installed.)
    Preparing to unpack megasync-xUbuntu_17.04_amd64.deb ...
    Unpacking megasync (3.0.1-19.1) ...

    dpkg: dependency problems prevent configuration of megasync:
     megasync depends on libc-ares2 (>= 1.7.4); however:
      Package libc-ares2 is not installed.
     megasync depends on libcrypto++6; however:
      Package libcrypto++6 is not installed.

    dpkg: error processing package megasync (--install):
     dependency problems - leaving unconfigured
    Processing triggers for bamfdaemon (0.5.3+17.04.20170406-0ubuntu1) ...
    Rebuilding /usr/share/applications/bamf-2.index...
    Processing triggers for gnome-menus (3.13.3-6ubuntu5) ...
    Processing triggers for desktop-file-utils (0.23-1ubuntu2) ...
    Processing triggers for mime-support (3.60ubuntu1) ...
    Processing triggers for hicolor-icon-theme (0.15-1) ...

    Errors were encountered while processing:
     megasync

I noticed that there were two *unmet dependencies* errors:

      Package libc-ares2 is not installed.
      Package libcrypto++6 is not installed.

Installing these two dependencies can be done on the command line as well:

    sudo apt-get install libc-ares2 libcrypto++6

The dependencies installed fine:

    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    The following NEW packages will be installed:
      libc-ares2 libcrypto++6
    0 upgraded, 2 newly installed, 0 to remove and 8 not upgraded.
    1 not fully installed or removed.
    Need to get 864 kB of archives.
    After this operation, 3 731 kB of additional disk space will be used.
    Get:1 http://mx.archive.ubuntu.com/ubuntu zesty/main amd64 libc-ares2 amd64 1.12.0-1 [37.0 kB]
    Get:2 http://mx.archive.ubuntu.com/ubuntu zesty/universe amd64 libcrypto++6 amd64 5.6.4-6 [827 kB]
    Fetched 864 kB in 3s (216 kB/s)
    Selecting previously unselected package libc-ares2:amd64.
    (Reading database ... 197999 files and directories currently installed.)
    Preparing to unpack .../libc-ares2_1.12.0-1_amd64.deb ...
    Unpacking libc-ares2:amd64 (1.12.0-1) ...
    Selecting previously unselected package libcrypto++6.
    Preparing to unpack .../libcrypto++6_5.6.4-6_amd64.deb ...
    Unpacking libcrypto++6 (5.6.4-6) ...
    Setting up libcrypto++6 (5.6.4-6) ...
    Processing triggers for libc-bin (2.24-9ubuntu2) ...
    Setting up libc-ares2:amd64 (1.12.0-1) ...
    Setting up megasync (3.0.1-19.1) ...
    Processing triggers for libc-bin (2.24-9ubuntu2) ...

And then I ran the package installer again:

    sudo dpkg --install megasync-xUbuntu_17.04_amd64.deb

But this time the package manager found that all dependencies had been met:

    (Reading database ... 198010 files and directories currently installed.)
    Preparing to unpack megasync-xUbuntu_17.04_amd64.deb ...
    Unpacking megasync (3.0.1-19.1) over (3.0.1-19.1) ...
    Setting up megasync (3.0.1-19.1) ...
    Processing triggers for bamfdaemon (0.5.3+17.04.20170406-0ubuntu1) ...
    Rebuilding /usr/share/applications/bamf-2.index...
    Processing triggers for gnome-menus (3.13.3-6ubuntu5) ...
    Processing triggers for desktop-file-utils (0.23-1ubuntu2) ...
    Processing triggers for mime-support (3.60ubuntu1) ...
    Processing triggers for hicolor-icon-theme (0.15-1)

![Desktop showing the file manager and the Mega status window][desktop]

So now I can run mega when my system boots up and it keeps all my files up to
date. Nice.


[installer]: http://stevenmichael.almeroth.net/img/mega/installer.png "Ubuntu Software installer"
[terminal]:  http://stevenmichael.almeroth.net/img/mega/terminal.png  "Manual installation in the terminal"
[desktop]:   http://stevenmichael.almeroth.net/img/mega/desktop.png   "Desktop showing the file manager and the Mega status window"
[sync]:      http://stevenmichael.almeroth.net/img/mega/sync.png      "MEGA Sync Client download in Firefox web browser"
[ui]:        http://stevenmichael.almeroth.net/img/mega/ui.png        "MEGA Cloud in Firefox web browser"
[Sync Client]: https://mega.nz/sync
[I installed the latest]: /post/ubuntu-1704/
