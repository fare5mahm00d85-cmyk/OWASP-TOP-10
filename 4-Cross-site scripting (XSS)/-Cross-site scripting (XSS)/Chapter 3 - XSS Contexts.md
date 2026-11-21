

---
# مقدمة

أكبر غلط بيعمله أي مبتدئ هو إنه يجرب نفس الـ Payload في كل مكان.

مثلاً يجرب:

```
<script>alert(1)</script>
```

في أي Input.

ولما يفشل يقول:

> "الموقع محمى."

وده غالبًا غلط.

السبب الحقيقي هو إنك استخدمت Payload في **Context** غلط.

---

# ما هو الـ Context؟

الـ Context هو:

> **المكان الذي ستظهر فيه بيانات المستخدم داخل الصفحة.**

المتصفح لا يعامل كل مكان في HTML بنفس الطريقة.

كل Context له قواعد مختلفة.

---

## مثال

إذا كانت الصفحة

```
<h1>USERNAME</h1>
```

فأنت داخل

```
HTML Context
```

أما إذا كانت

```
<input value="USERNAME">
```

فأنت داخل

```
Attribute Context
```

أما إذا كانت

```
<script>

var name="USERNAME";

</script>
```

فأنت داخل

```
JavaScript Context
```

كل واحد يحتاج طريقة مختلفة للتعامل معه.

---

# لماذا Context مهم؟

لأن المتصفح أثناء الـ Parsing يغير طريقة تفسير البيانات حسب مكانها.

مثال

```
<div>Hello</div>
```

يفهمها كـ HTML.

لكن

```
var x="Hello";
```

يفهمها كـ JavaScript.

---

# أنواع الـ Contexts

```
HTML Context

Attribute Context

JavaScript Context

CSS Context

URL Context

Comment Context

SVG Context

Template Context

JSON Context

XML Context
```

---

# أول Context

# HTML Context

---

## الشكل

```
<div>USER INPUT</div>
```

---

أو

```
<h1>USER INPUT</h1>
```

---

أو

```
<p>USER INPUT</p>
```

---

هنا المتصفح يتوقع

```
HTML
```

وليس JavaScript مباشرة.

---

## الرسم

```
<div>

USER INPUT

</div>
```

---

## ماذا يستطيع المستخدم إدخال؟

يمكنه إدخال:

- نص
- عناصر HTML
- إذا لم تتم معالجة الإدخال بشكل صحيح، فقد يضيف عناصر جديدة إلى الصفحة.

---

## كيف تعرف أنك داخل HTML Context؟

إذا ظهر الإدخال بين وسمين.

مثال

```
<p>Hello USER</p>
```

---

وليس داخل خاصية.

---

# مثال

```
<h2>

Ahmed

</h2>
```

Ahmed هنا داخل HTML.

---

# أشهر الأماكن

- Comments
- Posts
- Reviews
- Search Results
- Profile Description

---

# ثاني Context

# Attribute Context

---

يعني البيانات داخل Attribute.

مثال

```
<input value="USER">
```

---

أو

```
<img alt="USER">
```

---

أو

```
<a title="USER">
```

---

هنا المستخدم ليس داخل الصفحة مباشرة.

بل داخل خاصية.

---

## الرسم

```
<input

value="USER"

>
```

---

# أنواع Attribute Context

---

## Quoted Attribute

```
value="USER"
```

---

أو

```
title='USER'
```

---

البيانات بين علامات اقتباس.

---

## Unquoted Attribute

```
value=USER
```

---

لا توجد علامات اقتباس.

---

وده Context مختلف.

---

# كيف تعرف أنك داخل Attribute؟

إذا رأيت

```
name="USER"
```

أو

```
src="USER"
```

أو

```
href="USER"
```

---

# أشهر الـ Attributes

```
value

title

alt

src

href

id

class

style

data-*
```

---

# ثالث Context

# JavaScript Context

---

وهو أخطر Context.

---

مثال

```
<script>

var name="USER";

</script>
```

---

هنا البيانات أصبحت داخل JavaScript.

---

## الرسم

```
<script>

var x="USER";

</script>
```

---

أو

```
let age="USER";
```

---

المتصفح هنا يتوقع JavaScript.

---

# أنواع JavaScript Context

---

## داخل String

```
var x="USER";
```

---

## داخل Single Quote

```
var x='USER';
```

---

## داخل Template Literal

```
var x=`USER`;
```

---

## داخل Number

```
var age=USER;
```

---

## داخل Boolean

```
var admin=USER;
```

---

## داخل Object

```
var obj={

name:"USER"

}
```

---

كل نوع مختلف.

---

# كيف تعرف أنك داخل JavaScript؟

إذا وجدت البيانات داخل

```
<script>
```

أو داخل ملف JavaScript.

---

# رابع Context

# CSS Context

---

مثال

