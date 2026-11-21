

> **الفكرة الأساسية:**  
> Broken Access Control بتحصل لما التطبيق يسمح للمستخدم بالوصول إلى **وظائف أو بيانات أو حسابات** مش من حقه الوصول إليها.

أشهر الأسباب:

- الاعتماد على `user ID` أو `role` من الـrequest بدون تحقق كافي.
    
- عدم التحقق من الصلاحيات على الـBackend.
    
- الاعتماد على الـFrontend لإخفاء الوظائف الحساسة.
    
- ضعف التحقق من `Cookie` أو `Referer`.
    
- وجود خطوات حساسة بدون Authorization في كل خطوة.
    

---

## 01. Lab: Unprotected admin functionality

### 💡 الفكرة

الـAdmin panel موجود في مكان معروف أو مكشوف، والتطبيق مش عامل حماية حقيقية عليه.

### 🔎 الطريقة

نجرب مسارات شائعة مثل:

```text
/robots.txt
```

أحيانًا `robots.txt` يكشف مسار الـadmin.

بعد معرفة المسار، نفتح:

```text
/admin
```

لو التطبيق يسمح بالوصول للـAdmin بدون Authentication/Authorization مناسب → عندنا Broken Access Control.

### 🧠 المهم

`robots.txt` مش ثغرة في حد ذاته، لكن ممكن يكشف **مسارات حساسة**.

---

# 02. Lab: Unprotected admin functionality with unpredictable URL

### 💡 الفكرة

المسار مش سهل تخمينه، وبالتالي `/admin` مش هيشتغل.

لكن المسار السري ممكن يكون موجود داخل **Source Code** أو JavaScript.

### 🔎 الطريقة

نفتح:

```text
View Source
```

وندور على:

```text
admin
```

أو أي reference لمسار الـAdmin.

بعد معرفة الـURL نطلبه مباشرة.

### 🧠 المهم

إخفاء رابط الـAdmin = **Security Through Obscurity**  
وده مش Authorization حقيقي.

---

# 03. Lab: User role controlled by request parameter

### 💡 الفكرة

التطبيق بيعتمد على Parameter أو Cookie لتحديد صلاحية المستخدم.

مثلاً:

```text
role=user
```

أو:

```text
admin=false
```

لو قدرت تغيرها إلى:

```text
admin=true
```

والـBackend صدق القيمة → Broken Access Control.

### 🔎 Burp

نمسك الـRequest في Burp ونبحث عن:

```text
role
admin
isAdmin
```

أو أي Parameter له علاقة بالـPrivilege.

نجرب تغيير القيمة من:

```text
false
```

إلى:

```text
true
```

وفي بعض الحالات تكون القيمة موجودة في Cookie.

### 🧠 السبب

الـServer **لا يجب أن يثق في قيمة يرسلها المستخدم لتحديد صلاحياته**.

---

# 04. Lab: User role can be modified in user profile

### 💡 الفكرة

المستخدم العادي يقدر يعدل بيانات الـProfile، لكن الـBackend يسمح له بتعديل **Role** أيضًا.

مثلاً Request طبيعي:

```text
email=user@example.com
```

لو أضفنا Parameter خاص بالـRole مثل:

```text
roleid=2
```

والـServer يقبله → ممكن نرفع صلاحيات الحساب.

### 🔎 الفكرة في Burp

1. نسجل بحساب عادي.
    
2. نغير الـEmail.
    
3. نمسك Request التعديل.
    
4. نختبر إضافة Parameter خاص بالـRole.
    
5. نعيد إرسال الطلب.
    
6. نتحقق هل الـRole اتغير فعلًا.
    

### 🧠 السبب

المشكلة هنا إن الـBackend لم يقيّد الحقول التي يستطيع المستخدم تعديلها.

---

# 05. Lab: User ID controlled by request parameter

### 💡 الفكرة

التطبيق بيحدد بيانات المستخدم عن طريق `id` موجود في الـURL.

مثلاً:

```text
/my-account?id=123
```

لو أنت User 123 وقدرت تغير:

```text
id=124
```

وتشوف بيانات User 124 → **IDOR / Broken Access Control**.

### 🔎 الطريقة

1. Login بحساب عادي.
    
2. افتح صفحة الحساب.
    
3. راقب الـURL.
    
4. غير الـ`id`.
    
5. شوف هل تقدر توصل لبيانات مستخدم آخر.
    

في الـLab الهدف يكون الوصول إلى بيانات حساسة مثل:

```text
API Key
```

### 🧠 السبب

الـBackend لازم يتحقق:

```text
هل المستخدم الحالي مسموح له يشوف Object رقم 124؟
```

مش مجرد:

```text
هل Object 124 موجود؟
```

---

# 06. Lab: User ID controlled by request parameter, with unpredictable user IDs

### 💡 الفكرة

نفس فكرة IDOR السابقة، لكن الـUser ID مش رقم سهل التخمين.

مثلاً بدل:

```text
id=123
```

يكون:

```text
id=8f7a2c...
```

### 🔎 الطريقة

بدل التخمين، ندور على الـID في الأماكن اللي التطبيق بيعرض فيها بيانات المستخدم.

