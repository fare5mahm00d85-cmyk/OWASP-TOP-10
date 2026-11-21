
---


> **الهدف من الشيت:** فهم OAuth من الصفر، وفهم الـ Flow والـ Parameters والـ Tokens، وبعدها تعرف إزاي تحلل OAuth كـ Web Pentester وتفهم Labs بتاعة PortSwigger.

---

# 1. يعني إيه Authentication؟

**Authentication = التحقق من هوية المستخدم.**

يعني الموقع عايز يعرف:

> "إنت مين؟"

مثال عادي:

```text
Username: fares
Password: ********
```

الموقع يتأكد من البيانات، ولو صحيحة يعمل Login.

---

# 2. يعني إيه Authorization؟

**Authorization = تحديد إيه اللي المستخدم مسموح له يعمله.**

مثال:

```text
User
→ يقدر يشوف حسابه

Admin
→ يقدر يشوف المستخدمين
→ يقدر يحذف مستخدم
→ يقدر يعدل إعدادات النظام
```

احفظ الفرق:

```text
Authentication = Who are you?
Authorization   = What can you do?
```

---

# 3. OAuth يعني إيه؟

**OAuth 2.0** هو Authorization Framework يسمح لتطبيق إنه يحصل على صلاحية للوصول إلى موارد موجودة عند خدمة أخرى، بدون ما المستخدم يدي التطبيق كلمة السر الخاصة بالخدمة الأخرى.

مثال:

عندك موقع:

```text
example.com
```

وفيه:

```text
Login with Google
```

بدل ما تكتب باسورد Google داخل `example.com`، الموقع يوديك لـ Google.

هناك تعمل Login.

Google تتأكد من هويتك.

بعدها ترجع للموقع بمعلومات/صلاحية تسمح له بإكمال عملية تسجيل الدخول.

الشكل العام:

```text
User
  ↓
Website
  ↓
OAuth Provider
  ↓
Login
  ↓
Authorization Code / Token
  ↓
Website
  ↓
User Session
```

---

# 4. نقطة مهمة جدًا: OAuth أصله Authorization

OAuth في الأساس معمول للـ **Authorization** وليس Authentication.

يعني:

```text
OAuth
=
إعطاء تطبيق صلاحية للوصول إلى Resource
```

لكن تم استخدام OAuth في أنظمة تسجيل الدخول، وبالتالي ظهرت حاجة اسمها:

```text
OAuth Authentication
```

وده السبب إن PortSwigger عنده قسم:

```text
OAuth 2.0 authentication vulnerabilities
```

---

# 5. الأطراف الموجودة في OAuth

لازم تعرف الأربع أطراف الأساسية.

## 5.1 Resource Owner

ده صاحب البيانات.

غالبًا:

```text
User
```

مثلاً:

```text
Fares
```

---

## 5.2 Client

ده التطبيق أو الموقع اللي عايز يستخدم OAuth.

مثلاً:

```text
client.com
```

---

## 5.3 Authorization Server

ده السيرفر المسؤول عن:

- تسجيل الدخول
    
- طلب موافقة المستخدم
    
- إصدار Authorization Code
    
- إصدار Access Token
    

مثلاً:

```text
oauth.example.com
```

---

## 5.4 Resource Server

ده السيرفر اللي عليه الـ Resources أو بيانات المستخدم.

مثلاً:

```text
/api/userinfo
```

---

# 6. الصورة الكاملة

```text
                User
                  |
                  v
          Client Application
             client.com
                  |
                  v
        Authorization Server
          oauth.example.com
                  |
             Login / Consent
                  |
                  v
        Authorization Code
                  |
                  v
          Client Application
                  |
             Access Token
                  |
                  v
          Resource Server
             /userinfo
                  |
                  v
            User Data
```

---

# 7. OAuth Flow يعني إيه؟

الـ Flow هو طريقة تنفيذ عملية OAuth.

من أشهر الـ Flows:

```text
Authorization Code Grant
Implicit Grant
```

وفي التطبيقات الحديثة، **Authorization Code Flow** هو الأهم اللي لازم تفهمه.

---

# 8. Authorization Code Flow

ده أهم Flow في المذاكرة.

تخيل إن عندك:

```text
client.com
```

والمستخدم ضغط:

```text
Login with OAuth
```

الموقع يرسله إلى Authorization Server.

مثال:

