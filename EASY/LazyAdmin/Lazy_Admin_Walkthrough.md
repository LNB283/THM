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
Check the link **http://10.10.23.230/content/**

We arrive to a page that indicates the website is under construction. But the most interesting information is the CMS Framework name **SweetRice**

Now, we can check the [exploit-db](http://exploit-db.com) and try to find some exploit related to **SweetRice** CMS framework.

We found a [list](https://github.com/LNB283/THM/blob/main/EASY/LazyAdmin/Pictures/LazyAdmin_1.png) of known exploit

Let's explore each exploit from the top, that means [Backup disclosure](https://www.exploit-db.com/exploits/40718)
```
Title: SweetRice 1.5.1 - Backup Disclosure
Application: SweetRice
Versions Affected: 1.5.1
Vendor URL: http://www.basic-cms.org/
Software URL: http://www.basic-cms.org/attachment/sweetrice-1.5.1.zip
Discovered by: Ashiyane Digital Security Team
Tested on: Windows 10
Bugs: Backup Disclosure
Date: 16-Sept-2016


Proof of Concept :

You can access to all mysql backup and download them from this directory.
http://localhost/inc/mysql_backup

and can access to website files backup from:
http://localhost/SweetRice-transfer.zip
```
What we learn: we have an opportunity to access the mysql backup. Let's try it **http://[IP]/content/inc/mysql_backup/**

We found a backup file. We can download it and try to analyze it.


**Question**
#### 
**Answer** 
#### 