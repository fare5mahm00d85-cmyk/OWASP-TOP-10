
---
# مقدمة

عندما تسمع كلمة **DOM XSS**، أول شيء يجب أن تعرفه هو:

> **المشكلة ليست في الخادم (Server)، بل في JavaScript الذي يعمل داخل المتصفح.**

في Reflected وStored XSS غالبًا يكون الخادم جزءًا من المشكلة.

أما في DOM XSS، فقد تكون الصفحة نفسها آمنة من جهة الخادم، لكن كود JavaScript في المتصفح هو الذي يتعامل مع البيانات بشكل غير آمن.

---

# ما هو DOM؟

DOM اختصار لـ:

```
Document Object Model
```

وهو تمثيل الصفحة داخل المتصفح على شكل شجرة من الكائنات (Objects).

مثال:

```
<html>
  <body>
    <h1>Hello</h1>
    <button>Click</button>
  </body>
</html>
```

بعد أن يقرأها المتصفح تصبح:

```
Document
│
└── html
    │
    └── body
        │
        ├── h1
        │   └── "Hello"
        │
        └── button
            └── "Click"
```

JavaScript لا تتعامل مع HTML كنص.

بل تتعامل مع هذا الـ DOM.

---

# كيف يعمل DOM؟

الرسم

```
HTML

↓

HTML Parser

↓

DOM Tree

↓

JavaScript

↓

Modify DOM
```

أي تعديل في الصفحة يتم غالبًا من خلال DOM.

---

# ما هو DOM XSS؟

هو نوع من XSS يحدث عندما:

1. يقرأ JavaScript بيانات من مكان يمكن للمستخدم التأثير فيه.
2. ثم يضعها في مكان داخل الصفحة بطريقة غير آمنة.

إذن الفكرة الأساسية هي:

```
User-Controlled Data

↓

JavaScript

↓

DOM

↓

Unsafe Update
```

---

# Data Flow

لفهم DOM XSS يجب أن تتعلم تتبع البيانات.

اسأل دائمًا:

```
من أين جاءت البيانات؟

↓

وأين ذهبت؟
```

هذا يسمى

```
Data Flow Analysis
```

---

# أهم مفهومين

```
Source

↓

Sink
```

---

# ما هو Source؟

الـ Source هو:

> **أي مكان يقرأ منه JavaScript بيانات يمكن للمستخدم التحكم فيها.**

أمثلة شائعة:

- عنوان الصفحة (URL).
- Query Parameters.
- Fragment (`#...`).
- بيانات من Storage.
- رسائل بين النوافذ (`postMessage`).
- إدخالات المستخدم في النماذج.

الفكرة:

```
User

↓

Source

↓

JavaScript
```

---

# ما هو Sink؟

الـ Sink هو:

> **المكان الذي يرسل إليه JavaScript البيانات بعد قراءتها.**

قد يكون:

- تعديل نص عنصر.
- تعديل خاصية.
- إنشاء عنصر جديد.
- إرسال البيانات إلى API.
- حفظها في Storage.

ليست كل الـ Sinks خطرة.

الخطورة تعتمد على **كيف** تُستخدم البيانات.

---

# الرسم الكامل

```
User

↓

Source

↓

JavaScript

↓

Processing

↓

Sink

↓

Browser
```

---

# Source → Processing → Sink

هذه أهم جملة في DOM XSS.

أي تحليل يبدأ بها.

---

# مثال تحليلي

تخيل أن JavaScript:

1. قرأت قيمة من عنوان الصفحة.
2. خزنتها في متغير.
3. عدلت عليها قليلًا.
4. استخدمتها لتحديث الصفحة.

الرسم:

```
URL

↓

JavaScript

↓

Variable

↓

DOM Update
```

هنا تبدأ في تحليل:

- هل البيانات موثوقة؟
- هل تم التحقق منها؟
- كيف تم استخدامها؟

---

# DOM Parsing

المتصفح يقرأ HTML.

ثم يبني DOM.

بعد ذلك يبدأ JavaScript.

```
HTML

↓

DOM

↓

JavaScript

↓

DOM Update
```

---

# DOM Manipulation

JavaScript تستطيع:

- إنشاء عناصر.
- حذف عناصر.
- تعديل النص.
- تعديل الخصائص.
- تغيير الشكل.

كل هذا يسمى:

```
DOM Manipulation
```

---

# Data Sources الشائعة

|Source|الوصف|
|---|---|
|URL|عنوان الصفحة|
|Query Parameters|معاملات الرابط|
|Hash Fragment|الجزء بعد `#`|
|Form Inputs|بيانات النماذج|
|localStorage|بيانات محفوظة محليًا|
|sessionStorage|بيانات مؤقتة|
|postMessage|رسائل بين النوافذ|
|Cookies|بيانات الجلسة (وفقًا لخصائصها وإمكانية الوصول إليها)|

---

# Data Sinks

ليست كل الـ Sinks متساوية.

يمكن تقسيمها إلى:

```
DOM Update

↓

Attribute Update

↓

Text Update

↓

Network

↓

Storage
```

---

# DOM XSS Lifecycle

```
User

↓

URL

↓

JavaScript Reads

↓

Variable

↓

JavaScript Processes

↓

DOM Updated
```

---

# كيف أفكر أثناء التحليل؟

لا تبدأ بالسؤال:

> "هل يوجد XSS؟"

ابدأ بالسؤال:

```
من أين جاءت البيانات؟

↓

كيف تحركت؟

↓

هل تغيرت؟

↓

إلى أين وصلت؟

↓

كيف استخدمت؟
```

---

# أدوات تساعد في التحليل

## Chrome DevTools

- Sources
- Elements
- Console
- Network

---

## DOM Breakpoints

تساعدك على معرفة:

متى تغير عنصر داخل الصفحة.

---

## Pretty Print

إذا كان JavaScript مضغوطًا (Minified)، استخدم Pretty Print لتسهيل القراءة.

---

## Search in Sources

ابحث عن:

- أسماء المتغيرات.
- الدوال.
- أماكن قراءة البيانات.
- أماكن تعديل DOM.

---

## DOM Invader

إضافة تعليمية داخل Burp Suite Professional تساعد على تتبع تدفق البيانات من الـ Sources إلى بعض الـ Sinks أثناء تحليل تطبيقات الويب.

---

# Debugging

تعلم استخدام:

- Breakpoints.
- Step Over.
- Step Into.
- Watch Variables.
- Call Stack.

لأنها تساعدك على متابعة حركة البيانات خطوة بخطوة.

---

# الرسم الكامل

```
User Input

↓

Browser

↓

Source

↓

JavaScript

↓

Processing

↓

Sink

↓

DOM Updated
```

---

# أخطاء شائعة

❌ الاعتقاد أن DOM XSS يعتمد على الخادم.

❌ البحث عن Payload قبل فهم Data Flow.

❌ تجاهل قراءة JavaScript.

❌ عدم استخدام DevTools.

❌ التركيز على الـ Sink فقط ونسيان الـ Source.

❌ الاعتقاد أن كل تحديث للـ DOM يمثل ثغرة.

---

# كيف أفكر كـ Penetration Tester؟

كلما فتحت ملف JavaScript اسأل نفسك:

```
أين يقرأ البيانات؟

↓

Source

↓

كيف يعالجها؟

↓

Validation؟

Encoding؟

Sanitization؟

↓

أين يرسلها؟

↓

Sink
```

إذا أصبحت هذه الطريقة تلقائية لديك، ستصبح قراءة أكواد JavaScript أسهل بكثير.