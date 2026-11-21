
---
## 1. يعني إيه JSONP؟

**JSONP = JSON with Padding**

هي طريقة قديمة كانت بتستخدم علشان تسمح لموقع يطلب بيانات من **Domain مختلف** باستخدام JavaScript.

المشكلة الأساسية اللي كانت بتحاول تحلها هي:

**Same-Origin Policy (SOP)**

الـBrowser بيمنع JavaScript من موقع معين إنه يقرأ بيانات من Origin مختلف بشكل مباشر.

مثال:

```text
https://siteA.com
```

يحاول يقرأ:

```text
https://siteB.com/api
```

هنا بيظهر موضوع الـCross-Origin.

---

## 2. المشكلة مع Same-Origin Policy

الـBrowser عنده قاعدة أمنية اسمها:

**Same-Origin Policy**

يعني JavaScript من:

```text
https://example.com
```

مش المفروض يقدر يقرأ Response من:

```text
https://another.com
```

إلا لو فيه آلية تسمح بده، مثل CORS.

---

# 3. طيب JSONP بيعمل إيه؟

المطورين اكتشفوا إن الـBrowser بيسمح للصفحة بتحميل JavaScript من Domain مختلف باستخدام:

```html
<script src="https://example.com/file.js"></script>
```

فاستغلوا الـ`script` في جلب البيانات من Domain مختلف.

بدل ما الـServer يرجع JSON فقط:

```json
{
    "name": "Fares"
}
```

يرجع JavaScript:

```javascript
callback({
    "name": "Fares"
});
```

وهنا ظهر اسم:

**JSONP**

لأننا أخذنا JSON ووضعناه داخل Function.

---

# 4. JSON vs JSONP

### JSON:

```json
{
    "name": "Fares",
    "age": 21
}
```

ده مجرد Data.

### JSONP:

```javascript
callback({
    "name": "Fares",
    "age": 21
});
```

ده JavaScript قابل للتنفيذ.

والـ:

```text
callback(...)
```

هو الـPadding.

---

# 5. مثال كامل

عندنا Server:

```text
https://api.example.com/user
```

والصفحة تعمل:

```html
<script src="https://api.example.com/user?callback=handleData"></script>
```

الـServer يرجع:

```javascript
handleData({
    "name": "Fares",
    "role": "user"
});
```

وفي الصفحة:

```javascript
function handleData(data) {
    console.log(data.name);
}
```

الـBrowser ينفذ الـResponse كـJavaScript.

والنتيجة:

```text
Fares
```

---

# 6. ليه `<script>` مهم؟

لأن:

```html
<script src="https://other-site.com/file.js"></script>
```

مسموح له بتحميل JavaScript من Origin مختلف.

JSONP استغل السلوك ده.

الفكرة:

```text
Browser
   ↓
<script src="Cross-Origin URL">
   ↓
Server
   ↓
callback(JSON)
   ↓
Browser executes JavaScript
```

---

# 7. الشكل المشهور لـJSONP

Request:

```http
GET /api/user?callback=myFunction
```

Response:

```javascript
myFunction({
    "username": "Fares",
    "role": "user"
});
```

الـBrowser ينفذ:

```javascript
myFunction(data);
```

---

# 8. أهم Parameter تدور عليه

أثناء الـPentesting ابحث عن Parameters مثل:

```text
callback
cb
jsonp
callbackFunction
```

مثال:

```text
/api/user?callback=test
```

لو Response كان:

```javascript
test({
    "username": "Fares"
});
```

فده مؤشر إن الـEndpoint يستخدم JSONP.

---

# 9. هل JSONP نفسه ثغرة؟

لا.

دي نقطة مهمة جدًا:

**JSONP تقنية وليست Vulnerability بحد ذاتها.**

الثغرة ممكن تظهر بسبب طريقة تنفيذ JSONP.

مثلاً لو:

```text
callback
```

ممكن يتحكم فيه المستخدم والـServer يحطه مباشرة داخل JavaScript بدون Validation.

هنا ممكن تظهر مشكلة:

**JSONP Callback Injection**

وقد تؤدي في بعض الحالات إلى:

**XSS / JavaScript Injection**

---

# 10. مثال على المشكلة

السيرفر الطبيعي:

```text
/api/data?callback=test
```

يرجع:

```javascript
test({
    "name": "Fares"
});
```

المشكلة تبدأ لو الـServer بيثق في قيمة:

```text
callback
```

بدون التحقق منها.

يعني بدل ما يسمح بأسماء Functions طبيعية فقط، يقبل Input يتحول إلى JavaScript داخل الـResponse.

هنا الـPentester يبدأ يختبر:

> هل أقدر أحقن JavaScript في الـcallback؟

---

# 11. ليه ده ممكن يؤدي إلى XSS؟

لأن الـResponse مش JSON فقط.

هو:

```javascript
callback({...});
```

والـBrowser بيتعامل معاه كـJavaScript.

فلو المهاجم قدر يخرج من سياق الـFunction أو يتحكم في JavaScript الموجود في الـResponse، ممكن يحصل:

```text
User Input
    ↓
callback parameter
    ↓
Server
    ↓
JavaScript Response
    ↓
Browser executes it
    ↓
Possible XSS
```

---

# 12. JSONP وData Disclosure

JSONP ممكن يكون خطر كمان لو الـEndpoint بيرجع بيانات حساسة.

