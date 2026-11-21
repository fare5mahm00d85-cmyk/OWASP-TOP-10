
---

# Open Redirect — Open Redirection

## 1. ما هي Open Redirect؟

**Open Redirect** هي ثغرة Web تحدث عندما يسمح التطبيق للمستخدم بالتحكم في عنوان الـ URL الذي سيتم إعادة توجيه المستخدم إليه، بدون التحقق بشكل صحيح من أن الوجهة موثوقة.

بمعنى أبسط:

التطبيق عنده:

```text
Redirect User → https://example.com
```

لكن المستخدم يستطيع تغيير الوجهة إلى:

```text
https://attacker.com
```

فيقوم الموقع نفسه بإرسال المستخدم إلى الموقع الذي حدده المهاجم.

---

# 2. الفكرة الأساسية

تخيل عندك رابط:

```text
https://example.com/login?next=/dashboard
```

بعد تسجيل الدخول، الموقع يقول:

```text
روح إلى /dashboard
```

لو التطبيق يسمح لك بتغيير:

```text
next=/dashboard
```

إلى:

```text
next=https://attacker.com
```

فقد يحدث:

```text
User
 ↓
example.com
 ↓
Redirect
 ↓
attacker.com
```

وهنا توجد Open Redirect.

---

# 3. لماذا اسمها Open Redirect؟

لأن:

**Redirect**

يعني التطبيق يقوم بتحويل المستخدم من URL إلى URL آخر.

و:

**Open**

تعني أن الوجهة ليست مقيدة بشكل صحيح، والمستخدم يستطيع تحديد وجهة خارجية.

مثلاً:

```text
https://example.com/redirect?url=https://google.com
```

لو الموقع يسمح بأي URL:

```text
https://example.com/redirect?url=https://attacker.com
```

فهذا Open Redirect.

---

# 4. Redirect الطبيعي vs Open Redirect

## Redirect طبيعي

مثلاً:

```text
https://example.com/login?next=/dashboard
```

التطبيق يسمح فقط بالذهاب إلى صفحات داخل الموقع:

```text
/dashboard
/profile
/settings
```

ده طبيعي إذا كان التحقق مضبوطًا.

---

## Open Redirect

لو:

```text
https://example.com/login?next=https://attacker.com
```

والتطبيق يحول المستخدم إلى:

```text
https://attacker.com
```

فهنا المشكلة.

---

# 5. أشهر الأماكن التي تبحث فيها عن Open Redirect

أثناء الـ Pentest، ابحث عن Parameters لها علاقة بالـ Redirect.

أمثلة:

```text
url=
redirect=
redirect_url=
redirect_uri=
next=
return=
returnUrl=
return_url=
continue=
dest=
destination=
target=
go=
link=
callback=
```

مثلاً:

```text
https://example.com/redirect?url=/home
```

أو:

```text
https://example.com/login?next=/dashboard
```

أو:

```text
https://example.com/logout?redirect=/login
```

لكن وجود parameter اسمه `url` أو `redirect` **لا يعني وحده وجود الثغرة**.

لازم تختبر سلوك التطبيق.

---

# 6. كيف أكتشف Open Redirect؟

الـ methodology بسيطة.

## Step 1 — Find Redirect Parameter

مثلاً:

```text
https://example.com/redirect?url=/home
```

## Step 2 — غيّر الـ Destination

بدل:

```text
/home
```

جرب:

```text
https://example.com/
```

ثم:

```text
https://example.org/
```

ثم موقع خارجي تملكه أو بيئة اختبار مصرح بها.

مثلاً:

```text
https://attacker.example/
```

## Step 3 — راقب الـ Response

قد تجد:

```http
HTTP/1.1 302 Found
Location: https://attacker.example/
```

هنا التطبيق فعليًا يقوم بالـ redirect إلى الـ destination الذي تتحكم فيه.

---

# 7. الـ HTTP Redirect

أهم شيء تفهمه هو Header اسمه:

```http
Location:
```

مثلاً:

```http
HTTP/1.1 302 Found
Location: https://example.com/dashboard
```

الـ Browser يشوف:

```text
302
```

و:

```text
Location: ...
```

فيذهب إلى العنوان الموجود في `Location`.

---

# 8. أشهر Status Codes للـ Redirect

يمكن أن ترى:

```text
301
302
303
307
308
```

الأكثر شيوعًا في التطبيقات:

```text
302 Found
```

لكن Open Redirect ليست مرتبطة بـ 302 فقط.

المهم هو أن التطبيق يرسل المستخدم إلى destination يتحكم فيه المهاجم.

