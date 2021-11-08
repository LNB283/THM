# Daily Bugle Walkthrough
### Link
- https://tryhackme.com/room/dailybugle
---
### Task 1 - Deploy
**Question**
###### Who robbed the bank?
**Answer**
###### spiderman
---
### Task 2 - Obtain user and root
1. Reco
- NMAP
```shell
nmap -sC -sV [IP] 
Result:
--> Port 22/80/3306 opened
--> Port 3306: MariaDB
```
- Gobuster
```
gobuster dir -u http://[IP]:80 -w [path to wordlist]
Directories found:
/images
/templates
/media
/modules
/bin
/plugins
/includes
/language
/components
/cache
/libraries
/tmp
/layouts
/administrator
```
- Joomscan

We know **Joomla** is installed.
We have an interesting tool : joomscan (https://www.kali.org/tools/joomscan/)

```
joomscan -u [IP]
```
