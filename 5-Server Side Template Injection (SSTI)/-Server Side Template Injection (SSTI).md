---

---

---

## 1. ما هي SSTI؟

**Server-Side Template Injection (SSTI)** هي ثغرة بتحصل عندما يتم إدخال **User-Controlled Input** داخل **Server-Side Template** بطريقة غير آمنة، فيتعامل الـ Template Engine مع الـ input على أنه **Template Code / Expression** بدل ما يتعامل معه كـ Data عادية.

### الفكرة الأساسية

المفروض:

```text
User Input → Variable → Template → HTML
```

لكن في SSTI:

```text
User Input → Template نفسها → Template Engine → Evaluation
```

وبالتالي المهاجم قد يستطيع تنفيذ expressions أو الوصول إلى objects/functions داخل بيئة الـ Template، وفي بعض الحالات قد تتطور الثغرة إلى **Remote Code Execution (RCE)**.

---

# 2. ما هو الـ Template Engine؟

الـ Template Engine هو نظام يستخدمه السيرفر لإنشاء صفحات HTML ديناميكية.

مثال:

```html
<h1>Hello {{ username }}</h1>
```

لو:

```text
username = Fares
```

الناتج:

```html
<h1>Hello Fares</h1>
```

الـ Template Engine هو الذي يقوم بعملية **Rendering**.

### أمثلة على Template Engines

| Template Engine | اللغة      | Syntax شائع                |
| --------------- | ---------- | -------------------------- |
| **Jinja2**      | Python     | `{{ expression }}`         |
| **Twig**        | PHP        | `{{ expression }}`         |
| **FreeMarker**  | Java       | `${expression}`            |
| **Velocity**    | Java       | `$variable` / `#directive` |
| **Thymeleaf**   | Java       | `${expression}`            |
| **ERB**         | Ruby       | `<%= expression %>`        |
| **Handlebars**  | JavaScript | `{{expression}}`           |

---

# 3. أين تحدث المشكلة؟

المشكلة ليست في استخدام Template Engine نفسه.

المشكلة في أن الـ Developer يسمح للـ User بالتحكم في **Template Code**.

### التصميم الآمن

```text
Template ثابت:
Hello {{ name }}

User Input:
Fares

Result:
Hello Fares
```

هنا `Fares` مجرد Data.

### التصميم الخطير

لو التطبيق قام ببناء Template من User Input ثم عمل Rendering لها:

```text
User Input
    ↓
Template String
    ↓
Template Engine
```

هنا الـ input قد يتم تفسيره كـ Template Code.

---

# 4. مثال بسيط جدًا

لو التطبيق يستخدم Template:

```text
Hello {{ name }}
```

وأرسلت:

```text
Fares
```

النتيجة:

```text
Hello Fares
```

طبيعي.

لكن لو استطعت التحكم في الـ Template نفسها وأرسلت:

```text
{{ 7*7 }}
```

وكان الناتج:

```text
49
```

فهذا يعني أن السيرفر قام **بتفسير الـ expression** بدل عرضه كنص.

وهذه علامة قوية على وجود SSTI.

---

# 5. الفرق بين SSTI و XSS

دي من أهم النقاط.

## XSS

في XSS، الـ payload يتم تفسيره في:

```text
Browser / Client
```

مثلاً:

```html
<script>alert(1)</script>
```

الهدف هو تنفيذ JavaScript في متصفح المستخدم.

```text
Input
 ↓
HTML/JavaScript
 ↓
Browser
 ↓
JavaScript Execution
```

## SSTI

في SSTI، الـ payload يتم تفسيره في:

```text
Server
```

مثلاً:

```text
{{ 7*7 }}
```

والـ Server قد يرجع:

```text
49
```

```text
Input
 ↓
Server Template
 ↓
Template Engine
 ↓
Expression Evaluation
```

### احفظ:

> **XSS = Client-Side execution**

> **SSTI = Server-Side template evaluation**

---

# 6. اكتشاف SSTI

أول مرحلة في اختبار SSTI هي **Detection**.

لا تبدأ مباشرة بمحاولة RCE.

ابحث أولًا عن أي مكان يدخل فيه User Input إلى response يتم توليده باستخدام Template.

أمثلة:

```text
?name=Fares
?message=Hello
?subject=Test
?title=Welcome
```

أو أماكن مثل:

- صفحات Profile
    
- Email Templates
    
- PDF generation
    