---

# 9. مثال عملي

لديك:

```text
GET /redirect?url=/home
```

Response:

```http
HTTP/1.1 302 Found
Location: /home
```

نجرب:

```text
GET /redirect?url=https://attacker.example/
```

لو أصبح:

```http
HTTP/1.1 302 Found
Location: https://attacker.example/
```

فهذا يعني:

```text
User-controlled URL
        ↓
Redirect Function
        ↓
External Domain
```

وهذه هي الفكرة الأساسية للـ Open Redirect.

---

# 10. Open Redirect باستخدام Burp Suite

في Burp:

```text
Proxy
 ↓
HTTP History
 ↓
حدد Request فيه redirect
 ↓
Send to Repeater
```

مثلاً:

```http
GET /redirect?url=/home HTTP/1.1
Host: example.com
```

غيّر:

```text
url=/home
```

إلى:

```text
url=https://attacker.example/
```

ثم:

```text
Send
```

راقب:

```http
Location:
```

لو ظهر:

```http
Location: https://attacker.example/
```

فأنت أثبتّ أن الـ destination يمكن التحكم فيه.

---

# 11. Open Redirect في Login

ده من أشهر السيناريوهات.

مثلاً:

```text
https://example.com/login?next=/dashboard
```

بعد تسجيل الدخول:

```text
Login successful
       ↓
/dashboard
```

لو:

```text
next=https://attacker.example/
```

وأصبح:

```text
Login
 ↓
302
 ↓
attacker.example
```

فهنا يوجد Open Redirect.

---

# 12. لماذا Open Redirect تعتبر مشكلة أمنية؟

لوحدها، Open Redirect غالبًا أقل خطورة من:

- SQL Injection
    
- XSS
    
- RCE
    
- Authentication bypass
    

لكنها ممكن تصبح خطيرة عندما تدخل في Attack Chain.

أشهر استخداماتها:

```text
Open Redirect
      ↓
Phishing
```

أو:

```text
Open Redirect
      ↓
OAuth-related attacks
```

أو:

```text
Open Redirect
      ↓
Token / parameter leakage
```

حسب التطبيق والـ flow.

---

# 13. Open Redirect و Phishing

مثلاً المهاجم يرسل:

```text
https://trusted-site.com/redirect?url=https://attacker.example/login
```

المستخدم يرى أولاً:

```text
trusted-site.com
```

لأن الرابط يبدأ بالموقع الحقيقي.

لكن الموقع يقوم بتحويله إلى:

```text
attacker.example
```

وهذا قد يجعل الرابط أكثر إقناعًا في حملات التصيد.

**لكن Open Redirect وحدها لا تعني أن المستخدم سيتم اختراقه تلقائيًا.**

يظل الأمر يعتمد على:

- هل الضحية ستضغط؟
    
- هل الصفحة الخارجية مقنعة؟
    
- هل هناك بيانات حساسة؟
    
- هل توجد Attack Chain إضافية؟
    

---

# 14. Open Redirect و OAuth

دي نقطة مهمة جدًا.

OAuth flows تستخدم أحيانًا:

```text
redirect_uri
```

مثلاً:

```text
https://client.example/callback
```

لو كان التطبيق يسمح بـ Redirect غير مضبوط، قد تدخل Open Redirect في سلسلة هجوم تؤثر على OAuth flow.

لكن لازم تفرق بين:

### Open Redirect

الموقع يحولك إلى URL تتحكم فيه.

و:

### OAuth redirect_uri vulnerability

المشكلة تكون في التحقق من عنوان callback داخل OAuth flow.

Open Redirect قد تكون **جزءًا من Attack Chain** في بعض السيناريوهات، وليست بالضرورة OAuth vulnerability بحد ذاتها.

---

# 15. أهم أنواع الـ URL التي يجب فهمها

لفهم Open Redirect لازم تكون فاهم الفرق بين:

### Absolute URL

```text
https://attacker.example/path
```

### Relative URL

```text
/path
```

### Root-relative URL

```text
/dashboard
```

### Protocol-relative URL

```text
//attacker.example/path
```

وده مهم جدًا في بعض حالات الـ redirect validation.

---

# 16. Protocol-Relative URL

مثلاً:

```text
https://example.com/redirect?url=//attacker.example/
```

الـ Browser قد يتعامل معها كـ:

```text
https://attacker.example/
```

لأن:

```text
//domain.com
```

تعني استخدام نفس الـ scheme الحالي.

