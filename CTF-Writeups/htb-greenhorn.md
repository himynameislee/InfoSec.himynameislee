# HTB-greenhorn

As usual, started with nmap which showed a couple of open ports, but nothing too crazy. Port's 80 and 3000 were the ports of interest.

```bash
# Nmap 7.94SVN scan initiated Sat Jul 20 16:00:40 2024 as: nmap -sV -A -p- -Pn -T4 --open -oA nmap 10.129.64.237
Nmap scan report for greenhorn.htb (10.129.64.237)
Host is up (0.083s latency).
Not shown: 65524 closed tcp ports (conn-refused), 8 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 57:d6:92:8a:72:44:84:17:29:eb:5c:c9:63:6a:fe:fd (ECDSA)
|_  256 40:ea:17:b1:b6:c5:3f:42:56:67:4a:3c:ee:75:23:2f (ED25519)
80/tcp   open  http    nginx 1.18.0 (Ubuntu)
|_http-trane-info: Problem with XML parsing of /evox/about
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-title: Welcome to GreenHorn ! - GreenHorn
|_Requested resource was http://greenhorn.htb/?file=welcome-to-greenhorn
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-robots.txt: 2 disallowed entries 
|_/data/ /docs/
|_http-generator: pluck 4.7.18
3000/tcp open  ppp?
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=e5daf99543831c02; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=qJ3dD99v3rWFsObTAFhJ6JZSjJs6MTcyMTUwNTY5NTYxNTMzOTc1MA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sat, 20 Jul 2024 20:01:35 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>GreenHorn</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR3JlZW5Ib3JuIiwic2hvcnRfbmFtZSI6IkdyZWVuSG9ybiIsInN0YXJ0X3VybCI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLCJzaXplcyI6IjUxMng1MTIifSx7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvYX
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: GET
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=9e2019e44b08dbbc; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=A7z2PrHHcCfFerQrEFyxF8deoM46MTcyMTUwNTcwMTM5NDgxNDc2MA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sat, 20 Jul 2024 20:01:41 GMT
|_    Content-Length: 0
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3000-TCP:V=7.94SVN%I=7%D=7/20%Time=669C177C%P=x86_64-pc-linux-gnu%r
SF:(GenericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x
SF:20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Ba
SF:d\x20Request")%r(GetRequest,34B8,"HTTP/1\.0\x20200\x20OK\r\nCache-Contr
SF:ol:\x20max-age=0,\x20private,\x20must-revalidate,\x20no-transform\r\nCo
SF:ntent-Type:\x20text/html;\x20charset=utf-8\r\nSet-Cookie:\x20i_like_git
SF:ea=e5daf99543831c02;\x20Path=/;\x20HttpOnly;\x20SameSite=Lax\r\nSet-Coo
SF:kie:\x20_csrf=qJ3dD99v3rWFsObTAFhJ6JZSjJs6MTcyMTUwNTY5NTYxNTMzOTc1MA;\x
SF:20Path=/;\x20Max-Age=86400;\x20HttpOnly;\x20SameSite=Lax\r\nX-Frame-Opt
SF:ions:\x20SAMEORIGIN\r\nDate:\x20Sat,\x2020\x20Jul\x202024\x2020:01:35\x
SF:20GMT\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en-US\"\x20class=\"the
SF:me-auto\">\n<head>\n\t<meta\x20name=\"viewport\"\x20content=\"width=dev
SF:ice-width,\x20initial-scale=1\">\n\t<title>GreenHorn</title>\n\t<link\x
SF:20rel=\"manifest\"\x20href=\"data:application/json;base64,eyJuYW1lIjoiR
SF:3JlZW5Ib3JuIiwic2hvcnRfbmFtZSI6IkdyZWVuSG9ybiIsInN0YXJ0X3VybCI6Imh0dHA6
SF:Ly9ncmVlbmhvcm4uaHRiOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly9ncmVlbmh
SF:vcm4uaHRiOjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLC
SF:JzaXplcyI6IjUxMng1MTIifSx7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvY
SF:X")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20t
SF:ext/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x
SF:20Request")%r(HTTPOptions,1B0,"HTTP/1\.0\x20405\x20Method\x20Not\x20All
SF:owed\r\nAllow:\x20HEAD\r\nAllow:\x20GET\r\nAllow:\x20HEAD\r\nAllow:\x20
SF:GET\r\nCache-Control:\x20max-age=0,\x20private,\x20must-revalidate,\x20
SF:no-transform\r\nSet-Cookie:\x20i_like_gitea=9e2019e44b08dbbc;\x20Path=/
SF:;\x20HttpOnly;\x20SameSite=Lax\r\nSet-Cookie:\x20_csrf=A7z2PrHHcCfFerQr
SF:EFyxF8deoM46MTcyMTUwNTcwMTM5NDgxNDc2MA;\x20Path=/;\x20Max-Age=86400;\x2
SF:0HttpOnly;\x20SameSite=Lax\r\nX-Frame-Options:\x20SAMEORIGIN\r\nDate:\x
SF:20Sat,\x2020\x20Jul\x202024\x2020:01:41\x20GMT\r\nContent-Length:\x200\
SF:r\n\r\n")%r(RTSPRequest,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConte
SF:nt-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\
SF:n400\x20Bad\x20Request");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jul 20 16:02:29 2024 -- 1 IP address (1 host up) scanned in 108.93 seconds
```

Nothing too interesting over port 80 but I was able to find a login page for 'admin', as well as a version number for Pluck CMS.

<figure><img src=".gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

I decided to pivot to the website over port 3000, and it was a repo for Greenhorn.

<figure><img src=".gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

As I poked around, I eventually came across a 'pass.php' file

<figure><img src=".gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

Inside I discovered a hashed password.

<figure><img src=".gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

Not knowing what algorithm it used, I decided to throw it through crack station, and I was able to get a password.

<figure><img src=".gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

From there, I was able to login to the website using the login page I found earlier as 'admin'.

<figure><img src=".gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

Next I decided to look up any exploits for Pluck 4.7.18, and I found an exploit on [ExploitDB ](https://www.exploit-db.com/exploits/51592)for an RCE vulnerability.

<figure><img src=".gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

The exploit took some trial and error, but basically, I needed to upload a reverse shell, throw it in a zip file, and then run the PoC.

<figure><img src=".gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

And get a reverse shell

<figure><img src=".gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

Using the password from earlier, I was able to su to Junior and get the user.txt flag.

<figure><img src=".gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

The only interesting lead I has was the OpenVAS.pdf file, so I started a http server on the victim host and downloaded it.

<figure><img src=".gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

The pdf file contained a password that was pixeled out.

<figure><img src=".gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

I found a tool called [Depix](https://github.com/spipm/Depix), and after some more trial and error, I was able to de-pixelize it.

<figure><img src=".gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

After some more trial and error, it turns out that it was the password, and I was able to su to root and get the flag.

<figure><img src=".gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>
