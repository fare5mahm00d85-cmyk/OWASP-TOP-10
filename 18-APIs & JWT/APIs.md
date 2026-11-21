
----------
# PART 1 – API FUNDAMENTALS

## 1. يعني إيه API؟

اختصار **API = Application Programming Interface**.

ببساطة، الـ API هي واجهة بتسمح لبرنامج إنه يتواصل مع برنامج تاني، ويطلب منه بيانات أو يطلب منه ينفّذ عملية معينة.

تخيل إن عندك تطبيق بنك على الموبايل.

أنت فتحت التطبيق وعايز تعرف رصيدك.

هل التطبيق بيفتح قاعدة البيانات مباشرة ويقرأ الرصيد؟

غالبًا لأ.

اللي بيحصل:

1. أنت بتضغط على زر عرض الرصيد.
    
2. التطبيق بيبعت Request للـ API.
    
3. الـ API تستقبل الطلب.
    
4. الـ Backend يتأكد إنك مسجل دخول.
    
5. الـ Backend يتحقق إن الحساب ده ملكك.
    
6. يتواصل مع Database.
    
7. يسترجع الرصيد.
    
8. يرجع Response للتطبيق.
    
9. التطبيق يعرض الرصيد على الشاشة.
    

### شكل الاتصال

```
User
  |
  v
Mobile Application
  |
  | HTTP Request
  v
API
  |
  v
Backend Logic
  |
  v
Database
  |
  v
Backend Logic
  |
  v
API
  |
  | HTTP Response
  v
Mobile Application
  |
  v
Display Account Balance
```

### مثال عملي

التطبيق عايز يعرف بيانات المستخدم رقم 15.

يبعت:

```
GET /api/users/15 HTTP/1.1
Host: bank.example
Accept: application/json
Authorization: Bearer YOUR_TOKEN
```

الـ API ممكن ترجع:

```
HTTP/1.1 200 OK
Content-Type: application/json
```

```
{
  "id": 15,
  "username": "fares",
  "email": "fares@example.com",
  "balance": 15000
}
```

**إيه اللي يهمك كـ Pentester؟**

مش بس تعرف إن الـ API بترجع بيانات.

لازم تسأل:

- هل المستخدم مسجل دخول؟
    
- هل الـ Token صحيح؟
    
- هل المستخدم ده يحق له يشوف الحساب رقم 15؟
    
- هل ممكن يغيّر الرقم ويشوف حساب شخص تاني؟
    
- هل البيانات اللي رجعت فيها معلومات حساسة؟
    
- هل فيه Rate Limiting؟
    

دي الأسئلة اللي بتوصلك للثغرات.

---

## 2. الفرق بين Frontend وBackend وAPI

خلينا نفصلهم بمثال.

عندك موقع للتجارة الإلكترونية.

### Frontend

الجزء اللي المستخدم بيشوفه ويتعامل معاه.

زي:

- Login Page.
    
- Product Page.
    
- Shopping Cart.
    
- Profile Page.
    
- Buttons وForms.
    

ممكن يكون معمول باستخدام React أو Angular أو HTML وCSS وJavaScript.

### Backend

الجزء المسؤول عن تنفيذ منطق التطبيق.

زي:

- التحقق من تسجيل الدخول.
    
- حساب سعر الطلب.
    
- التأكد من صلاحية المستخدم.
    
- تنفيذ عملية الدفع.
    
- التعامل مع Database.
    

ممكن يكون معمول باستخدام ASP.NET Core أو Node.js أو Laravel أو Django.

### Database

المكان اللي بتتخزن فيه البيانات.

زي:

- Users.
    
- Password Hashes.
    
- Products.
    
- Orders.
    
- Payments.
    

### API

الوسيط اللي بيسمح للـ Frontend يتواصل مع الـ Backend.

مثال:

```
Frontend:
عايز بيانات المستخدم

       |
       v

API:
GET /api/profile

       |
       v

Backend:
يتحقق من Token والصلاحيات

       |
       v

Database:
يرجع بيانات المستخدم

       |
       v

API:
ترجع JSON Response

       |
       v

Frontend:
يعرض البيانات
```

**معلومة Pentesting:**

ممكن الـ Frontend يخفي زر Admin عن المستخدم العادي، لكن ده مش معناه إن الـ Backend بيمنعه من تنفيذ Admin Endpoint.

إخفاء الزر مش Authorization.

التحقق الحقيقي لازم يكون في الـ Backend.

---

## 3. أنواع APIs

### A. REST API

اختصار REST = Representational State Transfer.

ده أسلوب معماري شائع جدًا في بناء APIs.

بيعتمد غالبًا على HTTP Methods، وبيستخدم JSON لتبادل البيانات.

مثال:

```
GET    /api/users
GET    /api/users/15
POST   /api/users
PUT    /api/users/15
PATCH  /api/users/15
DELETE /api/users/15
```

كل مسار بيمثل Resource أو مجموعة Resources.

مثال:

`/api/users`

معناه التعامل مع مجموعة المستخدمين.

`/api/users/15`

معناه التعامل مع مستخدم محدد رقمه 15.

**Pentesting Example:**

لو عندك:

```
GET /api/users/15
```

جرب في بيئة مصرح بها:

```
GET /api/users/16
```

لو الحساب 16 ملك مستخدم آخر، والسيرفر رجع بياناته بدون التحقق من الصلاحيات، ممكن تكون عندك BOLA.

### B. SOAP API

SOAP = Simple Object Access Protocol.

بيعتمد على XML في تبادل الرسائل.

مثال مبسط:

```
<soap:Envelope>
  <soap:Body>
    <GetUser>
      <UserId>15</UserId>
    </GetUser>
  </soap:Body>
</soap:Envelope>
```

ممكن تلاقيه في أنظمة مؤسسية أو بنكية.

من منظور Pentesting، اهتم بـ:

- XML Parsing.
    
- Authentication.
    
- Authorization.
    
- XXE حسب طريقة معالجة XML.
    
- Information Disclosure.
    

### C. GraphQL API

GraphQL طريقة للتعامل مع البيانات بتسمح للعميل يحدد الحقول اللي محتاجها.

مثال Query:

```
{
  user(id: 15) {
    id
    username
    email
  }
}
```

بدل ما السيرفر يرجع كل بيانات المستخدم، العميل بيحدد الحقول المطلوبة.

**فين الخطر؟**

