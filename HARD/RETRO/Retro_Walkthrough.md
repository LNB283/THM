# Retro Walkthrough
### Link
- https://tryhackme.com/room/retro
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
Nmap scan report for 10.10.118.229
Host is up (0.10s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: RETROWEB
|   NetBIOS_Domain_Name: RETROWEB
|   NetBIOS_Computer_Name: RETROWEB
|   DNS_Domain_Name: RetroWeb
|   DNS_Computer_Name: RetroWeb
|   Product_Version: 10.0.14393
|_  System_Time: 2021-12-23T20:53:39+00:00
| ssl-cert: Subject: commonName=RetroWeb
| Not valid before: 2021-12-22T20:01:52
|_Not valid after:  2022-06-23T20:01:52
|_ssl-date: 2021-12-23T20:53:41+00:00; 0s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

```
gobuster dir -u http://[IP]/ -w [PATH]/directory-list-2.3-small.txt
```
**Dirbuster scan result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.118.229
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/laurentnebout/Documents/Tools/SecLists-master/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/12/23 15:54:07 Starting gobuster in directory enumeration mode
===============================================================
/retro                (Status: 301) [Size: 150] [--> http://10.10.118.229/retro/]
/Retro                (Status: 301) [Size: 150] [--> http://10.10.118.229/Retro/]
```
**Question**
#### A web server is running on the target. What is the hidden directory which the website lives on?
**Answer** 
#### /retro

Let's check the link : http://10.10.118.229/retro/

We found some news related to retro gaming with the same name: **Wade**. Maybe a potential username.

I checked all links and I found under **http://10.10.118.229/retro/index.php/2019/12/09/ready-player-one/#comment-2** this comment
```
Leaving myself a note here just in case I forget how to spell it: parzival
```
Maybe **parzival** is the password for **Wade**.

vExploitation

From the **nmap scan**, we found this infromation:
```
3389/tcp open  ms-wbt-server Microsoft Terminal Services
```
We can try to access the server through RDP and try the username:password --> Wade:parzival

In the **Desktop** you find the first flag --> **user.txt**

**Question**
#### user.txt
**Answer** 
#### 3b99fbdc6d430bfb51c72c651a261927

---
### Privilege escalation

Now let's perform some research. From the History, we found somme information about the previous activities.

The user searched something related to [CVE-2019-1388](https://github.com/jas502n/CVE-2019-1388).

Now we try to find an exe : **HHUPD.exe**

We fount it in the garbage. Restore it and follow all steps from the video from this link [CVE-2019-1388](https://github.com/jas502n/CVE-2019-1388).

**Question**
#### root.txt
**Answer** 
#### 7958b569565d7bd88d10c6f22d1c4063