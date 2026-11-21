
---
# مقدمة

تخيل أن موقعًا به خطأ أدى إلى تنفيذ JavaScript غير متوقع.

هل هذا يعني أن المهاجم يستطيع تنفيذ أي شيء؟

ليس بالضرورة.

إذا كان الموقع يستخدم **CSP** بشكل صحيح، فقد يمنع المتصفح تنفيذ أجزاء كبيرة من هذا الكود.

لهذا تعتبر CSP آخر خط دفاع مهم ضد كثير من هجمات XSS.

---

# ما هو CSP؟

CSP اختصار لـ

```
Content Security Policy
```

وهو:

> **سياسة أمنية يرسلها الخادم إلى المتصفح لتحديد المصادر (Sources) المسموح بتحميل وتنفيذ المحتوى منها.**

---

# كيف تعمل CSP؟

الرسم

```
Browser

↓

Receive HTML

↓

Receive CSP

↓

Parse Policy

↓

Allow / Block Resources
```

---

# أين يتم إرسال CSP؟

عادةً يتم إرسالها داخل Header يسمى:

```
Content-Security-Policy
```

مثال:

```
Content-Security-Policy: default-src 'self'
```

---

# ماذا يحدث داخل المتصفح؟

```
Server

↓

HTML + CSP Header

↓

Browser

↓

Read Policy

↓

Load Resources

↓

Block Disallowed Resources
```

---

# لماذا نستخدم CSP؟

الأهداف:

- تقليل أثر XSS.
- التحكم في مصادر JavaScript.
- التحكم في تحميل الصور والخطوط.
- منع تحميل ملفات من مواقع غير موثوقة.
- تقليل مخاطر حقن المحتوى.

---

# كيف يقرأ المتصفح CSP؟

كل سياسة تتكون من مجموعة من:

```
Directives
```

كل Directive تتحكم في نوع معين من الموارد.

---

# أشهر Directives

```
default-src
script-src
style-src
img-src
font-src
connect-src
media-src
object-src
frame-src
worker-src
base-uri
form-action
frame-ancestors
report-uri
report-to
upgrade-insecure-requests
```

---

# default-src

هو المصدر الافتراضي.

مثال:

```
Content-Security-Policy: default-src 'self'
```

معناه:

إذا لم يتم تحديد سياسة خاصة بنوع معين من الموارد، استخدم `'self'`.

---

# ماذا تعني 'self'؟

تعني:

```
نفس الموقع (Same Origin)
```

أي لا يسمح بتحميل الموارد إلا من نفس النطاق.

---

# script-src

من أهم الـ Directives.

وظيفته:

تحديد أماكن تحميل JavaScript.

مثال:

```
script-src 'self'
```

يعني:

يسمح بتحميل ملفات JavaScript من نفس الموقع فقط.

---

# style-src

خاص بملفات CSS.

مثال:

```
style-src 'self'
```

---

# img-src

خاص بالصور.

مثال:

```
img-src 'self' https://images.example.com
```

---

# font-src

خاص بالخطوط.

---

# connect-src

يتحكم في الاتصالات مثل:

- Fetch API
- XMLHttpRequest
- WebSocket
- EventSource

---

# media-src

للوسائط:

- Audio
- Video

---

# object-src

خاص بعناصر مثل:

```
<object>
<embed>
```

في التطبيقات الحديثة غالبًا يفضل تقييده بشدة أو تعطيله إذا لم يكن هناك حاجة إليه.

---

# frame-src

يتحكم في الصفحات التي يمكن تحميلها داخل:

```
iframe
```

---

# frame-ancestors

يحدد من يستطيع تضمين موقعك داخل إطار (Frame أو iframe).

يساعد في تقليل مخاطر **Clickjacking**.

---

# base-uri

يتحكم في استخدام عنصر:

```
<base>
```

---

# form-action

يحدد الوجهات المسموح للنماذج (Forms) بإرسال البيانات إليها.

---

# upgrade-insecure-requests

إذا كان الموقع يعمل بـ HTTPS،

يقوم المتصفح بمحاولة ترقية طلبات HTTP إلى HTTPS عند الإمكان.

---

# report-uri و report-to

يمكن استخدامهما لإرسال تقارير عندما يمنع المتصفح موردًا بسبب CSP.

يساعد ذلك المطورين على اكتشاف السياسات التي تحتاج إلى تعديل.

---

# Source Expressions

بعد كل Directive ستجد مصدرًا أو أكثر.

أشهرها:

```
'self'

'none'

https:

data:

blob:

*
```

---

# 'none'

تعني:

```
لا تسمح بأي مصدر.
```

---

# *

تعني:

```
أي مصدر.
```

وغالبًا لا تكون مناسبة في السياسات الصارمة.

---

# https:

يسمح بتحميل الموارد عبر HTTPS.

---

# data:

يسمح بموارد Data URI.

---

# blob:

يسمح بموارد Blob.

---

# Nonce

Nonce هو:

> **قيمة عشوائية جديدة يولدها الخادم لكل استجابة، ويستخدمها المتصفح للسماح بتشغيل عناصر Script المصرح بها فقط.**

مثال توضيحي:

```
Server
  ↓
Generate Random Nonce
  ↓
Send CSP + HTML
  ↓
Browser verifies the nonce
```

---

# Hash

بدلًا من استخدام Nonce،

يمكن السماح بتشغيل Script إذا كان محتواه يطابق Hash محددًا.

الفكرة:

```
Script

↓

Hash

↓

Compare

↓

Allow / Block
```

---

# Nonce vs Hash

|Nonce|Hash|
|---|---|
|يتغير مع كل استجابة|ثابت طالما لم يتغير محتوى الـ Script|
|مناسب للسكربتات الديناميكية|مناسب للسكربتات الثابتة|
|يولده الخادم|يحسب من محتوى السكربت|

---

# CSP Levels

يوجد أكثر من إصدار لـ CSP.

الأكثر استخدامًا حاليًا هو:

```
CSP Level 3
```

الذي أضاف تحسينات ودعمًا لخصائص جديدة مقارنة بالإصدارات السابقة.

---

# كيف تفحص CSP؟

افتح:

```
Chrome DevTools

↓

Network

↓

Response Headers
```

وابحث عن:

```
Content-Security-Policy
```

أو استخدم:

```
Security Tab
```

لرؤية معلومات أمنية إضافية عن الصفحة.

---

# ماذا تستفيد كـ Penetration Tester؟

عند رؤية CSP اسأل:

- هل توجد سياسة أصلًا؟
- هل هي صارمة أم متساهلة؟
- ما هي الـ Directives المستخدمة؟
- هل تم تقييد JavaScript؟
- هل توجد قيود على الاتصالات (`connect-src`)؟
- هل توجد تقارير (`report-to` أو `report-uri`)؟

هذا يساعدك على فهم البيئة الأمنية للتطبيق.

---

# CSP ليست بديلًا عن البرمجة الآمنة

قاعدة مهمة جدًا:

```
Input Validation
        +
Output Encoding
        +
Safe DOM APIs
        +
CSP
```

CSP **لا تصلح** الأخطاء البرمجية.

هي تقلل من آثارها.

---

# أخطاء شائعة

❌ الاعتقاد أن CSP تمنع جميع أنواع XSS.

❌ الاعتماد على CSP فقط.

❌ الخلط بين `default-src` و`script-src`.

❌ عدم فهم الفرق بين `nonce` و`hash`.

❌ استخدام سياسات عامة جدًا تجعل CSP أقل فاعلية.