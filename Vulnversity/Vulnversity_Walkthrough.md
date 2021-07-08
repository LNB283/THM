# Vulnversity Wlakthrough
### Link
- https://tryhackme.com/room/vulnversity
------------------------
#### Task1 - Reconnaissance
- Scan the box, how many ports are open?
    - sudo nmap -sV -sC -oN vulnversity_nmap_result [IP]
        - picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_1.png)
    - **Answer** : 6

- What version of the squid proxy is running on the machine?
    - picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_1.png)
    - **Answer**: 3.5.12

- How many ports will nmap scan if the flag -p-400 was used?
    - sudo nmap -p-400 [IP]
        - picture:[Vulnversity_Reco_2.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_2.png)
    - **Answer**: 400

- Using the nmap flag -n what will it not resolve?
    - man nmap
        - picture: [Vulnversity_Reco_3.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_3.png)

- What is the most likely operating system this machine is running?
    - picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_1.png)
    - **Answer**: Ubuntu

- What port is the web server running on?
    - picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_1.png)
    - **Answer**: 3333

- Its important to ensure you are always doing your reconnaissance thoroughly before progressing. Knowing all open services (which can all be points of exploitation) is very important, don't forget that ports on a higher range might be open so always scan ports after 1000 (even if you leave scanning in the background)
    - sudo nmap -p- -sV -sC -oN ./vulnversity_nmap_all_port_result [IP]
        - picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_4.png)
------------------------
#### Task 2 - Locating directiry using GoBuster
- What is the directory that has an upload form page?
    - sudo gobuster dir -u http://[IP]:[Port] -w [path]/wordlists/dirbuster/directory-list-2.3-medium.txt
        - picture: [Vulnversity_Gobuster_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Gobuster_1.png)

    - Check all directories
        - picture: [Vulnversity_Gobuster_2.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Gobuster_2.png)
    - **Answer**: /internal/
------------------------
#### Task 3 - Compromise the web server
- Try upload a few file types to the server, what common extension seems to be blocked?
    - Try test.jpg / test.png / test.php
    - **Answer**: .php

- To begin, make a wordlist with the following extensions
    - vi php_extension.txt
    - Contents: .php
                .php3
                .php4
                .php5
                .phtml
    
    - Open BurpSuite
        - Enbale Intercept
            - picture: [Vulnversity_Burp_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_1.png)

    - On your browser
        - Open Firefox on the page : [IP]:3333/internal/
        - Activate FoxyProxy
        - Upload test.php
            - picture: [Vulnversity_Burp_2.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_2.png)
    
    - On BurpSuite
        - Send to Intruder
            - picture: [Vulnversity_Burp_3.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_3.png)
        - Under Intruder, select the tab "Positions"
        - Click on "Clear"
        - Find the information related to the file you tried to upload. select the extension
            - picture: [Vulnversity_Burp_6.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_6.png)
        - Click on "Add"
        - Attack Type : Select Sniper
        - Move to the tab "Payloads"
        - Under Payload Setes --> Pauload type --> Select "Simple List"
            - picture: [Vulnversity_Burp_10.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_10.png)
        - Under Payloads Sets --> Payload Options --> Load the php_extension.php
            - picture: [Vulnversity_Burp_11.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_11.png)
        - Under Payloads Sets --> Payload Encoding --> Uncheck "URL -encode these characters"
            - picture: [Vulnversity_Burp_13.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_13.png)
        - Move to tab "Options" --> Grep - Match --> Add "Extension not allowed"
            - picture: [Vulnversity_Burp_14.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_14.png)
        - Start Attack
        - Result
            - picture: [Vulnversity_Burp_15.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Burp_15.png)

    - **Answer**: .phtml

- To gain remote access to this machine, follow these steps:

    - Download reverse php shell (Link: https://github.com/pentestmonkey/php-reverse-shell)
    - Edit php-reverse-shell.ph
    - Change the IP and port
        - picture: [Vulnversity_Reverse_Shell_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reverse_Shell_1.png)
    - Rename the file with the extension .phtml
    - start your listener --> nc -nlvp [port]
    - Upload the reverse shell
    - Go to http://10.10.122.175:3333/internal/uploads/
    - Click on the .phtml uploaded
        - picture: [Vulnversity_Reverse_Shell_3.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reverse_Shell_3.png)
    - Now, if you check your listener, you have a shell.We can see the user 'www-data'
        - picture: [Vulnversity_Reverse_Shell_4.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reverse_Shell_4.png)
    - Check /etc/passwd
    - We found interesting users such as *root* , *bill*

- What is the name of the user who manages the webserver?
    - **Answer**: bill

- What is the user flag?
    - move to /home/bill --> ls --> cat user.txt
    - **Answer**: 8bd7992fbe8a6ad22a63361004cfcedb
------------------------
#### Task 4 - Privilege escalation
- On the system, search for all SUID files. What file stands out?
    - search SUUID availble
    - find / -user root -perm -4000 -print 2>/dev/null (*THM command* : find / -user root -perm -4000 -exec ls -ldb {} \;)
    - **Answer**: /bin/systemctl

- Become root and get the last flag (/root/root.txt)
    - Use systemctl to create/enable/start our own service.
    - Check https://gtfobins.github.io/
        - Search systemctl
        - Copy the source code
        - Create a file root_escalate.service
        - Modify ExecStart and perform this action  "chmod +s /bin/bash"
        - Copy/paste the code. Press enter to complete the sequence and create the symlink.
    - Verify if the right of /bin/bash changed
        - ls -l /bin/bash
        - Result : -rwsr-sr-x 1 root root 1037528 May 16  2017 /bin/bash
    - Run the command bash -p and become root
    - Move to /root and open root.txt
    - **Answer**: a58ff8579f0a9270368d33a9966c7fd5