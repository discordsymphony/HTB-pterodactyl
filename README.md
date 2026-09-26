# HTB-pterodactyl

<img src="Images/00-Banner.png" width="600">

**Difficulty:** Medium

**OS:** Linux

**IP:** 10.129.92.38

**Date:** 07/02/2026

We began this machine with an Nmap scan which led to the discovery of a webserver on port 80, revealing a MonitorLand landing page that contained a link to the "play" subdomain and a link to changelogs.txt. Further subdomain enumeration revealed the "panel" subdomain, and analysis of changelogs.txt disclosed Pterodactyl Panel v1.11.10 was being used. After researching vulnerabilities, it emerged that Pterodactyl Panel was vulnerable to CVE-2025-49132, which we found a PoC for. While the exploit was relatively simple, it required modification to run successfully. Running the exploit gave Remote Code Execution on the server as wwwrun and we managed to obtain a shell using cURL.

Enumeration into the underlying Pterodactyl Panel files on the server returned an environment file containing MySQL credentials, where we were able to extract the encrypted password hash of the phileasfogg3 user. We then used John the Ripper to successfully recover the plaintext password.

Enumeration as phileasfogg3 revealed that we could read his mail, where it was discovered that the udisks daemon (udisksd) was potentially vulnerable. Searching for CVEs related to this version of udisksd resulted in finding CVE-2025-6018 and CVE-2025-6019. We then downloaded a PoC which required us to run a series of commands before achieving code execution as the root user. Leveraging this, we were able to catch a session as the root user and successfully complete the machine. 

**Key Findings**


| Finding         | Severity     | Impact                                                         |
| :-------------- | :----------- | :------------------------------------------------------------- |
| CVE-2025-49132 | Critical: 10 | Unauthenticated Remote Code Execution on a remote server running Pterodacty Panel 1.11.10. |
| CVE-2025-6018 | Critical: 7.8 | Authenticated privilege escalation on local machine. |
| CVE-2025-6019 | Critical: 7.0 | Authenticated Udisks Remote Code Execution leading to privilege escalation on local machine. |


