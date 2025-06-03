# UnderPass

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Machine IP: 10.10.11.48

### Nmap Enum

To start, I ran an Nmap scan to find open ports and services on the target HTB machine:

```
nmap -sV -sC 10.10.11.48
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

The Nmap scan finished, revealing two open ports: SSH (port 22) for potential remote access and HTTP (port 80) hosting a web server.

I investigated port 80 and found it running an Apache web server, displaying only the default Ubuntu welcome page.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Next, I conducted a UDP scan and identified an open SNMP service running on port 161, indicating potential for further enumeration.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### SNMP Walk&#x20;

Following the Nmap scan, I enumerated the SNMP service on port 161.  The results yielded little of immediate value, but a curious note caught my attention: “UnDerPass.htb is the only daloradius server in the basin!” This suggested a potential hostname and service to investigate further.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Fuzzing

I fuzzed the web directory on port 80 by appending /daloradius to the URL and testing .php extensions. This led me to discover a login page at /daloradius/app/users/login.php, a promising entry point for further exploration.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Research on the daloradius GitHub revealed that the application features two interfaces: one for operators (privileged users) and another for regular users. Based on this, I navigated to /daloradius/app/operators/login.php to access the operator login page, a potential target for further enumeration.

Searching for defaults creds for daloRADIUS and found this:

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

Once logged in, I navigated to the user management section to list all users. Among them, I found one user account with a password hash displayed, offering a potential lead for further exploitation.

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

I copied the password hash from the daloradius user account and submitted it to CrackStation, an online hash-cracking tool, to attempt to recover the plaintext password.

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

### SSH

After cracking the password hash, I obtained the plaintext password for the user svcMosh. Using these credentials, I attempted to log in via SSH on port 22 to gain access to the system.

After gaining SSH access, I ran ls to list the directory contents and found a user.txt file. I used cat user.txt to display its contents, revealing the user flag.

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

### Mosh-Server

After logging in as svcMosh, I ran sudo -l to check permissions. It showed svcMosh can run mosh-server with sudo without a password, a possible way to gain higher access.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

I wasn’t familiar with Mosh, so I checked the FAQ section on the website. I found the question, **“How do I run the mosh client and server separately?”** Following the instructions, I ran `mosh-server` with **sudo** and connected using `mosh-client`.

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>
