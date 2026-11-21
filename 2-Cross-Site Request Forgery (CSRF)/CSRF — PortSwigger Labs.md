

## Study Sheet — Ideas & Key Points

> **الفكرة العامة:**  
> CSRF = إجبار مستخدم مسجل دخول على تنفيذ Action على موقع آخر بدون ما يكون هو اللي طلب تنفيذ الـAction بنفسه.

---

# 01 — CSRF: No Protection

### 🎯 الفكرة

الـLab ده مفيهوش CSRF Protection أصلًا.

يعني مفيش:

```text
CSRF Token ❌
SameSite Protection ❌
Referer Validation ❌
```

وبالتالي نقدر نعمل **CSRF PoC عادي**.

### 🧠 الفكرة

المستخدم يكون Logged In في الموقع، ونخليه يفتح صفحة الـExploit الخاصة بنا.

الصفحة تبعت Request للموقع الهدف، والمتصفح يرسل الـSession Cookie تلقائيًا.

### 📌 الخلاصة

```text
No CSRF Protection
        ↓
Create CSRF Form
        ↓
Victim opens Exploit
        ↓
Request sent with Cookie
        ↓
Action executed
```

---

# 02 — CSRF: Method Change

### 🎯 الفكرة

هنا فيه CSRF Protection، لكن الـProtection مرتبط بالـHTTP Method.

مثلاً الـApplication متوقع:

```text
POST /my-account/change-email
```

ومش بيقبل الـCSRF بالطريقة العادية.

### 🧠 الحل

نجرب تغيير الـMethod من:

```text
POST
```

إلى:

```text
GET
```

باستخدام Method Override أو الطريقة التي يسمح بها التطبيق.

### الفكرة

```text
Protected POST
      ↓
Change Method
      ↓
GET
      ↓
Protection bypass
```

### 📌 الخلاصة

> لو الـCSRF Protection مطبق بشكل غير متناسق بين `POST` و`GET`، تغيير الـMethod ممكن يتجاوز الحماية.

---

# 03 — CSRF Where Token Validation Depends on Token Being Present

### 🎯 الفكرة

الـApplication عنده CSRF Token، لكن فيه مشكلة في طريقة التحقق.

لو الـToken موجود:

```text
Token موجود
→ يتم التحقق منه
```

لكن لو شلناه:

```text
Token غير موجود
→ Request ممكن يعدي
```

### 🧠 الاستغلال

بدل ما نبعت:

```text
csrf=TOKEN
```

نجرب حذف الـParameter بالكامل.

### 📌 الخلاصة

```text
Token Present
→ Validate

Token Missing
→ Validation skipped
→ CSRF
```

> **المشكلة:** الـServer يفترض أن عدم وجود الـToken معناه عدم الحاجة للتحقق.

---

# 04 — CSRF Where Token Is Not Tied to User Session

### 🎯 الفكرة

هنا الـCSRF Token موجود ويتم التحقق منه، لكن المشكلة:

> الـToken **مش مربوط بالـUser Session**.

يعني Token من Session/User ممكن يتم قبوله مع Session/User أخرى.

### 🧠 طريقة التفكير

عندنا:

```text
User 1
→ Token 1
```

و:

```text
User 2
→ Token 2
```

لو السيرفر يقبل:

```text
User 2 Session
+
Token 1
```

يبقى الـToken غير مربوط بالـSession.

### 🔹 الفكرة العملية

1. تدخل بالـUser الأول.
    
2. تعمل Request لتغيير الـEmail.
    
3. تستخرج الـCSRF Token.
    
4. تحتفظ بالـToken.
    
5. تسجل دخول بالـUser الثاني.
    
6. تستخدم Session الخاصة بالمستخدم الثاني مع **Token المستخدم الأول**.
    
7. إذا نجح الطلب → الـToken غير مربوط بالـSession.
    

مثال Token:

```html
<input required type="hidden"
       name="csrf"
       value="Rw8daFRRWidMlUUATtzrDqA4yKn7X7S0">
```

### 🎯 الخلل

```text
Session 1 → Token 1

Session 2 → Token 1
       ↓
Request accepted
```

### 📌 احفظ

> **CSRF Token لازم يكون مرتبط بالـSession/User، مش مجرد قيمة صحيحة.**

---

# 05 — CSRF Where Token Is Tied to Non-Session Cookie

### 🎯 الفكرة

هنا الـToken مش مربوط بالـSession مباشرة.

