# CTF Cheat-Sheet

- [Resources](#resources)
- [Nmap](#nmap)
- [Services](Services.md)
- [Web Applications](Web_Applications.md)
- [Shell](Shell.md)
- [Crypto](Crypto.md)
- [Linux Privilege Escalation](Linux_Privilege_Escalation.md)
- [Windows Privilege Escalation](Windows_Privilege_Escalation.md)
- [Forensics](Forensics.md)
- [File Transfer](File_Transfer.md)
- [Git](Git.md)

## Resources

- [Hacktricks](https://book.hacktricks.xyz/)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master)

## Nmap

```
nmap -vv -sV -sC -p- -Pn --min-rate 10000 -oN scan.txt <rhost>
namp -vv -sU <rhost>
nmap -p- --script vuln <rhost>
nmap -sn <rhosts>
```
