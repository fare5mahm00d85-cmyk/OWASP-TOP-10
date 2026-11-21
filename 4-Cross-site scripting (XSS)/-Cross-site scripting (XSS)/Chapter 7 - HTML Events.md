
---
# مقدمة

صفحة الويب ليست مجرد HTML ثابت.

هى تتفاعل مع المستخدم.

مثلاً:

- تضغط على زر.
- تحرك الماوس.
- تكتب فى Input.
- ترسل Form.
- تفتح الصفحة.

كل هذه تسمى

```
Events
```

---

# ما هو Event؟

الـ Event هو:

> **حدث يقع داخل الصفحة ويستطيع JavaScript الاستجابة له.**

مثال:

```
المستخدم ضغط زر

↓

حدث Click

↓

JavaScript ينفذ الدالة المرتبطة
```

---

# كيف تعمل Events؟

الرسم:

```
User

↓

Mouse

↓

Browser

↓

Event

↓

JavaScript

↓

Response
```

---

# مثال

عندما تضغط على زر:

```
Button

↓

Click

↓

JavaScript

↓

يفتح نافذة أو يرسل البيانات
```

---

# Event Driven Programming

JavaScript لغة تعتمد على الأحداث.

لا تعمل بشكل مستمر.

بل تنتظر حدوث Event.

```
Waiting...

↓

حدث Event

↓

تشغيل Function

↓

Waiting...
```

---

# أنواع الأحداث

```
Mouse Events

Keyboard Events

Form Events

Focus Events

Clipboard Events

Window Events

Animation Events

Pointer Events

Drag & Drop Events

Touch Events
```

---

# أولاً

# Mouse Events

أحداث خاصة بالماوس.

---

## Click

يحدث عند الضغط على العنصر.

---

## Double Click

يحدث عند الضغط مرتين.

---

## Mouse Down

يحدث عند نزول زر الماوس.

---

## Mouse Up

يحدث عند رفع زر الماوس.

---

## Mouse Move

يحدث أثناء تحريك الماوس.

---

## Mouse Enter

عند دخول مؤشر الماوس داخل العنصر.

---

## Mouse Leave

عند خروج المؤشر.

---

## Mouse Over

عند المرور فوق العنصر.

---

## Mouse Out

عند مغادرة العنصر.

---

الرسم

```
Mouse

↓

Move

↓

Enter

↓

Click

↓

Leave
```

---

# ثانياً

# Keyboard Events

---

## Key Down

يحدث عند الضغط على مفتاح.

---

## Key Up

يحدث عند رفع الإصبع.

---

## Key Press

حدث قديم وأصبح استخدامه أقل شيوعًا مقارنةً بـ `keydown` و`keyup`.

---

الرسم

```
Keyboard

↓

KeyDown

↓

KeyUp
```

---

# ثالثاً

# Form Events

---

## Submit

يحدث عند إرسال النموذج.

---

## Reset

يحدث عند إعادة تعيين النموذج.

---

## Input

يحدث كلما تغيرت قيمة الحقل.

---

## Change

يحدث بعد انتهاء التغيير (مثل مغادرة الحقل أو اختيار قيمة جديدة حسب نوع العنصر).

---

الرسم

```
User

↓

Typing

↓

Input

↓

Submit
```

---

# رابعاً

# Focus Events

---

## Focus

عندما يصبح العنصر نشطًا.

---

## Blur

عند فقدان التركيز.

---

## Focus In

دخول التركيز.

---

## Focus Out

خروج التركيز.

---

الرسم

```
Input

↓

Focus

↓

Typing

↓

Blur
```

---

# خامساً

# Window Events

---

## Load

يحدث بعد تحميل الصفحة بالكامل والموارد التابعة لها.

---

## DOMContentLoaded

يحدث بعد اكتمال بناء الـ DOM، وقد يكون قبل انتهاء تحميل الصور والملفات الأخرى.

---

## Resize

عند تغيير حجم النافذة.

---

## Scroll

عند تحريك الصفحة.

---

## Unload

كان يستخدم عند مغادرة الصفحة، لكن الاعتماد عليه أصبح محدودًا في المتصفحات الحديثة.

---

الرسم

```
Open Page

↓

DOM Ready

↓

Resources Loaded

↓

Load Event
```

