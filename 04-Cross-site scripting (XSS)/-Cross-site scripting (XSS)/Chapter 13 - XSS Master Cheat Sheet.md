
---
# XSS Roadmap

```
HTML
 ↓
HTTP
 ↓
JavaScript
 ↓
DOM
 ↓
Browser APIs
 ↓
XSS Basics
 ↓
XSS Types
 ↓
Contexts
 ↓
Encoding
 ↓
Events
 ↓
DOM XSS
 ↓
CSP
 ↓
Labs
 ↓
Bug Bounty Reports
```

---

# XSS Definition

```
Cross-Site Scripting (XSS)

هو نوع من الثغرات يحدث عندما يتم التعامل مع بيانات يسيطر عليها المستخدم بطريقة غير آمنة، مما قد يؤدي إلى تنفيذ JavaScript غير مقصود داخل المتصفح.
```

---

# أنواع XSS

```
Reflected XSS
-------------
البيانات ترجع مباشرة مع الاستجابة.

Stored XSS
----------
البيانات تُحفظ ثم تُعرض لاحقًا.

DOM XSS
--------
المشكلة في JavaScript داخل المتصفح وليس بالضرورة في الخادم.
```

---

# XSS Workflow

```
User Input
     ↓
Application
     ↓
Processing
     ↓
Output
     ↓
Browser
```

---

# أهم Contexts

```
HTML Context

Attribute Context

JavaScript Context

URL Context

CSS Context

JSON Context
```

> لكل Context طريقة معالجة مناسبة، ولا توجد طريقة واحدة تصلح للجميع.

---

# Data Flow

```
Source
   ↓
Processing
   ↓
Sink
```

السؤال الدائم:

```
من أين جاءت البيانات؟

↓

كيف عولجت؟

↓

إلى أين ذهبت؟
```

---

# أشهر Sources

```
URL
Query Parameters
Hash Fragment
Form Inputs
Cookies
localStorage
sessionStorage
postMessage
```

---

# أشهر فئات Sinks

```
DOM Updates
Attribute Updates
Text Updates
Network Requests
Storage Operations
```

ليست كل Sinks خطرة، ويعتمد الأمر على طريقة استخدامها.

---

# Browser APIs

```
window
document
location
history
navigator
fetch
XMLHttpRequest
localStorage
sessionStorage
URL
WebSocket
FileReader
Blob
Clipboard
Geolocation
Notification
```

---

# أهم HTML Events

```
click
dblclick
mousedown
mouseup
mousemove

keydown
keyup

input
change
submit

focus
blur

load
DOMContentLoaded

scroll
resize
```

---

# DOM Lifecycle

```
HTML
 ↓
Parser
 ↓
DOM
 ↓
JavaScript
 ↓
DOM Update
```

---

# HTTP Request Flow

```
Browser
   ↓
HTTP Request
   ↓
Server
   ↓
HTTP Response
   ↓
Browser
```

---

# Defense in Depth

```
Input Validation
        ↓
Sanitization
        ↓
Output Encoding
        ↓
Safe DOM APIs
        ↓
CSP
        ↓
HttpOnly Cookies
```

---

# Encoding

```
HTML Encoding
Attribute Encoding
JavaScript String Encoding
URL Encoding
```

> اختر نوع الـ Encoding المناسب للـ Context.

---

# Validation vs Sanitization

```
Validation
----------
يتحقق من أن البيانات مطابقة للقواعد.

Sanitization
------------
ينظف أو يزيل الأجزاء غير المسموح بها مع الحفاظ على المحتوى المقبول.
```

---

# Blacklist vs Whitelist

```
Blacklist
----------
امنع ما تعرف أنه غير مسموح.

Whitelist
----------
اسمح فقط بما تعرف أنه صحيح.
```

غالبًا ما تكون **Whitelist** أكثر أمانًا إذا أمكن استخدامها.

---

# CSP Summary

```
Purpose
-------
تقليل أثر XSS

Common Directives
-----------------
default-src
script-src
style-src
img-src
connect-src
frame-ancestors
form-action

Common Sources
--------------
'self'
'none'
https:
data:
blob:
```

---

# Chrome DevTools

```
Elements
--------
فحص عناصر الصفحة

Console
-------
رسائل JavaScript

Network
-------
Requests & Responses

Sources
-------
قراءة JavaScript

Application
-----------
Cookies
Local Storage
Session Storage
IndexedDB
```

---

# Burp Suite

```
Proxy
-------
اعتراض الطلبات

Repeater
---------
إعادة إرسال الطلبات

HTTP History
------------
مراجعة جميع الطلبات

Logger
------
تتبع نشاط التطبيق
```

---

# Lab Methodology

```
Understand Application
        ↓
Find Input
        ↓
Analyze Response
        ↓
Find Reflection
        ↓
Determine Context
        ↓
Check Encoding
        ↓
Check Sanitization
        ↓
Analyze JavaScript
        ↓
Trace Data Flow
        ↓
Document Findings
```

---

# Bug Bounty Report Analysis

```
Title
 ↓
Application
 ↓
Input
 ↓
Output
 ↓
Context
 ↓
Data Flow
 ↓
Impact
 ↓
Fix
 ↓
Lessons Learned
```

---

# Notes Template

```
Lab Name:
------------------

Goal:
------------------

Application:
------------------

Input:
------------------

Output:
------------------

Reflection:
------------------

Context:
------------------

Encoding:
------------------

Sanitization:
------------------

JavaScript:
------------------

Data Flow:
------------------

Defense:
------------------

Result:
------------------

Lessons Learned:
------------------
```

---

# Common Beginner Mistakes

```
❌ حفظ المصطلحات بدون فهم.

❌ التركيز على الأدوات فقط.

❌ تجاهل DevTools.

❌ عدم قراءة JavaScript.

❌ عدم فهم Context.

❌ عدم تتبع Data Flow.

❌ عدم كتابة Notes.

❌ الاعتماد على الحفظ بدل الفهم.
```

---

# Professional Mindset

قبل أي Lab اسأل نفسك:

```
كيف تعمل الصفحة؟

↓

ما هي وظيفة هذا الـ Feature؟

↓

أين يدخل المستخدم البيانات؟

↓

أين تظهر؟

↓

ما هو الـ Context؟

↓

كيف تتحرك البيانات؟

↓

ما هي وسائل الحماية الموجودة؟

↓

ما السبب الجذري للمشكلة (إن وجدت)؟
```

---

# Quick Revision (One Minute)

```
XSS Types
----------
Reflected
Stored
DOM

Contexts
---------
HTML
Attribute
JavaScript
URL
CSS
JSON

Flow
------
Source
 ↓
Processing
 ↓
Sink

Security
---------
Validation
Sanitization
Output Encoding
CSP
HttpOnly

Tools
------
Burp Suite
Chrome DevTools

Mindset
--------
افهم التطبيق
حدد الـ Context
تتبع البيانات
دوّن ملاحظاتك
```

---

# Golden Rules

```
✓ افهم قبل أن تختبر.
✓ لا تعتمد على الحفظ.
✓ لكل Context معالجة مختلفة.
✓ تتبع Data Flow دائمًا.
✓ اقرأ JavaScript.
✓ استخدم DevTools باستمرار.
✓ اكتب ملاحظات بعد كل Lab.
✓ تعلم من التقارير، ولا تكتفِ بنسخها.
✓ الأمن يعتمد على طبقات دفاع متعددة.
✓ الممارسة المنتظمة أهم من كثرة القراءة.
```