مثلاً لو عندنا API أو Endpoint معين:

```text
/api/user/...
```

نراقب الـRequests في Burp ونستخرج الـUser ID.

بعدها نستخدم الـID الخاص بالـTarget في الـRequest.

### 🧠 المهم

**Unpredictable ID ≠ Authorization**

حتى لو الـID صعب التخمين، لو حصلنا عليه بطريقة أخرى والتطبيق يسمح بالوصول إليه → الثغرة ما زالت موجودة.

---

# 07. Lab: User ID controlled by request parameter with data leakage in redirect

### 💡 الفكرة

الـIDOR موجود، لكن البيانات الحساسة مش بتظهر مباشرة في الصفحة.

بدل كده، التطبيق يعمل:

```text
Redirect
```

والـResponse أو الـRedirect يكشف البيانات.

### 🔎 الطريقة

1. Login.
    
2. نمسك Request الخاص بالحساب في Burp.
    
3. نرسله إلى Repeater.
    
4. نغير الـUser ID.
    
5. نرسل Request.
    
6. نفحص الـResponse والـRedirect بعناية.
    
7. نبحث عن:
    

```text
API key
```

أو أي بيانات حساسة.

### 🧠 Takeaway

لازم تفحص:

- Response Body
    
- Headers
    
- Redirects
    
- Location Header
    

مش الصفحة النهائية فقط.

---

# 08. Lab: User ID controlled by request parameter with password disclosure

### 💡 الفكرة

هنا الـIDOR يسمح لك بالوصول إلى صفحة مستخدم آخر، والصفحة نفسها تحتوي على **Password Disclosure**.

### 🔎 الطريقة

1. Login بحساب عادي.
    
2. نمسك Request الخاص بالحساب.
    
3. نرسله إلى Repeater.
    
4. نغير الـUser ID إلى حساب الـAdministrator.
    
5. نفحص الـResponse.
    
6. نبحث عن Password أو بيانات تسجيل الدخول.
    
7. استخدام بيانات الـAdministrator لإثبات تأثير الثغرة.
    

### 🧠 السبب

المشكلة الأساسية:

```text
User-controlled ID
        ↓
Backend
        ↓
No authorization check
        ↓
Another user's sensitive data
```

---

# 09. Lab: Insecure direct object references

## IDOR

### 💡 الفكرة

**IDOR = Insecure Direct Object Reference**

التطبيق يستخدم Reference مباشر إلى Object، مثل:

```text
file=1.txt
```

أو:

```text
id=123
```

بدون التأكد أن المستخدم يملك صلاحية الوصول إليه.

### 🔎 Lab

في الـChat مثلًا، نفتح:

```text
View transcript
```

ونمسك الـRequest في Burp.

نرسله إلى Repeater.

نلاحظ إن الوصول للملف يتم باستخدام:

```text
GET
```

ثم نغير اسم/رقم الملف إلى Object آخر، مثل:

```text
1.txt
```

ونفحص الـResponse.

في الـLab الهدف هو العثور على بيانات حساسة مثل Password.

### 🧠 Takeaway

IDOR مش شرط يكون:

```text
?id=123
```

ممكن يكون:

```text
file=1.txt
document=123
account=456
order=789
```

---

# 10. Lab: URL-based access control can be circumvented

### 💡 الفكرة

الـFrontend بيمنع الوصول إلى:

```text
/admin
```

لكن الـBackend عنده طريقة أخرى لمعالجة الـURL باستخدام Header مثل:

```http
X-Original-URL
```

المشكلة إن الـFrontend والـBackend مش متفقين على الـURL الحقيقي.

### 🧠 شكل الـArchitecture

```text
User
  │
  ▼
Frontend / Proxy
  │
  ▼
Backend Application
```

الـFrontend ممكن يشوف:

```text
GET /
```

لكن الـBackend يتعامل مع:

```http
X-Original-URL: /admin
```

### 🔎 الفكرة

نستخدم الـHeader لتحديد الـURL الذي سيعالجه الـBackend.

مثلاً:

```http
X-Original-URL: /admin
```

ولو الهدف حذف User:

```http
X-Original-URL: /admin/delete
```

مع الـParameter المطلوب للـUser.

### 🧠 السبب

الـAccess Control تم تطبيقه على مستوى الـFrontend فقط، بينما الـBackend يقبل URL مختلفًا من Header قابل للتحكم.

### 🔥 Takeaway

أي Authorization لازم يتطبق على **Backend** نفسه، وليس على الـFrontend/Proxy فقط.

---

# 11. Lab: Method-based access control can be circumvented

### 💡 الفكرة

الـApplication عنده Authorization مبني على **HTTP Method**.

مثلاً:

```http
POST /admin-roles
```

قد يكون محمي.

لكن طريقة أخرى لنفس العملية قد لا يتم عليها نفس الفحص.

### 🔎 الفكرة في Burp

في الـLab نبدأ من Request صحيح خاص بالـAdmin، ونلاحظ:

- Cookie
    
- HTTP Method
    
- Parameters
    
- Target username
    

