
---
# مقدمة

معظم المبتدئين يدخلون أي Lab ثم يبدأون في تجربة مدخلات عشوائية.

لكن المحترف يعمل بطريقة مختلفة.

هو لا يبدأ بالسؤال:

> **"ما هو الـ Payload؟"**

بل يبدأ بالسؤال:

> **"كيف تعمل الصفحة؟"**

---

# منهجية اختبار XSS

احفظ هذا المخطط لأنه سيكون أساس كل Lab.

```
1. Understand the Application
            ↓
2. Find User Input
            ↓
3. Send Request
            ↓
4. Analyze Response
            ↓
5. Locate Reflection
            ↓
6. Determine Context
            ↓
7. Check Encoding
            ↓
8. Check Sanitization
            ↓
9. Analyze JavaScript (إذا لزم)
            ↓
10. Confirm Behavior داخل البيئة التدريبية
```

---

# المرحلة الأولى

# Understand the Application

لا تبدأ بالاختبار مباشرة.

ابدأ بفهم الموقع.

اسأل نفسك:

- ما وظيفة الصفحة؟
- هل هي Search؟
- Login؟
- Profile؟
- Comments؟
- Chat؟
- Contact Form؟

كل صفحة لها طريقة عمل مختلفة.

---

# المرحلة الثانية

# Mapping

اعمل خريطة للموقع.

ابحث عن:

- Search
- Forms
- Comments
- Profile
- Upload
- Chat
- URL Parameters
- API Calls

الرسم:

```
Website
│
├── Login
├── Search
├── Blog
├── Contact
├── Profile
└── API
```

---

# المرحلة الثالثة

# Find Input Points

حدد كل مكان يمكن للمستخدم إدخال بيانات فيه.

مثل:

```
Search Box

Login

Comment

Profile

Feedback

Chat

URL Parameter

HTTP Header

Cookie
```

---

# المرحلة الرابعة

# Send Request

أرسل الطلب باستخدام:

- Browser
- Burp Suite Proxy
- Burp Repeater

ثم راقب الطلب والاستجابة.

---

# المرحلة الخامسة

# Analyze Response

لا تبحث عن التنفيذ مباشرة.

ابحث عن:

- أين ظهرت البيانات؟
- هل تغيرت؟
- هل اختفت؟
- هل تم ترميزها؟

---

# المرحلة السادسة

# Reflection

إذا ظهرت البيانات،

حدد:

```
Body؟

Attribute؟

JavaScript؟

JSON؟

HTML؟
```

هذه أهم خطوة.

---

# المرحلة السابعة

# Context Detection

حدد الـ Context بدقة.

```
HTML

↓

Attribute

↓

JavaScript

↓

CSS

↓

URL

↓

JSON
```

بعدها فقط تستطيع تفسير سلوك الصفحة.

---

# المرحلة الثامنة

# Encoding Analysis

هل البيانات أصبحت:

```
&lt;

&#60;

%3C
```

إذا حدث ذلك،

فكر:

- هل هذا HTML Encoding؟
- أم URL Encoding؟
- هل هو مناسب للـ Context؟

---

# المرحلة التاسعة

# Sanitization Analysis

اسأل:

- هل تم حذف جزء من البيانات؟
- هل تم تعديلها؟
- هل بقيت كما هي؟

هذا يساعدك على فهم آلية المعالجة.

---

# المرحلة العاشرة

# JavaScript Analysis

إذا كانت الصفحة تعتمد على JavaScript:

افتح:

```
Chrome DevTools

↓

Sources
```

وابحث عن:

- كيف تُقرأ البيانات؟
- كيف تُستخدم؟
- هل يتم تحديث DOM؟

---

# DOM Analysis

إذا كان Lab عن DOM XSS،

اسأل:

```
Source

↓

Processing

↓

Sink
```

ولا تبدأ من النهاية.

---

# استخدام Burp Suite

أهم الأدوات:

## Proxy

لمشاهدة الطلبات.

---

## Repeater

لإعادة إرسال نفس الطلب بعد التعديل.

---

## HTTP History

لمراجعة جميع الطلبات السابقة.

---

## Logger

لتسجيل حركة التطبيق أثناء الاختبار.

---

# استخدام Chrome DevTools

## Elements

لمعرفة مكان ظهور البيانات.

---

## Console

لرؤية الأخطاء البرمجية والتنبيهات.

---

## Network

لمراجعة الطلبات والاستجابات.

---

## Sources

لقراءة JavaScript وإضافة Breakpoints.

---

## Application

لمراجعة:

- Cookies
- Local Storage
- Session Storage
- IndexedDB

---

# كيفية قراءة JavaScript

لا تبدأ من أول الملف.

ابدأ بالبحث عن:

- أسماء الدوال.
- الأحداث (Events).
- أماكن قراءة البيانات.
- أماكن تحديث DOM.

إذا كان الملف مضغوطًا:

استخدم:

```
Pretty Print
```

---

# Debugging

تعلم:

```
Breakpoints

Step Over

Step Into

Watch

Call Stack
```

هذه الأدوات تساعدك على متابعة تنفيذ الكود وفهم تدفق البيانات.

---

# إدارة الملاحظات

لكل Lab أنشئ ملاحظة بالشكل التالي:

```
Lab Name:
---------------------

Goal:
---------------------

Input Points:
---------------------

Reflection:
---------------------

Context:
---------------------

Encoding:
---------------------

Sanitization:
---------------------

JavaScript:
---------------------

Result:
---------------------

Lessons Learned:
---------------------
```

---

# كيف تحلل Bug Bounty Report؟

عند قراءة أي تقرير:

لا تبدأ بالحل.

ابدأ بـ:

```
ما هو نوع XSS؟

↓

أين الـ Input؟

↓

أين الـ Output؟

↓

ما الـ Context؟

↓

كيف اكتُشفت المشكلة؟

↓

ما الدرس المستفاد؟
```

الهدف ليس نسخ الخطوات، بل فهم طريقة التفكير.

---

# كيف تفكر أثناء كل Lab؟

بدلًا من:

```
ما هو الـ Payload المناسب؟
```

فكر:

```
كيف تعمل الصفحة؟

↓

أين تدخل البيانات؟

↓

أين تخرج؟

↓

ما هو الـ Context؟

↓

هل يوجد Encoding؟

↓

هل يوجد Sanitization؟

↓

هل يوجد JavaScript؟

↓

كيف يتحرك الـ Data Flow؟
```

---

# الأخطاء الشائعة

❌ تجربة مدخلات عشوائية دون فهم الصفحة.

❌ تجاهل قراءة الـ Response.

❌ تجاهل DevTools.

❌ عدم تحديد الـ Context.

❌ التركيز على التنفيذ بدلًا من تحليل البيانات.

❌ عدم كتابة ملاحظات بعد كل Lab.

---

# خطة مذاكرة مقترحة

```
كل Lab

↓

افهم الصفحة

↓

حدد Input

↓

افحص Response

↓

حدد Context

↓

حلل JavaScript إذا لزم

↓

اكتب ملخصًا

↓

راجع بعد يوم
```