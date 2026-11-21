

---
1) Lab: Excessive trust in client-side controls
	الفكره هنا اريسبونس بيعرض السعر وتعرف انتا تغير السعر براحتك  
---
2) # Lab: High-level logic vulnerability 
	الفكره هنا ان لو غيرت عدد المنتجات للسالب بتحسب القيمه بالسالب وتنقص من السعر  
---
3) Lab: Inconsistent security controls 
	 الفكره هنا هتشوف الادمن الاسم بتاعه ايه هتلاقى admin هتشوف الميل بتاعه اسمه ايه وهتعمل حساب مستخدم عادى وجوه هتلاقى حانه تغير الاميل وتغيره 
---
4) Lab: Flawed enforcement of business rules 
	الفكره هنا ان فيه كوبون وهتسجل دخول فى اخر الصفه هتلاقى كوبون خوارزميه الكوبون ده ان ميستخدمش مرتين ورا بعض يعنى لو بدلت بينهم عادى وكدا خلثصت 
---
5) # Lab: Low-level logic flaw
	الفكره هنا انتا لما بتطلب كثير بيخلص الارقام الموجب ويخش على السالب ف انتا تزود لحد ما توصل لرقم وتشترى 
---
6) Lab: Inconsistent handling of exceptional input
	- افتح Burp Suite واستخدم Discover content لاكتشاف `/admin`، ولاحظ إن الوصول للوحة الإدارة متاح لموظفي `DontWannaCry`.
	- افتح Email client وسجّل الـ Email ID، ثم أنشئ حسابًا بإيميل طويل لا يقل عن 200 حرف، وأكّد الحساب ولاحظ إن الإيميل اتقص عند 255 حرفًا.
	- سجّل حسابًا جديدًا بإيميل طويل يحتوي على `@dontwannacry.com.YOUR-EMAIL-ID.web-security-academy.net`، واضبط طول الجزء الأول بحيث يكون حرف `m` في نهاية `@dontwannacry.com` هو الحرف رقم 255 بالضبط.
	- أكّد الحساب وسجّل الدخول؛ هتلاقي إنك حصلت على صلاحية الأدمن بسبب Email Truncation، وبعدها افتح `/admin` واحذف `carlos` لحل اللاب
---
7) Lab: Weak isolation on dual-use endpoint
	 الفكره هنا انتا هتعمل تغير كلمه السر وهتوقف الريكويست وهتلاقى لو شلت كلمه السر القديمه عادى وتقدر تغير كمان لاسم وتكتب اسم الاكونت ال انت عاوزه ال هو بتاع administrator وكدا بقيت ادمن 
---
8) Lab: Insufficient workflow validation
	الفكره هنا لو انتا اشتريت حاجه تانيه بتلاقى فى url cart/order-confirmation?order-confirmed=true 
	المفروض ده تستخدم لمنتج واحد بس لو جيت تحطها فى منتج تانى هتشتريه من غير ما تدفع 
---
9) # Lab: Authentication bypass via flawed state machine
	الفكره هنا لمل تسجل تسجيل دخول من حساب وبعده بتيجى اختيار ال role-selector علشان تختار ف انتا باستخدام ل بيرب تعمل droub للطلب ده وهتلاقى صفحه الادمن عندل علطول 
---
10) Lab: Infinite money logic flaw 
	;
---
11) Lab: Authentication bypass via encryption oracle
	