لو التطبيق مش بيطبق Authorization على مستوى كل Object أو Field، ممكن المستخدم يطلب بيانات مش من حقه يشوفها.

مثال:

```
{
  user(id: 15) {
    username
    email
    salary
    role
  }
}
```

وجود Field في GraphQL Schema لا يعني إن كل المستخدمين مسموح لهم يشوفوه.

### D. WebSocket API

WebSocket بيسمح بإنشاء اتصال مستمر بين Client وServer.

مثال: تطبيق Chat.

```
Client  <====== WebSocket Connection ======> Server
```

مفيد في:

- Live Chat.
    
- Notifications.
    
- Real-Time Dashboards.
    
- Online Games.
    

**Pentesting:**

اختبر هل المستخدم يقدر يشوف رسائل مستخدم تاني أو ينفذ عمليات غير مصرح بها من خلال WebSocket Messages.

---

# PART 2 – HTTP FUNDAMENTALS

## 4. يعني إيه HTTP Request؟

الـ HTTP Request هو الطلب اللي الـ Client بيبعته للـ Server.

مثال:

```
GET /api/products/10 HTTP/1.1
Host: shop.example.com
User-Agent: Mozilla/5.0
Accept: application/json
Authorization: Bearer TOKEN
```

خلينا نفهم كل جزء.

### 1. Method

بتحدد نوع العملية.

في المثال:

```
GET
```

معناها إننا بنطلب قراءة بيانات.

### 2. Path

```
/api/products/10
```

ده المسار اللي بنطلب منه البيانات.

### 3. HTTP Version

```
HTTP/1.1
```

نسخة البروتوكول المستخدمة.

### 4. Headers

معلومات إضافية بتتبعت مع الطلب.

مثال:

```
Content-Type: application/json
Authorization: Bearer TOKEN
Accept: application/json
```

### 5. Body

الـ Body بيحتوي على البيانات المرسلة للسيرفر، وبيظهر غالبًا مع POST وPUT وPATCH.

مثال:

```
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/json
```

```
{
  "username": "fares",
  "email": "fares@example.com",
  "password": "Password123"
}
```

هنا الـ Body هو الـ JSON اللي فيه بيانات المستخدم الجديد.

---

## 5. HTTP Methods بالتفصيل

### GET

استخدامه الأساسي جلب البيانات.

مثال:

```
GET /api/products HTTP/1.1
```

ممكن يرجع:

```
{
  "products": [
    {
      "id": 1,
      "name": "Laptop"
    },
    {
      "id": 2,
      "name": "Keyboard"
    }
  ]
}
```

**اختبارات أمنية:**

- هل المستخدم يقدر يشوف بيانات مش من حقه؟
    
- هل فيه بيانات حساسة في Response؟
    
- هل تغيير ID يسمح بالوصول لحساب آخر؟
    

### POST

بيُستخدم غالبًا لإنشاء Resource جديد أو تنفيذ عملية.

مثال:

```
POST /api/products HTTP/1.1
Content-Type: application/json
```

```
{
  "name": "Gaming Mouse",
  "price": 1500
}
```

**اختبارات أمنية:**

- هل المستخدم العادي يقدر ينشئ Product؟
    
- هل ممكن يحدد `ownerId` لمستخدم آخر؟
    
- هل ممكن يرسل خصائص إضافية غير مسموح بها؟
    

### PUT

غالبًا بيُستخدم لاستبدال بيانات Resource بالكامل.

مثال:

```
PUT /api/users/15 HTTP/1.1
Content-Type: application/json
```

```
{
  "username": "fares",
  "email": "new@example.com"
}
```

### PATCH

بيُستخدم لتعديل جزء من البيانات.

مثال:

```
PATCH /api/users/15 HTTP/1.1
Content-Type: application/json
```

```
{
  "email": "new@example.com"
}
```

**Pentesting Example:**

لو المستخدم مسموح له يعدّل Email فقط، جرب هل الـ Backend بيقبل إرسال:

```
{
  "email": "new@example.com",
  "role": "admin"
}
```

لو قبل تعديل `role` بدون صلاحية، دي ممكن تكون Mass Assignment أو Broken Object Property Level Authorization.

### DELETE

بيُستخدم لحذف Resource.

مثال:

```
DELETE /api/products/10 HTTP/1.1
```

اختبر هل المستخدم عنده صلاحية حذف الـ Product ده فعلًا.

**مهم:** أسماء الـ Methods مش ضمان للسلوك. لازم تفهم التطبيق نفسه؛ بعض APIs بتستخدم POST لتنفيذ عمليات مختلفة.

---

## 6. HTTP Status Codes بالتفصيل

### 200 OK

الطلب نجح.

مثال:

```
HTTP/1.1 200 OK
```

### 201 Created

تم إنشاء Resource جديد.

مثال: إنشاء حساب جديد.

### 204 No Content

العملية نجحت، لكن مفيش Response Body.

مثال: حذف بيانات بنجاح.

### 400 Bad Request

الطلب فيه مشكلة في البيانات أو التنسيق.

مثال:

إرسال JSON غير صحيح.

```
{
  "username": "fares",
```

### 401 Unauthorized

المستخدم مش مقدم بيانات Authentication صحيحة، أو الـ Token غير صالح.

مثال:

```
HTTP/1.1 401 Unauthorized
```

ممكن السبب يكون:

- Token غير موجود.
    
- Token منتهي.
    
- Signature غير صحيحة.
    

### 403 Forbidden

المستخدم معروف، لكن مش مسموح له ينفذ العملية.

مثال:

User عادي يحاول يفتح Admin Endpoint.

### 404 Not Found

المورد غير موجود، أو التطبيق بيستخدم 404 لإخفاء وجود مورد غير مسموح الوصول إليه.

### 405 Method Not Allowed

الـ Endpoint لا يسمح باستخدام الـ Method المطلوب.

### 409 Conflict

حصل تعارض مع الحالة الحالية للبيانات.

مثال: إنشاء Username موجود بالفعل.

### 415 Unsupported Media Type

نوع البيانات المرسلة غير مدعوم.

مثال: إرسال XML إلى Endpoint متوقع JSON فقط.

### 422 Unprocessable Content

البيانات مفهومة من ناحية التنسيق، لكن فيها مشكلة تمنع معالجتها.

مثال:

```
{
  "age": -500
}
```

### 429 Too Many Requests

