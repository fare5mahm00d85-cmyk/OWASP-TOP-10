
---
# مقدمة

JavaScript وحدها لا تستطيع التعامل مع المتصفح.

مثلاً:

هل JavaScript تستطيع معرفة عنوان الصفحة؟

لا.

هل تستطيع فتح نافذة؟

لا.

هل تستطيع إرسال Request؟

لا.

إذن من أين جاءت هذه الإمكانيات؟

الإجابة:

```
Browser APIs
```

---

# ما هو Browser API؟

هو:

> **واجهة برمجية يوفرها المتصفح حتى يستطيع JavaScript التعامل مع المتصفح والصفحة.**

الرسم

```
Browser

│

├── DOM API

├── Fetch API

├── History API

├── Storage API

├── URL API

├── Clipboard API

├── File API

├── WebSocket API

└── وغيرها...
```

---

# Browser API vs JavaScript

ناس كتير بتتلخبط بينهم.

JavaScript

↓

لغة برمجة.

Browser API

↓

وظائف أضافها المتصفح.

---

مثال

```
let x=5;
```

ده JavaScript.

أما

```
document.title
```

فده Browser API.

---

# أشهر Browser APIs

```
Window API

DOM API

Location API

History API

Navigator API

Storage API

Fetch API

XMLHttpRequest

URL API

Clipboard API

File API

Blob API

FileReader API

WebSocket API

Notification API

Geolocation API
```

---

# أولاً

# Window API

---

أكبر Object داخل المتصفح.

تقريبًا كل شيء يبدأ منه.

الرسم

```
Window

│

├── document

├── location

├── history

├── navigator

├── screen

├── localStorage

└── sessionStorage
```

---

مثال

```
window.innerWidth
```

يعرض عرض نافذة المتصفح.

---

يمكن غالبًا كتابة:

```
innerWidth
```

لأن Window هو الـ Global Object.

---

# ثانياً

# Document API

---

هو الذى يمثل الصفحة.

الرسم

```
Window

↓

Document

↓

DOM

↓

Elements
```

---

وظيفته

- قراءة الصفحة.
- تعديل الصفحة.
- البحث عن عناصر.

---

أشهر الدوال

```
getElementById()

querySelector()

querySelectorAll()

createElement()

appendChild()
```

---

# DOM API

الـ DOM API يسمح بالتعامل مع عناصر الصفحة.

مثال

```
HTML

↓

DOM

↓

JavaScript

↓

Modify
```

---

يمكنك

- إنشاء عنصر.
- حذف عنصر.
- تعديل نص.
- تعديل خصائص.

---

# ثالثاً

# Location API

---

تمثل عنوان الصفحة.

مثال

```
https://example.com/search?q=test#top
```

تتكون من:

```
Protocol

Host

Path

Query

Fragment
```

---

الرسم

```
https://example.com

↓

Protocol

↓

Host

↓

Path

↓

Query

↓

Hash
```

---

يمكن معرفة:

- Protocol
- Host
- Hostname
- Port
- Pathname
- Search
- Hash

---

# Query String

هو

```
?q=test
```

---

# Hash

هو

```
#section1
```

---

# رابعاً

# History API

---

تمثل سجل التصفح.

الرسم

```
Page1

↓

Page2

↓

Page3
```

---

وظيفتها

- الرجوع للخلف.
- الذهاب للأمام.
- تغيير عنوان الصفحة بدون إعادة تحميلها (في تطبيقات SPA).

---

# خامساً

# Navigator API

---

تمثل معلومات عن المتصفح.

يمكن معرفة:

- لغة المتصفح.
- حالة الاتصال.
- معلومات عامة عن البيئة.

---

الرسم

```
Browser

↓

Navigator

↓

Information
```

---

# سادساً

# Storage API

تنقسم إلى

```
Local Storage

Session Storage
```

---

# Local Storage

يخزن البيانات حتى بعد إغلاق المتصفح (إلى أن تُحذف).

الرسم

```
Browser

↓

Local Storage

↓

Persistent
```

---

# Session Storage

يشبه Local Storage.

لكن يحذف عند إغلاق التبويب.

---

# الفرق

|Local|Session|
|---|---|
|دائم نسبيًا|مؤقت|
|يبقى بعد إعادة تشغيل المتصفح|ينتهي مع إغلاق التبويب|

---

# سابعاً

# Fetch API

---

