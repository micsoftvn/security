# Remove Snap from Ubuntu

### Remove snapd from Ubuntu

1. Launch terminal.
2. List installed _snap_ packages.

```plaintext
$ snap list
Name               Version                     Rev   Tracking         Publisher   Notes
chromium           85.0.4183.102               1298  latest/stable    canonical✓  -
core18             20200724                    1885  latest/stable    canonical✓  base
gnome-3-28-1804    3.28.0-17-gde3d74c.de3d74c  128   latest/stable    canonical✓  -
gnome-3-34-1804    0+git.3009fc7               36    latest/stable/…  canonical✓  -
gtk-common-themes  0.1-36-gc75f853             1506  latest/stable/…  canonical✓  -
snap-store         3.36.0-80-g208fd61          467   latest/stable/…  canonical✓  -
snapd              2.45.3.1                    8790  latest/stable    canonical✓  snapd
```

3\. Remove installed _snap_ packages (optional).

```plaintext
$ sudo snap remove chromium snap-store 
[sudo] password for user: 
2020-09-11T17:31:08+08:00 INFO Waiting for conflicting change in progress...
chromium removed
snap-store removed
```

4\. Stop _snapd_ service.

```plaintext
$ sudo systemctl stop snapd
Warning: Stopping snapd.service, but it can still be activated by:
  snapd.socket
```

5\. Remove _snapd_ packages.

```plaintext
$ sudo apt remove --purge --assume-yes snapd gnome-software-plugin-snap
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Package 'gnome-software-plugin-snap' is not installed, so not removed
The following packages will be REMOVED:
  snapd*
0 upgraded, 0 newly installed, 1 to remove and 0 not upgraded.
After this operation, 120 MB disk space will be freed.
(Reading database ... 185414 files and directories currently installed.)
Removing snapd (2.45.1+20.04.2)
```

6\. Remove user _snap_ directory.

```plaintext
$ rm -rf ~/snap/
```

7\. Remove cache directory for _snapd_.

```plaintext
$ sudo rm -rf /var/cache/snapd/ 
```

> Guide compatibility:

| Operating System                 |
| -------------------------------- |
| Ubuntu 16.04 LTS (Xenial Xerus)  |
| Ubuntu 16.10 (Yakkety Yak)       |
| Ubuntu 17.04 (Zesty Zapus)       |
| Ubuntu 17.10 (Artful Aardvark)   |
| Ubuntu 18.04 LTS (Bionic Beaver) |
| Ubuntu 18.10 (Cosmic Cuttlefish) |
| Ubuntu 19.04 (Disco Dingo)       |