```http
GET /authorization?
client_id=123
&redirect_uri=https://client.com/callback
&response_type=code
&scope=openid%20profile
&state=XYZ
```

دلوقتي لازم نفهم كل Parameter.

---

# 9. client_id

مثال:

```text
client_id=123
```

ده بيحدد التطبيق اللي بيطلب OAuth.

يعني Authorization Server يعرف:

> مين الـ Client اللي بيطلب الصلاحية؟

مثلاً:

```text
client_id=123
```

يمثل:

```text
client.com
```

---

# 10. redirect_uri

دي من أهم الحاجات في OAuth Security.

مثال:

```text
redirect_uri=https://client.com/callback
```

معناها:

> بعد انتهاء عملية OAuth، رجّع المستخدم إلى العنوان ده.

الشكل:

```text
Authorization Server
        |
        v
https://client.com/callback
```

وده Attack Surface مهم جدًا.

لو السيرفر لا يتحقق من `redirect_uri` بشكل صحيح، ممكن يؤدي إلى تسريب:

```text
Authorization Code
```

أو:

```text
Access Token
```

---

# 11. response_type

مثال:

```text
response_type=code
```

معناه:

> أنا أريد Authorization Code.

في بعض الـ Flows ممكن يكون:

```text
response_type=token
```

وده مرتبط بالـ Implicit Flow.

---

# 12. scope

الـ Scope بيحدد الصلاحيات المطلوبة.

مثال:

```text
scope=openid profile email
```

ممكن يعني إن التطبيق يطلب:

```text
openid
profile
email
```

فكرة الـ Scope:

```text
Application
    |
    | "أنا محتاج الصلاحية دي"
    v
Authorization Server
```

---

# 13. state

مثال:

```text
state=ABC123
```

الـ `state` قيمة عشوائية مرتبطة بالـ Session.

الهدف الأساسي منها حماية OAuth Flow من هجمات مثل:

```text
CSRF
Login CSRF
```

الشكل:

```text
Client
  |
  | state=XYZ
  v
OAuth Server
  |
  | state=XYZ
  v
Client
```

المفروض التطبيق يتأكد إن:

```text
state returned
==
state associated with user's session
```

---

# 14. المستخدم يعمل Login

بعد ما المستخدم يروح للـ Authorization Server:

```text
OAuth Provider
      |
      v
Login
      |
      v
User Authentication
      |
      v
Consent
```

بعد نجاح العملية، ممكن يرجع Authorization Code.

---

# 15. Authorization Code

مثال:

```text
https://client.com/callback?code=ABC123&state=XYZ
```

هنا:

```text
code=ABC123
```

هو:

```text
Authorization Code
```

مهم جدًا:

> Authorization Code مش هو Access Token.

الـ Code بيستخدم في خطوة تالية للحصول على Access Token.

---

# 16. Token Endpoint

الـ Client يستخدم الـ Authorization Code في Token Endpoint.

مثال:

```http
POST /token

grant_type=authorization_code
code=ABC123
redirect_uri=https://client.com/callback
client_id=123
```

Authorization Server ممكن يرجع:

```json
{
    "access_token": "ACCESS_TOKEN_HERE"
}
```

---

# 17. Access Token

الـ Access Token هو credential يستخدم للوصول إلى الـ Resources المسموح بها.

مثلاً:

```http
GET /userinfo
Authorization: Bearer ACCESS_TOKEN
```

السيرفر ممكن يرجع:

```json
{
    "id": "123",
    "email": "user@example.com",
    "name": "Fares"
}
```

---

# 18. الفرق بين Authorization Code و Access Token

دي نقطة لازم تحفظها.

```text
Authorization Code
        |
        v
   Token Endpoint
        |
        v
Access Token
        |
        v
Resource Server
```

يعني:

```text
Code
=
مرحلة انتقالية

Access Token
=
Credential للوصول للـ Resource
```

---

# 19. إزاي OAuth يتحول إلى Login؟

هنا نقطة مهمة جدًا.

بعد ما التطبيق يحصل على Access Token:

```text
Access Token
      |
      v
/userinfo
      |
      v
User Information
      |
      v
Client يعرف هوية المستخدم
      |
      v
Application Session
      |
      v
Logged In
```

مثلاً:

```text
OAuth Provider
        |
        v
email = fares@example.com
        |
        v
Client
        |
        v
يعرف إن المستخدم هو Fares
        |
        v
يعمل Session
```