أحدث طريقة لإرسال HTTP Requests من JavaScript.

الرسم

```
Browser

↓

Fetch

↓

HTTP Request

↓

Server

↓

Response
```

---

تستخدم مع:

- GET
- POST
- PUT
- DELETE

وغيرها.

---

# ثامناً

# XMLHttpRequest

---

الطريقة القديمة لإرسال Requests.

ما زالت موجودة.

لكن Fetch أسهل وأكثر استخدامًا في التطبيقات الحديثة.

---

# مقارنة

|Fetch|XMLHttpRequest|
|---|---|
|أحدث|أقدم|
|يعتمد على Promises|يعتمد على Events|
|أسهل قراءة|أكثر تعقيدًا|

---

# تاسعاً

# URL API

---

تساعد على التعامل مع الروابط.

يمكن استخدامها لتحليل الرابط إلى أجزائه بدلًا من معالجته كنص عادي.

---

# عاشراً

# Clipboard API

---

تتعامل مع:

- Copy
- Paste
- Read
- Write

قد تتطلب إذنًا من المستخدم أو تفاعلًا مباشرًا حسب المتصفح.

---

# الحادى عشر

# File API

---

للتعامل مع الملفات التى يختارها المستخدم.

الرسم

```
User

↓

Choose File

↓

Browser

↓

JavaScript

↓

File API
```

---

# الثانى عشر

# Blob API

---

Blob

=

Binary Large Object

---

يمثل بيانات ثنائية.

مثل:

- صورة.
- PDF.
- ملف نصى.
- فيديو.

---

# الثالث عشر

# FileReader API

---

يقرأ محتويات الملفات التى اختارها المستخدم.

مثلاً

```
Image

↓

FileReader

↓

Base64

↓

Preview
```

---

# الرابع عشر

# WebSocket API

---

تسمح باتصال دائم بين العميل والخادم.

بدلاً من:

```
Request

↓

Response

↓

Close
```

تصبح

```
Open Connection

↓

Send

↓

Receive

↓

Send

↓

Receive

↓

Close
```

---

تستخدم فى

- Chat
- Games
- Live Notifications
- Dashboards

---

# الخامس عشر

# Notification API

---

تسمح للموقع بعرض إشعارات.

لكن بعد موافقة المستخدم.

---

# السادس عشر

# Geolocation API

---

تسمح للموقع بطلب موقع المستخدم.

لا تعمل إلا بعد موافقة المستخدم.

---

# السابع عشر

# Screen API

---

تعرض معلومات عن الشاشة.

مثل

- العرض.
- الارتفاع.
- دقة الشاشة.

---

# الثامن عشر

# Timing APIs

---

تستخدم لتنفيذ العمليات بعد وقت معين أو بشكل متكرر.

مثل:

- `setTimeout`
- `setInterval`
- `requestAnimationFrame`

---

# التاسع عشر

# Console API

---

للتعامل مع Console.

مثل

```
log

error

warn

table
```

تستخدم فى Debugging.

---

# كيف ترتبط Browser APIs بـ XSS؟

من منظور تحليل التطبيقات:

- تساعدك على فهم ماذا يفعل JavaScript داخل الصفحة.
- تساعدك على تتبع انتقال البيانات بين الصفحة والخادم.
- تساعدك على فهم كيفية التعامل مع DOM وStorage والطلبات.

عند قراءة كود JavaScript في موقع أو Lab، ستجد كثيرًا من هذه الـ APIs مستخدمة بشكل طبيعي.

---

# كيف أفكر كـ Penetration Tester؟

عندما ترى JavaScript اسأل:

```
هل يقرأ من:

Location؟

DOM؟

Storage؟

Cookies؟

Server؟

↓

ثم

إلى أين يرسل البيانات؟

DOM؟

Network؟

Storage؟
```

هذا التفكير مهم جدًا، خصوصًا عندما نصل إلى Chapter 9 (DOM XSS).

---

# أخطاء شائعة

❌ الاعتقاد أن Browser APIs جزء من لغة JavaScript نفسها.

❌ الخلط بين `document` و`window`.

❌ الخلط بين `localStorage` و`sessionStorage`.

❌ اعتبار `fetch` و`XMLHttpRequest` شيئًا واحدًا دون معرفة الفرق.

❌ نسيان أن بعض الـ APIs تتطلب إذنًا من المستخدم (مثل Geolocation وNotifications).