- Error pages
    
- Search results
    
- صفحات التخصيص
    
- Notification templates
    

---

# 7. اختبار الـ Template Expressions

يمكن تجربة expressions بسيطة وغير خطرة.

### اختبار شائع

```text
{{7*7}}
```

إذا ظهر:

```text
49
```

فهذه علامة قوية.

أما إذا ظهر:

```text
{{7*7}}
```

كما هو، فغالبًا تم التعامل معه كـ text.

---

# 8. لماذا نستخدم عملية حسابية؟

لأنها تساعدنا على معرفة هل الـ Template Engine يقوم بعملية **Evaluation** أم لا.

مثلاً:

```text
{{7*7}}
```

لو أصبحت:

```text
49
```

فهذا يختلف بوضوح عن مجرد Reflection.

بدلًا من:

```text
Input = {{7*7}}
Output = {{7*7}}
```

أصبح:

```text
Input = {{7*7}}
Output = 49
```

إذن حصل Evaluation.

---

# 9. أشهر Syntax للاختبار

الـ Syntax يختلف حسب الـ Template Engine.

أمثلة عامة:

```text
{{7*7}}
```

أو:

```text
${7*7}
```

أو:

```text
<%= 7*7 %>
```

أو Syntax أخرى حسب الـ Engine.

**مهم:** نجاح `{{7*7}}` وحده لا يعني بالضرورة أن الـ Engine هو Jinja2؛ يجب عمل fingerprinting.

---

# 10. Template Engine Fingerprinting

بعد إثبات أن هناك Template Evaluation، نحتاج إلى معرفة:

> ما هو الـ Template Engine؟

لأن طريقة الاستغلال تختلف من Engine لآخر.

مثلاً:

```text
Jinja2  → Python
Twig    → PHP
FreeMarker → Java
Velocity → Java
ERB → Ruby
```

### لماذا معرفة الـ Engine مهمة؟

لأن كل Engine لديه:

- Syntax مختلفة
    
- Objects مختلفة
    
- Functions مختلفة
    
- Security restrictions مختلفة
    
- Sandbox مختلفة
    
- طرق مختلفة للوصول إلى البيئة المحيطة
    

لذلك:

```text
Detect SSTI
      ↓
Identify Engine
      ↓
Understand Engine
      ↓
Determine Impact
```

---

# 11. Context مهم جدًا

مش كل SSTI تظهر في نفس المكان.

ممكن يكون الـ input داخل:

### HTML Template

```text
Hello {{ input }}
```

أو داخل:

### Attribute

```html
<input value="{{ input }}">
```

أو داخل:

### Email Template

```text
Dear {{ name }}
```

أو داخل:

### Error Page

```text
Error: {{ message }}
```

أو حتى في أنظمة توليد:

- PDF
    
- Emails
    
- Reports
    
- Notifications
    

لذلك لازم تعرف **أين يتم إدخال الـ input وكيف يتم Rendering له**.

---

# 12. SSTI Detection vs Reflection

مهم جدًا تفرق بين الاثنين.

## Reflection

ترسل:

```text
Fares
```

فيرجع:

```text
Fares
```

هذا لا يعني SSTI.

## Template Evaluation

ترسل:

```text
{{7*7}}
```

ويرجع:

```text
49
```

هنا عندك دليل أقوى على Template Evaluation.

---

# 13. Blind SSTI

مش كل SSTI ترجع النتيجة في الـ Response.

مثلاً:

```text
{{7*7}}
```

قد يتم تنفيذها، لكن لا ترى الناتج.

هذه تسمى:

**Blind SSTI**

أي أن الـ Server يقوم بالـ evaluation ولكن النتيجة غير ظاهرة مباشرة للمهاجم.

### كيف يمكن اكتشافها؟

حسب الـ Application، يمكن الاعتماد على:

- اختلاف الـ Response
    
- Template errors
    
- Timing behavior
    
- Out-of-band interactions
    

الفكرة:

```text
Input
 ↓
Template Engine
 ↓
Evaluation
 ↓
Result not directly visible
```

---

# 14. Error-Based Detection

أحيانًا إدخال expression غير صحيح ينتج Error من الـ Template Engine.

مثلاً قد يظهر في الـ Response:

```text
TemplateSyntaxError
```

أو:

```text
TemplateException
```

أو معلومات عن:

```text
Template
Engine
File
Line
```

