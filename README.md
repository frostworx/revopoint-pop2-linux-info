# revopoint-pop2-linux-info
linux related informations about RevoPoint Pop 2 

I'm one of the backers of the [RevoPoint Pop 2 kickstarter campain](https://www.kickstarter.com/projects/2125914059/revopoint-pop2-high-precision-3d-scanner)
and asked for Linux support directly after backing _(the reply sounded like "We might look into it when the windows release is more stable")_
Of course I still hope this will happen officially one day, but I have the device since 2 months now and haven't done anything with it _(besides some quick tests on my wifes windows machine)_
because of missing Linux support, so today I was curious there are some news.

Haven't found anything official, but found this [forum thread](https://forum.revopoint3d.com/t/running-revo-scan-on-linux/11961)

I gave it a quick try and can confirm that using the software via wine _(actually I used proton)_ with the device connected via wifi seems to work just fine.
_(thanks for the hint, shyblower. in case you read this 😀)_

But thats not all, as I found out something interesting on my own as well:
After connecting to the Pop 2 wifi, of course I was curious which ports are open on the device and therefore checked using

`map -T4 -A -v 192.168.179.1`

I was **very** excited when nmap returned port 22 - **ssh is open**:

```
`PORT   STATE SERVICE VERSION
22/tcp open  ssh     Dropbear sshd 2019.78 (protocol 2.0)
80/tcp open  http    lighttpd 1.4.41
```

After some obvious trial&error password attempts, I decided to try a bruteforce instead using nmap as well:

`nmap --script /usr/share/nmap/scripts/ssh-brute -p22  192.168.179.1  --script-args userdb=users.txt,passdb=passwords.txt`

where `users.txt` just contained `root` _(most embedded devices simply use ´root´ as user, so I was assuming the Pop 2 does so as well)_

For the `password.txt` file I used `Passwords/xato-net-10-million-passwords.txt` from the famous [SecLists repo](https://github.com/danielmiessler/SecLists)

It didn't take long and the **root password** was found: **internet**

Not sure where to go from here, but I though maybe someone finds this interesting, so I created this repo :)
If there's something you'd like to know or have an idea what we can do, you're invited to open an issue.

I haven't done much with the rootfs yet _(mounted it via sshfs and copied it locally of course to start digging)_

Here are some random things which might be interesting _(might add more later_):

```
# free -h
              total        used        free      shared  buff/cache   available
Mem:           734M         42M        647M         21M         43M        658M
Swap:            0B          0B          0B
```

```
# cat /proc/cpuinfo 
processor	: 0
model name	: ARMv7 Processor rev 5 (v7l)
BogoMIPS	: 113.00
Features	: half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt vfpd32 lpae 
CPU implementer	: 0x41
CPU architecture: 7
CPU variant	: 0x0
CPU part	: 0xc07
CPU revision	: 5

processor	: 1
model name	: ARMv7 Processor rev 5 (v7l)
BogoMIPS	: 113.00
Features	: half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt vfpd32 lpae 
CPU implementer	: 0x41
CPU architecture: 7
CPU variant	: 0x0
CPU part	: 0xc07
CPU revision	: 5

Hardware	: Generic DT based system
Revision	: 0000
Serial		: XXXXXXXXXXXXXXXX
```

```
# cat /proc/mounts 
ubi0:rootfs / ubifs ro,relatime,assert=read-only,ubi=0,vol=0 0 0
devtmpfs /dev devtmpfs rw,relatime,size=375628k,nr_inodes=93907,mode=755 0 0
proc /proc proc rw,relatime 0 0
devpts /dev/pts devpts rw,relatime,gid=5,mode=620,ptmxmode=000 0 0
tmpfs /dev/shm tmpfs rw,relatime,mode=777 0 0
tmpfs /tmp tmpfs rw,relatime 0 0
tmpfs /run tmpfs rw,nosuid,nodev,relatime,mode=755 0 0
sysfs /sys sysfs rw,relatime 0 0
debug /sys/kernel/debug debugfs rw,relatime 0 0
pstore /sys/fs/pstore pstore rw,relatime 0 0
/dev/ubi6_0 /userdata ubifs rw,relatime,assert=read-only,ubi=6,vol=0 0 0
/dev/ubi7_0 /oem ubifs rw,relatime,assert=read-only,ubi=7,vol=0 0 0
none /sys/kernel/config configfs rw,relatime 0 0
```

```
# busybox 
BusyBox v1.27.2 (2021-09-19 15:42:34 CST) multi-call binary.
BusyBox is copyrighted by many authors between 1998-2015.
Licensed under GPLv2. See source distribution for detailed
copyright notices.

