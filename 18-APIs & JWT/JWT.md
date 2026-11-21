
---
# PART 6 – JWT FROM ZERO

## 22. يعني إيه JWT؟

JWT = JSON Web Token.

هو Standard لتمثيل Claims بين طرفين، وغالبًا بيُستخدم لنقل معلومات عن المستخدم أو صلاحياته.

خلينا نعمل مثال كامل.

عندك موقع اسمه Readify.

المستخدم عمل Login باستخدام:

```
{
  "email": "fares@example.com",
  "password": "Password123"
}
```

السيرفر تحقق من البيانات.

بعد نجاح تسجيل الدخول، أصدر JWT.

التطبيق استلم الـ Token وبدأ يستخدمه في Requests اللاحقة.

### شكل العملية

```
User
  |
  v
Login Request
  |
  v
Backend Authentication
  |
  v
Verify Password
  |
  v
Generate JWT
  |
  v
Return JWT
  |
  v
Client Stores Token
  |
  v
Send Token With API Requests
  |
  v
Backend Validates Token
  |
  v
Authorize Request
```

### مثال

```
GET /api/profile HTTP/1.1
Host: readify.example
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

كلمة `Bearer` معناها إن العميل بيقدم Token كوسيلة للوصول.

**مهم:** امتلاك Token صالح مش معناه إن المستخدم مسموح له بكل العمليات.

---

## 23. JWT Structure

الـ JWT الشائع بصيغة JWS بيتكون من 3 أجزاء:

```
HEADER.PAYLOAD.SIGNATURE
```

مثال شكلي:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJzdWIiOiIxMDAiLCJyb2xlIjoidXNlciJ9
.
SIGNATURE_VALUE
```

الأجزاء:

1. Header.
    
2. Payload.
    
3. Signature.
    

---

## 24. Header بالتفصيل

الـ Header بيحتوي على معلومات عن الـ Token.

مثال:

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### alg

اختصار Algorithm.

بيحدد خوارزمية التوقيع المستخدمة.

مثال:

```
{
  "alg": "HS256"
}
```

### typ

اختصار Type.

بيحدد نوع الـ Token، وغالبًا بيكون JWT.

### مثال آخر

```
{
  "alg": "RS256",
  "typ": "JWT"
}
```

هنا الـ Token بيستخدم RS256.

**Pentesting:**

افحص قيمة `alg`، وتأكد إن السيرفر بيستخدم خوارزمية محددة وآمنة، ومبيرجعش يثق في أي خوارزمية يختارها العميل.

---

## 25. Payload بالتفصيل

الـ Payload بيحتوي على Claims.

مثال:

```
{
  "sub": "100",
  "username": "fares",
  "role": "user",
  "iat": 1794500000,
  "exp": 1794503600
}
```

نفهم كل قيمة:

- `sub`: معرّف المستخدم.
    
- `username`: اسم المستخدم.
    
- `role`: دوره في النظام.
    
- `iat`: وقت إصدار الـ Token.
    
- `exp`: وقت انتهاء الصلاحية.
    

### هل Payload مشفر؟

لا، مش بالضرورة.

في JWT من نوع JWS، محتوى الـ Payload بيكون عادة Base64URL Encoded، وده مش Encryption.

يعني لو معاك Token تقدر تفك ترميز الجزء الأوسط وتقرأ محتواه.

### مثال

قبل الترميز:

```
{
  "sub": "100",
  "role": "user"
}
```

بعد Base64URL Encoding ممكن يظهر كنص مشابه لـ:

```
eyJzdWIiOiIxMDAiLCJyb2xlIjoidXNlciJ9
```

ده Encoding مش Encryption.

**ممنوع تحط Password أو Secret Key أو بيانات مالية حساسة في Payload غير مشفّر.**

---

## 26. Signature بالتفصيل

الـ Signature هي الجزء اللي بيُستخدم للتحقق من سلامة وأصالة الـ Token.

خلينا نفترض إن Header وPayload هما:

```
Header:
{
  "alg": "HS256",
  "typ": "JWT"
}
```

```
Payload:
{
  "sub": "100",
  "role": "user"
}
```

بعد Base64URL Encoding بيتكوّن نص بالشكل ده:

```
EncodedHeader.EncodedPayload
```

بعد كده يتم إنشاء Signature باستخدام الخوارزمية والـ Secret Key.

بشكل مبسط:

```
HMACSHA256(
  EncodedHeader + "." + EncodedPayload,
  SecretKey
)
```

### ليه Signature مهمة؟

تخيل إن المستخدم غيّر:

```
{
  "role": "user"
}
```

