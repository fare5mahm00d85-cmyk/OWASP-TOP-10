
---
## 1. يعني إيه Clickjacking؟

**Clickjacking** أو **UI Redressing** هي ثغرة بيحاول فيها المهاجم يخدع المستخدم علشان يضغط على عنصر معين، بينما النقرة الحقيقية بتوصل لعنصر مختلف في موقع آخر.

بمعنى أبسط:

> المستخدم فاكر إنه ضغط على زر A، لكن هو في الحقيقة ضغط على زر B.

مثال:

```text
المستخدم شايف:

        🎁 Click to Win
             ↓
          [ CLICK ]

لكن في الحقيقة:

        الموقع الحقيقي
             ↓
       [ Delete Account ]
```

المستخدم يضغط على `CLICK`، لكن النقرة تصل إلى `Delete Account`.

---

# 2. الفكرة الأساسية للـ Clickjacking

أهم حاجة تفهمها هي إن المهاجم بيحاول يخلي **صفحة الضحية تظهر داخل صفحة المهاجم**.

وده غالبًا باستخدام:

```html
<iframe src="https://victim.com"></iframe>
```

الـ `iframe` يسمح بعرض صفحة من موقع داخل صفحة أخرى.

مثلاً:

```html
<iframe src="https://example.com/account"></iframe>
```

هنا صفحة:

```text
example.com/account
```

ممكن تظهر داخل موقع المهاجم.

---

# 3. شكل الهجوم

الفكرة تكون تقريبًا:

```text
        Attacker Website
┌───────────────────────────────┐
│                               │
│      🎁 WIN A FREE PHONE      │
│                               │
│          [ CLICK ]            │
│                               │
│   ┌───────────────────────┐   │
│   │                       │   │
│   │    Victim Website     │   │
│   │                       │   │
│   │   [Delete Account]    │   │
│   │                       │   │
│   └───────────────────────┘   │
│                               │
└───────────────────────────────┘
```

المستخدم يعتقد أنه يضغط:

```text
[ CLICK ]
```

لكن الـ `iframe` يكون في نفس مكان النقرة.

فتصل النقرة إلى:

```text
[Delete Account]
```

وهنا حصل Clickjacking.

---

# 4. ليه المهاجم محتاج المستخدم؟

Clickjacking غالبًا يعتمد على **User Interaction**.

يعني المهاجم مش بالضرورة ينفذ العملية بنفسه.

هو بيعمل صفحة تخدع المستخدم.

مثلاً:

```text
Attacker
   ↓
Fake Website
   ↓
Tricks Victim
   ↓
Victim Clicks
   ↓
Sensitive Action
```

---

# 5. ليه الـ Login مهم؟

افترض إن الموقع فيه:

```text
https://victim.com/account
```

والمستخدم عامل Login بالفعل.

المتصفح عنده Session للمستخدم.

لما المستخدم يتفاعل مع الموقع، المتصفح ممكن يستخدم جلسة المستخدم حسب سياسات الـ cookies والمتصفح.

لذلك لو المهاجم نجح في خداع المستخدم للنقر على Action حساس، العملية قد تُنفذ بصلاحيات المستخدم.

مثال:

```text
User Logged In
       ↓
Session
       ↓
Victim Website
       ↓
Sensitive Action
```

---

# 6. أهم عنصر: iframe

مثال:

```html
<iframe src="https://victim.com"></iframe>
```

معناه:

> اعرض صفحة victim.com داخل الصفحة الحالية.

وده هو الأساس في كثير من هجمات Clickjacking.

---

# 7. إخفاء الـ iframe

المهاجم ممكن يجعل الـ iframe شفافًا أو شبه شفاف باستخدام CSS.

مثال تعليمي:

```css
iframe {
    opacity: 0.1;
}
```

أو:

```css
iframe {
    opacity: 0;
}
```

الهدف:

```text
المستخدم يرى:
Fake Button

لكن المتصفح يحتوي:
Real Button
```

---

# 8. Overlay

