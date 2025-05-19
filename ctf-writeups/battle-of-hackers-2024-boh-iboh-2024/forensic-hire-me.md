# Forensic : Hire Me

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

After extracting the contents from the provided archive, I identified two important directories: `loot` and `recon`. Each of these folders likely contains specific artifacts or information essential for further forensic analysis.

Upon extracting the contents, I found two key directories: **loot** and **recon**. These contain various files that likely hold valuable information for further analysis.

### loot Directory <a href="#b8d0" id="b8d0"></a>

* **Groups.xml**: This file appears to contain information related to groups within the target system. This could include details about user group memberships, permissions, or roles. The data in this file can help determine which accounts have elevated privileges or might have been targeted during the attack.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### recon Directory <a href="#aee2" id="aee2"></a>

This folder contains multiple JSON files, each labeled with timestamps (likely indicating the time of the reconnaissance) and names corresponding to different system elements. Here’s a breakdown of the files:

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

During the investigation of the **Groups.xml** file extracted from the target environment, I identified an account named **rgraham**. The file contained critical user details, such as the account properties and an encrypted password field labeled `cpassword`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}"><User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="rgraham" image="2" changed="2024-08-15 06:59:13" uid="{BDA116FC-0E8C-4784-887F-71C9573859E3}"><Properties action="U" newName="" fullName="" description="" cpassword="FKhE/Beywcp8ZLLxH6LszmcuRiXceWaeEXvSJ5jKyJjqJ9vAidZiHVebDcE6n+Wi" changeLogon="0" noChange="0" neverExpires="0" acctDisabled="0" userName="rgraham"/></User>
</Groups>
```

Using `gpp-decrypt`, I successfully decrypted the password to "**L1ke\_OscP\_@gAiN}**" and identified it as part of a flag.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Continuing Forensic Analysis

After decrypting the `Groups.xml` file and obtaining the second part of the flag, I moved on to inspect the files in the `recon` folder. The folder contained multiple JSON files, and initially, using `cat` to read them made the output difficult to interpret.

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

To improve readability and make the contents more structured, I utilized the `jq` command. This tool formatted the JSON data into a more human-readable format, making it easier to analyze the information.

For example, I ran the following command on one of the JSON files:

```
jq . 20240815030319_computers.json
```

This gave me a nicely formatted output of the JSON structure, which made it simpler to parse through the data and identify relevant pieces of information.

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I examined the files in the `recon` folder for any unusual content. Most files were unremarkable, but I found some strange text in `20240815030319_users.json`.’

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

```xml
"description": "VwBlACAAZABpAGQAbgAnAHQAIABsAGUAYQByAG4AIABvAHUAcgAgAGwAZQBzAHMAbwBuACAAcwBvACAAdABoAGkAcwAgAGkAcwAgAG8AbgBlACAAaABhAGwAZgAgAG8AZgAgAHQAaABlACAAZgBsAGEAZwA6ACAASQBCAE8ASAAyADQAewBBAEQAXwBQAFcATgAzAGQAXwA=",                                                                                                                                                    

```

This peculiar string could be significant, so I plan to decode it to see if it reveals important information related to the investigation or the CTF challenge.

Upon discovering the unusual text in `20240815030319_users.json`, I suspected it to be Base64 encoded. To investigate further, I decoded it using the following command:

```
echo "VwBlACAAZABpAGQAbgAnAHQAIABsAGUAYQByAG4AIABvAHUAcgAgAGwAZQBzAHMAbwBuACAAcwBvACAAdABoAGkAcwAgAGkAcwAgAG8AbgBlACAAaABhAGwAZgAgAG8AZgAgAHQAaABlACAAZgBsAGEAZgA6ACAASQBCAE8ASAAyADQAewBBAEQAXwBQAFcATgAzAGQAXwA=" | base64 --decode
```

The decoded output revealed the following text:

```
We didn't learn our lesson so this is one half of the flag: IBOH24{AD_PWN3d_
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

Combining this with the previously obtained part of the flag, I was able to compile the complete flag for the CTF challenge:

### Flag

**IBOH24{AD\_PWN3d\_L1ke\_OscP\_@gAiN}**