عدد الطلبات تجاوز الحد المسموح.

### 500 Internal Server Error

خطأ داخلي في السيرفر.

**Pentesting Note:**

راقب اختلاف Status Codes وResponse Bodies أثناء الاختبار، لكن متعتبرش كل اختلاف ثغرة. لازم تثبت وجود تأثير أمني فعلي.

---

## 7. يعني إيه Headers؟

الـ Headers عبارة عن معلومات إضافية بتساعد السيرفر والعميل يفهموا طبيعة الطلب والاستجابة.

### أهم Request Headers

|Header|الوظيفة|
|---|---|
|Host|اسم السيرفر|
|User-Agent|معلومات عن العميل|
|Accept|أنواع البيانات المقبولة|
|Content-Type|نوع البيانات المرسلة|
|Authorization|بيانات التوثيق|
|Cookie|إرسال Cookies|
|Origin|مصدر الطلب|
|Referer|الصفحة التي جاء منها الطلب|

### أهم Response Headers

|Header|الوظيفة|
|---|---|
|Content-Type|نوع البيانات الراجعة|
|Set-Cookie|إنشاء أو تحديث Cookie|
|Server|معلومات عن السيرفر أحيانًا|
|Access-Control-Allow-Origin|إعداد CORS|
|Strict-Transport-Security|فرض HTTPS في المتصفح|
|X-Content-Type-Options|منع MIME Sniffing|

**مثال:**

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
X-Content-Type-Options: nosniff
```

هنا السيرفر بيقول إن الـ Response من نوع JSON، ومش المفروض يتخزن في Cache حسب إعداد `no-store`.

---

# PART 3 – JSON & API DATA

## 8. يعني إيه JSON؟

JSON = JavaScript Object Notation.

ده Format لتبادل البيانات، مش لغة برمجة.

مثال:

```
{
  "id": 15,
  "username": "fares",
  "active": true,
  "balance": 15000,
  "address": null
}
```

أنواع البيانات:

|النوع|مثال|
|---|---|
|String|`"fares"`|
|Number|`15000`|
|Boolean|`true`|
|Null|`null`|
|Array|`[1, 2, 3]`|
|Object|`{"id": 15}`|

### Nested JSON

ممكن Object يحتوي على Object تاني.

```
{
  "user": {
    "id": 15,
    "profile": {
      "username": "fares",
      "email": "fares@example.com"
    }
  }
}
```

### Arrays

```
{
  "orders": [
    {
      "id": 100,
      "total": 500
    },
    {
      "id": 101,
      "total": 900
    }
  ]
}
```

**Pentesting Note:**

افهم شكل الـ JSON قبل ما تبدأ الاختبار.

راجع:

- أسماء الحقول.
    
- أنواع البيانات.
    
- الحقول المطلوبة.
    
- الحقول الاختيارية.
    
- العلاقات بين Objects.
    
- الحقول الحساسة.
    

---

# PART 4 – AUTHENTICATION & AUTHORIZATION

## 9. Authentication بالتفصيل

Authentication معناها التحقق من هوية المستخدم.

السؤال:

**Who are you? – أنت مين؟**

مثال: المستخدم بيدخل Username وPassword.

```
POST /api/login HTTP/1.1
Content-Type: application/json
```

```
{
  "username": "fares",
  "password": "Password123"
}
```

السيرفر يتحقق من بيانات الدخول.

لو صحيحة، ممكن يرجع:

```
{
  "access_token": "eyJhbGciOi...",
  "token_type": "Bearer",
  "expires_in": 900
}
```

ده معناه إن التطبيق أصدر Access Token للمستخدم.

### أنواع Authentication

- Password Authentication.
    
- Multi-Factor Authentication (MFA).
    
- OTP Authentication.
    
- Session-Based Authentication.
    
- Token-Based Authentication.
    
- Biometric Authentication.
    

### Authentication Testing

اختبر:

1. هل يمكن تسجيل الدخول بدون Password؟
    
2. هل يوجد Rate Limiting؟
    
3. هل Password Reset آمن؟
    
4. هل OTP له صلاحية زمنية؟
    
5. هل يمكن إعادة استخدام OTP؟
    
6. هل يتم إبطال Session بعد Logout؟
    
7. هل يتم رفض Tokens المنتهية؟
    

---

## 10. Authorization بالتفصيل

Authorization معناها تحديد الصلاحيات المسموح بها للمستخدم.

السؤال:

**What are you allowed to do? – مسموح لك تعمل إيه؟**

مثال:

عندك نظام فيه 3 أنواع مستخدمين.

|Role|الصلاحيات|
|---|---|
|User|عرض بياناته وطلباته|
|Manager|عرض بيانات فريقه|
|Admin|إدارة المستخدمين والنظام|

### مثال

User يحاول:

```
GET /api/profile
```

السيرفر يسمح له.

لكن لو حاول:

```
DELETE /api/users/20
```

المفروض يتم رفض العملية لو مش من صلاحياته.

### الفرق الأساسي

Authentication بتحدد مين المستخدم.

Authorization بتحدد إيه اللي يقدر يعمله المستخدم.

**ممكن Authentication تكون سليمة تمامًا ومع ذلك التطبيق يكون فيه Authorization Vulnerability.**

وده من أهم المجالات اللي هتشتغل عليها في API Pentesting.

---

# PART 5 – OWASP API SECURITY TOP 10

## 11. API1:2023 – Broken Object Level Authorization (BOLA)

### الفكرة

التطبيق يسمح للمستخدم بالوصول إلى Object مش من حقه.

Object ممكن يكون:

- User.
    
- Order.
    
- Invoice.
    
- Payment.
    
- Book.
    
- Account.
    

### مثال عملي

عندك حسابين:

```
User A:
ID = 100

User B:
ID = 200
```

المستخدم A بيسجل الدخول، ويطلب:

```
GET /api/orders/1001 HTTP/1.1
Authorization: Bearer USER_A_TOKEN
```

السيرفر بيرجع الطلب.

المستخدم A لاحظ إن رقم الطلب بيتغير في الـ URL.

جرّب في بيئة الاختبار:

```
GET /api/orders/1002 HTTP/1.1
Authorization: Bearer USER_A_TOKEN
```

لو الطلب 1002 ملك User B، والسيرفر رجّع بياناته، يبقى فيه احتمال BOLA.

### ليه حصلت الثغرة؟

المطور عمل حاجة شبه:

```
var order = await db.Orders.FindAsync(orderId);
return Ok(order);
```

الكود جاب الطلب باستخدام ID فقط.

لكن مفيش تحقق إن الطلب ده ملك المستخدم الحالي.

### التصحيح المفاهيمي

لازم يكون فيه تحقق من الملكية:

```
var order = await db.Orders
    .FirstOrDefaultAsync(o =>
        o.Id == orderId &&
        o.UserId == currentUserId);