إذا كانت الحماية تتحقق بشكل سطحي من بداية الـ string، فقد تصبح هذه طريقة لتجاوز validation في بعض التطبيقات.

---

# 17. URL Encoding

الـ URL يمكن أن يحتوي على characters encoded.

مثلاً:

```text
%2F
```

تعني:

```text
/
```

و:

```text
%3A
```

تعني:

```text
:
```

لذلك أثناء الاختبار، يجب الانتباه إلى أن التطبيق أو الـ framework قد يقوم بعمل:

```text
Decode
```

قبل التحقق أو بعده.

وهذا قد يؤدي أحيانًا إلى اختلاف بين:

```text
What validator sees
```

و:

```text
What browser interprets
```

---

# 18. Validation Bypass

بعض التطبيقات تعمل validation بطريقة ضعيفة.

مثلاً المطور يقول:

```text
if URL starts with "https://example.com"
    allow
```

وهذا النوع من التحقق قد يكون غير كافٍ إذا لم يتم تحليل الـ URL بطريقة صحيحة.

لذلك في اختبار مصرح به، يجب التفكير في:

- Absolute URLs
    
- Relative URLs
    
- Protocol-relative URLs
    
- URL encoding
    
- Case differences
    
- Parsing differences
    
- Nested URLs
    

لكن لا تحفظ bypass واحد وتعتبره صالحًا دائمًا.

---

# 19. لماذا الـ URL Parsing مهم؟

الـ URL له أجزاء مختلفة:

```text
scheme://host:port/path?query#fragment
```

مثلاً:

```text
https://example.com:443/login?next=/home
```

الأجزاء:

```text
Scheme → https
Host   → example.com
Port   → 443
Path   → /login
Query  → next=/home
```

الـ validation الصحيح يجب أن يعتمد على **تحليل URL فعلي**، وليس مجرد string matching.

---

# 20. مثال على Validation ضعيف

تخيل:

```text
if "example.com" in url:
    allow
```

هذا منطق خطير لأن وجود النص:

```text
example.com
```

داخل URL لا يعني أن الـ host الحقيقي هو:

```text
example.com
```

الـ Developer يجب أن يستخدم URL parser ويتحقق من الـ hostname بطريقة صحيحة.

---

# 21. Open Redirect في JavaScript

مش كل Open Redirect يحصل عن طريق Server Response.

أحيانًا يكون Client-Side.

مثلاً JavaScript يستخدم:

```javascript
window.location = userInput;
```

أو:

```javascript
location.href = userInput;
```

أو:

```javascript
window.location.assign(userInput);
```

إذا كان `userInput` تحت سيطرة المستخدم بدون validation مناسب، فقد يكون عندك **Client-Side Open Redirect**.

---

# 22. Server-Side vs Client-Side Open Redirect

## Server-Side

مثلاً:

```http
HTTP/1.1 302 Found
Location: https://attacker.example/
```

الـ Server هو الذي يقوم بالـ redirect.

## Client-Side

مثلاً:

```javascript
window.location = url;
```

الـ Browser ينفذ الـ redirect بناءً على JavaScript.

---

# 23. DOM-Based Open Redirect

لو الـ URL أو parameter يتم قراءته من:

```text
location.search
```

ثم يوضع في:

```javascript
location.href
```

فقد تكون المشكلة DOM-based.

مثال مفاهيمي:

```javascript
const url = new URLSearchParams(location.search).get("url");
window.location.href = url;
```

لو المستخدم يتحكم في `url`، فالـ browser قد يذهب إلى destination يتحكم فيه.

---

# 24. Open Redirect vs XSS

ممكن يحصل خلط بينهم.

### Open Redirect

الهدف:

```text
Redirect victim → another URL
```

### XSS

الهدف:

```text
Execute JavaScript
```

وجود Redirect لا يعني XSS.

والـ Open Redirect عادةً لا تسمح وحدها بتنفيذ JavaScript داخل origin الموقع الموثوق.

---

# 25. Open Redirect vs SSRF

برضه مهم تفرق بينهم.

### Open Redirect

المستخدم المتأثر هو الذي يتم تحويله:

```text
Victim Browser
       ↓
Target URL
```

### SSRF

السيرفر نفسه هو الذي يرسل request:

```text
Attacker
   ↓
Server
   ↓
Internal Target
```

إذن:

```text
Open Redirect → Browser Redirect
SSRF          → Server-Side Request
```

---

# 26. كيف أعرف أن الـ Redirect Vulnerable؟

لا يكفي أن الموقع يعمل Redirect.

لازم يكون عندك:

1. User-controlled redirect destination.
    
2. Destination يمكن أن يكون خارج الموقع.
    
3. لا يوجد validation مناسب يمنع الوجهة غير الموثوقة.
    

مثلاً:

```text
/redirect?url=https://attacker.example/
```

ويظهر:

```http
Location: https://attacker.example/
```

هنا لديك دليل واضح.

---

# 27. Impact Assessment

عند كتابة تقرير لا تقول فقط:

> Open Redirect exists.

اشرح:

```text
Attacker-controlled URL
        ↓
Trusted domain
        ↓
Redirect
        ↓
External malicious site
```

ثم وضح السيناريو الواقعي.

مثلاً:

```text
A trusted-domain link can redirect a victim
to an attacker-controlled domain.
```

لكن severity تعتمد على:

- هل يتطلب user interaction؟
    
- هل يوجد OAuth flow؟
    
- هل يوجد sensitive data؟
    
- هل يوجد token leakage؟
    
- هل يمكن chaining مع vulnerability أخرى؟
    

---

# 28. خطوات اختبار Open Redirect — Checklist

```text
[ ] Find redirect-related parameters
[ ] Identify where redirect occurs
[ ] Send request to Burp Repeater
[ ] Replace destination with controlled external URL
[ ] Inspect Location header
[ ] Check server-side vs client-side redirect
[ ] Test relative destination
[ ] Test absolute destination
[ ] Understand URL parsing
[ ] Check validation
[ ] Assess possible attack chains
[ ] Document impact
```

---

# 29. مثال مذاكرة كامل

لدينا:

```text
https://example.com/login?next=/dashboard
```

### 1. الطبيعي

```text
next=/dashboard
```

الموقع يحولك:

```text
/login
   ↓
/dashboard
```

### 2. الاختبار

نغير:

```text
next=https://attacker.example/
```

### 3. Response

لو:

```http
HTTP/1.1 302 Found
Location: https://attacker.example/
```

### 4. الاستنتاج

التطبيق يسمح للمستخدم بالتحكم في destination.

إذن:

```text
Open Redirect
```

### 5. تقييم الخطورة

نسأل:

```text
هل يوجد OAuth؟
هل يوجد token في الـ URL؟
هل يوجد sensitive parameter؟
هل يمكن استخدام الرابط في phishing؟
هل توجد vulnerability أخرى يمكن chain معها؟
```

---

# 30. Burp Suite Workflow

احفظ الـ workflow ده:

```text
Browser
   ↓
Proxy
   ↓
HTTP History
   ↓
Find redirect request
   ↓
Send to Repeater
   ↓
Modify redirect parameter
   ↓
Send
   ↓
Inspect Location
   ↓
Confirm external redirect
   ↓
Assess impact
```

---

# 31. PortSwigger — كيف تفكر في الـ Lab؟

لو الـ Lab عن Open Redirect، لا تبدأ بالـ payload.

ابدأ بالسؤال:

> "فين التطبيق بيحدد للمستخدم يروح فين؟"

دور على:

```text
next
url
redirect
return
continue
destination
```

بعدها تتبع الـ flow.

مثلاً:

```text
Click Login
 ↓
Redirect
 ↓
Request
 ↓
Parameter
 ↓
Destination
```

ثم اختبر هل يمكنك التحكم في destination.

---

# 32. الحماية من Open Redirect

## الحل الأفضل

لا تسمح للمستخدم بإرسال URL عشوائي.

بدل:

```text
redirect_to = user_input
```

استخدم:

```text
Allowed destinations
```

مثلاً:

```text
/dashboard
/profile
/settings
```

---

# 33. استخدم Allowlist

بدل:

```text
Allow everything
```

استخدم:

```text
Allow only known destinations
```

مثلاً:

```text
/dashboard
/orders
/profile
```

ولو تحتاج External URLs، يجب تحديد domains المسموح بها بشكل واضح.

---

# 34. لا تعتمد على String Matching

❌ مثال ضعيف:

```text
if "trusted.com" in url:
    allow
```

لأن الـ URL قد يحتوي على `trusted.com` كنص دون أن يكون هو الـ hostname الحقيقي.

الأفضل:

```text
Parse URL
   ↓
Extract hostname
   ↓
Validate hostname
   ↓
Allow / Reject
```

---

# 35. Relative Redirect أفضل في حالات كثيرة

لو التطبيق لا يحتاج External Redirects، من الأفضل السماح بـ:

```text
/dashboard
/profile
```

بدل:

```text
https://any-domain.com
```