بدل ذلك، التطبيق عنده:

```text
CSRF Token
+
CSRF Cookie
```

ويعمل مقارنة بينهم.

مثلاً:

```text
csrf = ABC
csrfKey = ABC
```

### 🧠 المشكلة

الـApplication يتحقق من:

```text
Token == Cookie
```

لكن لا يتحقق بشكل صحيح أن الـCookie نفسها مرتبطة بالـSession.

### 🔹 طريقة التفكير

نحتاج نعرف:

```text
User 1
→ csrf
→ csrfKey
```

ثم نجرب استخدام الـقيم المرتبطة ببعض مع Session المستخدم الآخر.

لو الطلب نجح، يبقى الـCSRF protection ضعيف.

### 🚨 الجزء المهم في اللاب

نستغل **Cookie Injection** من خلال ثغرة تسمح لنا بإجبار المتصفح على تغيير `csrfKey`.

الفكرة العامة للـPayload:

```html
<img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None"
     onerror="document.forms[0].submit()">
```

### 🧠 الفكرة

```text
Attacker
   ↓
Set csrfKey Cookie
   ↓
Victim Browser
   ↓
CSRF Form
   ↓
csrf Token == csrfKey
   ↓
Request Accepted
```

### 📌 الخلاصة

> الحماية تعتمد على **Token + Cookie**، لكن الـCookie ليست مرتبطة بالـSession بشكل آمن.

---

# 06 — CSRF Where Token Is Duplicated in Cookie

### 🎯 الفكرة

هنا يوجد:

```text
CSRF Token
+
CSRF Cookie
```

والـServer يقارن الاثنين.

مثلاً:

```text
Request:
csrf=ABC

Cookie:
csrf=ABC
```

### 🧠 المشكلة

حتى لو غيرنا الـToken، لازم نغير الـCookie إلى **نفس القيمة**.

يعني:

```text
csrf = XYZ
```

و:

```text
Cookie csrf = XYZ
```

### 📌 الخلاصة

```text
Token
   ↕
Cookie
   ↓
Same Value
```

> الحماية هنا تعتمد على مساواة قيمتين يمكن للمهاجم التحكم فيهما.

---

# 07 — SameSite Lax Bypass via Method Override

### 🎯 الفكرة

`SameSite=Lax` يمنع بعض الـCross-Site Requests من إرسال Cookies، لكنه يسمح ببعض أنواع **Top-Level GET Navigation**.

اللاب يستغل الفرق ده.

### 🧠 الفكرة

لو الـAction الأصلي:

```text
POST
```

نحاول استخدام:

```text
GET
```

مع **Method Override** إذا كان التطبيق يدعمها.

مثلاً الفكرة تكون:

```text
POST
 ↓
GET
 ↓
Method Override
 ↓
Server treats it as POST
```

### 📌 الخلاصة

> الهدف هو جعل الطلب يبدو للمتصفح كـGET، بينما التطبيق يتعامل معه كـPOST.

---

# 08 — SameSite Strict Bypass via Client-Side Redirect

### 🎯 الفكرة

`SameSite=Strict` أكثر صرامة من `Lax`.

لكن اللاب يحتوي على **Client-Side Redirect** يمكن استغلاله.

### 🧠 الفكرة

بدل إرسال الـCSRF Request مباشرة من موقع المهاجم:

```text
Attacker
   ↓
Target
```

نحاول جعل الضحية تدخل أولًا إلى Endpoint داخل الموقع نفسه يحتوي على Redirect.

```text
Attacker
   ↓
Target Redirect
   ↓
Target Action
```

وبذلك يصبح الـNavigation بالنسبة للمتصفح داخل سياق الموقع الهدف.

### 🎯 نقطة مهمة

> لازم تكتشف الـClient-Side Redirect الموجود في ملفات/صفحات الـJavaScript وتفهم كيف يمكن التحكم في الـURL الذي يتم التوجيه إليه.

### 📌 الخلاصة

```text
SameSite=Strict
       ↓
Client-Side Redirect
       ↓
Same-Site Navigation
       ↓
CSRF
```

---

# 09 — CSRF Where Referer Validation Depends on Header Being Present

### 🎯 الفكرة

الموقع يستخدم:

```text
Referer Header
```

كوسيلة للحماية من CSRF.

لكن المشكلة:

> لو الـReferer موجود → يتم فحصه.

أما لو:

```text
Referer Header
```

