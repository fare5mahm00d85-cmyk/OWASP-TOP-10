
> **ما هي الـ Cookie؟**
> 
> الـ Cookie هي **ملف صغير أو قيمة نصية** يخزنها المتصفح للموقع، ويستخدمها الموقع لتذكر معلومات مثل تسجيل الدخول، اللغة، أو إعدادات المستخدم.

**مثال:**

```
Set-Cookie: session=abc123
```

بعدها سيرسل المتصفح الـ Cookie مع كل طلب لنفس الموقع:

```
Cookie: session=abc123
```

---

# 1) Session Cookie ⭐⭐⭐⭐⭐

## الشرح

هي Cookie **مؤقتة**.

تبقى موجودة طالما المتصفح مفتوح.

بمجرد غلق المتصفح تختفي.

---

## مثال

```
Set-Cookie: session=abc123
```

لاحظ أنه لا يوجد:

```
Expires
```

ولا

```
Max-Age
```

إذًا فهي Session Cookie.

---

## الاستخدام

- Login Session
- Authentication
- Shopping Cart

---

## في XSS

إذا كانت **ليست HttpOnly** فقد يستطيع JavaScript قراءتها:

```
console.log(document.cookie);
```

---

# 2) Persistent Cookie ⭐⭐⭐⭐⭐

## الشرح

هي Cookie يتم حفظها على الجهاز لفترة معينة.

تبقى حتى بعد إغلاق المتصفح.

---

## مثال

```
Set-Cookie: theme=dark;
Expires=Fri, 31 Dec 2027 23:59:59 GMT
```

أو

```
Set-Cookie: theme=dark;
Max-Age=3600
```

---

## الاستخدام

- Remember Me
- اللغة
- الثيم
- تفضيلات المستخدم

---

## الفرق

|النوع|بعد غلق المتصفح|
|---|---|
|Session|❌ تُحذف|
|Persistent|✅ تبقى|

---

# 3) HttpOnly ⭐⭐⭐⭐⭐

## الشرح

خاصية تمنع JavaScript من قراءة الـ Cookie.

---

بدون HttpOnly

```
Cookie:

session=abc123
```

يمكن قراءتها

```
console.log(document.cookie);
```

الناتج

```
session=abc123
```

---

مع HttpOnly

```
Set-Cookie: session=abc123;
HttpOnly
```

ثم

```
console.log(document.cookie);
```

لن تظهر هذه الـ Cookie في `document.cookie`.

---

## لماذا هي مهمة؟

تقلل خطر سرقة الـ Cookies باستخدام JavaScript إذا وُجدت ثغرة XSS، لأن السكربت لا يستطيع قراءة الـ Cookie الموسومة بـ `HttpOnly`.

> ملاحظة: وجود `HttpOnly` لا يمنع تنفيذ XSS نفسه، لكنه يمنع JavaScript من الوصول إلى هذه الـ Cookie.

---

# 4) Secure ⭐⭐⭐⭐⭐

## الشرح

يجعل الـ Cookie تُرسل فقط عند استخدام HTTPS.

---

بدون Secure

```
HTTP
```

ترسل Cookie.

---

```
HTTPS
```

ترسل Cookie.

---

مع Secure

```
Set-Cookie: session=abc123;
Secure
```

---

```
HTTP
```

❌ لا تُرسل.

---

```
HTTPS
```

✅ تُرسل.

---

## الفائدة

يحمي الـ Cookie من الإرسال عبر اتصال غير مشفر.

---

# 5) SameSite ⭐⭐⭐⭐⭐

## الشرح

يحدد هل يتم إرسال الـ Cookie عند الطلبات القادمة من مواقع أخرى (Cross-Site).

يستخدم بشكل أساسي لتقليل خطر **CSRF**.

---

يوجد ثلاثة أنواع.

---

# SameSite=Strict

```
Set-Cookie: session=abc123;
SameSite=Strict
```

### الشرح

أكثر الأنواع أمانًا.

لن تُرسل الـ Cookie مع أي طلب يأتي من موقع آخر.

---

مثال

أنت داخل

```
bank.com
```

ثم ضغطت رابطًا من

```
evil.com
```

إلى

```
bank.com
```

النتيجة

```
Cookie ❌
```

لن تُرسل.

---

# SameSite=Lax ⭐⭐⭐⭐⭐

```
Set-Cookie: session=abc123;
SameSite=Lax
```

### الشرح

الإعداد الافتراضي في معظم المتصفحات الحديثة.

يسمح بإرسال الـ Cookie في بعض الطلبات مثل الانتقال عبر رابط عادي (Top-Level Navigation)، لكنه يمنعها في كثير من الطلبات الأخرى القادمة من مواقع مختلفة.

---

مثال

```
evil.com
```

↓

يضغط المستخدم على رابط

↓

```
bank.com
```

قد تُرسل الـ Cookie حسب نوع الطلب وطريقة الانتقال.

---

# SameSite=None

```
Set-Cookie: session=abc123;
SameSite=None;
Secure
```

### الشرح

يسمح بإرسال الـ Cookie في جميع الطلبات، حتى Cross-Site.

**يشترط** استخدام الخاصية `Secure`.

---

# مقارنة SameSite

|النوع|Cross-Site|
|---|---|
|`Strict`|❌ يمنع الإرسال|
|`Lax`|⚠️ يسمح في حالات محددة|
|`None`|✅ يسمح دائمًا (مع `Secure`)|

---

# مثال كامل

```
Set-Cookie:
session=abc123;
HttpOnly;
Secure;
SameSite=Lax
```

معنى ذلك:

- `session=abc123` → قيمة الـ Cookie.
- `HttpOnly` → لا يمكن لـ JavaScript قراءتها.
- `Secure` → تُرسل فقط عبر HTTPS.
- `SameSite=Lax` → تُرسل في بعض الطلبات بين المواقع وتُمنع في أخرى وفق سياسة Lax.

---

# كيف ترى الـ Cookies في Burp Suite؟

ستجدها في الاستجابة:

```
HTTP/2 200 OK

Set-Cookie:
session=abc123;
HttpOnly;
Secure;
SameSite=Lax
```

وفي الطلب التالي:

```
GET / HTTP/2

Cookie:
session=abc123
```

---

# 🎯 ملخص للحفظ

| الخاصية               | الوظيفة                                     | الأهمية |
| --------------------- | ------------------------------------------- | ------- |
| **Session Cookie**    | تُحذف عند غلق المتصفح                       | ⭐⭐⭐⭐⭐   |
| **Persistent Cookie** | تبقى حتى انتهاء مدتها                       | ⭐⭐⭐⭐    |
| **HttpOnly**          | تمنع JavaScript من قراءة الـ Cookie         | ⭐⭐⭐⭐⭐   |
| **Secure**            | تُرسل فقط عبر HTTPS                         | ⭐⭐⭐⭐⭐   |
| **SameSite=Strict**   | يمنع إرسال الـ Cookie في الطلبات Cross-Site | ⭐⭐⭐⭐⭐   |
| **SameSite=Lax**      | يسمح في حالات محددة ويمنع في أخرى           | ⭐⭐⭐⭐⭐   |
| **SameSite=None**     | يسمح دائمًا ويتطلب `Secure`                 | ⭐⭐⭐⭐    |