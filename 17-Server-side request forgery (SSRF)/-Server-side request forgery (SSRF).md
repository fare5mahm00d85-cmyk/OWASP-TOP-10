

---

## 1. ما هي ثغرة SSRF؟

SSRF اختصار لـ Server-Side Request Forgery.

بالعربي: تزوير الطلبات من جهة الخادم.

هي ثغرة أمنية بتحصل لما المهاجم يقدر يخلي الـ Web Server يرسل HTTP Request إلى وجهة يحددها المهاجم، أو يتحكم في جزء من الوجهة، من غير ما التطبيق يتحقق بشكل آمن من الطلب.

بمعنى أبسط:

بدل ما المهاجم يتصل مباشرة بسيرفر داخلي أو خدمة مش متاحة من الإنترنت، بيستغل السيرفر نفسه علشان هو اللي يبعت الطلب للخدمة دي.

### مثال بسيط

تخيل إن عندك موقع بيستقبل URL علشان يعرض صورة موجودة عليه.

الطلب الطبيعي:

[https://example.com/fetch-image?url=https://images.example.org/photo.jpg](https://example.com/fetch-image?url=https://images.example.org/photo.jpg)

السيرفر يستقبل الـ URL، وبعدها يعمل Request للرابط ويجيب الصورة للمستخدم.

المشكلة تظهر لو المهاجم قدر يغيّر قيمة url إلى عنوان داخلي، مثل:

[http://127.0.0.1:8080/admin](http://127.0.0.1:8080/admin)

لو التطبيق بيقبل الوجهة دي من غير تحقق مناسب، ممكن السيرفر يطلب صفحة الإدارة الموجودة على جهازه نفسه.

**مهم:** نجاح الطلب لا يعني تلقائيًا إن المهاجم يقدر يشوف الرد أو يحصل على صلاحيات الإدارة. النتيجة بتعتمد على تصميم التطبيق، والخدمة المستهدفة، ووسائل الحماية الموجودة.

---

## 2. فهم الفرق بين Client-Side وServer-Side

### Client-Side Request

الـ Client هو جهاز المستخدم، غالبًا المتصفح.

مثال:

- المستخدم يفتح موقعًا.
    
- المتصفح يرسل Request إلى الموقع.
    
- الموقع يرد على المتصفح.
    

المستخدم عادةً بيتصل بالوجهات اللي يقدر جهازه يوصل لها.

### Server-Side Request

هنا التطبيق نفسه هو اللي بيبعت الطلب.

مثال:

- المستخدم يرسل URL إلى التطبيق.
    
- الـ Backend يستقبل الـ URL.
    
- الـ Backend يتصل بالوجهة.
    
- التطبيق يعالج الرد، وممكن يرجّع محتواه للمستخدم.
    

الفرق الجوهري إن السيرفر ممكن يكون عنده وصول إلى موارد لا يستطيع المستخدم الوصول إليها مباشرة، مثل:

- خدمات داخلية في شبكة الشركة.
    
- لوحات إدارة غير منشورة على الإنترنت.
    
- قواعد بيانات أو خدمات داخلية لها HTTP API.
    
- Cloud metadata services.
    

وده السبب اللي بيخلي SSRF خطيرة.

---

## 3. كيف تحدث SSRF؟ (Mechanism)

غالبًا الثغرة بتمر بالمراحل التالية:

1. التطبيق يستقبل Input من المستخدم.
    
2. الـ Input يحتوي على URL أو اسم Host أو جزء من عنوان.
    
3. الـ Backend يستخدم القيمة في HTTP Client أو مكتبة اتصال.
    
4. السيرفر يرسل Request إلى الوجهة.
    
5. المهاجم يتحكم في الوجهة أو يؤثر عليها.
    
6. لو مفيش Validation وNetwork Controls كافية، ممكن الطلب يوصل إلى مورد غير مسموح به.
    

مثال برمجي مبسط وغير آمن:

```
url = request.query["url"]
response = http_client.get(url)
return response.body
```

المشكلة هنا إن الكود بيستخدم URL مقدمًا من المستخدم مباشرة، من غير ما يوضح أي تحقق من الوجهة.

**ملحوظة:** ده مثال توضيحي، مش كود مناسب للاستخدام في Production.

---

## 4. أشهر الأماكن التي قد تظهر فيها SSRF

أثناء اختبار تطبيق ويب، دور على الخصائص اللي بتخلي السيرفر يجلب موارد خارجية أو يتصل بخدمات أخرى.

### A. Image URL / Image Import

المستخدم يدخل رابط صورة والسيرفر ينزّلها.

مثال:

```
POST /import-image
url=https://images.example.org/avatar.jpg
```

الاحتمال: السيرفر ممكن يسمح بوجهة غير متوقعة لو مفيش تحقق سليم.

### B. Webhook Configuration

التطبيق يسمح للمستخدم بتحديد عنوان يستقبل Notifications.

مثال:

```
POST /webhooks
callback_url=https://receiver.example.org/events
```

لو التطبيق بيتصل تلقائيًا بالعنوان، لازم يتأكد من الوجهات المسموحة.

### C. PDF Generator

التطبيق يحوّل صفحة ويب أو URL إلى PDF.

ممكن أداة توليد الـ PDF تحاول تجلب:

- HTML.
    
- الصور.
    
- CSS.
    
- ملفات JavaScript.
    
- موارد أخرى تشير إليها الصفحة.
    

### D. URL Preview

الموقع يعرض Preview لرابط قبل نشره، مثل صورة مصغرة وعنوان الصفحة.

غالبًا الـ Backend بيزور الرابط ويستخرج البيانات.

### E. Link Checker

أداة بتختبر إذا كان الرابط شغالًا أم لا.

لو المستخدم يقدر يتحكم في الرابط، ممكن يحاول جعل السيرفر يتصل بوجهة غير مسموحة.

### F. XML Parsers وDocument Processing

بعض تطبيقات معالجة XML أو المستندات بتجلب موارد خارجية. هنا ممكن تظهر SSRF ضمن سلسلة من المشاكل، وأحيانًا تتداخل مع XXE.

### G. API Integrations

التطبيق يتصل بخدمات خارجية أو داخلية بناءً على مدخلات المستخدم.

### H. File Import وPackage Fetching

التطبيق يسمح بجلب ملف أو Package من URL.

**قاعدة مهمة:** مش كل حقل URL معناه إن فيه SSRF. لازم تتأكد إن السيرفر فعلًا بيعمل Request إلى الوجهة المتحكم فيها.

---

## 5. أنواع SSRF

فيه أنواع رئيسية لازم تفرق بينها.

### النوع الأول: Basic SSRF / Response-Returning SSRF

ده النوع اللي التطبيق فيه بيبعت Request، وبعدها بيرجع محتوى الرد أو جزءًا منه للمهاجم.

مثال افتراضي:

```
GET /fetch?url=https://example.org/data
```

لو غيّرت الوجهة في بيئة اختبار مصرح بها، والتطبيق عرض محتوى الرد، يبقى عندك قناة مباشرة لملاحظة النتيجة.

المميزات:

- سهل نسبيًا في الملاحظة.
    
- ممكن يساعد في فهم رد الخدمة المستهدفة.
    
- قد يكشف معلومات من خدمات داخلية إذا كانت قابلة للوصول وكانت البيانات المعادة حساسة.
    

### النوع الثاني: Blind SSRF

في النوع ده السيرفر بيبعت Request، لكن التطبيق لا يعرض رد الوجهة للمستخدم.

مثال:

```
POST /check-url
url=https://example.org
```

التطبيق ممكن يرجع:

```
URL processed successfully
```

حتى لو السيرفر عمل Request بالفعل، المستخدم مش هيشوف محتوى الرد.

إزاي تكتشفه؟

- استخدام Callback URL تملكه في مختبر مصرح به.
    
- مراقبة DNS أو HTTP interactions.
    
- فحص سجلات السيرفر إذا كانت متاحة.
    
- مقارنة سلوك التطبيق عند تغيير المدخلات.
    
- ملاحظة اختلافات الوقت أو رسائل الخطأ، مع اعتبارها أدلة غير حاسمة وحدها.
    

**مهم:** عدم ظهور رد HTTP لا يعني عدم وجود SSRF.

### النوع الثالث: Second-Order SSRF

هنا المدخل الخبيث لا يُستخدم فورًا.

السيناريو:

1. المستخدم يحفظ URL في إعدادات الحساب.
    
2. التطبيق يخزن القيمة في قاعدة البيانات.
    
3. بعد فترة، Worker أو Scheduled Job يعالجها.
    
4. السيرفر يرسل Request إلى الوجهة المخزنة.
    

ده اسمه Second-Order SSRF لأن التأثير بيظهر في مرحلة لاحقة.

نقطة مهمة: لازم فحص المدخلات وقت التخزين ووقت الاستخدام الفعلي؛ لأن الـ URL ممكن يتغير أو يتفسر بشكل مختلف مع الوقت.

### النوع الرابع: SSRF عبر Protocols غير HTTP

بعض المكتبات أو الخدمات تسمح بأكثر من Protocol، مثل:

- HTTP
    
- HTTPS
    
- File
    
- بروتوكولات أخرى حسب المكتبة والإعدادات
    

لو التطبيق بيسمح ببروتوكولات غير متوقعة، المخاطر ممكن تزيد.

لكن مش كل SSRF تسمح باستخدام أي Protocol، ومش كل مكتبة بتدعم نفس البروتوكولات.

أفضل دفاع هو السماح بالبروتوكولات الضرورية فقط، ويفضل HTTP/HTTPS عند الحاجة إلى جلب صفحات ويب.

---

## 6. الفرق بين SSRF وCSRF وXSS

### SSRF — Server-Side Request Forgery

المهاجم يحاول جعل السيرفر يتصل بوجهة غير مقصودة.

الهدف المحتمل:

- الوصول لخدمات داخلية.
    
- استغلال ثقة السيرفر الشبكية.
    
- الوصول إلى Cloud Metadata.
    
- كشف معلومات من خدمات داخلية.
    

### CSRF — Cross-Site Request Forgery

المهاجم يحاول جعل متصفح مستخدم مسجل الدخول يرسل طلبًا غير مرغوب فيه إلى موقع يثق فيه المتصفح.

الهدف المحتمل:

- تغيير إعدادات الحساب.
    
- تنفيذ إجراء باسم المستخدم.
    
- تغيير بيانات إذا لم توجد حماية مناسبة.
    

### XSS — Cross-Site Scripting

المهاجم ينجح في تنفيذ JavaScript غير موثوق به داخل سياق صفحة يزورها المستخدم.

الهدف المحتمل:

- تنفيذ أفعال داخل جلسة المستخدم وفق القيود الموجودة.
    
- قراءة أو تعديل محتوى الصفحة.
    
- الوصول إلى بيانات متاحة لسكريبت الصفحة.
    

**الخلاصة:**

- SSRF: السيرفر هو اللي بيعمل الاتصال.
    
- CSRF: متصفح المستخدم بيعمل طلبًا غير مرغوب فيه.
    
- XSS: كود JavaScript غير موثوق يتم تنفيذه في المتصفح.
    

---

## 7. ليه SSRF خطيرة؟

الخطورة بتعتمد على مكان السيرفر، وصلاحياته، والخدمات اللي يقدر يوصل لها.

### A. الوصول إلى Internal Services

ممكن يكون عند المؤسسة خدمات داخلية لا تسمح باتصالات مباشرة من الإنترنت.

مثل:

- Internal Admin Panel.
    
- Internal API.
    
- Monitoring Dashboard.
    
- Services على منافذ داخلية.
    
- أدوات إدارة خاصة بالـ Infrastructure.
    

لو السيرفر المصاب عنده وصول للشبكة دي، فقد يصبح وسيطًا للوصول إلى تلك الخدمات.

لكن الوصول للشبكة لا يعني بالضرورة الوصول إلى كل الخدمات الموجودة عليها.

### B. Cloud Metadata

بعض بيئات Cloud بتوفر Metadata Service للمثيل نفسه، وقد تحتوي على معلومات تشغيلية أو بيانات اعتماد مرتبطة بالـ Workload.

في بعض البيئات، الوصول غير المصرح به لهذه الخدمة قد يسمح بالحصول على Credentials ذات صلاحيات مهمة.

المخاطر تختلف حسب مزود الخدمة والإعدادات:

- AWS.
    
- Azure.
    
- Google Cloud.
    

**مهم:** خدمات Metadata ليست متطابقة، وبعضها يفرض وسائل حماية إضافية. لا تفترض أن كل بيئة تسمح بالوصول إليها من خلال SSRF.

### C. Access-Control Bypass

قد تكون خدمة داخلية مصممة لقبول الطلبات القادمة من شبكة موثوقة فقط.

لو السيرفر المصاب مسموح له بالاتصال بها، فقد تستطيع SSRF تجاوز حدود الوصول الشبكي التي كانت تمنع المستخدم الخارجي.

### D. Sensitive Information Disclosure

قد يكشف الرد:

- بيانات إعدادات.
    
- معلومات عن الخدمات الداخلية.
    
- رسائل خطأ.
    
- بيانات API.
    
- معلومات تشغيلية حساسة.
    

### E. Potential Further Impact

في ظروف معينة، قد تساعد SSRF في سلسلة استغلال تؤدي إلى نتائج أكبر، مثل إساءة استخدام Credentials أو الوصول إلى وظائف إدارية.

لكن SSRF وحدها لا تعني تلقائيًا Remote Code Execution أو اختراق قاعدة البيانات.

---

## 8. عناوين مهمة في فهم SSRF

### 127.0.0.1

عنوان Loopback في IPv4.

معناه أن الاتصال يتجه إلى الجهاز المحلي الذي ينفذ الاتصال.

لو التطبيق نفسه موجود على السيرفر، فقد يشير هذا العنوان إلى السيرفر نفسه.

### localhost

اسم شائع يشير إلى الجهاز المحلي.

لكن النتيجة الفعلية تعتمد على إعدادات DNS وملف Hosts وطريقة حل الاسم.

### ::1

عنوان Loopback في IPv6.

### Private IPv4 Ranges

نطاقات IPv4 الخاصة الشائعة:

- 10.0.0.0/8
    
- 172.16.0.0/12
    
- 192.168.0.0/16
    

تُستخدم عادةً في الشبكات الداخلية، لكنها ليست دليلًا وحدها على أن الخدمة آمنة أو غير آمنة.

### Link-Local Addresses

النطاق:

- 169.254.0.0/16 في IPv4.
    

قد يُستخدم للاتصالات المحلية على مستوى الرابط، وتوجد ضمنه عناوين مستخدمة في بعض بيئات Cloud Metadata.

### IPv6

هناك أيضًا نطاقات IPv6 خاصة ومحلية، منها:

- ::1/128 للـ Loopback.
    
- fc00::/7 للـ Unique Local Addresses.
    
- fe80::/10 للـ Link-Local.
    

**نقطة مهمة:** الحماية لا تعتمد فقط على منع عنوان واحد؛ يجب التعامل مع IPv4 وIPv6 وطرق تحليل العناوين المختلفة.

---

## 9. إزاي تكتشف SSRF أثناء اختبار الاختراق؟

اتبع منهجية منظمة بدل ما تجرب عناوين عشوائية.

### Step 1: Identify URL Inputs

اجمع كل الأماكن اللي التطبيق بيستقبل فيها:

- URL.
    
- Hostname.
    
- Callback URL.
    
- Image URL.
    
- Webhook URL.
    
- Import URL.
    
- عنوان لخدمة خارجية.
    

سجل اسم الـ Parameter والـ Endpoint وطريقة الطلب.

### Step 2: Understand the Normal Behavior

ابدأ برابط خارجي آمن تملكه أو تتحكم فيه، أو رابطًا مخصصًا للمختبر.

لاحظ:

- هل التطبيق بيقبل المدخل؟
    
- هل بيرجع محتوى؟
    
- هل بيعرض Status Code؟
    
- هل فيه رسائل خطأ؟
    
- هل العملية متزامنة أم بتتم في الخلفية؟
    

### Step 3: Use an Authorized Callback Endpoint

في مختبر تملكه أو عندك تصريح لاختباره، استخدم نطاق Callback خاص بك.

راقب:

- وصول HTTP Request.
    
- وصول DNS Query.
    
- وقت وصول الطلب.
    
- Headers المتاحة.
    
- مسار الطلب إن ظهر.
    

لو وصلك Callback مرتبط بالمدخل، ده دليل قوي إن السيرفر أو مكوّنًا خلفيًا حاول الوصول للوجهة.

لكن لازم تستبعد احتمالات زي الـ Browser Requests أو أنظمة الفحص الآلية قبل ما تنسب الطلب إلى الوظيفة المستهدفة.

### Step 4: Test Controlled Variations

غيّر قيمة واحدة كل مرة:

- URL صالح.
    
- URL غير موجود.
    
- Hostname غير قابل للحل.
    
- بروتوكول غير مدعوم.
    
- وجهة غير مسموحة ضمن نطاق الاختبار.
    

راقب الفروق في السلوك.

### Step 5: Identify the Request Type

حدد إذا كان التطبيق:

- يعيد محتوى الرد.
    
- يعرض رسالة عامة فقط.
    
- يخزن URL لمعالجته لاحقًا.
    
- يتصل بالوجهة من خلال Proxy أو Worker.
    

ده بيساعدك تعرف إذا كانت الحالة Basic أو Blind أو Second-Order SSRF.

### Step 6: Verify Impact Safely

لو ظهر احتمال للوصول إلى خدمة داخلية، لا تختبر أنظمة ليست ضمن النطاق المصرح به.

استخدم بيئة مختبر أو خدمة داخلية تجريبية، وسجل أقل قدر ممكن من البيانات لإثبات المشكلة.

### Step 7: Document the Evidence

سجل:

- Endpoint.
    
- Parameter.
    
- Request وResponse بعد تنقيح الأسرار.
    
- Callback Evidence.
    
- التأثير الأمني المثبت.
    
- القيود التي منعت التحقق من بقية الأثر.
    

---

## 10. SSRF في PortSwigger Labs

في PortSwigger Web Security Academy، ادرس الموضوع بالترتيب التالي:

1. اكتشاف SSRF في الوظائف التي تجلب URL.
    
2. الوصول إلى موارد داخلية ضمن المختبر.
    
3. فهم SSRF التي تكشف Response.
    
4. فهم Blind SSRF باستخدام Out-of-Band Interaction.
    
5. فهم طرق تجاوز التحقق من URL في المختبرات المخصصة لذلك.
    
6. دراسة Second-Order SSRF إذا كان المختبر يغطيها.
    
7. مراجعة وسائل الحماية ومواطن فشلها.
    

### استخدام Burp Suite

الأدوات المفيدة:

- Proxy: لمراقبة وتعديل الطلبات.
    
- HTTP history: لتحديد الـ Endpoints والـ Parameters.
    
- Repeater: لتجربة تغييرات منظمة على الطلب.
    
- Collaborator: لاختبار Out-of-Band Interactions عند توفره في بيئتك.
    

لو Burp Collaborator غير متاح في نسختك أو إعدادك، استخدم وسيلة Callback أخرى مسموحًا بها في المختبر، أو اعتمد على أدلة المختبر المتاحة.

لا تعتمد على Response وحده؛ في Blind SSRF قد تكون الـ Callback هي الدليل الأهم.

---

## 11. URL Validation وSSRF Bypass

بعض التطبيقات تحاول منع SSRF باستخدام فحص بسيط، مثل رفض النصوص التي تحتوي على localhost أو عناوين داخلية معروفة.

المشكلة أن URL Parsing أعقد من مجرد البحث عن كلمة داخل النص.

### A. اختلاف تفسير URL

قد يختلف تفسير URL بين:

- كود التحقق.
    
- مكتبة HTTP Client.
    
- Proxy.
    
- Resolver.
    
- النظام الذي ينفذ الاتصال.
    

لو مكوّن التحقق فسر العنوان بطريقة ومكوّن الاتصال فسره بطريقة أخرى، ممكن يحصل اختلاف أمني.

### B. DNS Resolution

قد يتحقق التطبيق من اسم نطاق، ثم يتغير عنوان IP الذي يحل إليه الاسم قبل الاتصال.

هذا قد يسبب DNS Rebinding أو Time-of-Check to Time-of-Use (TOCTOU) issues، حسب التصميم.

### C. Redirects

قد يسمح التطبيق برابط خارجي، ثم يستقبل Redirect إلى وجهة غير مسموحة.

لو الـ HTTP Client يتبع التحويلات تلقائيًا دون إعادة التحقق من كل وجهة، ممكن تتجاوز الحماية.

### D. IPv4 وIPv6

حماية تعتمد على صيغة واحدة من العناوين قد تفشل في التعامل مع صيغ أو عناوين مكافئة أخرى.

### E. Userinfo وPort وEncoding

صياغة URL تشمل أجزاء متعددة:

- Scheme.
    
- Userinfo اختياري.
    
- Host.
    
- Port اختياري.
    
- Path.
    
- Query.
    
- Fragment.
    

لازم التحقق يعتمد على Parser موثوق وقرارات واضحة حول كل جزء، وليس Regex بسيطًا.

### F. DNS Rebinding

في بعض الهجمات، يتم استخدام اسم نطاق تتغير نتيجة DNS الخاصة به بين التحقق والاتصال.

النتيجة المحتملة: التحقق يسمح بعنوان، بينما الاتصال اللاحق يذهب إلى عنوان آخر.

الدفاع الفعلي يحتاج إلى ربط التحقق بالاتصال النهائي، وضبط DNS والـ Network Egress، وليس مجرد التحقق الأولي من الاسم.

**مهم:** مش كل اختلاف في URL يمثل Bypass فعليًا؛ لازم تثبت أن التطبيق اتصل بوجهة غير مسموحة.

---

## 12. الفرق بين SSRF وOpen Redirect

### Open Redirect

التطبيق يسمح بتوجيه المستخدم إلى URL خارجي غير موثوق.

عادةً المتصفح هو اللي بيتبع التحويل.

### SSRF

السيرفر نفسه هو اللي يتصل بالوجهة غير الموثوقة.

### العلاقة بينهما

أحيانًا يمكن استغلال Open Redirect على نطاق مسموح به لتوجيه HTTP Client إلى وجهة أخرى، إذا كان التطبيق المصاب يتبع Redirects.

لكن وجود Open Redirect لا يعني تلقائيًا وجود SSRF، ولا ينجح هذا السيناريو إلا لو آلية جلب الموارد تتبع التحويلات بطريقة تسمح بذلك.

---

## 13. Impact وSeverity

تقييم الخطورة يعتمد على عدة عوامل:

1. هل يمكن للمهاجم التحكم في الوجهة بالكامل؟
    
2. هل السيرفر يستطيع الوصول إلى شبكة داخلية؟
    
3. هل Response متاح للمهاجم؟
    
4. هل يمكن الوصول إلى Credentials أو بيانات حساسة؟
    
5. هل يوجد Authentication أو Authorization على الخدمة المستهدفة؟
    
6. هل الاتصال يسمح ببروتوكولات أو منافذ غير متوقعة؟
    
7. هل يوجد Egress Filtering؟
    
8. هل الاستغلال يحتاج صلاحيات مستخدم عادية أم حسابًا إداريًا؟
    

### أمثلة

- جلب صور من مواقع عامة فقط: قد يكون الخطر محدودًا إذا كانت القيود سليمة.
    
- قراءة بيانات من API داخلية: قد يكون الخطر أعلى.
    
- الوصول إلى Cloud Credentials بصلاحيات واسعة: قد يؤدي إلى أثر بالغ الخطورة.
    

لا تحدد Severity اعتمادًا على اسم الثغرة فقط؛ قيّم الأثر المثبت واحتمالية الاستغلال.

---

## 14. طرق الحماية من SSRF

### A. Allowlist للوجهات

أفضل اختيار غالبًا هو السماح بمجموعة محددة من الوجهات الموثوقة بدل السماح بأي URL.

مثال:

- السماح بنطاقات شركاء محددين.
    
- السماح بـ HTTPS فقط.
    
- تحديد Ports المسموح بها.
    
- رفض أي Host غير موجود في القائمة.
    

### B. URL Parsing موثوق

استخدم مكتبة موثوقة لتحليل URL، وقارن الأجزاء المستخرجة بقواعد محددة.

لا تعتمد على:

- String Matching وحده.
    
- Regex بسيط.
    
- رفض كلمة localhost فقط.
    

### C. Validate DNS and IP Addresses

تحقق من عناوين IP الناتجة عن حل DNS، مع مراعاة:

- IPv4.
    
- IPv6.
    
- Private Ranges.
    
- Loopback.
    
- Link-Local.
    
- العناوين غير القابلة للتوجيه العام حسب سياسة التطبيق.
    

ويجب منع تغير الوجهة بعد التحقق، قدر الإمكان، بالتعاون مع طبقة الاتصال والشبكة.

### D. Disable or Validate Redirects

عطّل اتباع Redirects إذا لم تكن ضرورية.

إذا كانت مطلوبة:

- أعد التحقق من كل وجهة.
    
- حدد عدد التحويلات.
    
- لا تسمح بتحويلات إلى وجهات محظورة.
    

### E. Network Egress Filtering

اجعل الشبكة تمنع الـ Backend من الاتصال بوجهات غير ضرورية.

مثال:

- السماح بالاتصال بالخدمات الخارجية المطلوبة فقط.
    
- منع الوصول إلى شبكات الإدارة.
    
- منع الاتصال بالمنافذ الحساسة.
    
- فرض قيود على مستوى Firewall أو Proxy.
    

دي طبقة مهمة حتى لو فشل التحقق داخل التطبيق.

### F. Cloud Metadata Protections

استخدم وسائل الحماية الموصى بها من مزود Cloud:

- حماية Metadata Endpoint.
    
- تقييد صلاحيات IAM أو Managed Identity.
    
- استخدام أقل صلاحية ممكنة.
    
- منع التطبيقات غير الموثوقة من الوصول إلى Metadata.
    

### G. Safe HTTP Client Configuration

حدد:

- Allowed Schemes.
    
- Timeout.
    
- Maximum Response Size.
    
- Redirect Policy.
    
- Proxy Configuration.
    
- Connection Limits.
    

### H. Avoid Passing Arbitrary URLs

لو المستخدم محتاج يختار موردًا، استخدم معرفًا داخليًا أو قائمة موارد محددة بدل استقبال URL كامل، إذا كان ذلك مناسبًا للتصميم.

### I. Logging and Monitoring

راقب:

- طلبات Backend إلى عناوين داخلية غير متوقعة.
    
- محاولات الاتصال بمنافذ غير معتادة.
    
- DNS Queries غير الطبيعية.
    
- معدلات Redirects المرتفعة.
    
- أخطاء DNS والاتصال المتكررة.
    

مع تجنب تسجيل Tokens أو بيانات اعتماد أو محتوى حساس دون ضرورة.

---

## 15. مثال آمن لفكرة الحماية

بدل الكود غير الآمن:

```
url = user_input
response = http_client.get(url)
```

يمكن أن يكون التصميم أكثر أمانًا بهذه الخطوات:

1. تحليل URL باستخدام مكتبة موثوقة.
    
2. رفض أي Scheme غير مسموح به.
    
3. التأكد من أن Host ضمن Allowlist.
    
4. التأكد من Port المسموح.
    
5. حل DNS والتحقق من العناوين.
    
6. منع الوصول إلى العناوين الداخلية غير المصرح بها.
    
7. الاتصال بطريقة تمنع إعادة التوجيه غير المراقبة أو تعيد التحقق عند كل Redirect.
    
8. تطبيق Network Egress Controls.
    
9. فرض Timeout وحد أقصى لحجم الرد.
    
10. تسجيل الحدث بطريقة لا تكشف أسرارًا.
    

**ملحوظة:** المثال ده تصميم دفاعي عام، وليس مكتبة جاهزة تضمن الحماية بمجرد تطبيق الخطوات حرفيًا.

---

## 16. أخطاء شائعة أثناء اختبار SSRF

### الخطأ الأول: اعتبار أي URL Parameter ثغرة

وجود URL في الطلب ليس دليلًا كافيًا.

لازم تثبت أن السيرفر هو اللي اتصل بالوجهة.

### الخطأ الثاني: الاعتماد على Response فقط

في Blind SSRF ممكن التطبيق لا يعرض أي محتوى من الوجهة.

### الخطأ الثالث: تجاهل DNS وRedirects

قد تحدث المشكلة أثناء حل الاسم أو بعد تحويل الطلب إلى وجهة أخرى.

### الخطأ الرابع: افتراض أن localhost يعني دائمًا السيرفر المستهدف

في البيئات متعددة الحاويات أو الـ Proxies أو الخدمات الموزعة، لازم تفهم أي مكوّن هو اللي ينفذ الاتصال.

### الخطأ الخامس: اعتبار كل SSRF حرجة

الخطورة تعتمد على إمكانية الوصول والأثر والصلاحيات.

### الخطأ السادس: تجاهل نطاق الاختبار

لا تختبر موارد داخلية أو Cloud Metadata خارج النطاق المصرح به.

### الخطأ السابع: الخلط بين DNS Lookup وHTTP Request

ظهور DNS Query يثبت أن جهة ما حاولت حل الاسم، لكنه لا يثبت وحده أن اتصال HTTP اكتمل أو أن محتوى الخدمة تم الوصول إليه.

---

## 17. نموذج تقرير Pentest لثغرة SSRF

### Title

Server-Side Request Forgery (SSRF) in URL Import Functionality

### Description

The application accepts a user-controlled URL and uses it to initiate a server-side request without sufficient validation of the destination. Under the tested conditions, an attacker may cause the server to access unintended destinations.

### Affected Endpoint

يُكتب الـ Endpoint الفعلي هنا.

### Affected Parameter

يُكتب اسم الـ Parameter الفعلي هنا.

### Steps to Reproduce

1. تسجيل الدخول إذا كانت الوظيفة تتطلب ذلك.
    
2. فتح الوظيفة المتأثرة.
    
3. إرسال URL إلى Callback Endpoint تملكه أو إلى مورد مخصص للمختبر.
    
4. مراقبة الطلب الوارد في سجل الـ Callback.
    
5. تسجيل الأدلة التي تثبت أن الطلب صادر من جهة الخادم.
    
6. توثيق أي أثر إضافي تم إثباته ضمن النطاق المصرح به.
    

### Impact

Depending on the server's network access and the affected functionality, SSRF may expose internal services or sensitive information, or allow unauthorized server-side interactions.

### Remediation

Implement strict destination allowlisting, robust URL parsing, IP and DNS validation, redirect controls, network egress restrictions, and appropriate Cloud Metadata protections.

---

## 18. أسئلة مقابلات مهمة (Interview Questions)

### Q1: What is SSRF?

SSRF is a vulnerability that allows an attacker to influence a server-side application into making requests to unintended destinations.

### Q2: Why is SSRF dangerous?

Because the server may have access to internal services, privileged APIs, or cloud metadata that are not directly accessible to an external attacker.

### Q3: What is Blind SSRF?

It is SSRF where the attacker cannot directly see the response from the destination. Out-of-band interactions may help confirm the server-side request.

### Q4: What is the difference between Basic SSRF and Blind SSRF?

In a response-returning SSRF, some response content is available to the attacker. In Blind SSRF, the response is not directly exposed.

### Q5: How do you detect SSRF?

Identify server-side URL-fetching functionality, use controlled test destinations, inspect request behavior, and confirm callbacks in an authorized environment.

### Q6: What is Second-Order SSRF?

It occurs when a stored URL is processed later by another component, such as a background worker.

### Q7: How can SSRF affect cloud environments?

Depending on the configuration, SSRF may allow access to metadata endpoints or credentials associated with a cloud workload.

### Q8: What is the best mitigation?

Use strict allowlisting, robust URL and DNS validation, safe redirect handling, least privilege, and network egress controls.

### Q9: Does SSRF always lead to RCE?

No. RCE is not an automatic consequence. It depends on the reachable services, their behavior, and additional vulnerabilities or misconfigurations.

### Q10: Is blocking localhost enough?

No. A robust defense must account for internal IP ranges, IPv6, DNS resolution, redirects, alternate URL parsing, and network-level restrictions.

---

## 19. ملخص للحفظ السريع

- SSRF = Server-Side Request Forgery.
    
- المهاجم يجعل السيرفر يرسل Request إلى وجهة غير مقصودة.
    
- السبب الشائع: جلب URL يتحكم فيه المستخدم دون تحقق مناسب.
    
- Basic SSRF: قد يعرض التطبيق Response من الوجهة.
    
- Blind SSRF: لا يعرض التطبيق Response مباشرة.
    
- Second-Order SSRF: يتم استخدام URL المخزن لاحقًا.
    
- أخطر النتائج المحتملة: الوصول إلى Internal Services أو Cloud Metadata أو معلومات حساسة.
    
- اكتشافها: تحديد URL-fetching functionality واستخدام Callback مراقب في بيئة مصرح بها.
    
- Burp Repeater مفيد لتغيير الطلبات ومقارنة السلوك.
    
- Burp Collaborator أو بديل Callback يساعد في اختبار Blind SSRF.
    
- أهم الدفاعات: Allowlist + URL Parsing + DNS/IP Validation + Redirect Controls + Egress Filtering + Least Privilege.
    
- وجود URL Parameter لا يكفي لإثبات الثغرة.
    
- SSRF لا تعني تلقائيًا RCE.
    
- يجب اختبارها داخل نطاق مصرح به فقط.
    

---

## 20. أهم المصطلحات

|Term|Meaning|
|---|---|
|SSRF|تزوير الطلبات من جهة الخادم|
|Backend|الجزء الذي ينفذ منطق التطبيق على السيرفر|
|Internal Service|خدمة داخلية غير مخصصة للوصول العام|
|Loopback|عنوان يرجع إلى الجهاز المحلي|
|DNS Resolution|تحويل اسم النطاق إلى عنوان IP|
|Redirect|تحويل الطلب إلى URL آخر|
|Blind SSRF|SSRF بدون عرض مباشر لرد الوجهة|
|OAST|اختبار التفاعلات الخارجية خارج القناة الأساسية|
|Callback|طلب وارد إلى نقطة مراقبة تتحكم فيها|
|Allowlist|قائمة بالوجهات المسموح بها|
|Egress Filtering|تقييد الاتصالات الصادرة من الشبكة|
|DNS Rebinding|تغيير نتائج DNS لاستغلال اختلاف وقت التحقق عن وقت الاتصال|
|Second-Order SSRF|SSRF تظهر عند استخدام مدخل مخزن لاحقًا|
|Cloud Metadata|معلومات توفرها بيئة Cloud للمثيل أو الـ Workload|

## الخلاصة النهائية

الفكرة الأساسية في SSRF مش مجرد إنك تغيّر URL؛ الفكرة إنك تستغل الثقة اللي التطبيق بيديها للطلبات الصادرة من السيرفر.

كمختبر اختراق، مهم تعرف:

1. فين التطبيق بيعمل Server-Side Requests.
    
2. إزاي تثبت أن السيرفر هو اللي اتصل بالوجهة.
    
3. هل فيه Response ظاهر أم Blind Interaction.
    
4. إيه الوجهات التي يمكن الوصول إليها فعليًا.
    
5. ما الأثر الأمني الذي تستطيع إثباته.
    
6. إزاي تمنع المشكلة على مستوى التطبيق والشبكة معًا.