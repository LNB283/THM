# Introduction to cryptography
### Link
- https://tryhackme.com/room/cryptographyintro
---
### Task 1
- Open this website: https://www.quipqiup.com/
- Copy/paste the encrypted message
    - **Answer**: Miyamoto Musashi

### Task 2
- First file: 
- Command line : gpg --decrypt -o quote01.txt quote01.txt.gpg
- In the window, you just need to give the key **s!kR3T55**
    - **Answer**: waste
- Second file:
- Command line:  openssl aes-256-cbc -d -in quote02 -out quote02_result.txt
- You will receive the following message **enter aes-256-cbc decryption password:**
- Indicate the key **s!kR3T55**
    - **Answer** : science
- Third file:
- Command line: gpg --decrypt --batch --cipher-algo CAMELLIA256 --passphrase 's!kR3T55' -o quote3_result.txt quote03.txt.gpg
    - **Answer**: understand

### Task 3
- Bob has received the file ciphertext_message sent to him from Alice. You can find the key you need in the same folder. What is the first word of the original plaintext?
- Command line: openssl pkeyutl -decrypt -in ciphertext_message -inkey private-key-bob.pem -out ciphertext_message_result
    - **Answer** : Perception

- Take a look at Bob’s private RSA key. What is the last byte of p? 
- Command line: openssl rsa -in private-key-bob.pem -text -noout
    - **Answer** : e7


- Take a look at Bob’s private RSA key. What is the last byte of q?
- Command line: openssl rsa -in private-key-bob.pem -text -noout
    - **Answer** : 27

### Task 4

- A set of Diffie-Hellman parameters can be found in the file dhparam.pem. What is the size of the prime number in bits?
- Command line: openssl dhparam -in dhparams.pem -text -noout
    - **Answer** : 4096


- What is the prime number’s last byte (least significant byte)?
- Command line: openssl dhparam -in dhparams.pem -text -noout
    - **Answer** : 4f

### Task 5

- What is the SHA256 checksum of the file order.json?
- Command line : sha256sum order.json **Command line used on Mac**
    - **Answer** : 2c34b68669427d15f76a1c06ab941e3e6038dacdfb9209455c87519a3ef2c660

- Open the file order.json and change the amount from 1000 to 9000. What is the new SHA256 checksum?
    - **Answer** : 11faeec5edc2a2bad82ab116bbe4df0f4bc6edd96adac7150bb4e6364a238466

- Using SHA256 and the key 3RfDFz82, what is the HMAC of order.txt?
- Command line : hmac256 3RfDFz82 order.txt
    - **Answer** : c7e4de386a09ef970300243a70a444ee2a4ca62413aeaeb7097d43d2c5fac89f

### Task 6

- What is the size of the public key in bits?
- Command line : openssl x509 -in cert.pem -text
    - **Answer** : 4096

- Till which year is this certificate valid?
- Command line : openssl x509 -in cert.pem -text
    - **Answer** : 2039     

### Task 7

- You were auditing a system when you discovered that the MD5 hash of the admin password is 3fc0a7acf087f549ac2b266baf94b8b1. What is the original password?
- Command line : Visit this website **https://md5decrypt.net/en/**
    - **Answer** : qwerty123