إلى:

```
{
  "role": "admin"
}
```

الـ Signature القديمة مش هتكون صحيحة بالنسبة للبيانات الجديدة.

لذلك لو السيرفر بيتحقق من التوقيع بشكل صحيح، المفروض يرفض الـ Token المعدّل.

**مهم:** Signature لا تعني إن البيانات سرية. هي بتحمي سلامتها وأصالتها، مش بتخفي محتواها.

---

# PART 7 – JWT CLAIMS

## 27. يعني إيه Claim؟

Claim هي معلومة موجودة داخل JWT عن المستخدم أو الجهة المصدرة أو صلاحية الـ Token.

مثال:

```
{
  "sub": "100",
  "role": "user",
  "exp": 1794503600
}
```

هنا عندنا 3 Claims:

- `sub`
    
- `role`
    
- `exp`
    

### Registered Claims

دي Claims معروفة في JWT Standard.

|Claim|معناها|مثال|
|---|---|---|
|iss|الجهة المصدرة|auth.example.com|
|sub|المستخدم المقصود|100|
|aud|الجهة المستقبلة|api.example.com|
|exp|وقت انتهاء الصلاحية|Unix Timestamp|
|nbf|لا يقبل قبل الوقت ده|Unix Timestamp|
|iat|وقت الإصدار|Unix Timestamp|
|jti|معرّف فريد للـ Token|token-123|

### مثال كامل

```
{
  "iss": "https://auth.readify.com",
  "sub": "100",
  "aud": "readify-api",
  "exp": 1794503600,
  "nbf": 1794499000,
  "iat": 1794499000,
  "jti": "token-123456"
}
```

### iss

مين أصدر الـ Token؟

السيرفر لازم يتحقق إن الـ Issuer هو الجهة الموثوقة المتوقعة.

### sub

الـ Subject، وغالبًا بيكون User ID.

### aud

الـ Audience، يعني الجهة اللي الـ Token موجه ليها.

مثال: Token مخصص لـ Readify API، مينفعش يتقبل في Service تانية لو مش موجه ليها.

### exp

وقت انتهاء الصلاحية.

لو الوقت الحالي أكبر من `exp`، المفروض يتم رفض الـ Token.

### nbf

الـ Token لا يُقبل قبل الوقت المحدد.

### iat

وقت إصدار الـ Token.

### jti

معرّف فريد للـ Token.

ممكن يُستخدم في أنظمة تتبع أو إبطال Tokens ومنع إعادة الاستخدام.

---

# PART 8 – JWS VS JWE

## 28. JWS

JWS = JSON Web Signature.

بيوفر:

- Integrity.
    
- Authenticity.
    

يعني بيسمح للسيرفر يتحقق إن البيانات لم يتم تعديلها، وإن التوقيع صادر باستخدام المفتاح الصحيح.

مثال:

```
HEADER.PAYLOAD.SIGNATURE
```

في الشكل الشائع للـ JWS Compact Serialization.

الـ Payload ممكن يكون قابلًا للقراءة.

## 29. JWE

JWE = JSON Web Encryption.

بيوفر Encryption لمحتوى الـ Token.

الشكل الشائع لـ JWE Compact Serialization بيتكون من 5 أجزاء:

```
ProtectedHeader.
EncryptedKey.
IV.
Ciphertext.
AuthenticationTag
```

الهدف هو حماية سرية البيانات ومحتواها من القراءة بدون المفاتيح المناسبة.

### الفرق

|JWS|JWE|
|---|---|
|توقيع|تشفير|
|يتحقق من سلامة وأصالة البيانات|يحمي سرية البيانات|
|Payload قابل للقراءة عادة|Payload مشفّر|
|3 أجزاء في الشكل الشائع|5 أجزاء في الشكل الشائع|

**احفظ:**

- Signing مش Encryption.
    
- Encoding مش Encryption.
    
- Hashing مش Encryption.
    

---

# PART 9 – JWT ALGORITHMS

## 30. HS256

HS256 = HMAC using SHA-256.

بيعتمد على Symmetric Cryptography.

يعني نفس الـ Secret Key بيستخدم في:

- إنشاء Signature.
    
- التحقق من Signature.
    

### مثال توضيحي

```
Application A:
Uses Secret Key to sign JWT.

Application B:
Uses Same Secret Key to verify JWT.
```

### الخطر

لو الـ Secret Key اتسرّب، أي شخص يمتلكه ممكن ينشئ Tokens بتوقيعات صحيحة.

### الحماية

- Secret قوي وعشوائي.
    
- تخزينه في مكان آمن.
    
