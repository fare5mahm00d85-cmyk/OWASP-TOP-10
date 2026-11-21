
---
1) Lab: Modifying serialized objects
	- تسجّل دخول بـ `wiener:peter` وتلاحظ إن الـ Session Cookie عبارة عن PHP Serialized Object متحوّل بـ URL وBase64.
	- تفك ترميز الـ Cookie وتغيّر قيمة `admin` من `b:0` (False) إلى `b:1` (True).
	- تبعت الطلب بعد التعديل، وتدخل على `/admin` علشان تحصل على صلاحيات الأدمن.
	- تبعت Request إلى `/admin/delete?username=carlos` لحذف المستخدم `carlos` وحل اللاب
	 او ممكن بعد ما تاخد الكوكى تغيرها فى الموقع بدل الخطوه الرابعه 
---
2) # Lab: Modifying serialized data types
	- تسجّل دخول بـ `wiener:peter` وتفحص الـ Session Cookie في Burp Inspector، وتلاحظ إنه PHP Serialized Object.
	- تغيّر `username` إلى `administrator` وتعدّل الطول إلى `s:13`، وتغيّر `access_token` من String إلى Integer بقيمة `0`، يعني `i:0`.
	- تضغط Apply changes وتبعت الطلب، وبكده تستغل اختلاف نوع البيانات لتجاوز المصادقة والدخول كـ Administrator.
	- تدخل على `/admin`، وبعدها تبعت طلب إلى `/admin/delete?username=carlos` علشان تحذف المستخدم وتحل اللاب.
	- وطبعا ممكن تكتب الكوكى فى المتصفح بدل اخر خطوه 
---
3) Lab: Using application functionality to exploit insecure deserialization
	- تسجّل دخول بـ `wiener:peter` وتلاحظ إن حذف الحساب بيتم عن طريق `POST /my-account/delete`.
	- تفحص الـ Session Cookie في Burp Inspector وتلاقي الخاصية `avatar_link` بتحتوي على مسار ملف الصورة.
	- تعدّل `avatar_link` إلى `/home/carlos/morale.txt` وتحدّث طول النص إلى `s:23`، ثم تضغط Apply changes.
	- تبعت طلب `POST /my-account/delete`، فتستغل الوظيفة الخطيرة المرتبطة بحذف الحساب لحذف ملف `morale.txt` من مجلد Carlos.
----
4) # Lab: Arbitrary object injection in PHP
	فحص الـ Session Cookie
	تسجّل دخول باستخدام `wiener:peter`، وتلاحظ إن الـ Cookie بتحتوي على كائن PHP متسلسل (Serialized Object).
	استخراج الكود المصدري
	تفتح الملف `/libs/CustomTemplate.php` من خلال Burp Repeater، وتضيف `~` لاسم الملف علشان تعرض الـ Source Code.
	اكتشاف الثغرة
	تلاحظ إن الدالة السحرية `__destruct()` بتستدعي `unlink()` باستخدام قيمة الخاصية `lock_file_path`، وده ممكن يؤدي لحذف الملف المحدد.
	إنشاء الـ Serialized Object
	تعدّل الكائن علشان الخاصية `lock_file_path` تشير إلى ملف الهدف، مع ضبط أطوال النصوص وأنواع البيانات بشكل صحيح.
	
	```
	O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
	```
	ترميز البيانات
	تعمل Base64 Encode للكائن، وبعدها URL Encode علشان تجهّزه للاستخدام داخل الـ Session Cookie.
	تنفيذ الاستغلال
	تستبدل الـ Session Cookie بالقيمة المعدّلة في Burp Repeater وتبعت الطلب. عند معالجة الكائن، بتتنفذ `__destruct()` وتستدعي `unlink()` لحذف ملف `morale.txt`، وبكده يتحل اللاب.
-------
5) Lab: Exploiting Java deserialization with Apache Commons 
	 
