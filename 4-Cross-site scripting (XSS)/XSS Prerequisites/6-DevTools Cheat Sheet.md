
---
 > **ما هي DevTools؟**
> 
> هي أدوات المطور الموجودة داخل المتصفح (Chrome / Edge / Firefox) وتستخدم لفحص صفحات الويب، وتشغيل JavaScript، وتحليل طلبات HTTP، ورؤية الـ Cookies والـ Local Storage.

**فتح DevTools:**

- `F12`
- أو `Ctrl + Shift + I`
- أو كليك يمين → **Inspect**

---

# 1) Inspect ⭐⭐⭐⭐⭐

## ما هو؟

هو أسرع طريقة لفتح DevTools على عنصر معين داخل الصفحة.

### الاستخدام

- معرفة مكان العنصر داخل HTML.
- معرفة الـ ID والـ Class.
- معرفة الـ Attributes.
- تعديل الصفحة مؤقتًا.

---

### مثال

لديك زر:

```
<button id="login-btn">Login</button>
```

اعمل **Inspect** على الزر.

سترى:

```
<button id="login-btn">Login</button>
```

ومن هنا تستطيع معرفة:

- الـ ID
- الـ Class
- نوع العنصر
- الأحداث (Events) إن وجدت

---

## متى تستخدمه في XSS؟

- معرفة المكان الذي تُعرض فيه البيانات (HTML Context أو Attribute Context).
- معرفة العناصر التي تريد استهدافها بـ JavaScript.
- تعديل الصفحة مؤقتًا لاختبار شيء.

---

# 2) Elements ⭐⭐⭐⭐⭐

## ما هو؟

يعرض **الـ DOM الحالي** للصفحة.

> أي تغييرات ينفذها JavaScript ستظهر هنا حتى لو لم تكن موجودة في ملف HTML الأصلي.

---

### ماذا تستطيع أن تفعل؟

- مشاهدة HTML.
- مشاهدة CSS.
- تعديل العناصر.
- حذف العناصر.
- إضافة عناصر جديدة.

---

### مثال

قبل التعديل:

```
<h1>Hello</h1>
```

عدّلها داخل Elements إلى:

```
<h1>Cyber Security</h1>
```

ستتغير الصفحة مباشرة، لكن التعديل **مؤقت** ويختفي عند إعادة تحميل الصفحة.

---

### في XSS

يمكنك تجربة:

```
<img src=x>
```

أو تعديل عنصر لترى كيف يتصرف JavaScript مع الصفحة.

---

# 3) Console ⭐⭐⭐⭐⭐

## ما هو؟

مكان تنفيذ أوامر JavaScript مباشرة.

---

### مثال

```
alert("Hello")
```

---

### مثال

```
console.log(document.title)
```

يعرض عنوان الصفحة.

---

### مثال

```
document.cookie
```

يعرض الـ Cookies التي يمكن لـ JavaScript الوصول إليها (غير `HttpOnly`).

---

### مثال

```
location.href
```

يعرض الرابط الحالي.

---

### مثال

```
document.querySelector("h1")
```

يعرض أول عنصر `<h1>`.

---

### مثال

```
document.getElementById("username").value
```

يعرض قيمة حقل الإدخال.

---

## لماذا Console مهم؟

- اختبار JavaScript بسرعة.
- تجربة أكواد XSS في بيئة الاختبار.
- قراءة الأخطاء (Errors).
- طباعة المتغيرات.

---

# 4) Network ⭐⭐⭐⭐⭐

## ما هو؟

يعرض جميع طلبات HTTP التي يرسلها المتصفح.

---

### ماذا سترى؟

- Requests
- Responses
- Headers
- Cookies
- Status Codes
- Request Body
- Response Body

---

### مثال

تسجل دخولًا.

ستجد طلبًا مثل:

```
POST /login
```

اضغط عليه.

---

سترى:

### Headers

```
Host: example.com

Cookie: session=abc123
```

---

### Request Body

```
username=admin

password=123
```

---

### Response