---

# 20. إمتى أبدأ أشك في OAuth كمختبر؟

لما أشوف:

```text
Login with Google
Login with Facebook
Login with GitHub
Login with Microsoft
```

أو Traffic فيه:

```text
/authorize
/oauth
/login/oauth
/callback
/token
/userinfo
```

ساعتها أبدأ أراقب الـ Flow.

---

# 21. OAuth Recon باستخدام Burp Suite

أول حاجة:

```text
Burp Suite
→ Proxy
→ HTTP history
```

اعمل Login باستخدام OAuth.

دور على Request شبيه:

```http
GET /authorization?
client_id=abc
&redirect_uri=https://client.com/callback
&response_type=code
&scope=openid
&state=XYZ
```

سجل القيم دي:

```text
client_id
redirect_uri
response_type
scope
state
```

وبعدين تابع باقي الـ Flow.

---

# 22. أهم Endpoints

ممكن تلاقي:

```text
/authorization
/token
/userinfo
/callback
```

أحيانًا تقدر تعرف الـ endpoints من:

```text
/.well-known/oauth-authorization-server
```

أو في OpenID Connect:

```text
/.well-known/openid-configuration
```

---

# 23. OAuth Attack Surface

كـ Pentester، متبصش للـ OAuth كـ Login Button.

بص للـ Flow كله:

```text
Authorization Request
        ↓
Login
        ↓
Consent
        ↓
Redirect
        ↓
Authorization Code
        ↓
Token
        ↓
Userinfo
        ↓
Application Session
```

كل مرحلة ممكن يكون فيها خطأ.

---

# 24. أهم OAuth Vulnerabilities

## 24.1 Flawed redirect_uri validation

دي من أهم الحاجات.

التطبيق المفروض يسمح:

```text
https://client.com/callback
```

لكن لو الـ Validation ضعيف، ممكن يقبل Redirect غير مقصود.

الفكرة:

```text
OAuth Server
     |
     | redirect_uri
     v
Weak Validation
     |
     v
Attacker-controlled destination
```

الهدف من الاختبار:

> هل الـ Authorization Code أو Token ممكن يروح لمكان غير موثوق؟

---

# 25. إيه اللي أختبره في redirect_uri؟

بشكل منهجي، شوف هل التطبيق يقبل تغييرات غير مقصودة مثل:

```text
Path changes
Subdomain changes
URL parsing edge cases
Encoding
```

المهم مش إنك تحفظ Payload معين.

المهم تفهم:

> هل السيرفر بيعمل Exact Validation للـ Redirect URI المسجل للـ Client؟

---

# 26. Flawed CSRF Protection

دي مرتبطة جدًا بـ:

```text
state
```

المفروض:

```text
Client
  |
  | state = random value
  v
OAuth
  |
  | same state
  v
Client
```

والـ Client يتحقق منه.

لو الـ Application لا يتحقق من `state` بشكل صحيح، ممكن يكون فيه مشكلة في ربط OAuth response بالـ Session الصحيحة.

---

# 27. Login CSRF

الفكرة ببساطة:

المهاجم يحاول يخلي الضحية تكمل OAuth Flow باستخدام **حساب OAuth الخاص بالمهاجم**.

لو التطبيق مش رابط الـ OAuth response بالـ Session الصحيحة، ممكن يحصل:

```text
Victim Browser
      |
      v
Attacker's OAuth Account
      |
      v
Victim Session
```

وده ممكن يؤدي إلى مشاكل في ربط الحساب أو بيانات المستخدم حسب التطبيق.

---

# 28. Leaking Authorization Codes

الـ Authorization Code حساس.

مثال:

```text
https://client.com/callback?code=ABC123
```

لو الـ Code اتسرب إلى:

```text
Attacker
```

ممكن يكون له تأثير حسب الـ Flow والحماية الموجودة.

لذلك أثناء الاختبار، دور على أماكن تسريب الـ Code مثل:

```text
Redirects
Referer
Logs
Third-party content
Browser history
URLs
```

---

# 29. Leaking Access Tokens

الـ Access Token أكثر حساسية لأنه قد يسمح بالوصول إلى Resources.

لو ظهر في مكان غير آمن:

```text
URL
Logs
Third-party resources
Client-side code
```