if (order == null)
    return NotFound();

return Ok(order);
```

المثال توضيحي، والتنفيذ الحقيقي يعتمد على تصميم التطبيق.

### طريقة الاختبار في Burp Suite

1. سجل الدخول بالحساب الأول.
    
2. افتح طلبًا تملكه.
    
3. افتح Burp Proxy History.
    
4. ابعت الطلب إلى Repeater.
    
5. غيّر Object ID.
    
6. راقب Response.
    
7. كرر الاختبار باستخدام حسابين تملكهما.
    
8. اختبر GET وPATCH وDELETE حسب الوظائف المتاحة.
    

### إثبات الثغرة

لازم تثبت إن حساب A قدر يوصل لبيانات حساب B بدون تصريح.

**مجرد تغيير رقم ID مش دليل كافي.**

---

## 12. API2:2023 – Broken Authentication

### الفكرة

وجود مشكلة في التحقق من هوية المستخدم.

### أمثلة

- Password Reset ضعيف.
    
- OTP يمكن إعادة استخدامه.
    
- عدم وجود Rate Limiting.
    
- قبول Token منتهي.
    
- ضعف Session Management.
    
- عدم التحقق من Token Signature.
    

### مثال: Token منتهي

عندك Token:

```
{
  "sub": "100",
  "exp": 1700000000
}
```

لو الوقت الحالي تجاوز قيمة `exp`، المفروض السيرفر يرفض الـ Token.

لو السيرفر قبله، يبقى فيه خلل في Expiration Validation.

### مثال: OTP

التطبيق يرسل OTP:

```
{
  "otp": "123456"
}
```

اختبر في بيئة مصرح بها:

- هل OTP صالح لفترة محددة؟
    
- هل يمكن استخدامه أكثر من مرة؟
    
- هل مرتبط بالحساب الصحيح؟
    
- هل عدد المحاولات محدود؟
    
- هل يتم إبطاله بعد نجاح العملية؟
    

---

## 13. API3:2023 – Broken Object Property Level Authorization

### الفكرة

المستخدم يقدر يقرأ أو يعدّل Property مش من صلاحياته.

### مثال: قراءة بيانات حساسة

الـ API ترجع:

```
{
  "id": 100,
  "username": "fares",
  "email": "fares@example.com",
  "role": "user",
  "salary": 20000,
  "internal_notes": "Private information"
}
```

المستخدم المفروض يشوف Username وEmail فقط.

لكن السيرفر رجع Salary وInternal Notes.

ده مثال على مشكلة في Property-Level Authorization أو Excessive Data Exposure.

### مثال: تعديل بيانات غير مسموح بها

الـ API بتسمح بتعديل:

```
{
  "username": "fares"
}
```

جربت إرسال:

```
{
  "username": "fares",
  "role": "admin"
}
```

لو التطبيق غيّر Role بالفعل بدون صلاحية، فده خلل أمني.

### الفرق بين BOLA وProperty-Level Authorization

- BOLA: الوصول إلى Object مش ملكك أو مش مسموح لك.
    
- Property-Level Authorization: الوصول أو التعديل على خصائص داخل Object مش مسموح لك.
    

---

## 14. Mass Assignment

### الفكرة

مشكلة بتحصل لما الـ Backend يربط بيانات المستخدم مباشرة بخصائص Model بدون تحديد الحقول المسموح بتعديلها.

### مثال

عندك User Model:

```
public class User
{
    public int Id { get; set; }

    public string Username { get; set; }

    public string Email { get; set; }

    public string Role { get; set; }

    public bool IsVerified { get; set; }
}
```

المستخدم مسموح له يعدّل Username وEmail.

لكن التطبيق بيستخدم كل البيانات اللي وصلت من الـ Request لتحديث الـ Model.

الطلب الطبيعي:

```
{
  "username": "fares",
  "email": "fares@example.com"
}
```

الطلب الإضافي:

```
{
  "username": "fares",
  "email": "fares@example.com",
  "role": "admin",
  "isVerified": true
}
```

لو السيرفر قبل الخصائص الإضافية وغيّرها، فده Mass Assignment.

### ليه؟

لأن التطبيق وثق في البيانات اللي جاية من المستخدم بدل ما يحدد بنفسه الخصائص المسموح تعديلها.

### الحماية

- استخدم DTOs.
    
- اعمل Allowlist للحقول.
    
- افصل User Profile عن Admin Properties.
    
- نفّذ Authorization في الـ Backend.
    

مثال DTO:

```
public class UpdateProfileDto
{
    public string Username { get; set; }

