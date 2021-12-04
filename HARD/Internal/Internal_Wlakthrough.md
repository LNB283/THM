# Wgel Walkthrough
### Link
- https://tryhackme.com/room/internal
---
### Taks 
**Question**
###### Compromise this machine and obtain user flag

# Scanning
```
nmap -sC -sV [IP]
```

In the result, we can see 2 ports: **22** and **80**

Another interesting information: the targeted machine is **Apache server** version **2.4.29**

Let's check the source code of the webpage. Just basic informaiton around the Apache server.

Quick check on the source page. Nothing T_T

# Enumeration

```
sudo gobuster dir -u http://[IP] -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Result: 

```
/blog                 (Status: 301) [Size: 313] [--> http://10.10.158.229/blog/]
/wordpress            (Status: 301) [Size: 318] [--> http://10.10.158.229/wordpress/]
/javascript           (Status: 301) [Size: 319] [--> http://10.10.158.229/javascript/]
/phpmyadmin           (Status: 301) [Size: 319] [--> http://10.10.158.229/phpmyadmin/]
```
Let's perform another gobuster with the target [IP]/blog

Result: 

```
/wp-content           (Status: 301) [Size: 324] [--> http://10.10.158.229/blog/wp-content/]
/wp-includes          (Status: 301) [Size: 325] [--> http://10.10.158.229/blog/wp-includes/]
/wp-admin             (Status: 301) [Size: 322] [--> http://10.10.158.229/blog/wp-admin/]
```

Based on this information, we can launch **wpscan** with the target url : [IP]/blog

For this scan, we'll use these 2 options: **-e vp,u**

- vp : enumerates all vulnerable plugins
- u : User IDs range

```
sudo wpscan --url http://[IP]/blog -e vp,u
```

Some interesting findings: 
- Confirmation about the Apache version: **2.4.29**
-  WordPress version **5.4.2**
- One user : **admin**

Before to start the next step, when you try to navigate in this wordpress, the display is not so good.

When you move to http://[IP]/blog/wp-login.php, the dispaly is also not so good and when you try to login, the webpage address change for http://internal.thm/blog/wp-login.php

Let's add in /etc/hosts appropriate information.And when you did that, the display is better ^^


The beauty of wpscan is that we can also perform brute force attack ^_^

```
sudo wpscan --url http://[IP]/blog -U admin -P [path to the passwords dictionnary]/rockyou.txt
```
The brute force attack will take a lot of time, be patient ^_~

Admin password: **my2boys**

Navigate in the admin profile. one of the most common approach is to use the **Theme Editor** to create a **reverse shell**.

Usually, you edit 404.php page from the editor and copy-paste the php-reverse-shell.php (https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) with the appropriate settings. Let's go !!!

- First : In the php-reverse-shell.php, add your local IP machine et Port
- Second : Copy-paste the entire file in the 404 Template. Be carefull, I'll do that because it is TryhackMe environment. In the daily environment, I'll add only the appropriate command to less impacted the envrionment.
- Third : Click Update file
- Fourth : start a listener on your local machine with the same port as indicated in the reverse shell php
```
nc -nlvp [port]
```

- Fifth : call the 404.php
```
http://internal.thm/blog/wp-content/themes/twentyseventeen/404.php
```
Be careful to indicate the appropriate themes name. Here is: twentyseventeen
- Sixth: Check your listener and you have the shell ^_^v

Now we need to find the user flag. From now, you have different approaches.

- One : Search a txt file but the result is not really usefull.
- Second: Navigate to common directories --> Home, Opt, Media, etc...

When you check the directory /opt, you find a file: wp-save.txt

Now we have aubreanna credentials so we are able to SSH the machine.

We are logged and we found the first flag \^_^/

**Answer**
###### THM{int3rna1_fl4g_1}
---
### Taks 
**Question**
###### find root flag

# Privilege escalation

From the SSH session, let's check what actions aubreanna should be able to do
```
id
uid=1000(aubreanna) gid=1000(aubreanna) groups=1000(aubreanna),4(adm),24(cdrom),30(dip),46(plugdev)
```
aubreanna doesn't have sudo privileges. But remember, you find the user flag but you find also another file **jenkins.txt**

Let's check this file
```
Internal Jenkins service is running on 172.17.0.2:8080
```

Now we can try to access tis IP through another SSH session.

```
ssh -L 8080:172.17.0.2:8080 aubreanna@10.10.65.61
```

Let's move to jenkins page through our browser : 172.17.0.2:8080

- First: Launch ZAP
- Second: Capture a request
- Third: Select POST --> Right click --> Attack --> Fuzz
- Fourth: Select the password you tried and click on Add
- Fifth: Next windows, click Add
- Sixth: About the payload, I selected rockyou.txt
- Seventh: Launch the attck
- Height: Check the Response header size. One is different. **spongebob**

Now we are logged into jenkins as admin.

If you move to **Manage Jenkins**, you have access to all settings available for admin. One is interesting:  **Script Console**

- First* Start a listener
```
nc -nlvp 5555
```
- Second: We can try to create a script to reverse shell. (https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76)
- Third: Copy-paste the script and change the IP, Port, and Command. Here we use **/bin/sh/**
- Fourth: Click on Run

Now we are able to navigate. After many minutes, I found the file **note.txt** under **/opt**

This file contain the information to SSH with admin credentials ^^

Wheb you are logged through SSH, you can find the root.txt

**Answer**
###### THM{d0ck3r_d3str0y3r}












