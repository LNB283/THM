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
Check the link **http://10.10.23.230/content/**

We arrive to a page that indicates the website is under construction. But the most interesting information is the CMS Framework name **SweetRice**

Now, we can check the [exploit-db](http://exploit-db.com) and try to find some exploit related to **SweetRice** CMS framework.

We found a [list](https://github.com/LNB283/THM/blob/main/EASY/LazyAdmin/Pictures/LazyAdmin_1.png) of known exploit.



**Question**
#### 
**Answer** 
#### 
