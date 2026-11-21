
---
# ما هو HTTP؟

**HTTP = HyperText Transfer Protocol**

هو البروتوكول الذي يتم من خلاله التواصل بين:

```
Browser (Client)
        │
        │ HTTP Request
        ▼
Web Server
        │
        │ HTTP Response
        ▼
Browser
```

كل مرة تفتح موقعًا أو تضغط زرًا أو تسجل دخولًا يحدث شيئان:

1. **Request** (الطلب)
2. **Response** (الاستجابة)

---

# أولًا: HTTP Request ⭐⭐⭐⭐⭐

## ما هو؟

هو الرسالة التي يرسلها المتصفح إلى السيرفر.

مثال:

```
GET /login HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: session=abc123
```

---

## مكونات الـ Request

### 1) Request Line

```
GET /login HTTP/1.1
```

تنقسم إلى:

|الجزء|الشرح|
|---|---|
|GET|نوع الطلب (Method)|
|/login|الصفحة المطلوبة|
|HTTP/1.1|إصدار البروتوكول|

---

### 2) Headers

بعد Request Line تأتي الـ Headers.

مثل:

```
Host: example.com
```

---

### 3) Body

يأتي مع بعض الطلبات مثل POST.

مثال

```
username=admin&password=123
```

---

# الشكل الكامل

```
POST /login HTTP/1.1

Host: example.com

User-Agent: Mozilla/5.0

Cookie: session=abc123

Content-Type: application/x-www-form-urlencoded

Content-Length: 27

username=admin&password=123
```

---

# ثانيًا: HTTP Response ⭐⭐⭐⭐⭐

## ما هو؟

هو رد السيرفر على الـ Request.

مثال

```
HTTP/1.1 200 OK

Content-Type: text/html

Content-Length: 250

<html>

....

</html>
```

---

## مكونات الـ Response

### 1) Status Line

```
HTTP/1.1 200 OK
```

تنقسم إلى:

|الجزء|الشرح|
|---|---|
|HTTP/1.1|الإصدار|
|200|Status Code|
|OK|الرسالة|

---

### 2) Response Headers

مثل

```
Content-Type: text/html
```

---

### 3) Body

وهو محتوى الصفحة.

مثال

```
<html>

<body>

Hello

</body>

</html>
```

---

# ثالثًا: HTTP Headers ⭐⭐⭐⭐⭐

## ما هي؟

هي معلومات إضافية تُرسل مع الطلب أو الاستجابة.

---

## أشهر Request Headers

---

### Host

اسم الموقع.

```
Host: example.com
```

---

### User-Agent

يعرف السيرفر نوع المتصفح.

```
User-Agent: Mozilla/5.0
```

---

### Cookie ⭐⭐⭐⭐⭐

يرسل Cookies إلى السيرفر.

```
Cookie: session=abc123
```

---

### Referer ⭐⭐⭐⭐

يعرف الصفحة التي جاء منها المستخدم.

```
Referer: https://google.com
```

ستراه كثيرًا في Labs الخاصة بـ CSRF.

---

### Origin ⭐⭐⭐⭐⭐

يعرف مصدر الطلب.

```
Origin: https://example.com
```

مهم جدًا في

- CORS
- CSRF

---

### Authorization

يرسل بيانات المصادقة.

```
Authorization: Bearer TOKEN
```

---

### Content-Type ⭐⭐⭐⭐⭐

يحدد نوع البيانات المرسلة.

مثال

```
Content-Type: application/json
```

أو

```
Content-Type: application/x-www-form-urlencoded
```

أو

```
Content-Type: multipart/form-data
```

---

### Accept

ما الذي يستطيع المتصفح استقباله.

```
Accept: text/html
```

---

### Accept-Language

لغة المستخدم.

```
Accept-Language: en-US
```

---

# أشهر Response Headers

---

### Set-Cookie ⭐⭐⭐⭐⭐

ينشئ Cookie.

