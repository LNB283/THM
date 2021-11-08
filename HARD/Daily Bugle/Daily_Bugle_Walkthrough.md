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