مثلاً:

```text
/api/account?callback=steal
```

والـResponse:

```javascript
steal({
    "email": "user@example.com",
    "name": "Fares"
});
```

لو التطبيق مصمم بطريقة تسمح بالوصول للـEndpoint من Cross-Origin context، فقد تصبح البيانات متاحة للكود الذي حمّل الـJSONP.

عشان كده JSONP غير مناسب عادةً للبيانات الحساسة.

---

# 13. JSONP vs CORS

## JSONP

يعتمد على:

```html
<script src="...">
```

والـResponse يكون JavaScript.

مثال:

```javascript
callback({"name":"Fares"});
```

## CORS

يعتمد على HTTP Headers.

مثلاً:

```http
Access-Control-Allow-Origin: https://example.com
```

والـResponse ممكن يكون JSON عادي:

```json
{
    "name": "Fares"
}
```

### احفظ:

```text
JSONP = JavaScript callback
CORS  = HTTP headers
```

---

# 14. JSONP vs JSON

|JSON|JSONP|
|---|---|
|Data|JavaScript + Data|
|ليس executable بحد ذاته|Response يتم التعامل معه كـJavaScript|
|يستخدم APIs بشكل طبيعي|تقنية قديمة للـCross-Origin|
|لا يحتاج Callback|يحتاج Callback|
|أكثر أمانًا من ناحية التنفيذ|يحتاج حذر شديد|

---

# 15. أثناء الـPentesting

لو لقيت:

```text
callback=
```

اعمل الآتي:

### Step 1

حدد الـEndpoint:

```text
/api/data?callback=test
```

### Step 2

شوف الـResponse.

لو:

```javascript
test({...});
```

يبقى غالبًا JSONP.

### Step 3

اختبر هل `callback` قابل للتحكم.

مثلاً:

```text
callback=abc
```

وشوف:

```javascript
abc({...});
```

### Step 4

اختبر الـValidation.

هل التطبيق يسمح فقط بأسماء Functions؟

ولا يسمح بـCharacters غير متوقعة؟

### Step 5

شوف البيانات اللي بيرجعها الـEndpoint.

هل هي:

```text
Public data
```

ولا:

```text
Sensitive data
```

### Step 6

لو الـcallback غير محمي، تحقق هل يمكن أن يؤدي إلى:

```text
JavaScript Injection
```

أو:

```text
XSS
```

---

# 16. مثال في Burp Suite

Request:

```http
GET /api/user?callback=test HTTP/1.1
Host: target.com
```

Response:

```javascript
test({
    "username":"Fares",
    "email":"test@example.com"
});
```

هنا لاحظنا:

```text
callback=test
```

والـResponse:

```text
test(...)
```

إذن الـEndpoint يستخدم JSONP.

بعدها نبدأ نختبر:

```text
هل callback يتم عمل Validation له؟
هل يمكن التحكم فيه؟
هل البيانات حساسة؟
هل يمكن تحويله إلى JavaScript Injection؟
```

---

# 17. علامات مهمة تحفظها

لو شفت:

```text
callback=
```

أو:

```text
cb=
```

أو:

```text
jsonp=
```

فكر في:

```text
JSONP
```

ولو الـResponse بالشكل:

```javascript
functionName({...});
```

فكر في:

```text
JSONP Endpoint
```

ولو الـFunction name قابل للتحكم بشكل غير آمن:

```text
JSONP Callback Injection
```

ولو أدى لتنفيذ JavaScript:

```text
XSS
```

---

# 18. الحماية

أفضل الممارسات:

- عدم استخدام JSONP لو مش ضروري.
    
- استخدام CORS بشكل صحيح عند الحاجة للـCross-Origin access.
    
- عمل Strict Validation للـ`callback`.
    
- عدم السماح بإدخال JavaScript داخل اسم الـCallback.
    
- عدم استخدام JSONP مع بيانات حساسة.
    
- تطبيق Authentication وAuthorization بشكل صحيح.
    
- استخدام Content Security Policy كطبقة دفاع إضافية عند الحاجة.
    

---

# 19. أهم Concept في الموضوع

افهم السلسلة دي كويس:

```text
Same-Origin Policy
        ↓
Cross-Origin restriction
        ↓
JSONP workaround
        ↓
<script src="">
        ↓
Server returns JavaScript
        ↓
callback(JSON)
        ↓
Potential security problem
        ↓
Callback Injection
        ↓
Possible XSS / Data Disclosure
```

---

# 20. ملخص سريع للمراجعة

**JSONP** تقنية قديمة تسمح بجلب بيانات من Origin مختلف باستخدام `<script>`.  
الـServer بيرجع JSON داخل Function مثل:

```javascript
callback({"name":"Fares"});
```

لأن الـResponse يعتبر JavaScript، فإن عدم التحقق من قيمة `callback` ممكن يؤدي إلى **JavaScript Injection أو XSS** حسب طريقة التطبيق.  
في الـPentesting، ابحث عن Parameters مثل `callback`, `cb`, و`jsonp`، وافحص الـResponse والـValidation والبيانات التي يتم إرجاعها.

### أهم 5 حاجات تحفظهم:

```text
1. JSONP = JSON with Padding
2. يعتمد على <script>
3. Response = JavaScript
4. callback parameter مهم جدًا في الاختبار
5. سوء التنفيذ قد يؤدي إلى XSS أو Data Disclosure
```