```
HTTP/2 200 OK
```

---

### لماذا هو مهم؟

ستعرف:

- هل يستخدم GET أم POST؟
- ما هي الـ Headers؟
- هل توجد Cookies؟
- هل يوجد Redirect؟
- ما هي الاستجابة؟

---

# 5) Sources ⭐⭐⭐⭐

## ما هو؟

يعرض ملفات الموقع.

مثل:

- HTML
- CSS
- JavaScript

---

### ماذا يفيد؟

يمكنك قراءة ملفات JavaScript.

مثال

```
function login(){

...

}
```

---

### أيضًا

يمكنك وضع Breakpoint.

---

## Breakpoint

هو إيقاف تنفيذ JavaScript عند سطر معين.

---

مثال

```
let x=5;

let y=10;

let z=x+y;
```

ضع Breakpoint عند

```
let z=x+y;
```

ثم نفذ الصفحة.

سيتوقف التنفيذ ويمكنك معرفة قيم المتغيرات.

---

## لماذا Sources مهم؟

- قراءة ملفات JavaScript.
- فهم منطق الموقع.
- تتبع تنفيذ الكود.
- تحليل DOM-Based XSS.

---

# 6) Application ⭐⭐⭐⭐⭐

> **من أهم تبويبات DevTools لمجال Web Security.**

يعرض كل بيانات التخزين الخاصة بالموقع.

---

## أولًا: Cookies ⭐⭐⭐⭐⭐

ستجد

```
Application

↓

Cookies

↓

example.com
```

---

ستظهر بيانات مثل:

|Name|Value|HttpOnly|Secure|SameSite|
|---|---|---|---|---|
|session|abc123|✅|✅|Lax|

---

### تستطيع معرفة

- اسم الـ Cookie.
- قيمتها.
- هل هي HttpOnly؟
- هل هي Secure؟
- ما هو SameSite؟

---

## ثانيًا: Local Storage ⭐⭐⭐⭐⭐

ستجد

```
Application

↓

Local Storage

↓

example.com
```

---

مثال

|Key|Value|
|---|---|
|username|fares|
|theme|dark|

---

### في Console

```
localStorage.getItem("username")
```

---

### إضافة قيمة

```
localStorage.setItem("token","123")
```

ثم ستظهر مباشرة داخل Application.

---

## ثالثًا: Session Storage

يشبه Local Storage.

لكن البيانات تُحذف عند غلق التبويب.

---

### مثال

```
sessionStorage.setItem("user","Ali");
```

---

## رابعًا: IndexedDB ⭐⭐⭐

قاعدة بيانات داخل المتصفح.

بعض المواقع تخزن فيها بيانات كبيرة.

---

## خامسًا: Cache Storage ⭐⭐⭐

يعرض الملفات المخزنة بواسطة Service Workers.

---

# مقارنة بين Local Storage و Session Storage

|الخاصية|Local Storage|Session Storage|
|---|---|---|
|يبقى بعد غلق المتصفح|✅|❌|
|السعة أكبر من Cookies|✅|✅|
|يُرسل مع كل Request|❌|❌|
|يمكن الوصول إليه بـ JavaScript|✅|✅|

---

# مقارنة بين Cookies و Local Storage

|الخاصية|Cookies|Local Storage|
|---|---|---|
|تُرسل مع كل Request|✅|❌|
|يمكن جعلها HttpOnly|✅|❌|
|تستخدم للجلسات (Sessions)|✅|أحيانًا|
|الوصول عبر JavaScript|إذا لم تكن `HttpOnly`|دائمًا|

---

# كيف تستخدم DevTools أثناء حل Labs؟

1. افتح الموقع.
2. افتح **Network** وسجل الطلبات.
3. افتح **Elements** لمعرفة مكان ظهور البيانات.
4. استخدم **Console** لاختبار JavaScript.
5. افتح **Application** لمراجعة Cookies وLocal Storage وSession Storage.
6. استخدم **Sources** إذا احتجت تحليل ملفات JavaScript أو تتبع تنفيذها.