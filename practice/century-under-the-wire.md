# Century - Under The Wire

### Century 1

```
The password for Century2 is the build version of the instance of PowerShell installed on this system.
```

We log in to Century1 using the credentials `century1/century1` . Retrieving the build version is straightforward:

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

Password for level 2 is: `10.0.14393.7870`.

### Century 2

```
The password for Century3 is the name of the built-in cmdlet that performs the wget like function within PowerShell PLUS the name of the file on the desktop.

NOTE:
– If the name of the cmdlet is “get-web” and the file on the desktop is named “1234”, the password would be “get-web1234”.
– The password will be lowercase no matter how it appears on the screen.	
```

To identify the command associated with an alias, use the `Get-Alias` cmdlet:

<figure><img src="../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

Password for level 3 is: `invoke-webrequest443`.

### Century 3

```
The password for Century4 is the number of files on the desktop.	
```

Here’s another straightforward example that introduces some new operators:

```
(Get-ChildItem -File ..\Desktop | Measure-Object).Count
```

<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

Password for level 4 is: `123`

### Century 4

```
The password for Century5 is the name of the file within a directory on the desktop that has spaces in its name.

NOTE:
– The password will be lowercase no matter how it appears on the screen.
```

This level is about working with folders that have spaces in their names. Windows Explorer only shows the name before the spaces with three dots, so people usually don’t notice.

<figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

Password for level 5 is: `15768`

### Century 5

```
The password for Century6 is the short name of the domain in which this system resides in PLUS the name of the file on the desktop.

NOTE:
– If the short name of the domain is “blob” and the file on the desktop is named “1234”, the password would be “blob1234”.
– The password will be lowercase no matter how it appears on the screen.
```

Let's start by retrieving the file from the Desktop and verifying the domain.

<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

Password for level 6 is `underthewire3347`.



### Century 6

```
The password for Century7 is the number of folders on the desktop.	
```

This level teaches how to use methods for counting things.

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

Password for level 7 is `197`

### Century 7

```
The password for Century8 is in a readme file somewhere within the contacts, desktop, documents, downloads, favorites, music, or videos folder in the user’s profile.

NOTE:
– The password will be lowercase no matter how it appears on the screen.
```

```
Get-Childitem –Path . -Include *readme* -File -Recurse -ErrorAction SilentlyContinue
```

<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Password for level 8 is `7points`

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

### Century 8

```
The password for Century9 is the number of unique entries within the file on the desktop.	
```

```
(Get-Content .\unique.txt | Sort-Object -Unique | Measure-Object).Count
```

<figure><img src="../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

Password for level 9 is `696`

### Century 9

```
The password for Century10 is the 161st word within the file on the desktop.

NOTE:
– The password will be lowercase no matter how it appears on the screen.
```

```
(Get-Content .\Word_File.txt -Raw).Split(" ")
```

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

### Century 10

```
The password for Century11 is the 10th and 8th word of the Windows Update service description combined PLUS the name of the file on the desktop.

NOTE:
– The password will be lowercase no matter how it appears on the screen.
– If the 10th and 8th word of the service description is “apple” and “juice” and the name of the file on the desktop is “88”, the password would be “applejuice88”.
```

First let’s get the file on the desktop:

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

```
Get-Service -Name "Windows Update" | Select-Object *
```

<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

Based on the above description, the password for the next level is: `windowsupdates110`.

### Century 11

```
The password for Century12 is the name of the hidden file within the contacts, desktop, documents, downloads, favorites, music, or videos folder in the user’s profile.

NOTE:
– Exclude “desktop.ini”.
– The password will be lowercase no matter how it appears on the screen.
```

```
Get-Childitem –Path Contacts,Desktop,Documents,Downloads,Favorites,Music,Videos -File -Attributes !D+H -Exclude d
esktop.ini -Recurse -ErrorAction SilentlyContinue
```

<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

The password for level 12 is `secret_sauce` .

### Century 12

```
The password for Century13 is the description of the computer designated as a Domain Controller within this domain PLUS the name of the file on the desktop.

NOTE:
– The password will be lowercase no matter how it appears on the screen.
– If the description “today_is” and the file on the desktop is named “_cool”, the password would be “today_is_cool”.
```

```
Get-ADDomainController
```

<figure><img src="../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

```
Get-ADComputer -Properties Description
```

<figure><img src="../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

Combine with the filename in the desktop

<figure><img src="../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

Password for the level 13 is `i_authenticate_things`

### Century 13

```
The password for Century14 is the number of words within the file on the desktop.	
```

```
(Get-ChildItem | get-content | Measure-Object -Word).Words
```

<figure><img src="../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

Password for the level 13 is `755`

### Century 14

```
The password for Century15 is the number of times the word “polo” appears within the file on the desktop.

NOTE:
– You should count the instances of the whole word only.
```

```
get-content .\countpolos | Select-String -Pattern "polo" | Measure-Object
```

<figure><img src="../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

The password is `1`

And that’s all! Overall a quick set of challenges.

