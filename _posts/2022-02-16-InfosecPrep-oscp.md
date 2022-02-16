---

layout: post
title: InfosecPrep: oscp
author: Mr. Pop
gh-badge:
  - star
  - fork
  - follow
tags:
 - Nmap 
 - ssh
 - SUID
 - privesc
comments: false
published: true
date: '2022-02-16'
---

### InfosecPrep:oscp
This machine was created by @falconspy and was uploaded on VulnHub . It is like any other Boot2Root type machine and is very easy. By creating this requirement it is ensured that at least only those will be selected who are ready to get into the PwK course and not someone who still hasn’t grasped the basics yet.
Or you can play on offsec-pg: https://portal.offensive-security.com/

Ok let’s start with scanning..

nmap scan

The reason why I use -oX infosecprep.xml is couse I you can read here!

The reason i use -oX infosecprep.xml is that i later convert the xml file to html and get a reviewed html results page. You can read here !

```
nmap -sC -sV 192.168.246.89 -oX infosecprep.xml  
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-13 14:47 CEST
Nmap scan report for 192.168.246.89
Host is up (0.30s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: WordPress 5.4.2
| http-robots.txt: 1 disallowed entry 
|_/secret.txt
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: OSCP Voucher &#8211; Just another WordPress site
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.53 seconds
```

Looks like we have /robots.txt.

OK when we go to the address of our target we see that it is a wordpress page

![infosec1.png](img/infosec1.png)


If we read the whole text we will find a username which in this case is: oscp

![infosec12png](img/infosec2.png)

Check /robot.txt

![infosec3.png](img/infosec3.png)

Woow we got /secret.txt Let’s open secret.txt… We can also download with wget ![http://$IP/secret.txt](http://$IP/secret.txt)

![infosec4.png](img/infosec4.png)

We have a simple _base64_ encoded text. Lets decoded…  
And the secret is out. The encoded string is nothing but a **_SSH private key_**. The key must belong to some user, so let us hunt for one.

**_Note_**:  
When decoding a file, its’ contents must be copied and pasted exactly as it is. No additional character or new line must be present in the pasted file. This is to ensure the decoding is done exactly as intended  
Let’s take a look at the website, as there might be some information there, intentional or otherwise.  
Upon browsing the index, the post gave us the user that is on the machine — oscp.

![infosec5.png](img/infosec5.png)

OK, let’s connect on server. In my case decrypted file name is hashdec.txt. Do not forget to give him permission: chmod 600 hashdec.txt

`ssh -i hashdec.txt oscp@$IP`

And we got a shell…

![infosec6.png](img/infosec6.png)

… and the first flag..

![infosec7.png](img/infosec7.png)

Great, it looks like we have the privilege escalation binary on hand.  
If you wanted to find this binary manually you could run the following command to get all the SUID binaries, and then compare those binaries against your system to find any non-standard binary or binaries that shouldn’t ideally have SUID enabled.

`find / -perm -u=s -type f 2>/dev/null`

![infosec8.png](img/infosec8.png)

The first command is to make bash a SUID binary, but we already have one so we’ll ignore that.  
All we have to do is execute our SUID binary as — /usr/bin/bash -p to get root.

![infosec9.png](img/infosec9.png)

Root flag is right there..

![infosec10.png](img/infosec10.png)

If you did not get something, or found some mistake, feel free to contact me.  
Take care, and keep hacking!

[BUY ME A BEER](https://www.buymeacoffee.com/ipopovic)