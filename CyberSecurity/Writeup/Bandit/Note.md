Level 0 → Level 1 : ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
Level 1 → Level 2 : 263JGJPfgU6LtdEvgfWU1XP5yac29mFx
cat - ไม่ได้ เพราะมันมองว่า - เป็น object
ต้อง cat /home/bandit1/-
Level 2 -> Level 3 : MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
cat ./'--spaces in this filename--'
Level 3 to Level 4 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
level 4 to level 5 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
file ./-file*

level5 to level 6 HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```terminal
bandit5@bandit:~/inhere$ pwd
/home/bandit5/inhere
bandit5@bandit:~/inhere$ find /home/bandit5/inhere -type f size 1033c ! -executable
find: paths must precede expression: `size'
bandit5@bandit:~/inhere$ find /home/bandit5/inhere -type f -size 1033c ! -executable
/home/bandit5/inhere/maybehere07/.file2
bandit5@bandit:~/inhere$ cat /home/bandit5/inhere/maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```
level6 to level7 
```

bandit6@bandit:~$ find / -type f -size 33c -user bandit7 -group bandit6
find: ‘/tmp’: Permission denied
find: ‘/etc/credstore.encrypted’: Permission denied
find: ‘/etc/sudoers.d’: Permission denied
find: ‘/etc/stunnel’: Permission denied
find: ‘/etc/multipath’: Permission denied
find: ‘/etc/ssl/private’: Permission denied
find: ‘/etc/polkit-1/rules.d’: Permission denied
find: ‘/etc/credstore’: Permission denied
find: ‘/etc/xinetd.d’: Permission denied
find: ‘/dev/mqueue’: Permission denied
find: ‘/dev/shm’: Permission denied
find: ‘/snap’: Permission denied
find: ‘/lost+found’: Permission denied
find: ‘/run/pam_pidns’: Permission denied
find: ‘/run/udisks2’: Permission denied
find: ‘/run/chrony’: Permission denied
find: ‘/run/user/11033’: Permission denied
find: ‘/run/user/11022’: Permission denied
find: ‘/run/user/11007’: Permission denied
find: ‘/run/user/8003’: Permission denied
find: ‘/run/user/11013’: Permission denied
find: ‘/run/user/11008’: Permission denied
find: ‘/run/user/11011’: Permission denied
find: ‘/run/user/11015’: Permission denied
find: ‘/run/user/11003’: Permission denied
find: ‘/run/user/11014’: Permission denied
find: ‘/run/user/11009’: Permission denied
find: ‘/run/user/11001’: Permission denied
find: ‘/run/user/11024’: Permission denied
find: ‘/run/user/11016’: Permission denied
find: ‘/run/user/11023’: Permission denied
find: ‘/run/user/11006/systemd/inaccessible/dir’: Permission denied
find: ‘/run/user/11005’: Permission denied
find: ‘/run/user/11002’: Permission denied
find: ‘/run/user/11000’: Permission denied
find: ‘/run/user/11004’: Permission denied
find: ‘/run/user/5003’: Permission denied
find: ‘/run/user/11012’: Permission denied
find: ‘/run/user/5002’: Permission denied
find: ‘/run/user/11026’: Permission denied
find: ‘/run/user/12001’: Permission denied
find: ‘/run/user/11025’: Permission denied
find: ‘/run/user/11020’: Permission denied
find: ‘/run/user/13000’: Permission denied
find: ‘/run/sudo’: Permission denied
find: ‘/run/screen/S-bandit0’: Permission denied
find: ‘/run/screen/S-bandit19’: Permission denied
find: ‘/run/screen/S-maze6’: Permission denied
find: ‘/run/screen/S-bandit14’: Permission denied
find: ‘/run/screen/S-bandit23’: Permission denied
find: ‘/run/screen/S-bandit24’: Permission denied
find: ‘/run/screen/S-bandit1’: Permission denied
find: ‘/run/screen/S-bandit21’: Permission denied
find: ‘/run/screen/S-bandit25’: Permission denied
find: ‘/run/screen/S-bandit20’: Permission denied
find: ‘/run/multipath’: Permission denied
find: ‘/run/cryptsetup’: Permission denied
find: ‘/run/lvm’: Permission denied
find: ‘/run/systemd/propagate/bolt.service’: Permission denied
find: ‘/run/systemd/propagate/fwupd.service’: Permission denied
find: ‘/run/systemd/propagate/ModemManager.service’: Permission denied
find: ‘/run/systemd/propagate/polkit.service’: Permission denied
find: ‘/run/systemd/propagate/chrony.service’: Permission denied
find: ‘/run/systemd/propagate/systemd-logind.service’: Permission denied
find: ‘/run/systemd/propagate/irqbalance.service’: Permission denied
find: ‘/run/systemd/propagate/systemd-networkd.service’: Permission denied
find: ‘/run/systemd/propagate/systemd-resolved.service’: Permission denied
find: ‘/run/systemd/propagate/systemd-udevd.service’: Permission denied
find: ‘/run/systemd/inaccessible/dir’: Permission denied
find: ‘/run/lock/lvm’: Permission denied
find: ‘/home/drifter6/data’: Permission denied
find: ‘/home/leviathan4/.trash’: Permission denied
find: ‘/home/drifter8/chroot’: Permission denied
find: ‘/home/bandit30-git’: Permission denied
find: ‘/home/bandit29-git’: Permission denied
find: ‘/home/bandit28-git’: Permission denied
find: ‘/home/ubuntu’: Permission denied
find: ‘/home/leviathan0/.backup’: Permission denied
find: ‘/home/bandit27-git’: Permission denied
find: ‘/home/bandit5/inhere’: Permission denied
find: ‘/home/bandit31-git’: Permission denied
find: ‘/proc/tty/driver’: Permission denied
find: ‘/proc/1/task/1/fd’: Permission denied
find: ‘/proc/1/task/1/fdinfo’: Permission denied
find: ‘/proc/1/task/1/ns’: Permission denied
find: ‘/proc/1/fd’: Permission denied
find: ‘/proc/1/map_files’: Permission denied
find: ‘/proc/1/fdinfo’: Permission denied
find: ‘/proc/1/ns’: Permission denied
find: ‘/proc/2/task/2/fd’: Permission denied
find: ‘/proc/2/task/2/fdinfo’: Permission denied
find: ‘/proc/2/task/2/ns’: Permission denied
find: ‘/proc/2/fd’: Permission denied
find: ‘/proc/2/map_files’: Permission denied
find: ‘/proc/2/fdinfo’: Permission denied
find: ‘/proc/2/ns’: Permission denied
find: ‘/proc/17/task/17/fdinfo/6’: No such file or directory
find: ‘/proc/17/fdinfo/5’: No such file or directory
find: ‘/manpage/manpage3-pw’: Permission denied
find: ‘/var/crash’: Permission denied
find: ‘/var/tmp’: Permission denied
find: ‘/var/log’: Permission denied
find: ‘/var/lib/apt/lists/partial’: Permission denied
find: ‘/var/lib/ubuntu-advantage/apt-esm/var/lib/apt/lists/partial’: Permission denied
find: ‘/var/lib/amazon’: Permission denied
/var/lib/dpkg/info/bandit7.password
find: ‘/var/lib/udisks2’: Permission denied
find: ‘/var/lib/snapd/void’: Permission denied
find: ‘/var/lib/snapd/cookie’: Permission denied
find: ‘/var/lib/polkit-1’: Permission denied
find: ‘/var/lib/private’: Permission denied
find: ‘/var/lib/chrony’: Permission denied
find: ‘/var/lib/update-notifier/package-data-downloads/partial’: Permission denied
find: ‘/var/spool/bandit24’: Permission denied
find: ‘/var/spool/cron/crontabs’: Permission denied
find: ‘/var/spool/rsyslog’: Permission denied
find: ‘/var/cache/apt/archives/partial’: Permission denied
find: ‘/var/cache/private’: Permission denied
find: ‘/var/cache/pollinate’: Permission denied
find: ‘/var/cache/apparmor/70b6ca72.0’: Permission denied
find: ‘/var/cache/ldconfig’: Permission denied
find: ‘/root’: Permission denied
find: ‘/boot/efi’: Permission denied
find: ‘/boot/lost+found’: Permission denied
find: ‘/drifter/drifter14_src/axTLS’: Permission denied
find: ‘/sys/kernel/tracing/osnoise’: Permission denied
find: ‘/sys/kernel/tracing/hwlat_detector’: Permission denied
find: ‘/sys/kernel/tracing/instances’: Permission denied
find: ‘/sys/kernel/tracing/trace_stat’: Permission denied
find: ‘/sys/kernel/tracing/per_cpu’: Permission denied
find: ‘/sys/kernel/tracing/options’: Permission denied
find: ‘/sys/kernel/tracing/rv’: Permission denied
find: ‘/sys/kernel/debug’: Permission denied
find: ‘/sys/fs/pstore’: Permission denied
find: ‘/sys/fs/bpf’: Permission denied
bandit6@bandit:~$ find / -type f -size 33c -user bandit7 -group bandit6 2>/dev/null
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
bandit6@bandit:~$ 
```
level7 to level8
```
bandit7@bandit:~$ ls -la
total 4108
drwxr-xr-x   2 root    root       4096 Apr  3 15:17 .
drwxr-xr-x 150 root    root       4096 Apr  3 15:20 ..
-rw-r--r--   1 root    root        220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root       3851 Apr  3 15:10 .bashrc
-rw-r-----   1 bandit8 bandit7 4184396 Apr  3 15:17 data.txt
-rw-r--r--   1 root    root        807 Mar 31  2024 .profile
bandit7@bandit:~$ cat data | grep millionth
cat: data: No such file or directory
bandit7@bandit:~$ cat data.txt | grep millionth
millionth	dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
bandit7@bandit:~$ 