- عدم وضعه داخل Source Code.
    
- Rotation للمفتاح عند الحاجة.
    

## 31. RS256

RS256 بيعتمد على RSA مع SHA-256.

بيستخدم مفتاحين:

### Private Key

يستخدم لإنشاء التوقيع.

لازم يفضل سري.

### Public Key

يستخدم للتحقق من التوقيع.

ممكن يتوزع على الخدمات اللي محتاجة تتحقق من Tokens.

### مثال

```
Authentication Server:
Private Key -> Sign JWT

API Server:
Public Key -> Verify JWT
```

### الفرق

|HS256|RS256|
|---|---|
|Symmetric|Asymmetric|
|Secret واحد|Public + Private|
|التوقيع والتحقق بنفس السر|التوقيع بمفتاح والتحقق بمفتاح آخر|
|مشاركة السر بين الخدمات|ممكن توزيع Public Key|

**مهم:** RS256 مش آمن تلقائيًا لمجرد إنه Asymmetric. لازم إدارة المفاتيح والتحقق من التوقيع والخوارزمية تكون صحيحة.

---

# PART 10 – ACCESS TOKEN & REFRESH TOKEN

## 32. Access Token

Token بيستخدمه العميل للوصول إلى Resources محمية.

مثال:

```
GET /api/profile HTTP/1.1
Authorization: Bearer ACCESS_TOKEN
```

غالبًا بيكون قصير الصلاحية.

مثال افتراضي: 15 دقيقة.

## 33. Refresh Token

بيستخدم للحصول على Access Token جديد بعد انتهاء صلاحية القديم.

مثال:

```
POST /api/auth/refresh HTTP/1.1
Content-Type: application/json
```

```
{
  "refreshToken": "REFRESH_TOKEN"
}
```

السيرفر يتحقق من Refresh Token، ولو صالح ممكن يرجع Access Token جديد.

### دورة حياة Tokens

```
User Login
    |
    v
Access Token + Refresh Token
    |
    v
Use Access Token
    |
    v
Access Token Expires
    |
    v
Send Refresh Token
    |
    v
Backend Validates Refresh Token
    |
    v
New Access Token
```

### الفرق

|Access Token|Refresh Token|
|---|---|
|الوصول للـ API|تجديد Access Token|
|قصير الصلاحية غالبًا|أطول صلاحية غالبًا|
|يستخدم في Requests|يستخدم في Refresh Endpoint|
|تسريبه خطر|تسريبه خطر وقد يسمح باستمرار الوصول|

### Refresh Token Rotation

فكرتها إن السيرفر يصدر Refresh Token جديد عند استخدام القديم، ويبطل القديم.

ده بيساعد في اكتشاف بعض حالات سرقة وإعادة استخدام Refresh Tokens.

---

# PART 11 – JWT STORAGE

## 34. LocalStorage

مكان تخزين داخل المتصفح يمكن لـ JavaScript الوصول إليه.

مثال:

```
localStorage.setItem("token", accessToken);
```

استرجاعه:

```
const token = localStorage.getItem("token");
```

### المشكلة الأمنية

لو التطبيق عنده XSS، ممكن JavaScript خبيث يقرأ البيانات الموجودة في LocalStorage.

لذلك تخزين Access Token هناك له مخاطر مهمة.

## 35. SessionStorage

مثال:

```
sessionStorage.setItem("token", accessToken);
```

بيكون مرتبطًا بجلسة الـ Tab.

لكن JavaScript يقدر يقرأه، وبالتالي هو أيضًا معرض لتأثير XSS.

## 36. Cookies

ممكن تخزين بيانات Session أو Tokens في Cookies.

فيه خصائص مهمة:

### HttpOnly

بتمنع JavaScript من قراءة الـ Cookie.

### Secure

بتخلي المتصفح يرسل الـ Cookie عبر HTTPS فقط.

### SameSite

بتساعد في التحكم في إرسال Cookies مع Cross-Site Requests، وتقليل مخاطر CSRF.

مثال توضيحي:

```
Set-Cookie: session=VALUE; HttpOnly; Secure; SameSite=Lax
```

**مهم:**

HttpOnly مش بيمنع كل تأثيرات XSS، لكنه بيصعّب سرقة قيمة الـ Cookie عن طريق JavaScript.

وCookies مش آمنة تلقائيًا؛ لازم إعدادها وطريقة استخدامها تكون صحيحة.

---

# PART 12 – JWT ATTACKS

## 37. None Algorithm Attack

### الفكرة

