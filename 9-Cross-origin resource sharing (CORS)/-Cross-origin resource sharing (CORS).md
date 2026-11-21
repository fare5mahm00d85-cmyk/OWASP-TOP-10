
---
## 1. يعني إيه CORS؟

CORS اختصار:

**Cross-Origin Resource Sharing**

وهو Mechanism موجود في الـ Browser بيسمح للـ Server إنه يحدد مين من الـ Origins الخارجية مسموح له يقرأ الـ Response بتاعه.

بمعنى أبسط:

لو عندي:

```text
https://app.example.com
```

والـ API موجود على:

```text
https://api.example.com
```

الاتنين Different Origins، والـ Browser عنده Security Policy تمنع القراءة بينهم بشكل افتراضي.

هنا CORS بيحدد:

> مين مسموح له يقرأ الـ Response؟

---

# 2. يعني إيه Origin؟

الـ Origin بيتكون من:

```text
Scheme + Host + Port
```

مثال:

```text
https://example.com:443
```

يعني:

```text
Scheme = https
Host   = example.com
Port   = 443
```

لو أي واحدة من دول اختلفت → Origin مختلف.

### مثال:

```text
https://example.com
https://api.example.com
```

Different Origin لأن الـ Host مختلف.

---

```text
http://example.com
https://example.com
```

Different Origin لأن الـ Scheme مختلف.

---

```text
https://example.com:443
https://example.com:8080
```

Different Origin لأن الـ Port مختلف.

---

# 3. Same-Origin Policy — SOP

قبل CORS لازم نفهم SOP.

**Same-Origin Policy**

دي Security Policy في الـ Browser.

وظيفتها إنها تمنع JavaScript الموجود في موقع من إنه يقرأ بيانات من Origin مختلف بشكل عشوائي.

مثلاً:

```text
https://attacker.com
```

عنده JavaScript:

```javascript
fetch("https://victim.com/api/account")
```

الـ Browser مش بيسمح للـ JavaScript إنه يقرأ الـ Response بشكل عادي لو الـ Cross-Origin access مش مسموح.

---

# 4. CORS علاقته بـ SOP

احفظها كده:

```text
SOP
↓
المنع الافتراضي

CORS
↓
السماح باستثناءات Cross-Origin بشكل محدد
```

يعني CORS مش بيلغي SOP.

هو بيخلي الـ Server يقول للـ Browser:

> الـ Origin ده مسموح له يقرأ الـ Response.

---

# 5. مثال بسيط

عندنا:

```text
Frontend:
https://app.example.com

API:
https://api.example.com
```

الـ Frontend يعمل:

```javascript
fetch("https://api.example.com/users")
```

الـ Browser يضيف:

```http
Origin: https://app.example.com
```

والـ API ممكن يرد:

```http
Access-Control-Allow-Origin: https://app.example.com
```

معناها:

> أنا سامح للـ Origin ده إنه يقرأ الـ Response.

---

# 6. أهم CORS Header

## Access-Control-Allow-Origin

ده أهم Header في CORS.

مثال:

```http
Access-Control-Allow-Origin: https://example.com
```

معناه:

```text
https://example.com
```

مسموح له بقراءة الـ Response.

---

## Wildcard *

مثال:

```http
Access-Control-Allow-Origin: *
```

معناها:

```text
Any Origin
```

لكن مهم:

`*` لوحدها مش معناها تلقائيًا إن فيه Vulnerability خطيرة.

لازم نشوف:

- هل البيانات حساسة؟
    
- هل Authentication موجود؟
    
- هل Credentials مطلوبة؟
    
- هل Browser يسمح بالـ Credentials في السيناريو؟
    
- هل Cookies موجودة؟
    
- هل SameSite يسمح؟
    

---

# 7. Origin Header

لما يحصل Cross-Origin Request، الـ Browser ممكن يرسل:

```http
Origin: https://attacker.com
```

مثال:

```http
GET /api/account HTTP/1.1
Host: victim.com
Origin: https://attacker.com
```

الـ Server بيقرأ الـ Origin ويقرر هل يسمح له ولا لأ.

---

# 8. أهم CORS Headers

## 1. Access-Control-Allow-Origin

بيحدد الـ Origin المسموح له.