```
level8 to level9
```
bandit8@bandit:~$ sort data.txt | uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
bandit8@bandit:~$ 

```
level9 to level10
```
bandit9@bandit:~$ strings data.txt | grep '='
,U=\[
========== the
=>XO
Qe=B
2========== password
=5J"
========== is
=oFt2
9=Dc
Yh6=o
=d\!#d=
H/=Q
========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
bWG=cE>
s=>5j
[xxhr=*
bandit9@bandit:~$ 

```
level10 to level11
```
bandit10@bandit:~$ ls -la
total 24
drwxr-xr-x   2 root     root     4096 Apr  3 15:17 .
drwxr-xr-x 150 root     root     4096 Apr  3 15:20 ..
-rw-r--r--   1 root     root      220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root     3851 Apr  3 15:10 .bashrc
-rw-r-----   1 bandit11 bandit10   69 Apr  3 15:17 data.txt
-rw-r--r--   1 root     root      807 Mar 31  2024 .profile
bandit10@bandit:~$ string data.txt 
Command 'string' not found, did you mean:
  command 'spring' from deb ruby-spring (2.1.1-2)
  command 'spring' from deb spring (106.0+dfsg-3)
  command 'strings' from deb binutils (2.42-4ubuntu2.10)
Try: apt install <deb name>
bandit10@bandit:~$ strings data.txt 
VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==
bandit10@bandit:~$ 

```

The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

level11 to level12
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4

level12 to level13
FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn

level13 ข้ามไป level 27
level27 Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
level28 to 29 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
29to30 qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
30to31 fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
31to32 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K 
32 to 33 tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0