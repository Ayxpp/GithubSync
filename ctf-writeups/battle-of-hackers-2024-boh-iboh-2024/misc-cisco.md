# Misc : Cisco

In this challenge, I was tasked with accessing a Cisco device using SSH credentials provided. The challenge involved analyzing a Cisco configuration file, which may contain sensitive information or misconfigurations that could be exploited.

### Initial Steps <a href="#f74f" id="f74f"></a>

1. **SSH Access**: I began by using the provided SSH credentials to connect to the Cisco device.

```
ssh guest@192.168.69.1

```

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

### Steps Taken

1. **PC-0 Initialization**: I started by launching the PC-0 virtual machine that was preconfigured to connect to the target router.
2. **Network Connectivity**: Ensured that the PC-0 had an active network connection to the router, verifying the IP address and network configuration settings to confirm connectivity.
3. **SSH Connection**: With the network established, I used SSH to connect to the router.

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

The credentials required for the login included the IP Adress , username ‘guest’ and the password ‘IBOH24,’ which I had previously obtained during the challenge.

After successfully logging into the router via SSH, I executed the command:

```shell
show running-config
```

The output:

```bash
IBOH24#show running-config
Building configuration...

Current configuration : 923 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname IBOH24
!
!
!
enable secret 5 $1$mERr$8mTx48MNbacAt2sMZ0dq8/
!
!
!
!
!
!
ip cef
no ipv6 cef
!
!
!
username guest privilege 15 password 7 08086E61214B51
username root privilege 15 password 7 08086E61214B510C003E022A03052317303A0C350E311E
!
!
license udi pid CISCO2901/K9 sn FTX15242Z92-
!
!
!
!
!
!
!
!
!
ip ssh version 2
ip domain-name IBOH24
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0
 ip address 192.168.69.1 255.255.255.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
ip classless
!
ip flow-export version 9
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
!
!
!
end
```

* The `guest` account has privilege level 15, indicating it has full administrative rights. Its encrypted password is `7 08086E61214B51`.
* The `root` account, also with privilege level 15, possesses a more complex encrypted password: `7 08086E61214B510C003E022A03052317303A0C350E311E`.



Upon further investigation, I decrypted the encrypted passwords using an online Cisco Type 7 Password Decryptor. The results were as follows:

* The encrypted password for the `guest` account:
* **Encrypted**: `08086E61214B51`
* **Decrypted**: `IBOH24`

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

* The encrypted password for the `root` account:
* **Encrypted**: `08086E61214B510C003E022A03052317303A0C350E311E`
* **Decrypted**: `IBOH24{rUnNINg_coNFiG}`

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

Got the flag !!!