الـ **Overlay** هو عنصر يتم وضعه فوق عنصر آخر.

مثلاً:

```text
Fake Button
     ↓
┌───────────────┐
│   CLICK HERE  │
└───────────────┘

       فوق

┌───────────────┐
│ Delete Account│
└───────────────┘
```

المستخدم يشوف الزر المزيف، لكن العنصر الموجود في طبقة الـ iframe قد يستقبل النقرة.

---

# 9. CSS المستخدم في الفكرة

من الخصائص المهمة:

```css
position
```

لتحديد مكان العنصر.

```css
opacity
```

لتقليل أو إخفاء ظهوره.

```css
z-index
```

للتحكم في ترتيب الطبقات.

مثال تعليمي:

```html
<style>
iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 1000px;
    height: 800px;
    opacity: 0.1;
}

button {
    position: absolute;
    top: 500px;
    left: 400px;
}
</style>
```

الفكرة مش إنك تحفظ الكود.

المهم تفهم:

```text
position → مكان العنصر
opacity → درجة ظهوره
z-index → ترتيب الطبقات
iframe → صفحة الضحية
```

---

# 10. Clickjacking vs XSS

دي نقطة مهمة جدًا.

## XSS

المهاجم يحاول إدخال JavaScript وتشغيله داخل سياق الموقع.

```text
Attacker
   ↓
Inject JavaScript
   ↓
Victim Website
   ↓
JavaScript Executes
```

## Clickjacking

المهاجم يخدع المستخدم في مكان النقرة.

```text
Attacker Website
       ↓
     iframe
       ↓
Victim Website
       ↓
Victim Clicks
       ↓
Unexpected Action
```

إذن:

```text
XSS
= تنفيذ كود

Clickjacking
= خداع النقرة
```

---

# 11. Clickjacking vs CSRF

الاتنين ممكن يتشابهوا، لكن مش نفس الحاجة.

## CSRF

المهاجم يحاول جعل المتصفح يرسل Request نيابة عن المستخدم.

مثلاً:

```http
POST /change-email
```

## Clickjacking

المهاجم يحاول خداع المستخدم ليضغط على Action معين.

```text
User thinks:
"Click to win"

Actually:
"Change Email"
```

الخلاصة:

```text
CSRF → التركيز على إرسال Request غير مقصود

Clickjacking → التركيز على خداع المستخدم في الـ UI والنقرة
```

---

# 12. إزاي أكتشف Clickjacking؟

في اختبار أمني مصرح به، ابدأ بالـ HTTP Response.

مثلاً:

```http
HTTP/1.1 200 OK
Content-Type: text/html
```

وابحث عن:

```http
X-Frame-Options
```

وكذلك:

```http
Content-Security-Policy
```

وبداخل CSP ابحث عن:

```text
frame-ancestors
```

---

# 13. X-Frame-Options

Header يستخدم للتحكم في إمكانية وضع الصفحة داخل iframe.

### DENY

```http
X-Frame-Options: DENY
```

معناه:

> لا تسمح للصفحة أن تظهر داخل iframe.

---

### SAMEORIGIN

```http
X-Frame-Options: SAMEORIGIN
```

معناه:

> السماح بالـ framing من نفس الـ origin فقط.

مثلاً:

```text
example.com
```

يسمح لمحتوى من نفس الـ origin، لكن موقعًا خارجيًا مثل:

```text
attacker.com
```

لا يُسمح له بذلك وفق هذه السياسة.

---

# 14. CSP

CSP =

**Content Security Policy**

وهي آلية أمنية للتحكم في مصادر وأنواع المحتوى التي يسمح بها الموقع.

ومن أهم الـ directives المرتبطة بالـ Clickjacking:

```text
frame-ancestors
```

مثال:

```http
Content-Security-Policy: frame-ancestors 'none'
```

معناه:

> لا تسمح لأي موقع بوضع الصفحة داخل frame.

---

# 15. frame-ancestors

مثال:

```http
Content-Security-Policy: frame-ancestors 'none'
```

