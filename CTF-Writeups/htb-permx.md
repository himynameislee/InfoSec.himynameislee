# HTB-permx

As usual, started with nmap but only ports 22 and 80 were open.

```bash
└─$ nmap -sV -A -p- -Pn -T4 10.129.68.134 --open -oA nmap
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-22 19:19 EDT
Nmap scan report for 10.129.68.134
Host is up (0.074s latency).
Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 e2:5c:5d:8c:47:3e:d8:72:f7:b4:80:03:49:86:6d:ef (ECDSA)
|_  256 1f:41:02:8e:6b:17:18:9c:a0:ac:54:23:e9:71:30:17 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://permx.htb
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.55 seconds

```

Enumeration of the main website didn't reveal anything.

I decided to use FFUF for some subdomain busting, and was successful

<figure><img src=".gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

I navigated to the lms subdomain, and was met with a login page for chamilo

<figure><img src=".gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

I poked around after using dirbuster, but nothing really of interest.

From there I looked into vulnerabilities on Chamilo to which I found an unauthenticated RCE vulnerability.

Following the instructions at this [link](https://starlabs.sg/advisories/23/23-3533/), I was able to upload a reverse shell

<figure><img src=".gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

Being the user www-data I couldn't do much. I uploaded linpeas and found a possible password.

<figure><img src=".gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

The only other user on the device was 'mtz', I tried my luck and was successful. Once I switched to mtz, I got the user flag.

<figure><img src=".gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

After getting the flag, I ran sudo -l, and saw I had sudo access to a script in /opt/acl.sh

Through some trial and error, I was able to escalate to root with the following and get the flag.

<figure><img src=".gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>
