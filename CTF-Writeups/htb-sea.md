# HTB-sea

## Write Up

Started with Nmap as per usual and nothing really of interest, only ports 22 & 80 were open

```bash
└─$ nmap -sV -A -p- -Pn -T4 -sC 10.129.102.1 --open  
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-10 15:25 EDT
Nmap scan report for 10.129.102.1
Host is up (0.077s latency).
Not shown: 65531 closed tcp ports (conn-refused), 2 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e3:54:e0:72:20:3c:01:42:93:d1:66:9d:90:0c:ab:e8 (RSA)
|   256 f3:24:4b:08:aa:51:9d:56:15:3d:67:56:74:7c:20:38 (ECDSA)
|_  256 30:b1:05:c6:41:50:ff:22:a3:7f:41:06:0e:67:fd:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Sea - Home
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .


```

The website proved to be more interesting. On it was a contact form.

<figure><img src=".gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

While it took some trial and error, I found an SSRF vulnerability. I set up my own HTTP server to see if the contact form would interact with it, and it did.

<figure><img src=".gitbook/assets/image (211).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (212).png" alt=""><figcaption></figcaption></figure>

With that in mind, I found [**CVE-2023-41425**](https://github.com/prodigiousMind/CVE-2023-41425)**.** This allowed me to achieve RCE through an XSS vulnerability.

<figure><img src=".gitbook/assets/image (213).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>

Which led me to get a shell as www-data

<figure><img src=".gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>

I couldn't do much as www-data, but I did find a hash passed word located in `/var/www/sea/data/database.js`

<figure><img src=".gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

The hash ended up being a bcrypt hash that needed to be slightly modified.

Once I changed the hash, I was able to crack it with hashcat

<figure><img src=".gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

From there I was able to log in as the user amay and get the user flag.

<figure><img src=".gitbook/assets/image (218).png" alt=""><figcaption></figcaption></figure>

Next, I ran Linpeas, and found an interesting cronjob that was hosting something over Google Chrome on port 8080.

<figure><img src=".gitbook/assets/image (219).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

Through port forwarding, I was able to access the host over 8080

<figure><img src=".gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

Which led to a System Monitoring tool.

<figure><img src=".gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

I did some experimentation, and I was able to inject commands via analyzing access.log.

Through command injection, I was actually trying to get a root shell but instead, I was able to get the root flag itself.

<figure><img src=".gitbook/assets/image (223).png" alt=""><figcaption></figcaption></figure>