وهذه المعلومات يمكن أن تساعد في تحديد الـ Engine أو الـ Context.

---

# 15. SSTI إلى RCE

أخطر سيناريو:

```text
SSTI
 ↓
Template Expression
 ↓
Access to Objects
 ↓
Access to Methods / Functions
 ↓
Dangerous functionality
 ↓
Command Execution
 ↓
RCE
```

لكن:

> **ليس كل SSTI يؤدي إلى RCE.**

قد تكون الـ SSTI محدودة فقط في:

- arithmetic
    
- string manipulation
    
- accessing variables
    
- reading template objects
    

حسب الـ Engine والـ configuration والـ sandbox.

---

# 16. لماذا قد تؤدي SSTI إلى RCE؟

لأن بعض Template Engines توفر للمـ Template إمكانية الوصول إلى Objects أو Methods أو Runtime functionality.

إذا استطاع المهاجم الانتقال من مجرد:

```text
Expression
```

إلى functionality تسمح بتنفيذ عمليات على النظام، فقد يصبح التأثير:

```text
Remote Code Execution
```

لكن إمكانية ذلك تعتمد على:

- Template Engine
    
- Version
    
- Configuration
    
- Sandbox
    
- Available objects
    
- Application implementation
    
- Permissions الخاصة بالـ Server process
    

---

# 17. Impact

تأثير SSTI يختلف حسب الحالة.

قد يسمح بـ:

### Low Impact

- تغيير output
    
- الوصول إلى template variables
    
- كشف معلومات
    

### Medium Impact

- قراءة بيانات داخل application context
    
- الوصول إلى objects
    
- كشف معلومات عن البيئة
    

### High/Critical Impact

إذا أمكن الوصول إلى code execution:

```text
RCE
```

ومن الممكن أن يؤدي ذلك إلى:

- قراءة ملفات يستطيع الـ process الوصول إليها
    
- الوصول إلى environment information
    
- الوصول إلى application secrets
    
- تنفيذ أوامر بصلاحيات الـ application
    

---

# 18. SSTI و Server-Side Code Execution

مهم تفرق بين:

```text
Template Evaluation
```

و:

```text
Code Execution
```

وجود:

```text
{{7*7}} → 49
```

يثبت أن هناك **Template Evaluation**.

لكن لا يثبت وحده وجود:

```text
RCE
```

لذلك في الـ Pentesting يجب إثبات الـ impact تدريجيًا.

---

# 19. Workflow في Burp Suite

عند اختبار Application باستخدام Burp:

### Step 1 — Find Input

حدد parameters التي يتحكم فيها المستخدم.

مثلاً:

```text
GET /?name=Fares
```

### Step 2 — Check Reflection

غيّر القيمة إلى:

```text
TEST123
```

وشوف أين تظهر.

### Step 3 — Test Expression

اختبر expression بسيط مثل:

```text
{{7*7}}
```

### Step 4 — Observe Response

لو:

```text
49
```

فهذه علامة مهمة.

### Step 5 — Fingerprint

حدد الـ Template Engine.

### Step 6 — Determine Impact

اعرف ما الذي يسمح به الـ Engine في هذا الـ Context.

### Step 7 — Stop at Proof

في اختبار حقيقي، لا تحتاج تنفيذ أوامر خطيرة لمجرد إثبات الثغرة إذا كان impact يمكن إثباته بطريقة آمنة.

---

# 20. مثال كامل

افترض أن عندك:

```text
GET /profile?name=Fares
```

الـ Response:

```text
Hello Fares
```

جرب:

```text
GET /profile?name={{7*7}}
```

لو ظهر:

```text
Hello 49
```

نستنتج:

```text
User Input
     ↓
Template
     ↓
Template Engine
     ↓
Expression Evaluation
```

بعدها:

```text
Identify Template Engine
          ↓
Study its objects/context
          ↓
Determine possible impact
```

---

# 21. SSTI في PortSwigger

في PortSwigger غالبًا الـ Lab بيطلب منك التعرف على:

1. مكان الـ Injection
    
2. هل الـ input يتم تفسيره؟
    
3. ما هو الـ Template Engine؟
    
4. ما هو الـ Context؟
    
5. ما الذي يمكن الوصول إليه؟
    
6. ما هو الـ Impact؟
    
7. تنفيذ الـ Lab objective
    

لذلك لا تحفظ Payloads فقط.

الأهم أنك تفهم:

```text
Why does this payload work?
```

