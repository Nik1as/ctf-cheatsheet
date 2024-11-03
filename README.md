# CTF Cheat-Sheet

- [Resources](#resources)
- [Nmap](#nmap)
- [Services](Services.md)
- [Web Applications](Web_Applications.md)
- [Command Injection](Command_Injection.md)
- [Shell](Shell.md)
- [Crypto](Crypto.md)
- [Active Directory](Active_Directory.md)
- [Buffer Overflow](Buffer_Overflow.md)
- [Linux Privilege Escalation](Linux_Privilege_Escalation.md)
- [Windows Privilege Escalation](Windows_Privilege_Escalation.md)
- [Forensics](Forensics.md)
- [File Transfer](File_Transfer.md)
- [Git](Git.md)

## Resources

- [Hacktricks](https://book.hacktricks.xyz/)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master)

## Nmap

```sh
# Full TCP-SYN scan with versions, scripts and output file
nmap -vv -sV -sC -p- -Pn --min-rate 10000 -oN scan.txt <rhost>

# Full slow scan
nmap -vv -A -T4 -p- -Pn --script vuln <rhost>

# Scan for UDP
nmap -vv -sU <rhost>

# Scan for vulnerabilities
nmap -p- --script vuln <rhost>

# OS detection
nmap -O --osscan-guess <rhost>

# Ping Sweep
nmap -sn <rhosts>
```