ثم نختبر هل يمكن تنفيذ نفس العملية باستخدام Request مختلف أو Method مختلف مع Session المستخدم العادي.

### 🧠 المشكلة

الـBackend لازم يطبق Authorization على **العملية نفسها**، وليس على Method معين فقط.

---

# 12. Lab: Multi-step process with no access control on one step

### 💡 الفكرة

عملية رفع الصلاحيات تتم على أكثر من Step.

مثلاً:

```text
Step 1
   ↓
Choose User
   ↓
Step 2
   ↓
Confirm
```

المشكلة إن التطبيق يحمي Step، لكنه نسي يعمل Authorization على Step آخر.

### 🔎 الطريقة

نراقب الـRequests في Burp أثناء عملية ترقية User.

نجد أكثر من Request.

مثلاً:

```text
Request 1 → اختيار المستخدم
Request 2 → تأكيد العملية
```

بعد ذلك نختبر تنفيذ الـStep الحساس باستخدام Session المستخدم الذي لا يمتلك صلاحية Admin.

### 🧠 الفكرة المهمة

حتى لو:

```text
Step 1 = Protected
```

لازم:

```text
Step 2 = Protected
```

وكل خطوة لازم تتحقق من:

```text
Authentication
+
Authorization
```

### 🔥 قاعدة مهمة

> **Authorization لازم يحصل في كل خطوة حساسة، مش أول خطوة فقط.**

---

# 13. Lab: Referer-based access control

### 💡 الفكرة

الـApplication بيعتمد على:

```http
Referer
```

علشان يحدد هل المستخدم جاي من صفحة Admin أم لا.

مثلاً ممكن يكون متوقع:

```http
Referer: https://LAB-ID.web-security-academy.net/admin
```

### 🔎 السيناريو

عندك:

```text
Admin → يقدر يرقّي User
Normal User → لا يستطيع
```

المشكلة إن الـBackend يعتمد على `Referer` بدل Authorization حقيقي.

نراقب Request الخاص بالعملية في Burp ونلاحظ:

```http
Cookie: ...
Referer: ...
```

ثم نختبر نفس العملية باستخدام Session المستخدم العادي مع التلاعب بالقيمة التي يعتمد عليها الـServer.

### 🧠 السبب

`Referer` مجرد HTTP Header يمكن للعميل التأثير عليه، لذلك لا يصلح وحده كدليل على صلاحيات المستخدم.

### 🔥 Takeaway

لا تستخدم:

```text
Referer
```

كبديل عن:

```text
Authorization
```

---

# 🧠 أهم Concepts في الـLabs

## 1. Authentication

السؤال:

> **مين أنت؟**

مثال:

```text
Login
Session
Cookie
```

---

## 2. Authorization

السؤال:

> **هل أنت مسموح لك تعمل العملية دي؟**

مثال:

```text
User → View own profile
Admin → Delete users
```

---

## 3. IDOR

السؤال:

> هل أقدر أغير Reference وأوصل لـObject مش بتاعي؟

مثال:

```text
/account?id=100
```

إلى:

```text
/account?id=101
```

---

## 4. Privilege Escalation

### Horizontal

User → User آخر بنفس مستوى الصلاحيات.

```text
User A
  ↓
User B
```

مثال:

```text
API Key
Password
Private data
```

### Vertical

User عادي → Admin.

```text
Normal User
     ↓
   Admin
```

مثال:

```text
Delete User
Promote User
Admin Panel
```

---

# 🔥 Master Methodology

لما تقابل أي Lab Access Control:

```text
1. Login
      ↓
2. Browse normally
      ↓
3. Intercept requests with Burp
      ↓
4. Identify sensitive functionality
      ↓
5. Look for:
   ├── ID
   ├── username
   ├── role
   ├── admin
   ├── Cookie
   ├── Referer
   ├── HTTP Method
   └── Hidden parameters
      ↓
6. Modify one thing at a time
      ↓
7. Replay request
      ↓
8. Compare response
      ↓
9. Check whether authorization was enforced
```

---

# 📌 Quick Revision Table

|Lab|الفكرة الأساسية|
|---|---|
|01|Unprotected Admin|
|02|Hidden/Unpredictable Admin URL|
|03|Role controlled by parameter|
|04|Role modification through profile|
|05|IDOR باستخدام User ID|
|06|IDOR مع IDs غير متوقعة|
|07|IDOR + Redirect data leakage|
|08|IDOR + Password disclosure|
|09|IDOR على ملفات/Objects|
|10|URL-based access control bypass|
|11|Method-based access control bypass|
|12|Missing authorization in multi-step flow|
|13|Referer-based access control|

---

# 🎯 الخلاصة

أهم حاجة تخرج بيها من الـLabs دي:

```text
Don't trust the client.
Don't trust IDs.
Don't trust hidden URLs.
Don't trust Referer.
Don't trust HTTP Method alone.
Don't protect only one step.
Always authorize on the server.
```

وأهم سؤال تسأله لنفسك في أي Access Control Lab:

> **"أنا User عادي، إيه اللي ممكن أغيره في الـRequest يخلي الـBackend يعاملني كأني User تاني أو Admin؟"**