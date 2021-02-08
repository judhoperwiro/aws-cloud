## Setup FTP Server on AWS Machine

##### Component 

- aws-ec2 machine with operating system ubuntu
- open-ssh

### 1.) Prepare User 

- Run as root. 
##### 1.1) Create User for ftpserver
Exec command: 
```
groupadd ftpserver 3100
useradd ftpserver -u 3001 -s /bin/bash -m -d /data/ftpserver -g ftpserver --disabled-password
```

### 2.) Prepare Remote Access 

- Run as ftpserver. 
##### 2.1) Create directory .ssh
Exec command: 
```
cd /data/ftpserver/
mkdir .ssh
chmod 700 .ssh
touch authorized_keys
chmod 600 authorized_keys
```

##### 2.2) Generate ssh key
Exec command: 
```
ssh-keygen -t rsa
```

##### 2.3) Copy text file from id_rsa.pub to authorized_keys
