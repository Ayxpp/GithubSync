# Century - Under The Wire

### Century 1

We log in to Century1 using the credentials `century1/century1` . Retrieving the build version is straightforward:

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

Password for level 2 is: `10.0.14393.7870`.

### Century 2

To identify the command associated with an alias, use the `Get-Alias` cmdlet:

<figure><img src="../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

Password for level 3 is: `invoke-webrequest443`.

### Century 3

Here’s another straightforward example that introduces some new operators:

```
(Get-ChildItem -File ..\Desktop | Measure-Object).Count
```

<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

Password for level 4 is: `123`

### Century 4

This level is about working with folders that have spaces in their names. Windows Explorer only shows the name before the spaces with three dots, so people usually don’t notice.

<figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

Password for level 5 is: `15768`

### Century 5

Let's start by retrieving the file from the Desktop and verifying the domain.

<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

Password for level 5 is `15768`



### Century 6

This level teaches how to use methods for counting things.

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

Password for level 6 is `197`

### Century 7

```
Get-Childitem –Path . -Include *readme* -File -Recurse -ErrorA
ction SilentlyContinue
```

<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

Password for level 7 is `7points`

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

### Century 8

```
(Get-Content .\unique.txt | Sort-Object -Unique | Measure-Object).Count
```

<figure><img src="../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

### Century 9

```
(Get-Content .\Word_File.txt -Raw).Split(" ")
```

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

### Century 10

First let’s get the file on the desktop:

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

```
Get-Service -Name "Windows Update" | Select-Object *
```

<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

Based on the above description, the password for the next level is: `windowsupdates110`.

### Century 11

```
Get-Childitem –Path Contacts,Desktop,Documents,Downloads,Favorites,Music,Videos -File -Attributes !D+H -Exclude d
esktop.ini -Recurse -ErrorAction SilentlyContinue
```

<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

### Century 12

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

Password for the level 12 is `i_authenticate_things`

### Century 13

```
(Get-ChildItem | get-content | Measure-Object -Word).Words
```

<figure><img src="../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

Password for the level 13 is `755`

### Century 14

```
get-content .\countpolos | Select-String -Pattern "polo" | Measure-Object
```

<figure><img src="../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

The password is `1`
