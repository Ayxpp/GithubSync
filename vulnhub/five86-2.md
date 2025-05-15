# Five86:2

To begin the enumeration process, I ran a comprehensive Nmap scan to identify the open ports and services on the target machine:

<figure><img src="../.gitbook/assets/image (12) (1) (1).png" alt="" width="527"><figcaption></figcaption></figure>

**Open Ports:**

* **Port 21:** FTP service running ProFTPD 1.3.5e
* **Port 80:** HTTP service running Apache HTTPD 2.4.41 (Ubuntu) with WordPress 5.1.4

To make interacting with the target machine more convenient, I added a reference to the target’s IP address in the `/etc/hosts` file. This allows me to use the hostname `five86-2` instead of typing the IP address every time.

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

After identifying that the target was running a WordPress site, I performed a directory brute-force scan using `dirb` to uncover hidden directories and files. This would help in finding potential entry points for exploitation:

```bash
dirb http://192.168.22.18/
```

The scan revealed several interesting directories and files related to the WordPress installation:

* `/wp-login.php`
* `/wp-admin/`
* `/wp-content/`
* `/wp-includes/`

<figure><img src="../.gitbook/assets/image (14) (1).png" alt="" width="260"><figcaption></figcaption></figure>

After identifying the WordPress login page (`/wp-login.php`), I decided to attempt logging in using common default credentials. I tried several combinations, including:

* **Username:** `admin` / **Password:** `admin`
* **Username:** `admin` / **Password:** `password`
* **Username:** `administrator` / **Password:** `admin123`

Unfortunately, none of these credentials worked, indicating that default login combinations were either not in use or the login was protected by more secure credentials.

At this point, I considered other methods of gaining access, such as enumerating users or looking for potential vulnerabilities in the WordPress installation.

**Enumerating WordPress Vulnerabilities with `wpscan`**

The first scan was aimed at enumerating available details about the WordPress setup:

```bash
wpscan --url http://192.168.22.18/
```

This scan helped to identify the WordPress version, potential themes, and plugins in use. However, it did not provide immediate access to the system.

Next, I attempted to brute-force the login credentials by using the `rockyou.txt` wordlist:

```bash
wpscan --url http://192.168.22.18/ --passwords /usr/share/wordlists/rockyou.txt
```

During the `wpscan` process, I successfully enumerated several usernames by analyzing the login error messages. The following users were identified:

\


<figure><img src="../.gitbook/assets/image (15) (1).png" alt="" width="487"><figcaption></figcaption></figure>

The attack successfully revealed the passwords for two accounts:

\
<img src="../.gitbook/assets/image (16).png" alt="" data-size="original">

With these credentials, I now had access to the WordPress dashboard for both users.

I successfully logged in using the credentials for the user “barney.” This authentication was successful and allowed me access to the system.

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

I navigated to the plugins section to review the installed plugins. I discovered the “Insert or Embed Articulate Content into WordPress Trial” plugin, version 4.2995. Next, I conducted a search to determine if there were any known exploits associated with this plugin.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

After conducting further research, I discovered a vulnerability associated with the “Insert or Embed Articulate Content into WordPress Trial” plugin. The specific exploit I found can be accessed at [Exploit-DB: Exploit 46981](https://www.exploit-db.com/exploits/46981). This vulnerability may allow for potential exploitation, and I plan to investigate its applicability to the current environment.

[Exploit-DB: Exploit 46981](https://www.exploit-db.com/exploits/46981) provides a method to exploit this plugin which I followed.

## Method

To exploit the identified vulnerability, I created a .zip archive containing two files: `index.html` and `shell.php`.

1. **Creating `index.html`:** I generated the `index.html` file with the following command:

```html
echo "<html>hello</html>" > index.html
```

**2. Creating `shell.php`:** I then created the `shell.php` file, utilizing the [Pentest Monkey PHP](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) reverse shell script to establish a netcat listener.

After preparing the `index.html` and `shell.php` files, I zipped them into a single archive using the following command:

```bash
zip payload.zip index.html shell.php
```

Using the “barney” account, I uploaded the `payload.zip` file to WordPress. This upload was performed through the "Add New Post" menu, where I utilized the e-learning block feature. Notably, this feature allowed for the upload of zipped files, which facilitated the successful addition of my payload to the site.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

After the upload, I ensured to insert the content as an `<iframe>` in the post, which would allow the execution of the shell script. And click “Insert”.

After successfully uploading the `payload.zip` and inserting it as an `<iframe>`, I obtained the URL path to the uploaded file. I then navigated to this URL in my browser to initiate the execution of the payload.

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

## Executing the Payload

Upon accessing the URL path, the index.html file I created earlier was displayed in the browser. With the webpage successfully loading, it was time to execute the shell.php script.

Before proceeding, I set up a netcat listener on my local machine using the following command:

```bash
nc -nvlp 4444
```

I refreshed the `shell.php` page to initiate the execution of the script. Moments later, I received a connection in my netcat listener, confirming that the reverse shell was successfully established.

<figure><img src="../.gitbook/assets/image (24).png" alt="" width="516"><figcaption></figcaption></figure>

Upon establishing the reverse shell connection, I executed the following command to upgrade the shell to a fully interactive one:

```bash
python3 -c "'import pty; pty.spawn("/bin/bash")'"
```

However, I encountered “Permission Denied” errors for most of them, indicating limited access.

Next, I attempted to switch users to “barney” using the previously obtained credentials, but the authentication failed. I then decided to try the credentials for the user “stephen,” which were successful and granted me access.

After successfully switching to the “stephen” account, I ran the command:

```
id
```

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

The output revealed that I was now operating under the user “stephen,” with a primary group identified as `(pcap)`. This indicated that I had access to certain capabilities associated with packet capturing.

With the elevated privileges under the “stephen” account, I utilized the `tcpdump` command to capture network traffic.

While analyzing the captured packets using `tcpdump`, I identified the username "paul" along with the corresponding password. This finding potentially opens up further avenues for privilege escalation or lateral movement within the system.

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

I then switched to the “paul” account using the command: Also sudo -l

```bash
su paul
sudo -l
```

The output indicated that the user “paul” could execute the following command without a password:

```bash
(peter) NOPASSWD: /usr/sbin/service
```

This presents an opportunity for privilege escalation, as “paul” can execute the `service` command as the user "peter" without requiring a password.

To explore the potential for privilege escalation through the `service` command, I visited the GTFOBins website at [https://gtfobins.github.io/gtfobins/service/](https://gtfobins.github.io/gtfobins/service/).

To escalate privileges to the “peter” account, I executed the following command:

```bash
sudo -u peter /usr/sbin/service ../../bin/sh
```

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

Tadaaa !!! Its worked !

<figure><img src="../.gitbook/assets/image (28).png" alt="" width="275"><figcaption></figcaption></figure>

## Root Access:

Once I had gained access as the user “peter,” I proceeded to switch to the root account by running the command:

```bash
su passwd root
```

I then changed the root password, which allowed me to successfully log in as the root user. This provided full administrative privileges over the system.

### Final investigation and flag retrieval

After gaining root access, I conducted a further investigation and navigated to the root directory. There, I discovered a file named `thisistheflag.txt`. I used the `cat` command to read its contents, successfully retrieving the flag.

With this, I completed the challenge!

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

Hehe..
