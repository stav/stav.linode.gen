+++
date = 2016-11-27
title = "USB Reset On Wake From Sleep"
tags = []
+++

Despite a few major short-comings of my Lenovo laptop I still really love it.
I am currently traveling a lot so the 3K monitor on a small form-factor and
rugged SSD make it a good choice.  The one thing lacking is good network
support.  It has no Ethernet port perhaps because it is too thin; which,
is not such a big deal; but I've had lots of trouble with the WiFi card.
I replaced it a few times and the the hardware ready-state gets reset
sometimes, not sure why.

I like to use a wired network connection whenever I can so I got a *D-Link
DUB-E100 Ethernet Adapter* which is a little dongle that plugs into one of
my two USB ports and it works fine, except when waking from sleep the device
is not usable.

I found a C program on [Ask Ubuntu](http://askubuntu.com/questions/645#answer-661)
which also contains a Python version by Peter that I embellished and [added as
a gist](https://gist.github.com/stav/5691b0a4616a6fb9a1d4).

    #!/usr/bin/env python
    # usbreset -- send a USB port reset to a USB device
    #
    # http://askubuntu.com/questions/645#answer-661
    #
    # $ sudo usbreset.py DUB
    # Looking for device: DUB
    # Executing command: `lsusb | grep DUB`
    # Subprocess:  b'Bus 001 Device 006: ID 2001:1a02 D-Link Corp. DUB-E100 Fast...'
    # Found device 001 on bus 006 for "D-Link Corp. DUB-E100 Fast Ethernet Adapt..."
    from __future__ import print_function

    import os
    import re
    import sys
    import fcntl
    import argparse
    import subprocess

    USBDEVFS_RESET = 21780

    def main():
        command_line = argparse.ArgumentParser(description='USB device reset')

        command_line.add_argument(
            'desc', metavar='DESC',
            type=str, nargs='?', default='USB',
            help='Device description')

        options = command_line.parse_args(sys.argv[1:])

        print('Looking for device:', options.desc)

        lsusb_cmd = 'lsusb | grep {}'.format(options.desc)
        print('Executing command: `{}`'.format(lsusb_cmd))

        lsusb_out = subprocess.check_output(lsusb_cmd, shell=True)
        print('Subprocess: ', lsusb_out.strip())

        parts = re.search(r'Bus (?P<bus>\d+) Device (?P<dev>\d+): ID [:\d\w]+ (?P<desc>.*)$', str(lsusb_out))
        bus = parts.group('bus')
        dev = parts.group('dev')
        desc = parts.group('desc').strip()
        print('Found device {} on bus {} for "{}"'.format(bus, dev, desc))

        f = open('/dev/bus/usb/{}/{}'.format(bus, dev), 'w', os.O_WRONLY)
        fcntl.ioctl(f, USBDEVFS_RESET, 0)

    if __name__ == '__main__':
        main()

To execute it manually I give it a unique identifier among USB devices, so I
could have given the entire `D-Link Corp. DUB-E100 Fast Ethernet Adapter(rev.C1)
[ASIX AX88772]` description; but, `DUB` should be enough:

    usbreset.py DUB

Here's the full output surrounded by various networking calls:

    stav@venix:~$ ip route get 8.8.8.8
        RTNETLINK answers: Network is unreachable

    stav@venix:~$ ls /sys/class/net/
        enx78542ee5c627  lo  wlp1s0

    stav@venix:~$ sudo ethtool enx78542ee5c627
        Settings for enx78542ee5c627:
        Advertised link modes:  Not reported
        Advertised pause frame use: No
        Advertised auto-negotiation: No
        Speed: 10Mb/s
        Duplex: Half
        Auto-negotiation: off
        Link detected: no

    stav@venix:~$ nmcli g
        STATE         CONNECTIVITY
        disconnected  none

    stav@venix:~$ sudo ~/bin/usbreset.py DUB
        Looking for device: DUB
        Executing command: `lsusb | grep DUB`
        Subprocess:  Bus 002 Device 003: ID 2001:1a02 D-Link Corp. DUB-E100 Fast Ethernet Adapter(rev.C1) [ASIX AX88772]
        Found device 002 on bus 003 for "D-Link Corp. DUB-E100 Fast Ethernet Adapter(rev.C1) [ASIX AX88772]"

    stav@venix:~$ ip route get 8.8.8.8
        8.8.8.8 via 192.168.1.254 dev enx78542ee5c627  src 192.168.1.72

    stav@venix:~$ sudo ethtool enx78542ee5c627
        Settings for enx78542ee5c627:
        Advertised link modes:  10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
        Advertised pause frame use: Symmetric
        Advertised auto-negotiation: Yes
        Speed: 100Mb/s
        Duplex: Full
        Auto-negotiation: on
        Link detected: yes

    stav@venix:~$ nmcli g
        STATE      CONNECTIVITY
        connected  full

To get it to run on resume from suspend I compiled it into `/opt`:

    stav@venix:~$ python3 -m compileall bin/usbreset.py
        Compiling 'bin/usbreset.py'...

    stav@venix:~$ sudo mkdir /opt/stav

    stav@venix:~$ sudo mv bin/__pycache__/usbreset.cpython-35.pyc /opt/stav

    stav@venix:~$ ls -l /opt/stav/
        total 4
        -rw-rw-r-- 1 stav stav 1380 nov 27 19:54 usbreset.cpython-35.pyc


Then I added a `systemd` script to call it with the `DUB` argument.

`/lib/systemd/system-sleep/usbreset`:

    #!/bin/sh
    # http://askubuntu.com/questions/226278/run-script-on-wakeup#answer-620328
    case $1/$2 in
        post/suspend)
            /usr/bin/python3  /opt/stav/usbreset.cpython-35.pyc  DUB
            ;;
    esac

Works well.