```
<style>

body{

background:USER;

}

</style>
```

---

أو

```
<div

style="color:USER"

>
```

---

هنا البيانات أصبحت داخل CSS.

---

## الرسم

```
CSS

↓

Browser

↓

Style Engine
```

---

# أشهر الأماكن

```
color

background

font

display

position

width

height
```

---

# خامس Context

# URL Context

---

مثال

```
<a href="USER">
```

---

أو

```
<img src="USER">
```

---

أو

```
<iframe src="USER">
```

---

المتصفح يتوقع

```
URL
```

---

وليس HTML.

---

# الرسم

```
href

↓

URL

↓

Navigation
```

---

# أشهر الأماكن

```
href

src

action

formaction

poster
```

---

# سادس Context

# HTML Comment Context

---

مثال

```
<!--

USER

-->
```

---

البيانات داخل Comment.

---

المتصفح لا يعرضها.

---

لكن ما زال لها قواعد Parsing خاصة.

---

# الرسم

```
<!--

USER

-->
```

---

# سابع Context

# SVG Context

---

SVG

هو

```
Scalable Vector Graphics
```

---

وهو XML.

---

مثال

![](data:image/svg+xml;utf8,%3Csvg%3E%0A%0AUSER%0A%0A%3C%2Fsvg%3E)

---

المتصفح يعامل SVG بطريقة مختلفة عن HTML في بعض الجوانب.

---

# أشهر عناصر SVG

```
svg

animate

circle

rect

path

g

text

image
```

---

# ثامن Context

# Template Context

---

مثال

```
let msg=`USER`;
```

---

Template Literal

---

أو داخل Template Engines مثل:

- Handlebars
- Mustache
- EJS
- Twig

---

كل Framework له قواعده.

---

# تاسع Context

# JSON Context

---

مثال

```
{

"name":"USER"

}
```

---

هنا البيانات داخل JSON.

---

وليس HTML.

---

# عاشر Context

# XML Context

---

مثال

```
<user>

USER

</user>
```

---

ويستخدم في:

- RSS
- SOAP
- SVG
- XML APIs

---

# كيف أحدد الـ Context؟

اسأل نفسك:

---

هل أنا داخل Tag؟

```
<div>

USER

</div>
```

↓

HTML

---

هل أنا داخل Attribute؟

```
value="USER"
```

↓

Attribute

---

هل أنا داخل

```
<script>
```

↓

JavaScript

---

هل أنا داخل

```
<style>
```

↓

CSS

---

هل أنا داخل

```
href=""
```

↓

URL

---

هل أنا داخل

```
<!-- -->
```

↓

Comment

---

هل أنا داخل

![](data:image/svg+xml;utf8,%3Csvg%3E%3C%2Fsvg%3E)

↓

SVG

---

هل أنا داخل JSON؟

↓

JSON

---

هل أنا داخل XML؟

↓

XML

---

# مقارنة

|Context|المتصفح يتوقع|
|---|---|
|HTML|عناصر HTML أو نص|
|Attribute|قيمة خاصية|
|JavaScript|كود JavaScript|
|CSS|قواعد CSS|
|URL|عنوان URL|
|SVG|عناصر SVG|
|JSON|بيانات JSON|
|XML|عناصر XML|
|Comment|تعليق|

---

# لماذا يفشل نفس الـ Payload؟

لأن كل Context له قواعد Parsing مختلفة.

مثال توضيحي:

إذا كان الإدخال داخل:

```
<input value="USER">
```

فالمتصفح يتوقع **قيمة لخاصية**، وليس عنصر HTML جديد.

أما إذا كان الإدخال داخل:

```
<div>USER</div>
```

فالمتصفح يتعامل معه كجزء من محتوى الصفحة.

لهذا السبب، **لا توجد Payload واحدة تعمل في كل الأماكن**.

---

# طريقة التفكير الصحيحة

كلما وجدت نقطة إدخال، اسأل نفسك بالترتيب:

```
1. أين تُعرض البيانات؟

↓

2. ما هو الـ Context؟

↓

3. ماذا يتوقع المتصفح في هذا المكان؟

↓

4. كيف ستُفسر البيانات؟

↓

5. هل أحتاج إلى الخروج من هذا الـ Context أم لا؟
```

هذه الأسئلة أهم من حفظ أي Payload.

---

# أخطاء شائعة

❌ تجربة نفس الـ Payload في كل مكان.

❌ عدم التمييز بين HTML وAttribute.

❌ اعتبار كل `<script>` هو JavaScript Context للمستخدم، بينما قد لا يكون الإدخال بداخله أصلًا.

❌ الخلط بين URL Context وAttribute Context (فالـ URL غالبًا يكون **داخل Attribute** مثل `href` أو `src`، لكنه يتبع قواعد إضافية لأنه يمثل عنوانًا).