```http
Access-Control-Allow-Origin: https://app.example.com
```

---

## 2. Access-Control-Allow-Credentials

مثال:

```http
Access-Control-Allow-Credentials: true
```

بيسمح باستخدام Credentials في Cross-Origin requests في الحالات التي يسمح بها Browser والسياسات الأخرى.

الـ Credentials ممكن تشمل:

```text
Cookies
Authorization credentials
TLS client certificates
```

---

## 3. Access-Control-Allow-Methods

بيحدد الـ HTTP Methods المسموح بها.

```http
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
```

---

## 4. Access-Control-Allow-Headers

بيحدد الـ Request Headers المسموح باستخدامها.

```http
Access-Control-Allow-Headers: Content-Type, Authorization
```

---

## 5. Access-Control-Expose-Headers

بيحدد Response Headers إضافية ممكن JavaScript يقرأها.

```http
Access-Control-Expose-Headers: X-Custom-Header
```

---

## 6. Access-Control-Max-Age

بيحدد مدة Cache لنتيجة الـ Preflight.

```http
Access-Control-Max-Age: 600
```

---

# 9. Simple Request

مش كل Cross-Origin Request بيعمل Preflight.

فيه Requests ممكن تتنفذ مباشرة لو مستوفية شروط الـ Simple Request.

أمثلة Methods ممكن تدخل ضمنها:

```text
GET
POST
HEAD
```

مع شروط معينة على الـ Headers والـ Content-Type.

---

# 10. Preflight Request

لو الـ Request مش Simple Request، الـ Browser ممكن يعمل حاجة اسمها:

**Preflight**

وهي Request باستخدام:

```http
OPTIONS
```

قبل الـ Request الحقيقي.

مثال:

```http
OPTIONS /api/user HTTP/1.1
Host: victim.com
Origin: https://attacker.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: Authorization
```

السيرفر ممكن يرد:

```http
HTTP/1.1 204 No Content

Access-Control-Allow-Origin: https://attacker.com
Access-Control-Allow-Methods: PUT
Access-Control-Allow-Headers: Authorization
```

لو الـ Browser شايف إن الطلب مسموح، يقدر يكمل بالـ Request الحقيقي.

---

# 11. ليه Preflight؟

فكر فيها كأن الـ Browser بيسأل السيرفر:

> أنا Origin مختلف، وعايز أعمل PUT وأبعت Authorization Header، ينفع؟

السيرفر يقول:

```text
YES
```

→ الطلب يكمل.

أو:

```text
NO
```

→ Browser يمنع JavaScript من إكمال الـ Cross-Origin operation وفق سياسات CORS.

---

# 12. CORS و Credentials

مثال JavaScript:

```javascript
fetch("https://victim.com/api/account", {
    credentials: "include"
})
```

هنا التطبيق بيطلب من Browser استخدام Credentials في الطلب إذا كانت السياسات تسمح.

السيرفر ممكن يرد:

```http
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Credentials: true
```

---

# 13. CORS و Cookies

دي نقطة مهمة جدًا في الـ Pentesting.

لو المستخدم عامل Login في:

```text
https://victim.com
```

وعنده:

```text
session cookie
```

وموقع attacker حاول يقرأ API حساس Cross-Origin، لازم نعرف هل الـ Cookie أصلًا هتتبعت في السيناريو ده.

هنا بنبص على:

```text
SameSite
Secure
HttpOnly
Domain
Path
```

خصوصًا:

```text
SameSite
```

لأنها بتأثر على إرسال الـ Cookies في Cross-Site contexts.

---

# 14. CORS vs CSRF

## CORS

المشكلة الأساسية:

```text
هل Origin خارجي يقدر يقرأ Response؟
```

---

## CSRF

المشكلة الأساسية:

```text
هل Attacker يقدر يخلي Browser الضحية يعمل Action؟
```

مثال CSRF:

```text
Attacker
   ↓
Browser الضحية
   ↓
Transfer Money
```

بينما CORS:

```text
Attacker Origin
   ↓
Browser
   ↓
Victim API
   ↓
هل Attacker يقدر يقرأ Response؟
```

---

# 15. CORS ≠ Authentication

CORS مش Authentication.

يعني مش وظيفته يعرف:

```text
مين المستخدم؟
```

ولا:

```text
هل المستخدم Logged in؟
```

ولا:

```text
هل عنده Permission؟
```

دي مسؤولية:

```text
Authentication
Authorization
Access Control
```

---

# 16. أشهر CORS Misconfigurations

## 1. Arbitrary Origin Reflection

دي من أهم المشاكل.

Attacker يبعت:

```http
Origin: https://attacker.com
```

والسيرفر يرد:

```http
Access-Control-Allow-Origin: https://attacker.com
```

من غير Validation صحيحة.

يعني السيرفر عمليًا بيقول:

> أي Origin تبعتهولي ممكن أسمح له.

لو الـ Endpoint فيه بيانات حساسة، واشتغلت بقية شروط الـ Browser/Credentials، ممكن يؤدي لتسريب بيانات.

---

# 17. Null Origin

ممكن تشوف:

```http
Access-Control-Allow-Origin: null
```

أو السيرفر يكون بيثق في:

```http
Origin: null
```

وده محتاج اختبار وفهم للسياق، لأن `null` ممكن يظهر في بعض Browser contexts.

---

# 18. Subdomain Trust

مثلاً الشركة عندها:

```text
example.com
```

والـ API بيثق في:

```text
*.example.com
```

لو فيه Subdomain غير موثوق أو قابل للسيطرة من المهاجم، الثقة الواسعة ممكن تسبب مشكلة.

---

# 19. Weak Origin Validation

خطأ شائع إن التطبيق يعمل String Matching غلط.

مثلاً بدل ما يتحقق من الـ Origin كـ URL بشكل صحيح، يعمل:

```text
contains("example.com")
```

وده ممكن يؤدي لقبول Domains مش موثوقة حسب طريقة التحقق.

---

# 20. CORS Testing باستخدام Burp Suite

كـ Pentester:

### Step 1

افتح:

```text
Burp Suite
→ Proxy
→ HTTP History
```

دور على API مهم، مثلاً:

```http
GET /api/account
```

---

### Step 2

ابعت الـ Request إلى:

```text
Repeater
```

---

### Step 3

ضيف:

```http
Origin: https://attacker.com
```

مثال:

```http
GET /api/account HTTP/1.1
Host: victim.com
Cookie: session=...
Origin: https://attacker.com
```

---

### Step 4

شوف الـ Response.

ابحث عن:

```http
Access-Control-Allow-Origin
```

و:

```http
Access-Control-Allow-Credentials
```

---

# 21. اختبار Origin مختلف

جرب:

```http
Origin: https://attacker.com
```

وبعدين:

```http
Origin: https://evil.com
```

وبعدين Origins غير موثوقة أخرى.

وشوف هل السيرفر:

```text
يرفضها
```

ولا:

```text
يعكسها
```

مثلاً:

```http
Origin: https://attacker.com
```

Response:

```http
Access-Control-Allow-Origin: https://attacker.com
```

هنا لازم تكمل التحقيق.

---

# 22. إيه اللي يخلي CORS Misconfiguration مهمة؟

مش مجرد إنك لقيت:

```http
Access-Control-Allow-Origin
```

لازم تسأل:

```text
1. هل Origin غير موثوق؟
2. هل الـ Endpoint يحتاج Authentication؟
3. هل الـ Response فيه بيانات حساسة؟
4. هل Credentials مستخدمة؟
5. هل Browser هيبعت الـ Credentials؟
6. هل Cookies تسمح بالـ Cross-Site scenario؟
7. هل Browser هيقدر يقرأ الـ Response؟
```

لو الإجابات متوافقة، هنا ممكن يكون عندك Security Impact حقيقي.

---

# 23. مثال كامل

Request:

```http
GET /api/account HTTP/1.1
Host: victim.com
Origin: https://attacker.com
Cookie: session=ABC123
```

Response:

```http
HTTP/1.1 200 OK

Access-Control-Allow-Origin: https://attacker.com
Access-Control-Allow-Credentials: true
```

Body:

```json
{
    "username": "Fares",
    "email": "fares@example.com",
    "balance": 50000
}
```

هنا بنلاحظ:

```text
Attacker Origin
       ↓
مقبول
       ↓
Credentials
       ↓
Endpoint حساس
       ↓
Sensitive Response
```

وده سيناريو يحتاج تقييم أمني واضح.

---

# 24. CORS Attack Flow

الفكرة العامة:

```text
Victim Login
     ↓
Session Cookie
     ↓
Victim visits attacker-controlled page
     ↓
JavaScript sends Cross-Origin Request
     ↓
Browser handles Credentials according to its policies
     ↓
Victim Server
     ↓
Bad CORS Configuration
     ↓
Response allowed to attacker Origin
     ↓
JavaScript may read sensitive Response
```

---

# 25. أهم نقطة في CORS

احفظ الجملة دي:

> **CORS vulnerability مش معناها إن المهاجم قدر يبعت Request؛ المشكلة الأساسية إن Origin غير موثوق قدر يقرأ Response حساس.**

---

# 26. Secure CORS

بدل:

```http
Access-Control-Allow-Origin: *
```

لو التطبيق محتاج Origin معين:

```http
Access-Control-Allow-Origin: https://app.example.com
```

والـ Server يعمل Allowlist صحيحة:

```text
Allowed:
https://app.example.com
https://admin.example.com
```

وأي Origin غير موجود:

```text
Reject / Don't allow
```

---

# 27. CORS Checklist للـ Pentester

```text
[ ] Identify API endpoints
[ ] Check Origin behavior
[ ] Add Origin: https://attacker.com
[ ] Check Access-Control-Allow-Origin
[ ] Check Access-Control-Allow-Credentials
[ ] Test different Origins
[ ] Test null Origin where relevant
[ ] Check subdomain trust
[ ] Check Origin validation
[ ] Identify sensitive endpoints
[ ] Check Authentication
[ ] Check Cookies
[ ] Check SameSite
[ ] Check if Response can actually be read
[ ] Confirm real security impact
```

---

# 28. أهم Headers للحفظ

```text
Origin
↓
بيحدد الـ Origin اللي جاي منه الطلب

Access-Control-Allow-Origin
↓
مين مسموح له يقرأ الـ Response

Access-Control-Allow-Credentials
↓
هل Cross-Origin credentials مسموحة

Access-Control-Allow-Methods
↓
الـ Methods المسموح بها

Access-Control-Allow-Headers
↓
الـ Request Headers المسموح بها

Access-Control-Expose-Headers
↓
Headers إضافية يقدر JavaScript يقرأها

Access-Control-Max-Age
↓
مدة Cache لنتيجة الـ Preflight
```

---

# 29. الخريطة اللي تحفظها

```text
CORS
│
├── Origin
│   ├── Scheme
│   ├── Host
│   └── Port
│
├── SOP
│   └── Same-Origin Policy
│
├── CORS Headers
│   ├── Allow-Origin
│   ├── Allow-Credentials
│   ├── Allow-Methods
│   ├── Allow-Headers
│   ├── Expose-Headers
│   └── Max-Age
│
├── Requests
│   ├── Simple Request
│   └── Preflight
│       └── OPTIONS
│
├── Credentials
│   └── Cookies
│       └── SameSite
│
└── Misconfigurations
    ├── Origin Reflection
    ├── Null Origin
    ├── Weak Validation
    ├── Subdomain Trust
    └── Bad Credentials Configuration
```

---

# 30. الخلاصة

```text
CORS = Cross-Origin Resource Sharing

SOP = Same-Origin Policy

Origin = Scheme + Host + Port

CORS بيسمح للسيرفر يحدد الـ Origins اللي مسموح لها تقرأ Cross-Origin Responses.

أهم Header:
Access-Control-Allow-Origin

Preflight:
OPTIONS Request قبل بعض الـ Cross-Origin Requests.

أهم حاجات في الاختبار:
Origin
Allow-Origin
Allow-Credentials
Cookies
SameSite
Sensitive Response

أشهر المشكلة:
Server يثق في Origin غير موثوق أو يعكسه بدون Validation صحيحة.

CORS ≠ CSRF
CORS ≠ Authentication
CORS ≠ Authorization

وأهم فكرة:
مش المهم إن Request اتبعت؛
المهم هل Origin غير موثوق قدر يقرأ Response حساس من التطبيق.
```