---

# سادساً

# Clipboard Events

---

## Copy

عند النسخ.

---

## Cut

عند القص.

---

## Paste

عند اللصق.

---

# سابعاً

# Drag & Drop Events

---

## Drag

عند سحب عنصر.

---

## Drag Start

بداية السحب.

---

## Drag End

نهاية السحب.

---

## Drop

عند إفلات العنصر.

---

الرسم

```
Drag

↓

Move

↓

Drop
```

---

# ثامناً

# Pointer Events

توحد التعامل مع:

- Mouse
- Touch
- Pen

---

أهمها:

```
Pointer Down

Pointer Up

Pointer Move

Pointer Enter

Pointer Leave
```

---

# تاسعاً

# Touch Events

خاصة بالهواتف.

---

تشمل:

```
Touch Start

Touch End

Touch Move

Touch Cancel
```

---

# عاشراً

# Animation Events

---

## Animation Start

بداية الحركة.

---

## Animation End

نهاية الحركة.

---

## Animation Iteration

كل مرة تتكرر فيها الحركة.

---

# Event Target

كل Event له عنصر مستهدف.

مثال:

```
Button

↓

Click

↓

Target = Button
```

---

# Event Listener

بدلاً من كتابة الكود داخل HTML، يفضل في JavaScript ربط الأحداث باستخدام مستمعات الأحداث (Event Listeners)، لأنها تجعل الكود أكثر تنظيمًا وأسهل في الصيانة.

---

# Event Propagation

من أهم المفاهيم.

عندما يحدث Event.

قد ينتقل بين العناصر.

---

مثال

```
BODY

↓

DIV

↓

BUTTON
```

ضغطت Button.

من الذى يستقبل Event؟

الإجابة تعتمد على مرحلة الانتشار.

---

# Capturing Phase

```
BODY

↓

DIV

↓

BUTTON
```

من الخارج للداخل.

---

# Bubbling Phase

```
BUTTON

↓

DIV

↓

BODY
```

من الداخل للخارج.

---

# Event Object

كل Event يحمل معلومات.

مثل:

- نوع الحدث.
- العنصر المستهدف.
- الوقت.
- إحداثيات الماوس (في أحداث الماوس).
- المفتاح المضغوط (في أحداث لوحة المفاتيح).

---

# Default Action

بعض العناصر لها سلوك افتراضي.

مثلاً

الرابط:

```
Click

↓

Open Link
```

والنموذج:

```
Submit

↓

Send Form
```

يمكن للمطور التحكم في هذا السلوك عند الحاجة.

---

# أشهر الأحداث

|Event|الاستخدام|
|---|---|
|click|الضغط|
|dblclick|ضغطتين|
|input|الكتابة|
|change|تغيير القيمة|
|submit|إرسال نموذج|
|focus|الحصول على التركيز|
|blur|فقدان التركيز|
|load|انتهاء تحميل الصفحة|
|DOMContentLoaded|اكتمال بناء DOM|
|resize|تغيير حجم النافذة|
|scroll|تحريك الصفحة|
|copy|النسخ|
|paste|اللصق|
|drag|السحب|
|drop|الإفلات|
|pointerdown|بداية التفاعل بالمؤشر|
|pointerup|نهاية التفاعل بالمؤشر|
|touchstart|بداية اللمس|
|touchend|نهاية اللمس|

---

# كيف تستفيد من Events كـ Penetration Tester؟

لا تحفظ أسماء الأحداث فقط.

اسأل نفسك:

- متى يحدث هذا الحدث؟
- هل يحتاج تفاعلًا من المستخدم؟
- هل يعمل تلقائيًا؟
- هل هو مرتبط بعنصر معين؟
- هل يمكن رؤيته في DevTools؟

هذا الفهم يساعدك على تحليل سلوك الصفحة أثناء مراجعة التطبيقات.

---

# أخطاء شائعة

❌ الاعتقاد أن كل الأحداث تعمل تلقائيًا.

❌ الخلط بين `input` و`change`.

❌ الخلط بين `load` و`DOMContentLoaded`.

❌ نسيان أن بعض الأحداث مرتبطة بعناصر محددة فقط.

❌ عدم فهم الفرق بين Capturing وBubbling.