---

# 22. الفرق بين SSTI و SQL Injection

كلاهما Injection، لكن الـ Sink مختلف.

### SQL Injection

```text
User Input
 ↓
SQL Query
 ↓
Database
```

مثلاً التطبيق يبني:

```sql
SELECT * FROM users WHERE name = 'INPUT'
```

### SSTI

```text
User Input
 ↓
Template
 ↓
Template Engine
```

مثلاً:

```text
Hello {{ INPUT }}
```

### الخلاصة

```text
SQLi → Database interpreter

SSTI → Template interpreter
```

---

# 23. الفرق بين SSTI و Command Injection

### Command Injection

الـ input يدخل مباشرة في system command.

```text
User Input
 ↓
OS Command
 ↓
Operating System
```

### SSTI

```text
User Input
 ↓
Template
 ↓
Template Engine
```

وفي بعض الحالات:

```text
SSTI → Template functionality → RCE
```

لكن الاثنين ليسا نفس الثغرة.

---

# 24. لماذا SSTI خطيرة؟

لأن الـ Template Engine يعمل **على السيرفر**.

لو كان الـ Engine يسمح بقدرات واسعة، فإن المهاجم قد ينتقل من مجرد التحكم في output إلى الوصول إلى وظائف داخل الـ application أو البيئة التي يعمل بها.

لذلك SSTI التي تؤدي إلى RCE يمكن أن تكون **Critical**.

---

# 25. أسباب حدوث SSTI

أهم سبب:

## Unsafe Template Construction

المطور يسمح للمستخدم بالتحكم في الـ Template نفسها.

المشكلة ليست:

```text
User Data
```

لكن:

```text
User-Controlled Template
```

---

# 26. الطريقة الصحيحة للتعامل مع User Input

### ❌ تصميم خطير

```text
User Input
     ↓
Template String
     ↓
Render
```

### ✅ تصميم أفضل

```text
Fixed Template
     ↓
Variables
     ↓
User Input as Data
     ↓
Render
```

مثلاً:

```text
Template:
Hello {{ name }}

name:
Fares
```

هنا `Fares` مجرد قيمة للمتغير.

---

# 27. الحماية من SSTI

## 1. لا تجعل User Input جزءًا من Template Code

خلي الـ Template ثابتة.

## 2. افصل Data عن Template

User input يجب أن يكون:

```text
Variable
```

وليس:

```text
Template
```

## 3. استخدم Safe APIs

استخدم طرق الـ Template Engine المخصصة لإدخال variables بدل Dynamic Template Construction.

## 4. قلل صلاحيات التطبيق

استخدم:

- Least Privilege
    
- Restricted service accounts
    
- Container isolation
    
- Filesystem restrictions
    

لأن هذه الإجراءات لا تمنع SSTI وحدها، لكنها تقلل الـ impact إذا حدث exploitation.

## 5. استخدم Sandbox عندما يكون مناسبًا

بعض الـ Engines توفر Sandbox، لكن يجب عدم اعتبارها دائمًا حماية مطلقة.

## 6. حدّث الـ Template Engine

استخدام إصدارات قديمة قد يسبب مشاكل أمنية إضافية.

---

# 28. هل Encoding يمنع SSTI؟

ليس بالضرورة.

الـ Encoding وescaping يعتمد على الـ Context.

المشكلة الأساسية هي:

```text
User Input
```

أصبح:

```text
Template Code
```

ولذلك الحل الحقيقي هو منع الـ input من أن يصبح Template Code من الأساس.

---

# 29. أهم المصطلحات

### Template

صفحة أو نص يحتوي على placeholders وlogic.

### Template Engine

البرنامج الذي يقوم بعملية Rendering.

### Rendering

تحويل الـ Template إلى الناتج النهائي.

### Expression

تعليمة أو قيمة يتم تفسيرها داخل الـ Template.

مثال:

```text
{{7*7}}
```

### SSTI

User Input يتم تفسيره كـ Template.

### Blind SSTI

الـ Template يتم تفسيره لكن النتيجة ليست ظاهرة مباشرة.

### RCE

Remote Code Execution.

---

# 30. أهم علامات SSTI

احفظ العلامات التالية:

```text
{{7*7}} → 49
```

أو وجود:

```text
Template errors
```

أو ظهور أسماء مثل:

```text
Jinja
Twig
FreeMarker
Velocity
```

