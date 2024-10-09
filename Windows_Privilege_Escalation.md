# Windows

- [winPEAS](https://github.com/peass-ng/PEASS-ng/releases)
- connect to the target
    ```
    evil-winrm -i <rhost> -u <user> -p <password>
    evil-winrm -i <rhost> -u <user> H <hash-pass> 
    ```

## System Enumeration

```
net users
net users <username>
whoami /priv
net localgroup
```

Find all important files:
```
cd C:\Users
tree /F
```