```
Set-Cookie:

session=abc123;
HttpOnly;
Secure
```

---

### Content-Type

نوع البيانات.

```
Content-Type: text/html
```

---

### Content-Length

حجم الصفحة.

```
Content-Length: 250
```

---

### Location

إعادة توجيه المستخدم.

```
Location: /login
```

---

### Server

نوع السيرفر.

```
Server: Apache
```

---

### X-Frame-Options

حماية ضد Clickjacking.

```
X-Frame-Options: DENY
```

---

### CSP (Content-Security-Policy)

يحدد ما الذي يسمح للمتصفح بتشغيله (مثل السكربتات والصور والمصادر).

```
Content-Security-Policy:
default-src 'self'
```

ستراه كثيرًا أثناء دراسة XSS.

---

# رابعًا: Cookies ⭐⭐⭐⭐⭐

السيرفر يرسلها هكذا:

```
Set-Cookie:

session=abc123
```

بعدها المتصفح يرسلها تلقائيًا:

```
Cookie:

session=abc123
```

---

# خامسًا: GET ⭐⭐⭐⭐⭐

## ما هو؟

يستخدم لجلب البيانات من السيرفر.

---

### مثال

```
GET /products?id=5 HTTP/1.1
```

---

لاحظ

```
?id=5
```

يسمى

```
Query Parameter
```

---

### خصائص GET

✅ البيانات داخل URL

✅ يمكن حفظه Bookmark

✅ يمكن مشاركته

✅ له حدود في طول الرابط

❌ لا ترسل كلمات المرور به عادة

---

### مثال

```
https://example.com/search?q=laptop
```

---

# سادسًا: POST ⭐⭐⭐⭐⭐

## ما هو؟

يرسل بيانات إلى السيرفر.

---

### مثال

```
POST /login HTTP/1.1

Content-Type:
application/x-www-form-urlencoded

username=admin&password=123
```

---

لاحظ

البيانات داخل

```
Request Body
```

وليس داخل الرابط.

---

### خصائص POST

✅ البيانات داخل Body

✅ يستخدم Login

✅ يستخدم Register

✅ يستخدم Change Password

✅ يستخدم Upload

---

# الفرق بين GET و POST

|الخاصية|GET|POST|
|---|---|---|
|مكان البيانات|URL|Body|
|ظاهر في الرابط|✅|❌|
|مناسب لكلمات المرور|❌|✅|
|يستخدم للقراءة|✅|❌|
|يستخدم للإرسال أو التعديل|❌|✅|
|يمكن عمل Bookmark|✅|❌|

---

# أشهر Status Codes ⭐⭐⭐⭐⭐

## 2xx نجاح

|الكود|المعنى|
|---|---|
|200|OK|
|201|Created|
|204|No Content|

---

## 3xx إعادة توجيه

|الكود|المعنى|
|---|---|
|301|Moved Permanently|
|302|Found (Redirect)|
|304|Not Modified|

---

## 4xx خطأ من العميل

|الكود|المعنى|
|---|---|
|400|Bad Request|
|401|Unauthorized|
|403|Forbidden|
|404|Not Found|
|405|Method Not Allowed|
|429|Too Many Requests|

---

## 5xx خطأ من السيرفر

|الكود|المعنى|
|---|---|
|500|Internal Server Error|
|502|Bad Gateway|
|503|Service Unavailable|
|504|Gateway Timeout|

---

# دورة حياة الطلب كاملة

```
1- تكتب الرابط

        │

        ▼

Browser

        │

        ▼

HTTP Request

(GET /login)

        │

        ▼

Server

        │

يعالج الطلب

        │

        ▼

HTTP Response

(200 OK)

        │

        ▼

Browser

يعرض الصفحة
```

---

# كيف يظهر ذلك في Burp Suite؟

## Request

```
GET /profile HTTP/2

Host: example.com

Cookie: session=abc123
```

---

## Response

```
HTTP/2 200 OK

Content-Type: text/html

Set-Cookie: theme=dark
```