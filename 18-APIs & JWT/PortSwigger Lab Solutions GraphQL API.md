
--- 
1) Lab: Accessing private GraphQL posts 
	**الفكرة:** الموقع بيخفي مقال من صفحة الـ Blog، لكنه لا يمنع الوصول لبياناته من خلال GraphQL API.
	

	1. راقبنا `POST /graphql/v1` في Burp HTTP history ولاحظنا إن المقال رقم `3` مش موجود في قائمة المقالات.
		    
		1. استخدمنا `Introspection` لاكتشاف الـ Schema والحقول المتاحة داخل `BlogPost`.
		    
		2. اكتشفنا وجود حقل حساس اسمه `postPassword`.
		    
		3. عدّلنا قيمة `id` في Variables إلى `3`، وأضفنا `postPassword` إلى الـ Query.
		    
		4. أرسلنا الطلب إلى السيرفر واستخرجنا كلمة السر من Response واستخدمناها لحل اللاب.
		    
		
		**Vulnerability:** Broken Access Control / Excessive Data Exposure.
---
2) Lab: Accidental exposure of private GraphQL fields 
	 الموقع بيكشف بيانات تسجيل الدخول الخاصة بالمستخدمين من خلال GraphQL API بسبب ضعف التحكم في الوصول، وده بيسمح للمهاجم بالحصول على بيانات الأدمن، تسجيل الدخول بحسابه، ثم حذف المستخدم `carlos`. 
---
3) Lab: Finding a hidden GraphQL endpoint
	 فكرة اللاب إن الموقع مخبّي **GraphQL Endpoint** المسؤول عن إدارة المستخدمين، وكمان عامل حماية ضعيفة ضد `Introspection`.

	المطلوب إنك تكتشف الـ Endpoint المخفي، وتتجاوز الحماية عن طريق تعديل الـ Introspection Query، وتعرف `ID` الخاص بالمستخدم `carlos`، وبعدها تستخدم `Mutation` لحذفه. 
----
4) # Lab: Bypassing GraphQL brute force protections
	 الفكرة ببساطة إن الموقع بيمنعك من تجربة كلمات مرور كتير عن طريق Rate Limiting، لكن GraphQL بيسمحلك تستخدم **Aliases** علشان تنفذ محاولات Login متعددة داخل Request واحد بدل ما تبعت كل محاولة في Request منفصل. بالتالي بنقدر نختبر قائمة كلمات المرور مرة واحدة، ونشوف أنهي محاولة رجعت `success: true`، ونعرف منها الباسورد الصحيح لحساب Carlos. 
	 ### **خطوات الحل بالتفصيل**
	
	1. افتح اللاب واضغط على **My account**، وحاول تسجيل الدخول ببيانات خاطئة.
	    
	2. افتح Burp Suite → **Proxy → HTTP History**، وابحث عن طلب تسجيل الدخول الذي يحتوي على GraphQL Mutation.
	    
	3. اضغط كليك يمين على الطلب واختر **Send to Repeater**.
	    
	4. افتح GraphQL Tab، وجهّز Mutation تحتوي على عدة Aliases، كل Alias يمثل محاولة Login بكلمة مرور مختلفة.
	    
	5. مثال على جزء من الـ Payload:
	    
	
	```
	mutation login {
	    bruteforce0: login(input:{
	        password: "123456",
	        username: "carlos"
	    }) {
	        token
	        success
	    }
	
	    bruteforce1: login(input:{
	        password: "password",
	        username: "carlos"
	    }) {
	        token
	        success
	    }
	
	    bruteforce2: login(input:{
	        password: "12345678",
	        username: "carlos"
	    }) {
	        token
	        success
	    }
	}
	```
	
	6. أكمل باقي كلمات المرور بنفس الطريقة، مع زيادة رقم Alias في كل محاولة.
	    
	7. احذف `operationName` و`variables` من الطلب الأصلي لو موجودين.
	    
	8. اضغط **Send**، وستظهر نتيجة كل محاولة في Response.
	    
	9. ابحث عن `success: true` لتحديد الـ Alias الذي نجح، ثم ارجع إلى الـ Request لمعرفة كلمة المرور المقابلة.
	    
	10. سجّل الدخول بحساب `carlos` باستخدام كلمة المرور الصحيحة.
---
5)  