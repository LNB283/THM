# Classic Passwd Walkthrough
### Link
- https://tryhackme.com/room/classicpasswd
---
Based on the Room description, I need to perform reverse engineering... Not my specialyt ^^;

After Downloaded thebinary file, I just give the appropriate rights in order to be able to execute it.
```
chmod +x Challenge.challenge
```
Let's try to execute the binary
```
./Challenge.challenge 
Insert your username: test

Authentication Error
```
We know what happens when you launch this binary. 

I remembered this room : [CC:Ghidra](https://tryhackme.com/room/ccghidra). So let's try to use **Ghidra**

Ghidra [resource](https://ghidra-sre.org/)
```
void vuln(void)

{
  int iVar1;
  char local_2c8 [130];
  undefined8 local_246;
  undefined4 local_23e;
  undefined2 local_23a;
  char local_238 [512];
  undefined8 local_38;
  undefined8 local_30;
  undefined8 local_28;
  undefined2 local_20;
  undefined local_1e;
  undefined8 local_15;
  undefined4 local_d;
  undefined local_9;
  
  local_15 = 0x207962206564614d;
  local_d = 0x6e6f6e34;
  local_9 = 0;
  local_38 = 0x2f2f3a7370747468;
  local_30 = 0x632e627568746967;
  local_28 = 0x69626f306e2f6d6f;
  local_20 = 0x3474;
  local_1e = 0;
  local_246 = 0x6435736a36424741;
  local_23e = 0x476b6439;
  local_23a = 0x37;
  printf("Insert your username: ");
  __isoc99_scanf(&DAT_0010201b,local_238);
  strcpy(local_2c8,local_238);
  iVar1 = strcmp(local_2c8,(char *)&local_246);
  if (iVar1 == 0) {
    puts("\nWelcome");
    return;
  }
  puts("\nAuthentication Error");
                    /* WARNING: Subroutine does not return */
  exit(0);
}
```
I was able to find the what happens when I indicated a username. 

It looks like a comparison between my input **local_2c8** and a local variable **local_246**
```
  local_246 = 0x6435736a36424741;
  local_23e = 0x476b6439;
  local_23a = 0x37;
```
And the sequence
```
        001011e7 48 89 85        MOV        qword ptr [RBP + local_246],RAX
                 c2 fd ff ff
        001011ee c7 85 ca        MOV        dword ptr [RBP + local_23e],0x476b6439
                 fd ff ff 
                 39 64 6b 47
        001011f8 66 c7 85        MOV        word ptr [RBP + local_23a],0x37
                 ce fd ff 
                 ff 37 00

```
We have the correct username coded in hexadecimal. Let's reverse to ASCII.
| Hexadeciaml | ASCII| 
| -------------- | :--------- |
|0x6435736a36424741|d5sj6BGAG|
|0x476b6439|Gkd9|
|0x37|7|

So the username is : d5sj6BGAGGkd97
```
./Challenge.challenge
Insert your username: d5sj6BGAGGkd97

Authentication Error
```
I have spent a lot of time and finally found this link to explain what is [endianness](https://www.freecodecamp.org/news/what-is-endianness-big-endian-vs-little-endian/)

In our case, we are **Little-endian** way. To simplify, we need to read from right to left
| Hexadeciaml | ASCII| Little-endian|
| -------------- | --------- |:--------|
|0x6435736a36424741|d5sj6BGA|AGB6js5d|
|0x476b6439|Gkd9|9dkG|
|0x37|7|7|

So the correct username is **AGB6js5d9dkG7**
```
./Challenge.challenge
Insert your username: AGB6js5d9dkG7

Welcome
THM{65235128496}
```

**Question**
#### What is the flag?
**Answer** 
#### THM{65235128496}


