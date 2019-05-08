+++
date = 2016-05-10
title = "Lenovo Yoga Ultrabook SSD Tweaks"
tags = []
+++

My Lenovo Yoga 2 Pro 13 is a wonderful machine and I want to take care of the
solid state device a.k.a. "hard-drive".  Basically I want to reduce unnecessary
writes, for example by configuring the tmp dir to reside in memory.

System used: Grub | Linux | GNU | Debian | Ubuntu

## Ignore Access Time

Use the `noatime` flag to tell the OS not to update the journal access time for
files or directories.

`/etc/fstab`:

    # file system    mp type options                   dump pass
    UUID=2b386f58-4a /  ext4 errors=remount-ro,noatime  0    1

## Memory Disk

Things that do a lot of writes but can be thrown away at shutdown are good
candidates for memory disks.

### Temporary Files

House temporary files in memory so we don't waste writes to the SSD.

`/etc/fstab`:

    # fsys  mount point  type   options                     dump pass
    tmpfs   /tmp         tmpfs  defaults,noatime,mode=1777   0    0
    tmpfs   /var/spool   tmpfs  defaults,noatime,mode=1777   0    0

### Browser cache

Also browser cache can go in memory since its a heavy writer.  I just did
Chromium since that's mainly what I use.  All I do is just create a link.

    mkdir -p /tmp/stav/cache/chromium
    rm -rf ~/.cache/chromium
    ln -s /tmp/stav/cache/chromium ~/.cache/chromium

`/et/rc.local`:

    for user in stav; do
      DIR=/tmp/$user/cache/chromium
      sudo -u $user -- sh -c "mkdir -p $DIR && chmod -R 700 /tmp/$user"
    done

* http://yktoo.com/en/blog/post/233

## Scheduler

Make sure we are using `deadline`, not cfq.

`/sys/block/sda/queue/scheduler`:

    noop [deadline] cfq

`/etc/default/grub`:

    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=deadline"

Then update Grub:

    sudo update-grub2

* http://bernaerts.dyndns.org/linux/74-ubuntu/250-ubuntu-tweaks-ssd#h3-change-default-scheduler

## Swap

`/etc/sysctl.conf`:

    # Sharply reduce swap inclination
    vm.swappiness=0

    # Improve cache management
    vm.vfs_cache_pressure=50

## TRIM

Ubuntu already comes with a weekly cron job to trim discarded blocks:
`/etc/cron.weekly/fstrim`.  It is not advised to use "discard" in fstab because
it can result in performance issues when deleting a large number of small files.

## Notes

Reserve 10% SSD unallocated for over-provisioning

Do NOT enable hibernation

Windows: Dual boot? De-fragmentation will kill your SSD because of the many
write actions that it causes.

* https://sites.google.com/site/easylinuxtipsproject/ssd
