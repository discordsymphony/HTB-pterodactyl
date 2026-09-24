# HTB-pterodactyl

We will begin this machine by using Nmap to scan the server for all open TCP ports, service versions and perform script scanning:

```bash
nmap -p0-65535 -sCV 10.129.91.107
```

### Results:

```
PORT     STATE  SERVICE    VERSION
22/tcp   open   ssh        OpenSSH 9.6 (protocol 2.0)
| ssh-hostkey: 
|   256 a3:74:1e:a3:ad:02:14:01:00:e6:ab:b4:18:84:16:e0 (ECDSA)
|_  256 65:c8:33:17:7a:d6:52:3d:63:c3:e4:a9:60:64:2d:cc (ED25519)
80/tcp   open   http       nginx 1.21.5
|_http-server-header: nginx/1.21.5
|_http-title: My Minecraft Server
```

## User -> wwwrun

Visiting the web server on port 80, reveals a MonitorLand landing page, which appears to be related to the game Minecraft: 

<img src="Images/01-Landing-Page.png" width="600">

The landing page also reveals the play.pterodactyl.htb subdomain and a changelogs file. Before moving on, let's search for any more subdomains.

#### Running FFUF:

```
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://pterodactyl.htb/ -H 'Host: FUZZ.pterodactyl.htb' -fc 302
```

#### Output:

```
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://pterodactyl.htb/
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.pterodactyl.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response status: 302
________________________________________________

panel                   [Status: 200, Size: 1897, Words: 490, Lines: 36, Duration: 269ms]
```

As we can see, FFUF returns to us the subdomain "panel". Let's add these to our **/etc/hosts** file:

#### Editing hosts file:

```
cat /etc/hosts
```

#### Output:

```
127.0.0.1	localhost
127.0.1.1	pwnbox7.1

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
127.0.0.1 localhost
127.0.1.1 htb-06gjp1juuo htb-06gjp1juuo.htb-cloud.com
10.129.92.38    pterodactyl.htb play.pterodactyl.htb panel.pterodactyl.htb
```

---
