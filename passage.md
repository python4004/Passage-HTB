# Hack The Box - Passage
## This is my writeup and walkthrough for Pssage from Hack The Box.

![card](https://user-images.githubusercontent.com/36403473/94084107-04f9e900-fe05-11ea-9a3c-dfe7c1a04bf4.png)
## Description
<p>
A vulnerable management system by remote code excute
Through it I was able to enter the server and I found a file with words that I decrypted with a base64 , then I found that it had a hash sha2-256 type, then I decrypted  it and found a password for User Paul that took his credintials  on the server and was able to access his file and found its flag
</p>

## `Enumeration`

#### 1-Nmap 

```
nmap -sV -sV  10.10.10.206
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-24 01:22 EET
Nmap scan report for Passage.htb (10.10.10.206)
Host is up (0.41s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 43.56 seconds
```
#### 2-User 

we have port 22 and 80 so i visited http://passage.htb:80 .\

**Note** `I added passage domain to machine ip to /etc/hosts first ` .\

I didn't find useful thing , i tried to brute force directory but server down several times so i decided to find another way 
talk look again i discovered that this site powered by `CuteNews`

![Screenshot from 2020-09-24 01-32-38](https://user-images.githubusercontent.com/36403473/94085004-3a073b00-fe07-11ea-9e7f-f5c8f25c1d28.png)
![Screenshot from 2020-09-20 23-43-27](https://user-images.githubusercontent.com/36403473/94085678-0f1de680-fe09-11ea-9c80-c02a0cb28a59.png)

After using google i discovered that `CuteNews` is management system So I searched if `CuteNews` vulnerable or not\
Finally i found that `CuteNews` vulnerable by Remote Code Execution.\

I downloaded from exploit-db website exploit and installed the exploit on metasploit.  [CuteNews 2.1.2 - 'avatar' Remote Code Execution](https://www.exploit-db.com/exploits/46698)
.
This exploit need account credential do after alter searching i found login screen i created an account

![Screenshot from 2020-09-24 00-35-28](https://user-images.githubusercontent.com/36403473/94086085-33c68e00-fe0a-11ea-854e-04587e727691.png)

After running exploit from **metasploit**
![Screenshot from 2020-09-24 02-08-59](https://user-images.githubusercontent.com/36403473/94086738-15fa2880-fe0c-11ea-80da-bccb43c74618.png)
After enumerat the server i found file call `lines` in `/var/www/html/CuteNews/cdata/users
` directory 
have important informations 
```
<?php die('Direct call - access denied'); ?>
YToxOntzOjU6ImVtYWlsIjthOjE6e3M6MTY6InBhdWxAcGFzc2FnZS5odGIiO3M6MTA6InBhdWwtY29sZXMiO319
<?php die('Direct call - access denied'); ?>
YToxOntzOjI6ImlkIjthOjE6e2k6MTU5ODgyOTgzMztzOjY6ImVncmU1NSI7fX0=
<?php die('Direct call - access denied'); ?>
YToxOntzOjU6ImVtYWlsIjthOjE6e3M6MTU6ImVncmU1NUB0ZXN0LmNvbSI7czo2OiJlZ3JlNTUiO319
<?php die('Direct call - access denied'); ?>
YToxOntzOjQ6Im5hbWUiO2E6MTp7czo1OiJhZG1pbiI7YTo4OntzOjI6ImlkIjtzOjEwOiIxNTkyNDgzMDQ3IjtzOjQ6Im5hbWUiO3M6NToiYWRtaW4iO3M6MzoiYWNsIjtzOjE6IjEiO3M6NToiZW1haWwiO3M6MTc6Im5hZGF2QHBhc3NhZ2UuaHRiIjtzOjQ6InBhc3MiO3M6NjQ6IjcxNDRhOGI1MzFjMjdhNjBiNTFkODFhZTE2YmUzYTgxY2VmNzIyZTExYjQzYTI2ZmRlMGNhOTdmOWUxNDg1ZTEiO3M6MzoibHRzIjtzOjEwOiIxNTkyNDg3OTg4IjtzOjM6ImJhbiI7czoxOiIwIjtzOjM6ImNudCI7czoxOiIyIjt9fX0=
<?php die('Direct call - access denied'); ?>
YToxOntzOjI6ImlkIjthOjE6e2k6MTU5MjQ4MzI4MTtzOjk6InNpZC1tZWllciI7fX0=
<?php die('Direct call - access denied'); ?>
YToxOntzOjU6ImVtYWlsIjthOjE6e3M6MTc6Im5hZGF2QHBhc3NhZ2UuaHRiIjtzOjU6ImFkbWluIjt9fQ==
<?php die('Direct call - access denied'); ?>
YToxOntzOjU6ImVtYWlsIjthOjE6e3M6MTU6ImtpbUBleGFtcGxlLmNvbSI7czo5OiJraW0tc3dpZnQiO319
<?php die('Direct call - access denied'); ?>
YToxOntzOjI6ImlkIjthOjE6e2k6MTU5MjQ4MzIzNjtzOjEwOiJwYXVsLWNvbGVzIjt9fQ==
<?php die('Direct call - access denied'); ?>
YToxOntzOjQ6Im5hbWUiO2E6MTp7czo5OiJzaWQtbWVpZXIiO2E6OTp7czoyOiJpZCI7czoxMDoiMTU5MjQ4MzI4MSI7czo0OiJuYW1lIjtzOjk6InNpZC1tZWllciI7czozOiJhY2wiO3M6MToiMyI7czo1OiJlbWFpbCI7czoxNToic2lkQGV4YW1wbGUuY29tIjtzOjQ6Im5pY2siO3M6OToiU2lkIE1laWVyIjtzOjQ6InBhc3MiO3M6NjQ6IjRiZGQwYTBiYjQ3ZmM5ZjY2Y2JmMWE4OTgyZmQyZDM0NGQyYWVjMjgzZDFhZmFlYmI0NjUzZWMzOTU0ZGZmODgiO3M6MzoibHRzIjtzOjEwOiIxNTkyNDg1NjQ1IjtzOjM6ImJhbiI7czoxOiIwIjtzOjM6ImNudCI7czoxOiIyIjt9fX0=
<?php die('Direct call - access denied'); ?>
YToxOntzOjI6ImlkIjthOjE6e2k6MTU5MjQ4MzA0NztzOjU6ImFkbWluIjt9fQ==
<?php die('Direct call - access denied'); ?>
YToxOntzOjU6ImVtYWlsIjthOjE6e3M6MTU6InNpZEBleGFtcGxlLmNvbSI7czo5OiJzaWQtbWVpZXIiO319
<?php die('Direct call - access denied'); ?>
YToxOntzOjQ6Im5hbWUiO2E6MTp7czoxMDoicGF1bC1jb2xlcyI7YTo5OntzOjI6ImlkIjtzOjEwOiIxNTkyNDgzMjM2IjtzOjQ6Im5hbWUiO3M6MTA6InBhdWwtY29sZXMiO3M6MzoiYWNsIjtzOjE6IjIiO3M6NToiZW1haWwiO3M6MTY6InBhdWxAcGFzc2FnZS5odGIiO3M6NDoibmljayI7czoxMDoiUGF1bCBDb2xlcyI7czo0OiJwYXNzIjtzOjY0OiJlMjZmM2U4NmQxZjgxMDgxMjA3MjNlYmU2OTBlNWQzZDYxNjI4ZjQxMzAwNzZlYzZjYjQzZjE2ZjQ5NzI3M2NkIjtzOjM6Imx0cyI7czoxMDoiMTU5MjQ4NTU1NiI7czozOiJiYW4iO3M6MToiMCI7czozOiJjbnQiO3M6MToiMiI7fX19
<?php die('Direct call - access denied'); ?>
YToxOntzOjQ6Im5hbWUiO2E6MTp7czo5OiJraW0tc3dpZnQiO2E6OTp7czoyOiJpZCI7czoxMDoiMTU5MjQ4MzMwOSI7czo0OiJuYW1lIjtzOjk6ImtpbS1zd2lmdCI7czozOiJhY2wiO3M6MToiMyI7czo1OiJlbWFpbCI7czoxNToia2ltQGV4YW1wbGUuY29tIjtzOjQ6Im5pY2siO3M6OToiS2ltIFN3aWZ0IjtzOjQ6InBhc3MiO3M6NjQ6ImY2NjlhNmY2OTFmOThhYjA1NjIzNTZjMGNkNWQ1ZTdkY2RjMjBhMDc5NDFjODZhZGNmY2U5YWYzMDg1ZmJlY2EiO3M6MzoibHRzIjtzOjEwOiIxNTkyNDg3MDk2IjtzOjM6ImJhbiI7czoxOiIwIjtzOjM6ImNudCI7czoxOiIzIjt9fX0=
<?php die('Direct call - access denied'); ?>
<?php die('Direct call - access denied'); ?>
<?php die('Direct call - access denied'); ?>
YToxOntzOjQ6Im5hbWUiO2E6MTp7czo2OiJlZ3JlNTUiO2E6MTE6e3M6MjoiaWQiO3M6MTA6IjE1OTg4Mjk4MzMiO3M6NDoibmFtZSI7czo2OiJlZ3JlNTUiO3M6MzoiYWNsIjtzOjE6IjQiO3M6NToiZW1haWwiO3M6MTU6ImVncmU1NUB0ZXN0LmNvbSI7czo0OiJuaWNrIjtzOjY6ImVncmU1NSI7czo0OiJwYXNzIjtzOjY0OiI0ZGIxZjBiZmQ2M2JlMDU4ZDRhYjA0ZjE4ZjY1MzMxYWMxMWJiNDk0YjU3OTJjNDgwZmFmN2ZiMGM0MGZhOWNjIjtzOjQ6Im1vcmUiO3M6NjA6IllUb3lPbnR6T2pRNkluTnBkR1VpTzNNNk1Eb2lJanR6T2pVNkltRmliM1YwSWp0ek9qQTZJaUk3ZlE9PSI7czozOiJsdHMiO3M6MTA6IjE1OTg4MzQwNzkiO3M6MzoiYmFuIjtzOjE6IjAiO3M6NjoiYXZhdGFyIjtzOjI2OiJhdmF0YXJfZWdyZTU1X3Nwd3ZndWp3LnBocCI7czo2OiJlLWhpZGUiO3M6MDoiIjt9fX0=
<?php die('Direct call - access denied'); ?>
YToxOntzOjI6ImlkIjthOjE6e2k6MTU5MjQ4MzMwOTtzOjk6ImtpbS1zd2lmdCI7fX0=
```
I decoded this lines to base64 and i found hash this .
![Screenshot from 2020-09-24 00-05-48](https://user-images.githubusercontent.com/36403473/94087687-b5b8b600-fe0e-11ea-831e-e0f2ba2581c3.png)
 I got to know the type of encryption used `SHA2-256` and then I decrypt it online and it was the password

![Screenshot from 2020-09-24 00-07-08](https://user-images.githubusercontent.com/36403473/94087840-0f20e500-fe0f-11ea-9360-7c0c57b46a56.png)
now i have paul credential 
![Screenshot from 2020-09-24 00-07-55](https://user-images.githubusercontent.com/36403473/94087985-6d4dc800-fe0f-11ea-8a9f-4883c3e83a60.png)
In paul file there is a file for ssh ,And I had ssh private and public key ,so i copied ssh private key and used port 22 to login by **paul** private key 


![Screenshot from 2020-09-24 00-08-33](https://user-images.githubusercontent.com/36403473/94088312-7b501880-fe10-11ea-830b-7186ff692726.png)
I have successfully logged in and gained user flag 
![Screenshot from 2020-09-24 02-46-44](https://user-images.githubusercontent.com/36403473/94088605-311b6700-fe11-11ea-8b8b-b011f1b834c1.png)

#### 3-Root 

At this stage it took a lot of time, but I remembered that I have another user and therefore it may be useful, but I cannot see the content of the file
I decided to turn off the laptop And watch a movie.\
I started over
The first, after some thought, I decided to see the processes that happened on the machines using ` ps -auwx` command.
![Screenshot from 2020-09-22 13-54-19](https://user-images.githubusercontent.com/36403473/94089842-69707480-fe14-11ea-9299-bb5fba86a1bd.png)

I found **Paul** used the same ssh private to log into the **nadav** account\
![Screenshot from 2020-09-24 03-14-57](https://user-images.githubusercontent.com/36403473/94090115-1814b500-fe15-11ea-975f-c04851993659.png)

I tried to see the processes created by **nadav** `ps -auwx` And I noticed **dbus-daemon - Message bus daemon**\
D-Bus as its inter-process communications (IPC) mediator
![Screenshot from 2020-09-24 03-26-28](https://user-images.githubusercontent.com/36403473/94090759-b6554a80-fe16-11ea-8c40-8f969c8ddae2.png)

In these moments a time and a stage began to learn more about the **dbas** system.
After the search, I found an exploitation of a security vulnerability , and therefore I decided to review the conference file to find out if he was injured from the site or system side\

you can read more about **dbus-daemon**  from herer [dbus-daemon ](https://linux.die.net/man/1/dbus-daemon).
![Screenshot from 2020-09-24 03-42-52](https://user-images.githubusercontent.com/36403473/94091664-14832d00-fe19-11ea-9cd8-cf063fad6bd3.png)
After researching, I found that there was indeed a security issue in USBCreator D-Bus Privilege Escalation in Ubuntu Desktop.
[USBCreator D-Bus Privilege Escalation ](https://unit42.paloaltonetworks.com/usbcreator-d-bus-privilege-escalation-in-ubuntu-desktop/)

The explanation of the exploitation is that I can copy the content of a file or modify it to a file without obtaining permission from the owner of that file\
 The idea is that I created a test.txt file in tmp directory  and copied the contents of the `root/root.txt` to `tmp/test.txt` by **gdbus** commnad that i found in the USBCreator D-Bus exploit.

![Screenshot from 2020-09-23 23-11-32](https://user-images.githubusercontent.com/36403473/94148032-64441180-fe76-11ea-9f5d-0deba52540a8.png)




