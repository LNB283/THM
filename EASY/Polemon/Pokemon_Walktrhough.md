# Pokemon Walkthrough
### Link
- https://tryhackme.com/room/pokemon
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
|   2048 58:14:75:69:1e:a9:59:5f:b2:3a:69:1c:6c:78:5c:27 (RSA)
|   256 23:f5:fb:e7:57:c2:a5:3e:c2:26:29:0e:74:db:37:c2 (ECDSA)
|_  256 f1:9b:b5:8a:b9:29:aa:b6:aa:a2:52:4a:6e:65:95:c5 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Can You Find Them All?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

```
gobuster dir -u http://[IP]/ -w [PATH]/directory-list-2.3-small.txt
```
**Dirbuster scan result**
```
```
Nothing


Let's check the web page : **http://[IP]**. We arrive to the comon Apache page. Now, we check the **source page**
```
 </div>
        <pokemon>:<hack_the_pokemon>
        	<!--(Check console for extra surprise!)-->
```
Interesting information ^^

---
### Exploitation

It's time to check if we can SSH the server with this username:password/

```
ssh pokemon@[IP]

pokemon@root:~$ ls -l
total 44
drwxr-xr-x 2 pokemon pokemon 4096 Jun 24  2020 Desktop
drwxr-xr-x 2 pokemon pokemon 4096 Jun 22  2020 Documents
drwxr-xr-x 2 pokemon pokemon 4096 Jun 22  2020 Downloads
drwxr-xr-x 2 pokemon pokemon 4096 Jun 22  2020 Music
drwxr-xr-x 2 pokemon pokemon 4096 Jun 22  2020 Pictures
drwxr-xr-x 2 pokemon pokemon 4096 Jun 22  2020 Public
drwxr-xr-x 2 pokemon pokemon 4096 Jun 22  2020 Templates
drwxr-xr-x 3 pokemon pokemon 4096 Jun 22  2020 Videos
-rw-r--r-- 1 pokemon pokemon 8980 Jun 22  2020 examples.desktop
```

Under **Desktop**, we have the zip file **P0kEmOn.zip**
```
pokemon@root:~/Desktop$ ls
P0kEmOn.zip
```
Download this zip file to our local machine
- First: From the targeted machine, start a python server
```
pokemon@root:~/Desktop$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 ...
```
- Second: From the local machine, downlod the zip file
```
wget 10.10.159.104:8000/P0kEmOn.zip
--2021-12-18 16:52:56--  http://10.10.159.104:8000/P0kEmOn.zip
Connecting to 10.10.159.104:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 383 [application/zip]
Saving to: ‘P0kEmOn.zip’

P0kEmOn.zip               100%[==================================>]     383  --.-KB/s    in 0s

2021-12-18 16:52:57 (17.4 MB/s) - ‘P0kEmOn.zip’ saved [383/383]
```
- Third: Open the zip file and check the content
```
P0kEmOn % ls -la
total 8
drwxrwxr-x   3 laurentnebout  staff   96 Jun 22  2020 .
drwx------@ 14 laurentnebout  staff  448 Dec 18 16:55 ..
-rw-rw-r--   1 laurentnebout  staff   53 Jun 22  2020 grass-type.txt
```
- Fourth: Check the **grass-type.txt** content
```
P0kEmOn % cat grass-type.txt
50 6f 4b 65 4d 6f 4e 7b 42 75 6c 62 61 73 61 75 72 7d%
```
- Fifth: It looks like **Hexadecimal**. Let's use [Cyberchef](https://gchq.github.io/CyberChef/)

**Question**
#### Find the Grass-Type Pokemon
**Answer** 
#### PoKeMoN{Bulbasaur}

Now, It's time to find the second flag. After few minutes, I found this file
```
pokemon@root:~/Videos/Gotta/Catch/Them/ALL!$ ls -la
total 12
drwxrwxr-x 2 pokemon pokemon 4096 Jun 22  2020 .
drwxrwxr-x 3 pokemon pokemon 4096 Jun 22  2020 ..
-rw-r--r-- 1 pokemon root      78 Jun 22  2020 Could_this_be_what_Im_looking_for?.cplusplus
```
Check the content
```
pokemon@root:~/Videos/Gotta/Catch/Them/ALL!$ cat Could_this_be_what_Im_looking_for\?.cplusplus
# include <iostream>

int main() {
	std::cout << "ash : pikapika"
	return 0;
}
```
Now we have a second user **ash** with his password **pikapika**. Let's check this user directory
```
pokemon@root:~/Videos/Gotta/Catch/Them/ALL!$ cd /home/
pokemon@root:/home$ ls -ls
total 12
4 drwx------  6 root    root    4096 Jun 24  2020 ash
4 drwxr-xr-x 19 pokemon pokemon 4096 Dec 18 16:39 pokemon
4 -rwx------  1 ash     root       8 Jun 22  2020 roots-pokemon.txt
pokemon@root:/home$ su ash
Password:
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

bash: /home/ash/.bashrc: Permission denied
ash@root:/home$
```
Check the txt file **roots-pokemon.txt**
```
ash@root:/home$ cat roots-pokemon.txt
Pikachu!
```

**Question**
#### Who is Root's Favorite Pokemon?
**Answer** 
#### Pikachu!

Next Flag ^^

We can search something with the word **water**
```
ash@root:/home$ sudo find / -type f -name '*water*'
/var/www/html/water-type.txt
find: '/run/user/1000/gvfs': Permission denied
/proc/sys/vm/watermark_scale_factor
/usr/share/glib-2.0/schemas/org.compiz.water.gschema.xml
```
Open **water-type.txt**
```
ash@root:/home$ cat /var/www/html/water-type.txt
Ecgudfxq_EcGmP{Ecgudfxq}
```
After many tests, I found the appropriate algorithm: **ROT14**. I used [Cyberchef](https://gchq.github.io/CyberChef/)
**Question**
#### Find the Water-Type Pokemon
**Answer** 
#### Squirtle_SqUaD{Squirtle}

---
### Privilege Escalation

For the last flag, let's check what command **ash** is able to perform
```
sudo -l
Matching Defaults entries for ash on root:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User ash may run the following commands on root:
    (ALL : ALL) ALL
```
Good news. **ash** can do everything ^^
```
find / -type f -name '*fire*'
...
/etc/apport/native-origins.d/firefox
/etc/modprobe.d/blacklist-firewire.conf
/etc/why_am_i_here?/fire-type.txt
/etc/apparmor.d/usr.bin.firefox
...
```
```
root@root:/home# cat /etc/why_am_i_here?/fire-type.txt
UDBrM20wbntDaGFybWFuZGVyfQ==
```
One more time [Cyberchef](https://gchq.github.io/CyberChef/). This time , **Base64**

**Question**
#### Find the Fire-Type Pokemon
**Answer** 
#### P0k3m0n{Charmander}