ممكن يؤدي إلى سرقة الـ Token.

---

# 30. Implicit Flow

في الـ Implicit Flow، الـ Access Token ممكن يتم إرجاعه مباشرة إلى الـ Browser بدل الحصول أولًا على Authorization Code.

مثال مبسط:

```text
response_type=token
```

وده سبب إن الـ Token يكون معرض أكثر للظهور في Browser-side flow.

PortSwigger عنده Labs مخصصة لأخطاء تنفيذ الـ Implicit Flow.

---

# 31. Scope

الـ Scope يحدد الصلاحيات.

مثلاً:

```text
scope=read
```

المستخدم وافق على:

```text
read
```

لكن لو التطبيق يسمح بتغيير الـ Scope إلى صلاحيات أكبر بدون تحقق مناسب:

```text
scope=read write admin
```

هنا ممكن تظهر:

```text
Flawed Scope Validation
```

الفكرة:

```text
Requested Scope
       ↓
Authorization
       ↓
Granted Scope
       ↓
Application
```

لازم التطبيق مايفترضش إن الـ Scope المطلوب من العميل هو نفسه الـ Scope المسموح به.

---

# 32. Unverified User Registration

دي مشكلة ممكن تظهر لما Client يثق في بيانات هوية من OAuth Provider بدون التأكد من أنها موثقة.

مثلاً:

```text
OAuth data
     |
     v
email = victim@example.com
     |
     v
Application trusts it blindly
```

لو الـ Email أو Identity claim غير موثقة بالشكل المطلوب، ممكن يحصل Account Linking أو Account Takeover في تطبيقات معينة.

الفكرة الأساسية:

> **لا تثق في Identity Data لمجرد أنها جاءت من OAuth بدون التحقق من مصدرها وصحتها.**

---

# 33. OpenID Connect

بعد ما تفهم OAuth، لازم تفهم:

```text
OpenID Connect
```

اختصارها:

```text
OIDC
```

OIDC مبنية فوق OAuth 2.0 وبتضيف Identity Layer.

ببساطة:

```text
OAuth
=
Authorization

OpenID Connect
=
OAuth + Authentication / Identity
```

---

# 34. ID Token

في OIDC ممكن تحصل على:

```text
ID Token
```

وده غالبًا يكون:

```text
JWT
```

مثال شكلي:

```text
xxxxx.yyyyy.zzzzz
```

ويتكون JWT من:

```text
Header
Payload
Signature
```

---

# 35. Access Token vs ID Token

مهم جدًا.

## Access Token

يستخدم للوصول إلى:

```text
Resource Server
```

مثلاً:

```http
Authorization: Bearer TOKEN
```

---

## ID Token

يستخدم لإعطاء معلومات عن:

```text
Authentication / Identity
```

يعني:

```text
Access Token
→ Access Resource

ID Token
→ Identity Information
```

متخلطش بينهم.

---

# 36. أهم Claims في ID Token

ممكن تلاقي:

```text
sub
iss
aud
exp
iat
```

### `sub`

معرف المستخدم.

### `iss`

الجهة التي أصدرت الـ Token.

### `aud`

الجمهور/الـ Client الذي صدر له الـ Token.

### `exp`

وقت انتهاء الصلاحية.

### `iat`

وقت الإصدار.

---

# 37. OAuth Pentesting Methodology

لما تقابل OAuth، امشي بالترتيب ده.

## Step 1 — Identify

حدد:

```text
OAuth Provider
Client
Authorization Server
Resource Server
```

---

## Step 2 — Capture

استخدم:

```text
Burp Suite
```

وسجل:

```text
Authorization Request
Callback
Token Request
API Requests
```

---

## Step 3 — Analyze Parameters

ركز على:

```text
client_id
redirect_uri
response_type
scope
state
```

وفي OIDC:

```text
nonce
response_mode
```

---

## Step 4 — Trace the Code

شوف:

```text
Authorization Code
```

بيطلع فين؟

بيروح فين؟

بيتستخدم إزاي؟

هل مربوط بالـ Client؟

هل له صلاحية محدودة؟

---

## Step 5 — Trace the Token

شوف:

```text
Access Token
```

بيتخزن فين؟

بيتبعث فين؟

هل يظهر في URL؟

هل ممكن يتسرب؟

---

## Step 6 — Check Redirect

اسأل:

```text
هل redirect_uri بيتعمله Exact Validation؟
```

