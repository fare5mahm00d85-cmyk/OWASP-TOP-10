
----
## ## JWT
---
1) Lab: JWT authentication bypass via unverified signature
	 1. سجلنا الدخول باستخدام `wiener:peter`، ومن Burp → Proxy → HTTP history اكتشفنا إن Session Cookie عبارة عن JWT مكوّن من Header وPayload وSignature.
	2. فحصنا الـ Payload واكتشفنا Claim اسمه `sub`، وده بيحدد هوية المستخدم، وكانت قيمته الأصلية `wiener`.
	3. أرسلنا طلب `GET /admin` إلى Repeater، ولاحظنا إن الوصول للـ Admin Panel يتطلب تسجيل الدخول بحساب Administrator.
	4. عدّلنا قيمة `sub` من `wiener` إلى `administrator` باستخدام JWT Inspector، ثم ضغطنا Apply changes، وأعدنا إرسال الطلب بدون إنشاء Signature صحيحة.
	5. بعد قبول التوكن والوصول إلى Admin Panel، استخرجنا رابط حذف المستخدم `carlos` ونفذنا طلب الحذف لحل اللاب.
	**السبب الأمني:** السيرفر لا يتحقق من صحة JWT Signature، وبالتالي يمكن للمهاجم تعديل بيانات Payload وانتحال هوية مستخدم آخر.
	**الحماية:** التحقق من Signature في كل Request، وتحديد خوارزمية التوقيع المسموح بها من جهة السيرفر، وعدم الوثوق في Claims قبل التحقق من التوكن.
---
2) # Lab: JWT authentication bypass via flawed signature verification
	**الفكرة:** السيرفر بيقبل JWT بدون التحقق من صحة التوقيع عند استخدام `alg: none`، وده يسمح بتزوير هوية المستخدم.
	1. سجل الدخول بحساب `wiener:peter` واعترض طلب `GET /my-account` من Burp.
	2. أرسل الطلب إلى Repeater وغيّر `sub` في JWT Payload إلى `administrator`.
	3. غيّر `alg` في JWT Header إلى `none` واضغط Apply changes.
	4. احذف الـ Signature بالكامل مع الاحتفاظ بالنقطة الأخيرة `.`، ثم اطلب `/admin`.
	5. ابحث عن `/admin/delete?username=carlos` في الـ Response ونفّذ الطلب لحذف المستخدم.
	**الثغرة:** Flawed JWT Signature Verification.
	**الأثر الأمني:** Authentication Bypass والوصول لصلاحيات Administrator دون امتلاك بيانات دخوله.
---
3) Lab: JWT authentication bypass via weak signing key
	 1. الموقع كان بيستخدم JWT بخوارزمية **HS256**، لكن المفتاح السري المستخدم في التوقيع كان ضعيفًا وسهل التخمين.
	2. استخدمنا **Hashcat** مع Wordlist لعمل Dictionary Attack على JWT الأصلي، واكتشفنا أن المفتاح السري هو `secret1`.
	3. حوّلنا المفتاح إلى Base64 وأضفناه داخل **JWT Editor Keys** بصيغة JWK علشان نستخدمه في توقيع التوكن.
	4. عدّلنا قيمة `sub` في Payload من `wiener` إلى `administrator`، ثم استخدمنا **Sign** لإنشاء توقيع صحيح بالمفتاح المكتشف.
	5. استخدمنا JWT الجديد للوصول إلى `/admin`، ومنه نفّذنا طلب حذف المستخدم `carlos`.
	**الثغرة:** Weak JWT Signing Key — مفتاح توقيع ضعيف يسمح بتزوير التوكنات وانتحال هوية مستخدم آخر.
---
4) # Lab: JWT authentication bypass via jwk header injection 
	1. السيرفر بيقبل مفتاح التحقق من داخل JWT Header باستخدام `jwk` من غير ما يتأكد إنه موثوق.
	2. بنعمل **RSA Key Pair** جديد من تبويب **JWT Editor Keys**.
	3. بنعدّل قيمة `sub` في الـ Payload إلى `administrator`.
	4. من **Attack → Embedded JWK** بنختار المفتاح، وBurp بيضيف الـ Public Key ويوقّع التوكن تلقائيًا بالـ Private Key.
	5. بنبعت الطلب إلى `/admin`، وبعد الدخول بنستخدم رابط حذف `carlos` لإكمال اللاب.
---
5) # Lab: JWT authentication bypass via jku header injection
	**. فين الثغرة؟**
	السيرفر بيستخدم JWT لتحديد هوية المستخدم، وبيستخدم `jku` علشان يعرف مكان ملف المفاتيح اللي هيستخدمها للتحقق من التوقيع. المشكلة إنه بيقبل أي رابط من غير ما يتأكد إنه موثوق.
	**2. بنعمل إيه؟**
	بننشئ مفتاح RSA خاص بينا، ونرفع المفتاح العام (Public Key) على Exploit Server علشان السيرفر يقدر يوصله.
	**3. بنخدع السيرفر إزاي؟**
	بنعدّل JWT Header ونضيف `jku` برابط ملف المفاتيح بتاعنا، ونخلي `kid` يطابق المفتاح اللي رفعناه.
	**4. إزاي بنغيّر صلاحياتنا؟**
	بنغيّر `sub` في الـ Payload من `wiener` إلى `administrator`، وبعدها نوقّع التوكن بالـ Private Key الخاص بينا. كده التوقيع يطابق المفتاح العام اللي السيرفر هيجيبه من رابطنا.
	**5. النتيجة إيه؟**
	السيرفر بيقبل التوكن ويفتكر إننا Administrator، فنقدر ندخل `/admin` ونستخدم رابط حذف `carlos`.
	**الفرق المهم بين اللابين:**
	- `jwk`: بنحط المفتاح العام نفسه داخل JWT Header.
	- `jku`: بنحط رابط خارجي السيرفر بيجيب منه المفتاح العام.
---
6) # Lab: JWT authentication bypass via kid header path traversal
	1. لسيرفر بيستخدم `kid` لتحديد مكان مفتاح التحقق من JWT داخل نظام الملفات.
	2. بسبب عدم التحقق من المسار، بنستغل ثغرة **Path Traversal** للوصول إلى ملف `/dev/null`.
	3. بنغيّر `alg` إلى `HS256` علشان نستخدم توقيع HMAC بدل RSA.
	4. بنغيّر `sub` إلى `administrator` ونوقّع JWT باستخدام مفتاح فارغ؛ لأن محتوى `/dev/null` فارغ.
	5. السيرفر يقبل التوقيع ويدخلنا `/admin`، وبكده نقدر نحذف المستخدم `carlos`.
	**جوهر الثغرة:** عدم التحقق من قيمة `kid` سمح لنا بالتحكم في ملف المفتاح الذي يستخدمه السيرفر للتحقق من التوقيع.