    public string Email { get; set; }
}
```

كده الـ DTO مش بيسمح بتعديل Role من خلال العملية دي.

---

## 15. API4:2023 – Unrestricted Resource Consumption

### الفكرة

التطبيق يسمح للمستخدم باستهلاك موارد السيرفر بشكل غير محدود أو بدون قيود كافية.

### مثال: Pagination

طلب طبيعي:

```
GET /api/products?page=1&pageSize=20
```

لكن المستخدم يرسل:

```
GET /api/products?page=1&pageSize=1000000
```

لو السيرفر نفّذ الطلب بدون Maximum Limit، ممكن يستهلك Memory وCPU بشكل ضخم.

### أمثلة أخرى

- رفع ملفات كبيرة جدًا.
    
- إرسال Requests كثيرة.
    
- تنفيذ عمليات حسابية مكلفة.
    
- إرسال عمليات بحث معقدة.
    
- عدم تحديد Timeout.
    

### الحماية

- Rate Limiting.
    
- Maximum Request Size.
    
- Pagination Limits.
    
- Timeouts.
    
- Resource Quotas.
    
- مراقبة الاستخدام.
    

---

## 16. API5:2023 – Broken Function Level Authorization (BFLA)

### الفكرة

المستخدم يقدر ينفّذ Function مش من صلاحياته.

### مثال

عندك:

```
GET /api/profile
```

مسموح لكل مستخدم.

وعندك:

```
GET /api/admin/users
```

مسموح للـ Admin فقط.

لو المستخدم العادي قدر ينفّذ Endpoint الخاص بالـ Admin، فده ممكن يكون BFLA.

### الفرق بين BOLA وBFLA

تخيل إنك في نظام بنك.

**BOLA:**

تقدر تشوف حساب عميل تاني عن طريق تغيير Account ID.

**BFLA:**

تقدر تنفّذ وظيفة تحويل أموال أو حذف حسابات رغم إنك مستخدم عادي.

### طريقة الاختبار

- استخدم حساب User.
    
- اجمع Endpoints الخاصة بالـ Admin.
    
- جرّب الوصول ليها بحساب User.
    
- اختبر HTTP Methods المختلفة.
    
- راقب هل الـ Backend بيطبق Authorization فعلًا.
    

---

## 17. API6:2023 – Unrestricted Access to Sensitive Business Flows

### الفكرة

إمكانية إساءة استخدام العمليات التجارية الحساسة.

### مثال: Coupon

عندك متجر بيدي المستخدم خصم 20% باستخدام Coupon.

العملية الطبيعية:

```
1. User selects product.
2. User enters coupon.
3. Backend validates coupon.
4. Backend applies discount.
5. Order is created.
```

المشكلة ممكن تحصل لو المستخدم قدر يستخدم نفس Coupon أكثر من مرة رغم إن قواعد النظام بتسمح باستخدامه مرة واحدة فقط.

### أمثلة

- تكرار استخدام Coupon.
    
- حجز نفس الموعد عدة مرات.
    
- تجاوز قيود عدد المنتجات.
    
- إساءة استخدام Referral Program.
    
- تكرار عمليات مالية.
    

**مهم:** مش كل تكرار Request ثغرة. لازم تكون فيه قاعدة Business Rule تم تجاوزها بالفعل.

---

## 18. API7:2023 – Server Side Request Forgery (SSRF)

### الفكرة

التطبيق بيخلي السيرفر يرسل HTTP Request إلى عنوان يحدده المستخدم.

### مثال

عندك API بتستقبل URL لصورة:

```
POST /api/import-image HTTP/1.1
Content-Type: application/json
```

```
{
  "imageUrl": "https://example.com/image.png"
}
```

السيرفر بيعمل Download للصورة من العنوان ده.

لو مفيش Validation مناسب، المستخدم ممكن يحاول توجيه السيرفر إلى Internal Resources.

### ليه دي خطيرة؟

لأن السيرفر ممكن يكون عنده وصول إلى موارد داخلية مش متاحة من الإنترنت.

### الحماية

- URL Allowlist.
    
- منع الوصول إلى Private IP Ranges.
    
- التحقق من Redirects.
    
- منع الوصول إلى Metadata Endpoints.
    
- تقييد Protocols.
    
- استخدام Network Segmentation.
    

**ملاحظة:** اختبر SSRF في بيئة تملكها أو عندك تصريح صريح لاختبارها.

---

## 19. API8:2023 – Security Misconfiguration

### الفكرة

إعدادات أمنية غير صحيحة.

### أمثلة

- Debug Mode شغال في Production.
    
- Detailed Errors.
    
- Default Passwords.
    
- CORS غير آمن.
    
- عدم استخدام HTTPS.
    
- Security Headers ناقصة.
    
- Directory Listing.
    
- Exposed Swagger Documentation مع بيانات حساسة.
    

### مثال

السيرفر يرجع:

```
{
  "error": "Database connection failed",
  "database": "internal-db-01",
  "username": "app_user",
  "stackTrace": "..."
}
```

ده ممكن يكشف معلومات داخلية.

المفروض المستخدم يحصل على رسالة عامة، والتفاصيل تتسجل في Logs آمنة.

---

## 20. API9:2023 – Improper Inventory Management

### الفكرة

عدم وجود حصر دقيق لكل APIs والـ Versions الموجودة.

### مثال

```
/api/v1/users
/api/v2/users
/api/v3/users
```

الشركة بتستخدم v3، لكن v1 لسه متاح.

ممكن v1 يكون فيه:

- Authentication ضعيف.
    
- Authorization ناقص.
    
- معلومات حساسة.
    
- Endpoints قديمة.
    

### Pentesting

راجع:

- API Documentation.
    
- Swagger.
    
- Subdomains.
    
- Old Versions.
    
- Staging APIs.
    
- Development APIs.
    
- Deprecated Endpoints.
    

---

## 21. API10:2023 – Unsafe Consumption of APIs

### الفكرة

التطبيق بيثق في بيانات جاية من Third-Party API بدون التحقق منها بشكل كافٍ.

### مثال

متجر إلكتروني بيستخدم خدمة خارجية لحساب سعر الشحن.

الخدمة بترجع:

```
{
  "shippingCost": 50,
  "currency": "EGP"
}
```

لو التطبيق وثق في Response بدون Validation، ممكن يتعامل مع بيانات غير متوقعة أو غير صحيحة.

### الحماية

- Validate Responses.
    
- Validate Data Types.
    
- استخدم HTTPS.
    
- ضع Timeouts.
    
- تعامل مع Errors بأمان.
    
- تحقق من Schema.
    
- لا تثق تلقائيًا في External Services.
    

---
----
---
---
---
---
---
----
---
---
----

  
---

# PART 14 – BURP SUITE PRACTICAL TESTING

## 46. يعني إيه Burp Suite؟

Burp Suite أداة لاختبار أمان تطبيقات الويب.

من أهم الأدوات اللي هتستخدمها في Web Penetration Testing.

### أهم الأجزاء

**Proxy**

بيعترض Requests وResponses بين المتصفح والسيرفر.

**HTTP History**

بيحتفظ بالطلبات اللي اتعملت.

**Repeater**

يسمح بإعادة إرسال Request بعد تعديله.

**Intruder**

يساعد في إجراء اختبارات متكررة على Parameters.

**Decoder**

لفك وترميز البيانات.

**Comparer**

لمقارنة Requests أو Responses.

---

## 47. اختبار API باستخدام Burp

مثال:

عندك Endpoint:

```
GET /api/users/100 HTTP/1.1
Host: readify.local
Authorization: Bearer USER_A_TOKEN
```

### الخطوة الأولى

افتح التطبيق من Browser متوصل بـ Burp.

### الخطوة الثانية

اعمل Login.

### الخطوة الثالثة

افتح Proxy ثم HTTP History.

هتلاقي Requests اللي التطبيق بعتها.

### الخطوة الرابعة

اختار Request الخاص بالمستخدم.

### الخطوة الخامسة

ابعت الطلب إلى Repeater.

### الخطوة السادسة

اختبر تغيير Parameters أو Headers أو Body.

مثال:

```
GET /api/users/100
```

إلى:

```
GET /api/users/101
```

### الخطوة السابعة

قارن Response.

اسأل نفسك:

- هل البيانات اتغيرت؟
    
- هل وصلت لبيانات مستخدم تاني؟
    
- هل السيرفر رجع 403؟
    
- هل رجع 404؟
    
- هل فيه Sensitive Information؟
    
- هل العملية اتنفذت فعلًا؟
    

**استخدم حسابات اختبار تملكها، ولا تختبر بيانات مستخدمين حقيقيين بدون تصريح.**

---

## 48.  
---

# PART 15 – CORS & CSRF

## 49. CORS

CORS = Cross-Origin Resource Sharing.

آلية في المتصفح بتتحكم في السماح لصفحات من Origin معين بقراءة Responses من Origin مختلف.

### يعني إيه Origin؟

الـ Origin بيتكون من:

- Scheme.
    
- Host.
    
- Port.
    

مثال:

```
https://example.com:443
```

ده Origin مختلف عن:

```
http://example.com:80
```

### مثال

عندك Frontend:

```
https://app.example.com
```

وعندك API:

```
https://api.example.com
```

الـ API ممكن ترجع:

```
Access-Control-Allow-Origin: https://app.example.com
```

ده بيسمح للـ Browser بالتعامل مع الـ Response من الـ Origin المحدد وفق سياسة CORS.

### Headers مهمة

- Access-Control-Allow-Origin
    
- Access-Control-Allow-Methods
    
- Access-Control-Allow-Headers
    
- Access-Control-Allow-Credentials
    

### مثال إعداد غير آمن محتمل

لو السيرفر بيعكس أي Origin يوصله بدون Validation، ممكن يكون فيه CORS Misconfiguration.

ولو بيسمح بـ Credentials مع Origins غير موثوقة، الخطر ممكن يكون أكبر.

**مهم:** CORS مش بديل عن Authorization، ومش بيمنع استخدام أدوات زي Burp أو curl في إرسال Requests مباشرة.

---

## 50. CSRF

CSRF = Cross-Site Request Forgery.

هجوم بيحاول يخلي متصفح المستخدم يرسل Request غير مرغوب فيه لموقع هو مسجل الدخول فيه.

### مثال

أنت مسجل الدخول في موقع بنك.

المتصفح محتفظ بـ Session Cookie.

فتحت موقع خبيث.

الموقع الخبيث حاول يخلي المتصفح يرسل Request إلى البنك.

لو البنك بيعتمد على Cookies المرسلة تلقائيًا ومفيش حماية مناسبة، ممكن يتأثر بالهجوم.

### الفرق بين CSRF وXSS

**XSS:**

المهاجم بيحاول تنفيذ JavaScript داخل سياق موقع موثوق.

**CSRF:**

المهاجم بيحاول استغلال متصفح المستخدم لإرسال Request غير مرغوب فيه إلى موقع تاني.

### الحماية

- CSRF Tokens.
    
- SameSite Cookies.
    
- Origin Validation.
    
- التحقق من الطلبات الحساسة.
    
- استخدام Authentication مناسب.
    

---

# PART 16 – RATE LIMITING & INPUT VALIDATION

## 51. Rate Limiting

آلية لتحديد عدد Requests المسموح بها خلال فترة زمنية.

مثال:

```
100 Requests per minute
```

لو المستخدم تجاوز الحد، السيرفر ممكن يرجع:

```
HTTP/1.1 429 Too Many Requests
```

### ليه مهم؟

بيساعد في تقليل:

- Brute Force.
    
- Credential Stuffing.
    
- OTP Abuse.
    
- Resource Exhaustion.
    
- Automated Abuse.
    

### مثال

عندك Login Endpoint:

```
POST /api/login
```

اختبر هل فيه حدود لمحاولات تسجيل الدخول، وهل فيه حماية مناسبة ضد المحاولات الآلية.

---

## 52. Input Validation

الـ Input Validation معناها التحقق من البيانات اللي المستخدم بيبعتها.

مثال:

التطبيق متوقع:

```
{
  "age": 21
}
```

لكن المستخدم يرسل:

```
{
  "age": "hello"
}
```

المفروض السيرفر يتحقق من نوع البيانات.

### أنواع Validation

- Type Validation.
    
- Length Validation.
    
- Format Validation.
    
- Range Validation.
    
- Required Fields.
    
- Business Rule Validation.
    

### مثال: Quantity

الطلب الطبيعي:

```
{
  "quantity": 2
}
```

لكن:

```
{
  "quantity": -100
}
```

لازم يتم رفضه لو الكمية السالبة غير مسموح بها.

**مهم:** Validation في Frontend مش كفاية. لازم Backend Validation.

---

# PART 17 – OAUTH 2.0 & OPENID CONNECT

## 53. JWT vs OAuth 2.0

مش نفس الحاجة.

### JWT

Token Format لتمثيل Claims.

### OAuth 2.0

Authorization Framework بيسمح لتطبيق بالحصول على صلاحيات للوصول إلى Resources نيابة عن المستخدم.

مثال:

تطبيق عايز يوصل لصورك على خدمة خارجية بموافقتك، من غير ما تديله Password.

### OpenID Connect

Identity Layer مبني فوق OAuth 2.0، وبيساعد في التحقق من هوية المستخدم.

### الفرق

|Technology|الوظيفة|
|---|---|
|JWT|Token Format|
|OAuth 2.0|Authorization Framework|
|OpenID Connect|Identity / Authentication Layer|

**معلومة مهمة:** مش كل Access Token لازم يكون JWT؛ ممكن يكون Opaque Token.

---

# PART 18 – PRACTICAL EXAMPLE: READIFY API

خلينا نطبق المفاهيم على مشروع Readify، منصة لقراءة الكتب التقنية.

عندنا 3 أنواع مستخدمين:

- Guest.
    
- User.
    
- Admin.
    

## 54. API Endpoints

```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/refresh