بعض التطبيقات قد تتعامل بشكل غير آمن مع خوارزمية `none`، وهي حالة لا يوجد فيها توقيع في JWT.

مثال Header:

```
{
  "alg": "none",
  "typ": "JWT"
}
```

المشكلة لو السيرفر قبل Token غير موقع رغم إن النظام المفروض يشترط Signature.

### اختبار آمن

داخل تطبيق تدريبي:

1. التقط JWT.
    
2. افحص Header.
    
3. اختبر رفض الخوارزميات غير المسموح بها.
    
4. تحقق من إن السيرفر لا يقبل Token بدون Signature.
    

### الحماية

- حدد Algorithms المسموح بها في إعدادات السيرفر.
    
- ارفض `none` في أنظمة التوقيع المطلوبة.
    
- استخدم مكتبة JWT موثوقة ومحدثة.
    

---

## 38. Algorithm Confusion Attack

### الفكرة

بتحصل لما التطبيق يختار طريقة التحقق من التوقيع بشكل غير آمن، ويثق في Algorithm الموجودة في Header بدل ما يفرض إعدادًا ثابتًا وآمنًا.

مثال:

التطبيق المفروض يستخدم RS256.

لكن لو التحقق معمول بطريقة غير صحيحة، ممكن يحصل خلط بين طريقة التحقق باستخدام Public Key وطريقة HMAC.

### ليه خطيرة؟

لأن طريقة التعامل مع المفاتيح بتختلف بين Symmetric وAsymmetric Algorithms.

لو التطبيق استخدم المفتاح بطريقة غير صحيحة، ممكن يقبل Token مزور.

### الحماية

- فرض Algorithm محدد.
    
- فصل إعدادات التوقيع عن بيانات المستخدم.
    
- استخدام مكتبات موثوقة.
    
- التحقق من Key Type وAlgorithm Compatibility.
    

---

## 39. Weak Secret Attack

### الفكرة

لو التطبيق بيستخدم HS256 مع Secret ضعيف، ممكن يكون قابلًا للتخمين.

مثال Secret ضعيف:

```
secret123
```

Secret أقوى:

```
RandomlyGeneratedHighEntropySecret
```

لكن الأفضل يكون Secret عشوائيًا قويًا مولدًا باستخدام Secure Random Generator، مش كلمة بشرية عادية.

### ليه؟

HS256 بيعتمد على Secret Key.

لو حد عرفه، يقدر ينشئ JWT بتوقيع صحيح.

### الحماية

- استخدم Secret قوي وعشوائي.
    
- متستخدمش كلمات مرور بشرية كـ Signing Secret.
    
- متشاركش Secret مع أشخاص أو Services غير محتاجة له.
    
- اعمل Rotation عند الاشتباه في التسريب.
    

---

## 40. Token Manipulation

### الفكرة

المهاجم يحاول تعديل بيانات JWT.

مثال Payload الأصلي:

```
{
  "sub": "100",
  "role": "user"
}
```

Payload بعد التعديل:

```
{
  "sub": "100",
  "role": "admin"
}
```

لو التطبيق بيقبل التعديل بدون التحقق من Signature، فده خلل خطير.

### نقطة مهمة

مجرد إنك قدرت تفك Base64 وتعدّل النص مش معناه إنك نجحت في الهجوم.

لازم السيرفر يقبل الـ Token المعدّل ويتعامل معاه كأنه صحيح.

### الحماية

التحقق الصحيح من Signature، مع تطبيق Authorization Server-Side.

---

## 41. Expiration Validation

### الفكرة

التحقق من وقت انتهاء صلاحية الـ Token.

مثال:

```
{
  "sub": "100",
  "exp": 1794500000
}
```

لو الوقت الحالي أكبر من `exp`، المفروض يتم رفض Token.

### الاختبار

1. احصل على Token صالح.
    
2. افحص قيمة `exp`.
    
3. استخدم Token بعد انتهاء صلاحيته داخل بيئة الاختبار.
    
4. راقب هل السيرفر بيرفضه.
    

### الحماية

- Validate `exp`.
    
- استخدم Clock Synchronization.
    
- حدد Clock Skew Tolerance مناسبة.
    
- متقبلش Tokens منتهية.
    

---

## 42. Replay Attack

### الفكرة

إعادة إرسال Request أو Token صالح بهدف تكرار عملية أو إعادة استخدام بيانات تم التقاطها.

مثال:

عندك Request لتحويل مبلغ:

```
POST /api/transfer HTTP/1.1
Authorization: Bearer TOKEN
Content-Type: application/json
```

```
{
  "toAccount": "200",
  "amount": 500
}
```

