# HTB-Codify

As usual, we start out with Nmap and do some initial recon of the website.

Nmap only provided that ports 22 and 80 were open, but the website was interesting

```bash
# Nmap 7.94 scan initiated Sat Nov  4 15:53:45 2023 as: nmap -p- -Pn -A -sC -T4 -oG nmap.txt 10.129.109.166
Host: 10.129.109.166 (codify.htb)	Status: Up
Host: 10.129.109.166 (codify.htb)	Ports: 22/open/tcp//ssh//OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)/, 80/open/tcp//http//Apache httpd 2.4.52/, 3000/open/tcp//http//Node.js Express framework/	Ignored State: closed (65532)
# Nmap done at Sat Nov  4 15:54:21 2023 -- 1 IP address (1 host up) scanned in 36.32 seconds
```

<figure><img src=".gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

After reading the front page, I click on try now and get presented with an editor

<figure><img src=".gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

Not knowing a thing about scripting with nodejs, I came across the limitations page, which restricts specific modules

<figure><img src=".gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

Dirbuster did not show me anything useful either. I take a second look at the 'about us' page, and I notice that vm2 has a hyperlink. I also take note of the words 'widely used'.

<figure><img src=".gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

The link goes to GitHub, which turns out to be a widely used platform, so I started looking for vulnerabilities.

I come across CVE-2023-32314. After reading about it, I found the POC at [this link.](https://gist.github.com/arkark/e9f5cf5782dec8321095be3e52acf5ac) In short, this is a sandbox escape vulnerability that allows RCE via the Function in the host context

<figure><img src=".gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

After playing around, I see that the script works and I test out 'ls -la' and 'whoami'. I eventually came across a potential username, 'joshua.

<figure><img src=".gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

For the hell of it, I do an ssh brute force against 'joshua', just to see what happens.

I know now it's time to get a reverse shell. I checked to see if this host had python and bash. It had both, but I decided to try bash.

After some experimenting, I do get a reverse shell with an encoded bash script.

<figure><img src=".gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

Looking around as the user 'svc' there isn't much to do. I eventually checked back at my brute force, and I saw Joshaua's password was popped: spongebob1

<figure><img src=".gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

I was then able to SSH in and get the user flag

<figure><img src=".gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

After getting the flag, I start up my HTTP server and get Linpeas on the device. Linpeas doesn't really show me anything interesting. I also do a sudo -l check on Joshua, and I do have sudo privileges for a MySQL file located in /opt/scripts

<figure><img src=".gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

This next set was a whole lot of trial and error. But I eventually found I could execute the file with sudo privileges and get a different output.

<figure><img src=".gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

Also while looking at the script, we can see some unquoted paths, which is a sign of SQL Wild Card Injection.

I did some research, it turns out we can guess or brute force the first password character followed by \* to bypass the password prompt. and we can also brute force every character of the password till we find all characters of the password.

After a short amount of time, we get our root password, and then our flag

<figure><img src=".gitbook/assets/image (87).png" alt="" width="561"><figcaption></figcaption></figure>

There's also an alternate route to take with a tool called '[pspy](https://github.com/DominicBreuker/pspy)'.

Once I downloaded the tool, I moved it onto the victim host and ran it. Once it started, I opened another SSH session with Joshua and re-ran the mySQL file with sudo. I was able to produce the credentials in clear text.

<figure><img src=".gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>
