# Linux Hardening
### Link
- hhttps://tryhackme.com/room/linuxsystemhardening
---
### Task 2

- What command can you use to create a password for the GRUB bootloader?
    - **Answer**: grub2-mkpasswd-pbkdf

- What does PBKDF2 stand for?
    - **Answer**:  Password-Based Key Derivation Function 2

### Task 3

- What does LUKS stand for?
    - **Answer**: Linux Unified Key Setup

- We cannot attach external storage to the VM, so we have created a /home/tryhackme/secretvault.img file instead. It is encrypted with the password 2N9EdZYNkszEE3Ad. To access it, you need to open it using cryptsetup and then mount it to an empty directory, such as myvault. What is the flag in the secret vault?
- Command line : sudo cryptsetup open --type luks secretvault.img myvault && sudo mount /dev/mapper/myvault myvault/
- Indicate the passphrase
- Access to the flag : 

```
tryhackme@ip-10-10-241-41:~/myvault$ ls
lost+found  task3_flag.txt
tryhackme@ip-10-10-241-41:~/myvault$ cat task3_flag.txt
```
- **Answer**: THM{LUKS_not_LUX}

### Task 4

- There is a firewall running on the Linux VM. It is allowing port 22 TCP as we can ssh into the machine. It is allowing another TCP port; what is it?
- Command line: sudo ufw status
```
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
14298/udp                  ALLOW       Anywhere
12526/tcp                  ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
14298/udp (v6)             ALLOW       Anywhere (v6)
12526/tcp (v6)             ALLOW       Anywhere (v6)
```
- **Answer**: 12526

- What is the allowed UDP port?
- **Answer**: 14298

### Task 5

- What flag is hidden in the sshd_config file?
```
tryhackme@ip-10-10-241-41:~/myvault$ cat /etc/ssh/sshd_config

```
- **Answer**: THM{secure_SEA_shell}

### Task 6

- One way to disable an account is to edit the passwd file and change the account’s shell. What is the suggested value to use for the shell?
    - **Answer**: /sbin/nologin**


- What is the name of the RedHat and Fedora systems sudoers group?
  - **Answer**: wheel


- What is the name of the sudoers group on Debian and Ubuntu systems?
  - **Answer**:  sudo

- Other than tryhackme and ubuntu, what is the username that belongs to the sudoers group?
- Command line: cat /etc/group | grep sudo
```
sudo:x:27:ubuntu,tryhackme,blacksmith
```
- **Answer**: blacksmith

### Task 7

- Besides FTPS, what is another secure replacement for TFTP and FTP?
    - **Answer**: SFTP

### Task 8


- What command would you use to update an older Red Hat system?
    - **Answer**: yum update

- What command would you use to update a modern Fedora system?
    - **Answer**: dnf update

- What two commands are required to update a Debian system? (Connect the two commands with &&.)
    - **Answer**: apt update && upgrade

- What does yum stand for?
    - **Answer**: Yellowdog Updater, Modified 

- What does dnf stand for?
    - **Answer**: dandified yum

- What flag is hidden in the sources.list file?
- Search the file
```
tryhackme@ip-10-10-241-41:~/myvault$ find / -type f -name sources.list 2>/dev/null
/snap/core/14946/etc/apt/sources.list
/usr/share/doc/apt/examples/sources.list
/etc/apt/sources.list
/var/lib/ubuntu-advantage/apt-esm/etc/apt/sources.list
```
- open it
```
tryhackme@ip-10-10-241-41:~/myvault$ cat /etc/apt/sources.list
# THM{not_Advanced_Persistent_Threat}
```
- **Answer**: THM{not_Advanced_Persistent_Threat}

### Task 9

- What command can you use to display the last 15 lines of kern.log?
    - **Answer**: tail -n 15 kern.log

- What command can you use to display the lines containing the word denied in the file secure?
    - **Answer**: grep denied secure