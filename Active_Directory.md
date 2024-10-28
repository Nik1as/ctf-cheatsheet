# Active Directory

```
smbclient -L //<rhost>/ -U <user>
smbclient //<rhost>/<share> -U <user>

enum4linux -a <ip>

ldapdomaindump ldap://<rhost> -u '<domain>\<user>' -p '<password>'

netexec smb <rhost> -u guest -p '<password>' --rid-brute
netexec smb <rhost> -u users.txt -p '<password>' 
```