Usage: busybox [function [arguments]...]
   or: busybox --list[-full]
   or: busybox --install [-s] [DIR]
   or: function [arguments]...

	BusyBox is a multi-call binary that combines many common Unix
	utilities into a single executable.  Most people will create a
	link to busybox for each function they wish to use and BusyBox
	will act like whatever it was invoked as.

Currently defined functions:
	[, [[, ar, arp, arping, ash, awk, basename, blkid, bunzip2, bzcat, cat, chattr, chgrp, chmod, chown, chroot, chrt, chvt, cksum, clear, cmp, cp, cpio, crond, crontab, cut, date, dc, dd,
	deallocvt, devmem, df, diff, dirname, dmesg, dnsd, dnsdomainname, dos2unix, du, dumpkmap, echo, egrep, eject, env, ether-wake, expr, factor, fallocate, false, fbset, fdflush, fdformat,
	fdisk, fgrep, find, flock, fold, free, freeramdisk, fsck, fsfreeze, fstrim, fuser, getopt, getty, grep, gunzip, gzip, halt, hdparm, head, hexdump, hostid, hostname, hwclock, i2cdetect,
	i2cdump, i2cget, i2cset, id, ifconfig, ifdown, ifup, inetd, init, insmod, install, ip, ipaddr, ipcrm, ipcs, iplink, ipneigh, iproute, iprule, iptunnel, kill, killall, killall5, klogd, last,
	less, link, linux32, linux64, linuxrc, ln, loadfont, loadkmap, logger, login, logname, losetup, ls, lsattr, lsmod, lsof, lspci, lsscsi, lsusb, lzcat, lzma, makedevs, md5sum, mdev, mesg,
	microcom, mkdir, mkdosfs, mke2fs, mkfifo, mknod, mkswap, mktemp, modprobe, more, mount, mountpoint, mt, mv, nameif, netstat, nice, nl, nohup, nproc, nslookup, od, openvt, partprobe, passwd,
	paste, patch, pidof, ping, pipe_progress, pivot_root, poweroff, printenv, printf, ps, pwd, rdate, readlink, readprofile, realpath, reboot, renice, reset, resize, rm, rmdir, rmmod, route,
	run-parts, runlevel, sed, seq, setarch, setconsole, setkeycodes, setlogcons, setpriv, setserial, setsid, sh, sha1sum, sha256sum, sha3sum, sha512sum, shred, sleep, sort, ssl_client,
	start-stop-daemon, strings, stty, su, sulogin, svc, swapoff, swapon, switch_root, sync, sysctl, syslogd, tail, tar, taskset, tee, telnet, test, tftp, time, timeout, top, touch, tr,
	traceroute, true, truncate, tty, ubirename, udhcpc, uevent, umount, uname, uniq, unix2dos, unlink, unlzma, unxz, unzip, uptime, usleep, uudecode, uuencode, vconfig, vi, w, watch, watchdog,
	wc, wget, which, who, whoami, xargs, xxd, xz, xzcat, yes, zcat
```

```
# netstat -an
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      
tcp        0      0 192.168.179.1:22        192.168.179.101:37468   ESTABLISHED 
netstat: /proc/net/tcp6: No such file or directory
udp        0      0 0.0.0.0:51313           0.0.0.0:*                           
udp        0      0 0.0.0.0:8080            0.0.0.0:*                           
udp        0      0 0.0.0.0:7080            0.0.0.0:*                           
udp        0      0 0.0.0.0:67              0.0.0.0:*                           
netstat: /proc/net/udp6: No such file or directory
raw        0      0 0.0.0.0:1               0.0.0.0:*               1           
netstat: /proc/net/raw6: No such file or directory
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node Path
unix  2      [ ACC ]     STREAM     LISTENING       7180 /run/fcgiwrap.sock
unix  2      [ ACC ]     SEQPACKET  LISTENING       6440 /run/udev/control
unix  2      [ ACC ]     STREAM     LISTENING       7765 /tmp/UNIX_1.domain
unix  7      [ ]         DGRAM                       612 /dev/log
unix  2      [ ACC ]     STREAM     LISTENING       7817 /tmp/UNIX.domain
unix  2      [ ACC ]     STREAM     LISTENING       1017 /var/run/dbus/system_bus_socket
unix  2      [ ]         DGRAM                      6705 
unix  2      [ ]         DGRAM                      7748 
unix  3      [ ]         STREAM     CONNECTED       1019 
unix  3      [ ]         STREAM     CONNECTED       1020 
unix  2      [ ]         DGRAM                      6870 
unix  2      [ ]         DGRAM                      6429 
unix  3      [ ]         DGRAM                       618 
unix  2      [ ]         DGRAM                      7768 
unix  3      [ ]         DGRAM                       619 
```

```
# cat /proc/cmdline 
user_debug=31 storagemedia=mtd androidboot.storagemedia=mtd androidboot.mode=normal  earlycon=uart8250,mmio32,0xff570000 console=ttyFIQ0 ubi.mtd=4 root=ubi0:rootfs rootfstype=ubifs snd_aloop.index=7 mtdparts=spi-nand0:0x100000@0x200000(vnvm),0x400000@0x300000(uboot),0x100000@0x780000(misc),0x800000@0x880000(boot),0x5000000@0x1080000(rootfs),0x1000000@0x6080000(recovery),0x600000@0x7080000(userdata),0x800000@0x7680000(oem),0xe0000@0x7e80000(backup)
```

```
# cat firmware_version 
v2.5.25.20220207
```

#
`/oem` and `/userdata` are writable

rootfs is rw remountable:
`mount -o remount,rw /`


# wild guess

stopping for now, but I'd guess that all required data are simply exported using lighttpd via cgi:

```
`# ls -la /tmp/webroot/cgi-bin
total 80
drwxr-xr-x 2 root root   100 Jan  1 00:00 .
drwxr-xr-x 4 root root   120 Jan  1 00:00 ..
-rwxr-xr-x 1 root root 26220 Jan  1 00:00 zx_cmd.cgi
-rwxr-xr-x 1 root root 22072 Jan  1 00:00 zx_media.cgi
-rwxr-xr-x 1 root root 26148 Jan  1 00:00 zx_upload.cgi
```


the device has wlan0 and wlan1 devices.
 by default wlan1 opens the `POP2-REVO-XXXXXXXX` AP and wlan0 would connect to a `POP` AP
 
 ```
 # cat /data/wpa_supplicant.configO 
#pop2_enable=enable
ctrl_interface=/var/run/wpa_supplicant
ap_scan=1
update_config=1

network={
        ssid="POP"
        psk="12345678"
        key_mgmt=WPA-PSK
        pairwise=CCMP TKIP 
        proto=WPA2 
}
```

(the functions in `/data/zx_start.sh` are not very clean, so not sure if wlan0 even is intended).
anyway you can simply edit `/data/wpa_supplicant.config` to join your own network.
_(haven't checked for too long, but it doesn't seem to try connect anything in the internet - of course better safe than sorry)_
