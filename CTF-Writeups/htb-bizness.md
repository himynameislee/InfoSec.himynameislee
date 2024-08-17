# HTB-bizness

### Write-up

Started with nmap and noted open ports, nothing really popped out.

```bash
# Nmap 7.94SVN scan initiated Sun Jan  7 13:35:32 2024 as: nmap -Pn -A -sC -sT -p- -T4 -oN nmap.txt 10.129.176.156
Warning: 10.129.176.156 giving up on port because retransmission cap hit (6).
Nmap scan report for bizness.htb (10.129.176.156)
Host is up (0.11s latency).
Not shown: 65527 closed tcp ports (conn-refused)
PORT      STATE    SERVICE    VERSION
22/tcp    open     ssh        OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 3e:21:d5:dc:2e:61:eb:8f:a6:3b:24:2a:b7:1c:05:d3 (RSA)
|   256 39:11:42:3f:0c:25:00:08:d7:2f:1b:51:e0:43:9d:85 (ECDSA)
|_  256 b0:6f:a0:0a:9e:df:b1:7a:49:78:86:b2:35:40:ec:95 (ED25519)
80/tcp    open     http       nginx 1.18.0
|_http-server-header: nginx/1.18.0
|_http-title: Did not follow redirect to https://bizness.htb/
443/tcp   open     ssl/http   nginx 1.18.0
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=UK
| Not valid before: 2023-12-14T20:03:40
|_Not valid after:  2328-11-10T20:03:40
|_http-server-header: nginx/1.18.0
|_http-title: 400 The plain HTTP request was sent to HTTPS port
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
| tls-nextprotoneg: 
|_  http/1.1
4879/tcp  filtered wsdl-event
15044/tcp filtered unknown
35317/tcp open     tcpwrapped
38485/tcp filtered unknown
58618/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jan  7 13:49:55 2024 -- 1 IP address (1 host up) scanned in 863.01 seconds
```

Did a recon of the website, and saw it was running 'Apache OFBiz" as stated on the logon page

<figure><img src=".gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

Decided to look for a vulnerability, and found an [RCE ](https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass)that was brand new.

<figure><img src=".gitbook/assets/image (177).png" alt="" width="563"><figcaption></figcaption></figure>

Decided to give it a shot, and after some trial and error, I got a shell.

<figure><img src=".gitbook/assets/image (178).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (179).png" alt="" width="557"><figcaption></figcaption></figure>

Not long after, I found the user.txt flag

<figure><img src=".gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

Moving on, I ran Linpeas, but didn't find anything that stuck out. A manual search revealed a file called 'AdminUserLogonData.xml'.

<figure><img src=".gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

This gave me a salted password.

<figure><img src=".gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>

To decrypt it, I found the following script online.

```python
import hashlib
import base64
import os
def cryptBytes(hash_type, salt, value):
    if not hash_type:
        hash_type = "SHA"
    if not salt:
        salt = base64.urlsafe_b64encode(os.urandom(16)).decode('utf-8')
    hash_obj = hashlib.new(hash_type)
    hash_obj.update(salt.encode('utf-8'))
    hash_obj.update(value)
    hashed_bytes = hash_obj.digest()
    result = f"${hash_type}${salt}${base64.urlsafe_b64encode(hashed_bytes).decode('utf-8').replace('+', '.')}"
    return result
def getCryptedBytes(hash_type, salt, value):
    try:
        hash_obj = hashlib.new(hash_type)
        hash_obj.update(salt.encode('utf-8'))
        hash_obj.update(value)
        hashed_bytes = hash_obj.digest()
        return base64.urlsafe_b64encode(hashed_bytes).decode('utf-8').replace('+', '.')
    except hashlib.NoSuchAlgorithmException as e:
        raise Exception(f"Error while computing hash of type {hash_type}: {e}")
hash_type = "SHA1"
salt = "d"
search = "$SHA1$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I="
wordlist = '/usr/share/wordlists/rockyou.txt'
with open(wordlist,'r',encoding='latin-1') as password_list:
    for password in password_list:
        value = password.strip()
        hashed_password = cryptBytes(hash_type, salt, value.encode('utf-8'))
        # print(hashed_password)
        if hashed_password == search:
            print(f'Found Password:{value}, hash:{hashed_password}')
```

And success, the password is

```
monkeybizness
```

<figure><img src=".gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>

Now in my crappy shell, I switched my user to 'root' and got the root flag.

<figure><img src=".gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>
