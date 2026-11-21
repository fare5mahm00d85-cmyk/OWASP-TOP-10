
---

1) Lab: Basic SSRF against the local server
	 **فكرة الثغرة:**  
	الموقع فيه خاصية Stock Check بتخلي السيرفر يطلب بيانات من رابط يحدده المستخدم. لو مفيش تحقق مناسب من الرابط، ممكن نستغل ده للوصول لخدمات وصفحات داخلية.
	**خطوات الحل:**
	1. اعترضت طلب Stock Check باستخدام Burp Suite.
	2. غيّرت قيمة الرابط إلى `http://localhost/admin`.
	3. استخدمت استجابة السيرفر للوصول إلى واجهة الإدارة الداخلية.
	4. استخرجت رابط حذف المستخدم `carlos` ونفّذت طلب الحذف.
---
2) Lab: Basic SSRF against another back-end system 
	**فكرة الثغرة:**  
	الموقع فيه خاصية Stock Check بتخلي السيرفر يرسل طلبات لأنظمة داخلية. هنا الهدف مش `localhost`، لكن اكتشاف نظام داخلي على الشبكة `192.168.0.X` بيشغّل لوحة الإدارة على البورت `8080`.
	**خطوات الحل:**
	1. اعترضت طلب Stock Check باستخدام Burp Suite.
	2. جرّبت عناوين الـ IP في النطاق `192.168.0.1` إلى `192.168.0.254` مع البورت `8080`، باستخدام خاصية SSRF.
	3. قارنت الاستجابات لحد ما وصلت لعنوان الـ IP اللي عليه واجهة الإدارة.
	4. فتحت `/admin` على العنوان المكتشف، وبعدها حذفت المستخدم `carlos`
---
3) # Lab: Blind SSRF with out-of-band detection 

---
4) Lab: SSRF with blacklist-based input filter 
	- اعترض طلب Check stock باستخدام Burp Suite وابعت الطلب إلى Repeater.
	- جرّب `http://127.0.0.1/` وهتلاحظ إن الطلب محظور بسبب Blacklist Filter.
	- تجاوز الفلتر باستخدام `http://127.1/` بدلًا من `127.0.0.1`.
	- للوصول إلى Admin استخدم `http://127.1/%2561dmin` لتجاوز فلتر المسارات باستخدام Double URL Encoding.
	- افتح Admin Interface واحذف المستخدم `carlos` لإكمال اللاب.
----
5) Lab: SSRF with filter bypass via open redirection vulnerability
	- اعترض طلب Check stock باستخدام Burp Suite وأرسله إلى Repeater.
	- اكتشف إن تغيير `stockApi` مباشرةً للوصول إلى Host مختلف ممنوع.
	- اكتشف ثغرة Open Redirect من خلال Next Product واستخدام قيمة `path` في `Location Header`.
	- استغل الثغرة لتوجيه الطلب إلى `http://192.168.0.12:8080/admin`.
	- غيّر المسار إلى `http://192.168.0.12:8080/admin/delete?username=carlos` لحذف المستخدم وإكمال اللاب. 
---
6) Lab: Blind SSRF with Shellshock exploitation

---
7) Lab: SSRF with whitelist-based input filter 
	- اعترض طلب Check stock باستخدام Burp Suite وأرسله إلى Repeater.
	- جرّب `http://127.0.0.1/` ولاحظ إن التطبيق بيعمل Parsing للـ URL ويتحقق من الـ Hostname باستخدام Whitelist.
	- استخدم `http://username@stock.weliketoshop.net/` ولاحظ إن الطلب اتقبل بسبب دعم Embedded Credentials.
	- اعمل Double URL Encoding للرمز `#` باستخدام `%2523` لتجاوز التحقق من الـ Hostname.
	- استخدم `http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos` للوصول إلى Admin وحذف المستخدم `carlos`