أو اختلاف behavior عند إدخال Template syntax.

لكن لا تعتمد على علامة واحدة فقط؛ يجب تأكيد الثغرة.

---

# 31. أهم أخطاء المبتدئين

### الخطأ 1

اعتبار أي Reflection = SSTI.

❌ خطأ.

### الخطأ 2

اعتبار `{{7*7}} → 49` دليلًا مباشرًا على RCE.

❌ لا.

هو دليل على Template Evaluation.

### الخطأ 3

حفظ Payloads بدون فهم الـ Engine.

❌ غير عملي.

### الخطأ 4

الخلط بين XSS وSSTI.

```text
XSS → Browser
SSTI → Server Template Engine
```

### الخطأ 5

البدء بـ RCE قبل إثبات SSTI.

الأصح:

```text
Detection
 ↓
Confirmation
 ↓
Fingerprinting
 ↓
Impact
```

---

# 32. طريقة مذاكرة SSTI

ذاكرها بالترتيب:

```text
1. Template Engines
        ↓
2. Server-Side Rendering
        ↓
3. SSTI Concept
        ↓
4. Detection
        ↓
5. Template Fingerprinting
        ↓
6. Context
        ↓
7. Blind SSTI
        ↓
8. Impact
        ↓
9. SSTI → RCE
        ↓
10. Prevention
        ↓
11. PortSwigger Labs
```

---

# 33. Cheat Sheet

## Definition

```text
SSTI =
Server-Side Template Injection
```

## Main idea

```text
User Input
   ↓
Template
   ↓
Template Engine
   ↓
Evaluation
```

## Detection

```text
{{7*7}}
```

إذا:

```text
49
```

→ Template Evaluation suspected.

## Important Engines

```text
Jinja2       → Python
Twig         → PHP
FreeMarker   → Java
Velocity     → Java
Thymeleaf    → Java
ERB          → Ruby
Handlebars   → JavaScript
```

## XSS vs SSTI

```text
XSS  → Client / Browser
SSTI → Server / Template Engine
```

## Impact

```text
SSTI
 ↓
Information Disclosure
 ↓
Access to Objects/Functions
 ↓
Potential RCE
```

لكن:

```text
SSTI ≠ Always RCE
```

## Prevention

```text
Fixed Templates
+
User Input as Data
+
Safe APIs
+
Least Privilege
+
Sandbox where appropriate
+
Updates
```

---

# 34. أهم أسئلة Interview

### ما هي SSTI؟

ثغرة تحدث عندما يتم تفسير User-Controlled Input كجزء من Server-Side Template بدل التعامل معه كـ Data.

### كيف تكتشفها؟

باختبار Template Expressions بسيطة ومراقبة ما إذا كان السيرفر يقوم بتقييمها.

مثال:

```text
{{7*7}} → 49
```

### هل SSTI تؤدي دائمًا إلى RCE؟

لا.

يعتمد ذلك على الـ Template Engine والـ Configuration والـ Context والـ Objects والـ Permissions المتاحة.

### ما الفرق بينها وبين XSS؟

XSS يتم تنفيذها في Client/Browser، بينما SSTI تحدث أثناء Server-Side Template Rendering.

### ما أشهر Template Engines؟

Jinja2، Twig، FreeMarker، Velocity، Thymeleaf، ERB وغيرها.

### كيف تمنع SSTI؟

منع User Input من أن يصبح Template Code، وفصل الـ Data عن الـ Template، واستخدام Safe APIs واتباع Least Privilege.

---

# 35. الخلاصة النهائية

الفكرة كلها يمكن تلخيصها في:

```text
          NORMAL
User Input
     ↓
Variable
     ↓
Template
     ↓
HTML


          SSTI
User Input
     ↓
Template Code
     ↓
Template Engine
     ↓
Evaluation
     ↓
Potential Impact
     ↓
Potential RCE
```

أهم شيء تحفظه:

> **SSTI ليست مجرد أن الـ input يظهر في الصفحة؛ SSTI تعني أن الـ input أصبح جزءًا من Template ويتم تفسيره بواسطة Server-Side Template Engine.**

والـ Methodology الصحيحة:

```text
Find Input
    ↓
Test Reflection
    ↓
Test Template Expression
    ↓
Confirm Evaluation
    ↓
Identify Engine
    ↓
Identify Context
    ↓
Determine Impact
    ↓
Assess RCE Possibility
    ↓
Report / Remediate
```