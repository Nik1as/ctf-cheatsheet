# Active Directory

```
smbclient -L //<target-ip>/ -U <user>
smbclient //<target-ip>/<share> -U <user>

enum4linux -a <ip>

ldapdomaindump ldap://<target-ip> -u '<domain>\<user>' -p '<password>'

netexec smb <target-ip> -u guest -p '<password>' --rid-brute
netexec smb <target-ip> -u users.txt -p '<password>' 
```