لو التطبيق نفّذ نفس العملية كل مرة يتم فيها إرسال Request، من غير آلية مناسبة لمنع التكرار في العملية الحساسة، ممكن يحصل تكرار للتحويل.

### الحماية

حسب تصميم العملية:

- Idempotency Keys.
    
- One-Time Tokens.
    
- Nonces.
    
- Request Expiration.
    
- Transaction State Validation.
    
- Replay Detection.
    

**مهم:** JWT العادي مش بيمنع Replay تلقائيًا.

---

## 43. JWT Revocation

### المشكلة

JWT غالبًا Stateless.

يعني السيرفر ممكن يتحقق من Token باستخدام التوقيع والـ Claims من غير ما يسأل Database عن Session في كل Request.

لكن ده بيخلق مشكلة:

لو Token اتسرق، ممكن يفضل صالح لحد انتهاء صلاحيته.

### الحلول

**1. Short-Lived Access Token**

تخلي Access Token قصير الصلاحية.

**2. Denylist**

تحتفظ بمعرفات Tokens اللي تم إبطالها، مثل `jti`.

**3. Token Versioning**

تربط Tokens بإصدار معين من حساب المستخدم، وتقدر تبطل Tokens القديمة عند تغيير الإصدار.

**4. Refresh Token Revocation**

تبطل Refresh Token عند Logout أو الاشتباه في تسريبه.

---

## 44. Header Parameter Attacks

بعض JWT Headers ممكن تحتوي على:

### kid

Key ID.

بيحدد المفتاح المستخدم للتحقق من التوقيع.

### jku

JWK Set URL.

بيحدد عنوان ممكن يحتوي على مجموعة مفاتيح عامة.

### x5u

X.509 URL.

بيحدد عنوان شهادة X.509.

### فين المشكلة؟

لو التطبيق وثق في قيم Header بدون Validation، ممكن يحصل:

- اختيار مفتاح غير موثوق.
    
- Key Confusion.
    
- SSRF.
    
- Path Traversal حسب طريقة التعامل مع `kid`.
    

### الحماية

- استخدم Key Sources موثوقة.
    
- لا تسمح بمصادر مفاتيح عشوائية.
    
- تحقق من `kid` باستخدام Mapping آمن.
    
- متستخدمش قيم Header كمسارات ملفات بدون Validation.
    
- امنع الوصول إلى URLs غير موثوقة.
    

---

# PART 13 – JWT VALIDATION

## 45. إيه اللي لازم السيرفر يتحقق منه؟

متكتفيش بالتحقق من Signature فقط.

راجع كل نقطة:

### 1. Signature

هل التوقيع صحيح؟

### 2. Algorithm

هل الخوارزمية ضمن القائمة المسموح بها؟

### 3. exp

هل انتهت الصلاحية؟

### 4. iss

هل الجهة المصدرة موثوقة؟

### 5. aud

هل الـ Token موجه للخدمة الحالية؟

### 6. nbf

هل بدأ وقت صلاحية الـ Token؟

### 7. sub

هل المستخدم المقصود موجود وصحيح؟

### 8. Authorization

هل المستخدم مسموح له بتنفيذ العملية المطلوبة؟

**مثال مهم:**

Token صحيح وموقع بشكل سليم، لكن فيه:

```
{
  "sub": "100",
  "role": "user"
}
```

ده مش معناه إن صاحبه يقدر ينفّذ:

```
DELETE /api/admin/users/200
```

لازم السيرفر يتحقق من صلاحيات العملية نفسها.

---
اختبار JWT داخل Burp

### الخطوة الأولى: الحصول على Token

سجل الدخول.

مثال Response:

```
{
  "access_token": "HEADER.PAYLOAD.SIGNATURE"
}
```

### الخطوة الثانية: تحديد مكان استخدامه

ممكن تلاقيه في:

```
Authorization: Bearer TOKEN
```

أو Cookie حسب التطبيق.

### الخطوة الثالثة: فحص Token

انسخ JWT وافحص:

- Header.
    
- Payload.
    
- Claims.
    
- Algorithm.
    
- Expiration.
    

### الخطوة الرابعة: اختبارات Validation

في بيئة تدريبية، اختبر:

- Token غير صالح.
    
- Token منتهي.
    
- Signature غير صحيحة.
    
- Token بدون Signature.
    
- Issuer غير صحيح.
    
- Audience غير صحيحة.
    

### الخطوة الخامسة: اختبارات Authorization

استخدم حسابين مختلفين.

اختبر هل حساب A يقدر يشوف أو يعدّل بيانات حساب B.