GET    /api/books
GET    /api/books/{id}
GET    /api/books/{id}/content

GET    /api/profile
PATCH  /api/profile

GET    /api/admin/users
POST   /api/admin/books
DELETE /api/admin/books/{id}
```

## 55. تحديد الصلاحيات

|Endpoint|Guest|User|Admin|
|---|---|---|---|
|عرض الكتب|Yes|Yes|Yes|
|قراءة كتاب مجاني|حسب سياسة التطبيق|Yes|Yes|
|عرض Profile|No|Own Profile|حسب السياسة|
|إضافة كتاب|No|No|Yes|
|حذف كتاب|No|No|Yes|
|عرض المستخدمين|No|No|Yes|

## 56. أمثلة ثغرات محتملة في Readify

### Example 1: BOLA

المستخدم A يطلب:

```
GET /api/library/100
```

لكن Library رقم 100 تخص المستخدم B.

لو التطبيق رجّع بياناتها، ممكن تكون BOLA.

### Example 2: BFLA

المستخدم العادي يطلب:

```
POST /api/admin/books
```

لو قدر يضيف كتاب رغم إنه مش Admin، ممكن تكون BFLA.

### Example 3: Mass Assignment

المستخدم يعدّل Profile:

```
{
  "username": "fares",
  "role": "admin"
}
```

لو الـ Backend غيّر Role، فيه خلل.

### Example 4: Excessive Data Exposure

طلب عرض الكتب يرجع:

```
{
  "id": 10,
  "title": "Web Security",
  "author": "Example",
  "internal_storage_path": "/private/storage/...",
  "admin_notes": "..."
}
```

المفروض البيانات الداخلية دي ما توصلش للمستخدم بدون سبب مشروع.

### Example 5: JWT Validation

المستخدم يرسل JWT منتهي الصلاحية.

لو السيرفر سمح له بقراءة محتوى الكتب المحمية، يبقى فيه مشكلة في Token Validation.

---

# PART 19 – API PENTESTING METHODOLOGY

## 57. خطوات اختبار API من البداية

### المرحلة الأولى: Reconnaissance

اجمع معلومات عن:

- Domains.
    
- Subdomains.
    
- API Endpoints.
    
- API Versions.
    
- Swagger/OpenAPI.
    
- HTTP Methods.
    
- Parameters.
    

### المرحلة الثانية: Authentication Testing

اختبر:

- Login.
    
- Registration.
    
- Password Reset.
    
- OTP.
    
- MFA.
    
- Session Management.
    
- JWT Validation.
    

### المرحلة الثالثة: Authorization Testing

اختبر:

- BOLA.
    
- BFLA.
    
- Property-Level Authorization.
    
- Privilege Escalation.
    
- Role Checks.
    

### المرحلة الرابعة: Input Testing

اختبر حسب طبيعة التطبيق:

- SQL Injection.
    
- NoSQL Injection.
    
- Command Injection.
    
- Path Traversal.
    
- Type Confusion.
    
- Mass Assignment.
    

### المرحلة الخامسة: Business Logic

اختبر:

- تكرار العمليات.
    
- تجاوز القيود.
    
- ترتيب العمليات.
    
- إساءة استخدام الخصومات.
    
- الوصول غير المصرح به للوظائف الحساسة.
    

### المرحلة السادسة: Configuration

راجع:

- CORS.
    
- HTTPS.
    
- Security Headers.
    
- Error Handling.
    
- Debug Mode.
    
- API Documentation.
    
- Old API Versions.
    

### المرحلة السابعة: Reporting

وثّق:

- Vulnerability Name.
    
- Affected Endpoint.
    
- Severity.
    
- Steps to Reproduce.
    
- Evidence.
    
- Impact.
    
- Remediation.
    

---

# PART 20 – IMPORTANT DIFFERENCES TO MEMORIZE

## 58. BOLA vs BFLA

|BOLA|BFLA|
|---|---|
|Object-Level Authorization|Function-Level Authorization|
|الوصول إلى Object غير مسموح|تنفيذ Function غير مسموح|
|مثال: مشاهدة Order لشخص آخر|مثال: تنفيذ Admin Function|

## 59. BOLA vs Property-Level Authorization

|BOLA|Property-Level|
|---|---|
|الوصول إلى Object|الوصول إلى Property داخل Object|
|User A يشوف Order الخاص بـ User B|User يغيّر Role الخاص به|

## 60. Authentication vs Authorization

|Authentication|Authorization|
|---|---|
|من أنت؟|مسموح لك تعمل إيه؟|
|التحقق من الهوية|التحقق من الصلاحيات|
|Login|Access Control|

## 61. Encoding vs Encryption vs Hashing vs Signing

|المصطلح|المعنى|
|---|---|
|Encoding|تغيير تمثيل البيانات|
|Encryption|إخفاء البيانات باستخدام مفتاح|
|Hashing|إنتاج قيمة Hash من البيانات|
|Signing|إنشاء توقيع للتحقق من الأصالة والسلامة|

أمثلة:

- Base64URL: Encoding.
    
- AES: Encryption.
    
- SHA-256: Hashing.
    
- HMAC-SHA256: Message Authentication Code.
    
- RSA Signature: Digital Signature.
    

**احفظ:** Base64 مش Encryption.

---

# PART 21 – PRACTICAL TOOLS & LABS

## 62. أدوات مهمة

|Tool|الاستخدام|
|---|---|
|Burp Suite|اعتراض وتحليل وتعديل HTTP Traffic|
|Postman|إرسال API Requests|
|curl|إرسال Requests من Terminal|
|JWT Editor|تحليل واختبار JWT|
|jwt.io|فهم JWT Structure|
|Swagger UI|عرض API Documentation|
|OWASP ZAP|اختبار أمان Web وAPIs|

## 63. منصات التدريب

### PortSwigger Web Security Academy

[https://portswigger.net/web-security](https://portswigger.net/web-security)

ركز على:

- API Testing.
    
- Access Control.
    
- Authentication.
    
- JWT Attacks.
    
- Business Logic.
    
- SSRF.
    

### OWASP crAPI

[https://github.com/OWASP/crAPI](https://github.com/OWASP/crAPI)

تطبيق تدريبي متخصص في API Security.

### OWASP Juice Shop

[https://owasp.org/www-project-juice-shop/](https://owasp.org/www-project-juice-shop/)

تطبيق تدريبي فيه ثغرات Web Security متنوعة.

---

# PART 22 – REVISION QUESTIONS

## APIs

**1. What is an API?**

وسيلة تسمح لتطبيقات مختلفة بالتواصل وتبادل البيانات أو تنفيذ العمليات.

**2. What is an Endpoint?**

مسار محدد داخل API لتنفيذ وظيفة معينة.

**3. What is REST API?**

أسلوب معماري لبناء APIs بيعتمد غالبًا على HTTP Methods وResources.

**4. What is the difference between GET and POST?**

GET بيُستخدم غالبًا لجلب البيانات، وPOST لإنشاء بيانات أو تنفيذ عمليات.

**5. What is BOLA?**

الوصول غير المصرح به إلى Object بسبب ضعف Object-Level Authorization.

**6. What is BFLA?**

السماح لمستخدم بتنفيذ Function مش من صلاحياته.

**7. What is Mass Assignment?**

ربط بيانات المستخدم بخصائص داخلية بدون تحديد الخصائص المسموح تعديلها.

**8. What is SSRF?**

التأثير على Requests بيبعتها السيرفر إلى موارد أو عناوين أخرى.

**9. What is Rate Limiting?**

تحديد عدد Requests خلال فترة زمنية.

## JWT

**10. What is JWT?**

Standard لتمثيل Claims بين طرفين.

**11. What are the three parts of a common JWS JWT?**

Header وPayload وSignature.

**12. Is JWT encrypted by default?**

لا، الـ JWS الشائع Signed وليس Encrypted.

**13. What is the purpose of Signature?**

التحقق من سلامة وأصالة البيانات.

**14. What is the difference between HS256 and RS256?**

HS256 بيستخدم Secret مشترك، وRS256 بيستخدم Private Key للتوقيع وPublic Key للتحقق.

**15. What is the exp Claim?**

وقت انتهاء صلاحية الـ Token.

**16. What is the aud Claim?**

الجهة المستقبلة المقصودة بالـ Token.

**17. What is JWT Revocation?**

إبطال Token قبل انتهاء صلاحيته.

**18. What is Replay Attack?**

إعادة استخدام Request أو Token بطريقة غير مصرح بها.

**19. What is Algorithm Confusion?**

خلل في التعامل مع JWT Algorithms يسمح بقبول Token بطريقة غير آمنة.

**20. Does JWT prevent BOLA?**

لا. JWT ممكن يثبت هوية المستخدم، لكن التطبيق لازم يطبق Authorization على كل Object.

---

# PART 23 – FINAL SECURITY CHECKLIST

## API Checklist

- كل Endpoint عليه Authentication مناسب.
    
- كل Object عليه Authorization صحيح.
    
- كل Function عليها Permission Check.
    
- كل Property عليها Access Control.
    
- لا توجد Sensitive Data Exposure.
    
- لا توجد Mass Assignment.
    
- يوجد Rate Limiting.
    
- يوجد Input Validation.
    
- توجد Pagination Limits.
    
- توجد Request Size Limits.
    
- لا توجد Detailed Error Messages.
    
- CORS مضبوط بشكل آمن.
    
- API Versions القديمة تمت مراجعتها.
    
- Third-Party Responses يتم التحقق منها.
    
- HTTPS مستخدم في الاتصالات الحساسة.
    

## JWT Checklist

- استخدام Algorithms موثوقة ومحددة.
    
- رفض `none` عند اشتراط التوقيع.
    
- التحقق من Signature.
    
- التحقق من `exp`.
    
- التحقق من `iss`.
    
- التحقق من `aud`.
    
- التحقق من `nbf` عند استخدامه.
    
- عدم وضع Secrets داخل Payload.
    
- استخدام Strong Signing Keys.
    
- حماية Private Keys.
    
- استخدام Short-Lived Access Tokens.
    
- حماية Refresh Tokens.
    
- تطبيق Refresh Token Rotation عند الحاجة.
    
- وجود Revocation Mechanism مناسب.
    
- عدم الاعتماد على Role داخل Token وحده دون Authorization Server-Side.
    

---

# REFERENCES – مصادر موثوقة

1. OWASP API Security Top 10  
    [https://owasp.org/API-Security/](https://owasp.org/API-Security/)
    
2. OWASP JWT Cheat Sheet  
    [https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html)
    
3. OWASP REST Security Cheat Sheet  
    [https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html)
    
4. PortSwigger Web Security Academy  
    [https://portswigger.net/web-security](https://portswigger.net/web-security)
    
5. RFC 7519 – JSON Web Token  
    [https://www.rfc-editor.org/rfc/rfc7519](https://www.rfc-editor.org/rfc/rfc7519)
    
6. RFC 8725 – JSON Web Token Best Current Practices  
    [https://www.rfc-editor.org/rfc/rfc8725](https://www.rfc-editor.org/rfc/rfc8725)
    

---

# FINAL MINDSET – طريقة تفكير Web Penetration Tester

وأنت بتختبر أي API، اسأل نفسك الأسئلة دي:

1. مين المستخدم اللي بيبعت الـ Request؟
    
2. هل المستخدم متحقق من هويته؟
    
3. هل الـ Token صحيح؟
    
4. هل الـ Token منتهي الصلاحية؟
    
5. هل المستخدم مسموح له يدخل الـ Endpoint؟
    
6. هل المستخدم مسموح له يوصل للـ Object؟
    
7. هل مسموح له يعدّل الـ Properties دي؟
    
8. هل يقدر ينفّذ Admin Functions؟
    
9. هل يقدر يتجاوز Business Rules؟
    
10. هل فيه Rate Limiting؟
    
11. هل الـ Response بتكشف بيانات حساسة؟
    
12. هل السيرفر بيتحقق من البيانات بنفسه؟
    

**أهم قاعدة:**

لا تثق في أي بيانات جاية من الـ Client.

سواء كانت:

- User ID.
    
- Role.
    
- Permission.
    
- JWT Claims.
    
- Object ID.
    
- Price.
    
- Quantity.
    
- Account Number.
    

كل معلومة حساسة لازم يتم التحقق منها في الـ Backend.

**هدفك مش إنك تعرف تبعت Requests بس؛ هدفك تفهم منطق التطبيق، وتكتشف هل المستخدم يقدر يعمل حاجة مش من حقه ولا لأ.**