---

## Step 7 — Check CSRF

اسأل:

```text
هل state موجود؟
```

ولو موجود:

```text
هل مربوط بالـ Session؟
```

---

## Step 8 — Check Scope

اسأل:

```text
هل أقدر أطلب Scope أكبر؟
```

وهل السيرفر فعلًا يمنع ده؟

---

## Step 9 — Check Identity

لو OAuth مستخدم في Login:

```text
إزاي التطبيق بيحدد المستخدم؟
```

مثلاً:

```text
email
sub
user_id
```

وهل البيانات دي موثوقة ومتحقق منها؟

---

# 38. Checklist تحفظها

```text
OAuth Pentesting Checklist

[ ] Identify OAuth Provider
[ ] Identify Client
[ ] Identify Authorization Server
[ ] Identify Resource Server

[ ] Capture /authorization
[ ] Capture /callback
[ ] Capture /token
[ ] Capture /userinfo

[ ] Check client_id
[ ] Check redirect_uri
[ ] Check response_type
[ ] Check scope
[ ] Check state

[ ] Check redirect_uri validation
[ ] Check state validation
[ ] Check authorization code handling
[ ] Check token handling
[ ] Check scope validation
[ ] Check identity validation
[ ] Check token/code leakage

[ ] Identify OAuth Flow
[ ] Check Authorization Code Flow
[ ] Check Implicit Flow

[ ] Identify OpenID Connect
[ ] Check ID Token
[ ] Check JWT claims
[ ] Check issuer
[ ] Check audience
[ ] Check expiration
```

---

# 39. إزاي تفكر في OAuth كـ Pentester؟

لما تشوف:

```text
GET /authorization
```

متقولش:

> "دي Request OAuth وخلاص."

قول:

```text
مين الـ Client؟
```

```text
فين الـ redirect؟
```

```text
فين الـ state؟
```

```text
إيه الـ scope؟
```

```text
الـ code هيروح فين؟
```

```text
الـ token هيروح فين؟
```

```text
إزاي التطبيق عرف إن ده المستخدم؟
```

دي طريقة التفكير الصح.

---

# 40. أهم Labs في PortSwigger

بعد الأساسيات، ركز على Labs المتعلقة بـ:

```text
OAuth 2.0 authentication vulnerabilities
```

ومن الأفكار التي ستقابلها:

```text
Authentication bypass via OAuth implicit flow

Flawed CSRF protection

Leaking authorization codes and access tokens

Flawed redirect_uri validation

Flawed scope validation

Unverified user registration
```

كل Lab منهم بيركز على جزء مختلف من الـ OAuth Flow.

---

# 41. الخلاصة الكبيرة

احفظ الـ Flow ده:

```text
User
 ↓
Client
 ↓
Authorization Server
 ↓
Login
 ↓
Consent
 ↓
Authorization Code
 ↓
Client
 ↓
Token Endpoint
 ↓
Access Token
 ↓
Resource Server
 ↓
User Information
 ↓
Application Session
```

وأهم Parameters:

```text
client_id
redirect_uri
response_type
scope
state
```

وأهم Tokens:

```text
Authorization Code
Access Token
ID Token
```

وأهم المشاكل:

```text
redirect_uri validation
state / CSRF
code leakage
token leakage
scope validation
identity validation
implicit flow problems
OIDC / ID Token validation
```

---

# 🎯 المستوى اللي عايز توصله

مش المطلوب إنك تحفظ:

```text
Payload 1
Payload 2
Payload 3
```

المطلوب لما تشوف Request زي:

```http
GET /authorization?
client_id=123
&redirect_uri=https://client.com/callback
&response_type=code
&scope=openid
&state=XYZ
```

تعرف فورًا:

```text
client_id
→ مين التطبيق؟

redirect_uri
→ الكود هيرجع فين؟

response_type
→ Code ولا Token؟

scope
→ عايز صلاحيات إيه؟

state
→ مربوط بالـ Session ولا لأ؟
```

وبعدها تتابع:

```text
code
 ↓
token
 ↓
userinfo
 ↓
session
```

**لو فهمت السلسلة دي كويس، هتكون فاهم أساس OAuth فعلًا، والـ PortSwigger Labs هتتحول من حفظ خطوات إلى تحليل للـ Flow واكتشاف مكان الـ Misconfiguration.**