وبذلك تقل مساحة الهجوم.

---

# 36. أهم Bypass Concepts

في الاختبارات المصرح بها، يجب أن تعرف أن validation الضعيف قد يتأثر بـ:

```text
//example.com
```

أو:

```text
URL Encoding
```

أو:

```text
Different URL parsing
```

أو:

```text
Nested URL parameters
```

أو:

```text
Relative paths
```

أو اختلافات:

```text
Scheme
Host
Port
Path
```

الفكرة الأساسية:

> لا تختبر الـ string فقط؛ اختبر كيف يقوم الـ Application وBrowser بعمل parsing للـ URL.

---

# 37. أهم Payloads / Test Values

لـ Detection في Lab أو بيئة مصرح بها:

### Relative URL

```text
/dashboard
```

### External URL

```text
https://example.org/
```

### Protocol-relative

```text
//example.org/
```

### Encoded characters

```text
https%3A%2F%2Fexample.org%2F
```

هذه ليست كلها bypasses؛ هي **test cases** لمعرفة كيف يتعامل التطبيق مع أنواع مختلفة من الـ URLs.

---

# 38. Open Redirect Attack Chain

Open Redirect قد تدخل في سلسلة:

```text
Open Redirect
      ↓
Trusted URL
      ↓
Victim clicks
      ↓
External malicious site
      ↓
Phishing / Additional attack
```

وفي سيناريوهات أخرى:

```text
Open Redirect
      ↓
OAuth Flow
      ↓
Improper redirect validation
      ↓
Potential sensitive-data exposure
```

لكن يجب إثبات كل خطوة في الـ chain؛ لا تفترض أن وجود Open Redirect يعني تلقائيًا وجود OAuth token theft.

---

# 39. أهم الأخطاء

### ❌ الخطأ الأول

أي 302 = Open Redirect.

لا.

قد يكون Redirect طبيعي وآمن.

### ❌ الخطأ الثاني

أي parameter اسمه `url` = vulnerability.

لا.

لازم تختبر behavior.

### ❌ الخطأ الثالث

Open Redirect = XSS.

لا.

### ❌ الخطأ الرابع

Open Redirect = SSRF.

لا.

### ❌ الخطأ الخامس

مجرد أن redirect إلى Google يعمل يعني vulnerability.

ليس بالضرورة؛ المهم هل التطبيق يسمح بوجهة غير موثوقة بشكل غير مقصود.

---

# 40. أسئلة Interview

### ما هي Open Redirect؟

ثغرة تسمح للمهاجم بالتحكم في وجهة Redirect الخاصة بالتطبيق، مما يسمح بتحويل المستخدم من موقع موثوق إلى موقع آخر غير موثوق.

### كيف تكتشفها؟

أبحث عن redirect parameters مثل:

```text
url
redirect
next
return
destination
continue
```

ثم أختبر هل يمكن التحكم في الوجهة الخارجية وأراقب `Location` أو سلوك الـ JavaScript.

### ما هو HTTP Header المهم؟

```http
Location:
```

### ما أشهر Status Code؟

```text
302
```

لكن يمكن استخدام status codes أخرى مثل:

```text
301
303
307
308
```

### هل Open Redirect خطيرة؟

قد تكون Low/Medium في بعض الحالات، لكن يمكن أن تصبح أكثر خطورة إذا دخلت في Attack Chain مثل OAuth أو phishing أو تسريب بيانات حساسة.

### كيف تمنعها؟

- Allowlist
    
- السماح بـ relative paths عند الإمكان
    
- URL parsing صحيح
    
- التحقق من hostname
    
- عدم الاعتماد على string matching
    
- رفض destinations غير الموثوقة
    

---

# 41. الخلاصة

احفظ المعادلة:

```text
User-Controlled Destination
          +
Unsafe Redirect
          =
Open Redirect
```

والـ workflow:

```text
Find Redirect
      ↓
Find Parameter
      ↓
Control Destination
      ↓
Test External URL
      ↓
Inspect Location / JS
      ↓
Confirm
      ↓
Assess Impact
      ↓
Check Attack Chains
```

والفرق الأساسي:

```text
Open Redirect
→ Browser is redirected

SSRF
→ Server makes the request

XSS
→ JavaScript executes

SSTI
→ Server-side Template is evaluated
```

### أهم 5 حاجات تحفظهم

```text
1. Find redirect parameters.
2. Test whether destination is user-controlled.
3. Inspect Location header / client-side redirect.
4. Understand URL parsing and validation.
5. Assess impact and possible attack chains.
```