غير موجود:

```text
Validation may be skipped
```

### 🧠 المنطق

```text
Referer موجود
     ↓
Check Referer

Referer غير موجود
     ↓
No Check
     ↓
CSRF
```

### 📌 الخلاصة

> الـSecurity Check لازم يرفض الطلب عند غياب الـReferer، وليس يفترض أنه آمن.

---

# 10 — CSRF with Broken Referer Validation

### 🎯 الفكرة

الموقع يستخدم `Referer` لمنع CSRF، لكن طريقة التحقق ضعيفة.

بدل ما يتأكد أن الـReferer **بالضبط** من الموقع الموثوق، يعمل بحث عن اسم الدومين داخل الـURL.

مثلاً بدل:

```text
Referer == trusted-domain
```

يعمل بشكل منطقي مثل:

```text
Referer contains trusted-domain
```

### 🚨 المشكلة

المهاجم يقدر يجعل اسم موقع الضحية يظهر داخل URL الخاص بموقع الـExploit.

---

## 🔹 Client-Side History Manipulation

نستخدم:

```javascript
history.pushState("", "", "/?LAB-ID.web-security-academy.net");
```

### 🧠 ماذا تفعل؟

تغير الـURL الظاهر في المتصفح بدون إعادة تحميل الصفحة.

فيصبح شكل الـURL مثل:

```text
https://exploit-server.net/?LAB-ID.web-security-academy.net
```

لاحظ:

```text
LAB-ID.web-security-academy.net
```

موجود داخل الـURL.

---

## 🔹 إجبار المتصفح على إرسال Referer

نستخدم:

```html
<meta name="referrer" content="unsafe-url">
```

بحيث يسمح بإرسال الـReferer الكامل.

### النتيجة

الـServer قد يستقبل:

```http
Referer: https://exploit-server.net/?LAB-ID.web-security-academy.net
```

### 🧠 ماذا يرى الـServer؟

لو التحقق ضعيف ويبحث فقط عن:

```text
LAB-ID.web-security-academy.net
```

فسيجده داخل الـReferer.

وبالتالي:

```text
Exploit Server
       ↓
Referer contains Target Domain
       ↓
Bad Validation
       ↓
Request Accepted
       ↓
CSRF
```

### 📌 الخلاصة

```text
Correct Validation:
Referer = trusted origin

Broken Validation:
Referer contains trusted origin
```

---

# 🧠 CSRF Master Flow

```text
                    CSRF
                      │
       ┌──────────────┴──────────────┐
       │                             │
   No Protection                 Protection
       │                             │
   CSRF Form              ┌──────────┼──────────┐
                          │          │          │
                       Token       Cookie    Referer
                          │          │          │
                          ↓          ↓          ↓
                     Missing?    Not tied?   Missing?
                     Reusable?   Duplicated? Contains?
                          │          │          │
                          └──────────┴──────────┘
                                     ↓
                                  Bypass
```

---

# ⭐ أهم الأفكار للحفظ

|Lab|الفكرة الأساسية|
|---|---|
|01|No CSRF Protection|
|02|Method Change / Override|
|03|Missing Token = Validation Bypass|
|04|Token Not Tied to Session|
|05|Token Tied to Non-Session Cookie|
|06|Duplicated Token in Cookie|
|07|SameSite Lax + Method Override|
|08|SameSite Strict + Client-Side Redirect|
|09|Missing Referer = Validation Bypass|
|10|Broken Referer Validation|

---

# ⚡ Quick Revision

```text
01 → No Protection
02 → Change Method

03 → Remove CSRF Token
04 → Token ≠ Session Bound
05 → Token + Non-Session Cookie
06 → Token = Cookie

07 → SameSite Lax → Method Override
08 → SameSite Strict → Client-Side Redirect

09 → Missing Referer
10 → Broken Referer "contains"
```

> [!important] أهم فرق تحفظه  
> **Lab 04:** المشكلة في ارتباط الـToken بالـSession.  
> **Lab 05:** المشكلة في ارتباط الـToken بالـCookie وليس بالـSession.  
> **Lab 06:** الـToken موجود في الـRequest والـCookie ويتم مقارنتهما، لكن يمكن التحكم فيهما معًا.  
> **Lab 09:** حذف الـReferer يتجاوز الـValidation.  
> **Lab 10:** الـReferer موجود، لكن الـValidation نفسها مكتوبة بشكل خاطئ.