يعني:

```text
أي موقع
   ↓
❌ لا يستطيع framing
```

---

مثال:

```http
Content-Security-Policy: frame-ancestors 'self'
```

يعني:

```text
نفس الموقع
   ↓
✅ مسموح

موقع خارجي
   ↓
❌ غير مسموح
```

---

وممكن السماح لمصدر محدد:

```http
Content-Security-Policy: frame-ancestors https://trusted.example
```

---

# 16. أهم Headers تحفظهم

```text
X-Frame-Options
        ↓
DENY
SAMEORIGIN
```

و:

```text
Content-Security-Policy
        ↓
frame-ancestors
```

ودي من أهم الحاجات اللي تبحث عنها أثناء اختبار Clickjacking.

---

# 17. هل غياب X-Frame-Options يعني أن الموقع Vulnerable؟

❌ لا.

دي نقطة مهمة جدًا.

لو شفت:

```text
No X-Frame-Options
```

ده معناه فقط إن الـ header ده غير موجود.

لكن لازم تشوف هل فيه:

```text
CSP frame-ancestors
```

وبعد كده تختبر هل الصفحة فعلًا قابلة للـ framing وهل يوجد Action حساس يمكن استغلاله عن طريق click.

يعني:

```text
Missing X-Frame-Options
        ↓
Potential Indicator
        ↓
مش دليل نهائي لوحده
```

---

# 18. شروط وجود Clickjacking قابل للاستغلال

عادةً نبحث عن مجموعة عوامل:

```text
1. الصفحة قابلة للـ framing
        +
2. يوجد Action حساس
        +
3. الـ Action يعتمد على User Click
        +
4. المستخدم لديه الصلاحية
        +
5. يمكن خداع المستخدم في مكان النقرة
```

لو كل ده متوفر، يبقى عندنا سيناريو Clickjacking عملي.

---

# 19. أمثلة على Actions حساسة

ممكن يكون:

```text
Delete Account
Change Email
Change Password
Change Settings
Enable Feature
Disable Feature
Transfer Action
Like
Share
Follow
```

طبعًا التأثير الحقيقي يعتمد على التطبيق نفسه.

---

# 20. Clickjacking مش لازم يكون Delete Account

مش شرط.

الفكرة مش مرتبطة بـ Action معين.

أي Action حساس يعتمد على click ممكن يكون هدفًا.

مثلاً:

```text
Click
  ↓
Confirm
  ↓
Sensitive Operation
```

---

# 21. أنواع Clickjacking

## Basic Clickjacking

أبسط شكل:

```text
iframe
   ↓
Hidden/Transparent
   ↓
Fake UI
   ↓
Victim Click
```

---

## Likejacking

خداع المستخدم للضغط على:

```text
Like
Share
Follow
```

---

## Cursorjacking

خداع المستخدم بالنسبة لمكان المؤشر أو النقرة.

---

## Double-clickjacking

نوع أكثر تعقيدًا يعتمد على توقيت النقرات المزدوجة وتغيير العنصر الموجود تحت المؤشر بين النقرات.

في البداية ركز على:

```text
Basic Clickjacking
```

---

# 22. Clickjacking و SameSite Cookies

`SameSite` خاص بالـ Cookies.

أمثلة:

```text
SameSite=Strict
SameSite=Lax
SameSite=None
```

وهو يتحكم في بعض حالات إرسال الـ cookies في السياقات cross-site.

لكن:

> SameSite مش بديل عن حماية Clickjacking.

الحماية الأساسية ضد framing تكون عن طريق:

```text
CSP frame-ancestors
```

و/أو:

```text
X-Frame-Options
```

---

# 23. طريقة اختبار Clickjacking في Burp

افتح:

```text
Burp Suite
   ↓
Proxy
   ↓
HTTP History
```

اختار الصفحة المهمة.

ابحث في الـ Response عن:

```text
X-Frame-Options
```

ثم:

```text
Content-Security-Policy
```

ثم:

```text
frame-ancestors
```

بعد ذلك اسأل:

```text
هل الصفحة قابلة للـ framing؟
```

ثم:

```text
هل يوجد Action حساس؟
```

ثم:

```text
هل الـ Action يحتاج Click؟
```

---

# 24. طريقة التفكير في PortSwigger Lab

لما تدخل Clickjacking Lab:

```text
                    Lab
                     ↓
             افتح الصفحة المهمة
                     ↓
              راقب الـ Response
                     ↓
        ┌────────────┴────────────┐
        ↓                         ↓
X-Frame-Options            CSP frame-ancestors
        ↓                         ↓
     موجود؟                    موجود؟
        ↓                         ↓
   هل يمنع framing؟        هل يمنع framing؟
                     ↓
              ابحث عن Action
                     ↓
              Sensitive Action
                     ↓
                User Click
                     ↓
              Build iframe
                     ↓
             Align the target
                     ↓
               Test the click
```

---

# 25. مثال ذهني بسيط جدًا

تخيل عندك:

```text
Victim Website

[ Delete Account ]
```

المهاجم يعمل:

```text
Attacker Website

[ Click here to claim prize ]
```

ويضع صفحة الضحية خلف الزر.

المستخدم يرى:

```text
[ Click here to claim prize ]
```

لكن النقرة الحقيقية تكون:

```text
[ Delete Account ]
```

إذن:

```text
Visual Target
     ≠
Real Target
```

ودي أهم فكرة في Clickjacking.

---

# 26. الحماية من Clickjacking

أفضل شيء تفكر فيه كـ Developer:

### CSP

```http
Content-Security-Policy: frame-ancestors 'none'
```

لو الصفحة لا تحتاج framing.

أو:

```http
Content-Security-Policy: frame-ancestors 'self'
```

لو محتاج framing من نفس الموقع.

---

### X-Frame-Options

```http
X-Frame-Options: DENY
```

أو:

```http
X-Frame-Options: SAMEORIGIN
```

بحسب احتياجات التطبيق.

---

# 27. حماية إضافية للـ Sensitive Actions

حتى مع وجود حماية framing، العمليات الحساسة يفضل أن يكون لها حماية إضافية.

مثلاً:

```text
Delete Account
       ↓
Re-authentication
       ↓
Confirm Password
       ↓
Delete
```

أو:

```text
Sensitive Action
       ↓
Confirmation
       ↓
Action
```

---

# 28. Clickjacking Cheat Sheet

```text
Clickjacking
= خداع المستخدم للنقر على عنصر مختلف عن الذي يراه.

الأساس:
iframe + Overlay + User Interaction

أهم شيء في الاختبار:
X-Frame-Options
CSP
frame-ancestors

X-Frame-Options:
DENY
SAMEORIGIN

CSP:
frame-ancestors 'none'
frame-ancestors 'self'

الفرق:
XSS → تنفيذ JavaScript
CSRF → إرسال Request غير مقصود
Clickjacking → خداع المستخدم في الـ UI والنقرة

غياب X-Frame-Options:
ليس دليلًا كافيًا لوحده.

لازم نتحقق من:
Framing
+
Sensitive Action
+
User Click
+
User Privileges
+
قابلية خداع المستخدم

الحماية:
CSP frame-ancestors
+
X-Frame-Options
+
تأكيد/إعادة مصادقة العمليات الحساسة
```

# 🎯 أهم 5 حاجات تحفظهم

```text
1️⃣ Clickjacking = اختطاف/خداع النقرة.

2️⃣ iframe هو العنصر الأساسي في كثير من سيناريوهات Clickjacking.

3️⃣ المستخدم يرى عنصرًا، لكن النقرة تصل لعنصر آخر.

4️⃣ أهم Headers:
   X-Frame-Options
   CSP frame-ancestors

5️⃣ Missing Header ≠ Vulnerability مباشرة.
   لازم تثبت إن الصفحة قابلة للـ framing
   ويوجد Action حساس قابل للاستغلال.
```