# Nmap

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
