

---
## API testing

---

1) Lab: Exploiting an API endpoint using documentation 
	 انتا هنا هتغير ايميل وتشوف فى ال api وتشيل منه براميتر براميتر لحد ما يبقا صح وتشوف الريكوست فى البروزر وتغدل ال url الى الحذف 
	 
	  الـ API Documentation بتوضح الـ Endpoints والـ HTTP Methods المتاحة في التطبيق.  
	قدرنا نكتشفها عن طريق التلاعب بالـ API Path باستخدام Burp Repeater.  
	اكتشفنا إن التوثيق مكشوف ومتاح الوصول ليه.  
	من خلاله عرفنا بوجود `DELETE` Endpoint لحذف المستخدمين.  
	استغلينا الـ Endpoint لحذف `carlos` بسبب غياب التحقق المناسب من الصلاحيات.
---
2) Lab: Exploiting server-side parameter pollution in a query string
	1. التطبيق بياخد الـ `username` من المستخدم وبيستخدمه في تكوين Request داخلي للـ API، والمشكلة إنه مش بيعمل Encoding بشكل آمن.
    
	2. استغلينا `%26` لإضافة Parameter جديد اسمه `field`، واستخدمنا `%23` لقطع باقي الـ Parameters من الـ Request الداخلي.
	    
	3. من خلال رسائل الخطأ عرفنا إن الـ API محتاج Parameter اسمه `field`، واستخدمنا Burp Intruder لاكتشاف القيم المقبولة زي `email`.
	    
	4. راجعنا ملف JavaScript واكتشفنا إن التطبيق بيستخدم `reset_token` في عملية إعادة تعيين كلمة المرور، فجربناه كقيمة للـ `field` ونجحنا في استخراج Token الأدمن.
	    
	5. استخدمنا الـ Token للوصول لصفحة تغيير كلمة مرور الأدمن، وبعد تغييرها سجلنا الدخول بحسابه وحذفنا `carlos`.
	
	
	**الفكرة الأساسية:** قدرنا نغيّر طريقة معالجة الـ Backend للـ Request عن طريق حقن Parameters إضافية، وده سمح لنا باستخراج بيانات حساسة والوصول لحساب الأدمن.

---
3) Lab: Finding and exploiting an unused API endpoint 
	1. فتحنا منتج الجاكيت واعترضنا الـ Request المسؤول عن عرض سعره، واكتشفنا Endpoint اسمه `/api/products/1/price`.
	2. أرسلنا الـ Request إلى Repeater وغيّرنا HTTP Method من `GET` إلى `PATCH`.
	3. من رسائل الخطأ عرفنا إن السيرفر محتاج `Content-Type: application/json` وParameter اسمه `price`.
	4. أرسلنا `{"price":0}`، فالسيرفر قبل الطلب وغيّر سعر الجاكيت إلى `$0.00`.
	5. أضفنا الجاكيت إلى Basket وضغطنا `Place order` لإتمام الشراء وحل اللاب.
	**الفكرة الأساسية:** استغلال Unused API Endpoint يسمح لمستخدم عادي بتعديل سعر منتج بسبب غياب التحقق المناسب من الصلاحيات.

---
4) # Lab: Exploiting a mass assignment vulnerability 
	**الفكرة:** ثغرة Mass Assignment بتحصل لما الـ Backend يسمح للمستخدم بإرسال أو تعديل Properties إضافية في الـ Request، حتى لو مش ظاهرة في واجهة الموقع أو مش المفروض المستخدم يتحكم فيها.
	**اللي حصل في اللاب:**
	1. سجلنا الدخول بحساب `wiener:peter`، واشترينا المنتج وضفناه للـ Basket، لكن لما ضغطنا Place order ظهرت رسالة `INSUFFICIENT_FUNDS` لأن الرصيد مش كفاية.
	2. فتحنا Burp → Proxy → HTTP history، ولاحظنا وجود طلبين `GET` و`POST` إلى `/api/checkout`.
	3. فحصنا Response بتاع GET، واكتشفنا وجود Parameter اسمه `chosen_discount`، وده مش موجود في POST Request الأصلي.
	4. أضفنا `chosen_discount` إلى POST Body بالقيمة `percentage: 0`، والسيرفر قبل الـ Parameter بدون Error، وبعدها جرّبنا القيمة `"x"` وظهر Error بيوضح إن القيمة لازم تكون رقمًا، وده أكد إن السيرفر بيعالج الـ Parameter.
	5. غيّرنا قيمة `percentage` إلى `100`، وبكده قدرنا نتحكم في نسبة الخصم ونحل اللاب.
	**السبب الأمني:** الـ Backend كان بيسمح للمستخدم بتعديل `chosen_discount` رغم إنها قيمة المفروض السيرفر هو اللي يتحكم فيها، وده أدى إلى الحصول على خصم غير مصرح به.
	**الحماية:** استخدام Allowlist للـ Properties المسموح للمستخدم بتعديلها، وحساب الخصومات والتحقق منها على السيرفر فقط، وعدم الثقة في البيانات القادمة من الـ Client.
---
5) Lab: Exploiting server-side parameter pollution in a REST URL
	 1. بدأنا بطلب Password Reset للمستخدم `administrator`، ولاحظنا إن السيرفر بيستخدم قيمة `username` في تكوين URL داخلي، واستنتجنا ده من تجارب `#` و`?` و`../` وملاحظة اختلاف رسائل الخطأ.
	2. استخدمنا `../` للخروج من مسار الـ API، ووصلنا إلى ملف `openapi.json`، ومنه اكتشفنا شكل الـ Internal API:  
	`/api/internal/v1/users/{username}/field/{field}`.
	3. حقنّا `field/foo` داخل قيمة `username`، فالسيرفر كشف إن الحقل المدعوم هو `email`، وده أكد إننا قدرنا نتحكم في جزء من المسار الداخلي.
	4. راجعنا ملف `forgotPassword.js` واكتشفنا إن عملية إعادة تعيين كلمة المرور بتستخدم Parameter اسمه `passwordResetToken`. الإصدار الحالي رفض الحقل، فجرّبنا إصدار API مختلفًا باستخدام `v1`.
	5. استخدمنا `../../v1/users/administrator/field/passwordResetToken` لاستخراج Reset Token، ثم غيّرنا كلمة مرور Administrator، وسجّلنا الدخول وحذفنا `carlos`.
	**السبب الأمني:** السيرفر بيبني REST URL باستخدام User Input بدون معالجة أو تحقق آمن، وده سمح بالتلاعب بالمسار الداخلي والوصول إلى بيانات حساسة.
	**الحماية:** التحقق من المدخلات، وعدم تركيب URLs باستخدام User Input مباشرة، وتقييد المسارات والحقول المسموح بها، وحماية بيانات إعادة تعيين كلمة المرور.
---
---
---
---
---
## part two JWT
---
1) Lab: JWT authentication bypass via unverified signature
	 