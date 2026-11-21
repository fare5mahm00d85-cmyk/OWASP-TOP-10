
---
# 0. قبل ما نبدأ: إحنا بنتعامل مع إيه أصلًا؟

عشان تفهم LFI وPath Traversal، لازم الأول تعرف إن أي Web Application ممكن يتعامل مع ملفات موجودة على السيرفر.

مثلاً عندنا موقع:

```text
https://example.com
```

وعلى السيرفر ممكن يكون عنده:

```text
/var/www/html/
```

وجواه:

```text
index.php
login.php
images/
uploads/
documents/
```

والـ `images` مثلًا:

```text
images/
├── logo.png
├── cat.jpg
└── profile.jpg
```

لما المستخدم يطلب:

```text
/image?file=cat.jpg
```

الـ application ممكن يبني المسار:

```text
/var/www/html/images/cat.jpg
```

ويقرأ الملف ويرجعه للمستخدم.

لحد هنا كل شيء طبيعي.

المشكلة تبدأ لما:

> المستخدم يكون قادرًا على التحكم في الـ path أكثر مما يجب.

وهنا ندخل في Path Traversal وLFI.

---

# 1. يعني إيه Filesystem؟

## Filesystem

هو الطريقة التي ينظم بها نظام التشغيل:

- الملفات
    
- الـ folders
    
- أماكن تخزين الملفات
    
- permissions
    
- paths
    

مثال:

```text
/home/fares/
```

جواه:

```text
documents/
downloads/
pictures/
```

وجوا:

```text
documents/
```

ممكن يكون:

```text
cv.pdf
project.docx
notes.txt
```

إذن عندنا:

```text
Filesystem
│
├── Directory
│   ├── File
│   ├── File
│   └── Directory
│
└── Directory
```

---

# 2. يعني إيه Directory؟

Directory = Folder.

مثال:

```text
/var/www/html/images/
```

الـ `images` هنا Directory.

جواه:

```text
cat.jpg
dog.jpg
logo.png
```

---

# 3. يعني إيه File Path؟

الـ Path هو العنوان الذي يحدد مكان الملف.

مثلاً:

```text
/var/www/html/images/cat.jpg
```

نقدر نفككه:

```text
/
└── var
    └── www
        └── html
            └── images
                └── cat.jpg
```

آخر جزء:

```text
cat.jpg
```

هو الملف.

أما:

```text
/var/www/html/images/
```

فهو المكان الذي يحتوي عليه.

---

# 4. Root Directory

في Linux يوجد:

```text
/
```

وده يسمى:

```text
Root Directory
```

وهو أعلى مستوى في filesystem.

مثلاً:

```text
/
├── etc
├── home
├── var
├── tmp
├── usr
└── ...
```

لاحظ:

```text
/
```

مش هو نفس:

```text
/root/
```

الأول:

```text
/
```

هو Root Directory.

أما:

```text
/root/
```

فهو Home Directory للمستخدم root.

---

# 5. `.` و `..`

دي من أهم الحاجات في الموضوع كله.

## النقطة الواحدة

```text
.
```

تعني:

> Current Directory

أي الـ directory الحالي.

مثال:

```text
/home/fares/
```

فإن:

```text
./test.txt
```

تعني:

```text
/home/fares/test.txt
```

---

# 6. النقطتين `..`

```text
..
```

تعني:

> Parent Directory

يعني الـ folder الذي يحتوي على الـ folder الحالي.

مثال:

```text
/home/fares/documents/
```

الـ Parent Directory هو:

```text
/home/fares/
```

إذن:

```text
../
```

ترجع خطوة للخلف.

---

# 7. مثال مهم جدًا

عندنا:

```text
/home/fares/documents/project/
```

لو استخدمنا:

```text
../
```

نصبح:

```text
/home/fares/documents/
```

ولو:

```text
../../
```

نصبح:

```text
/home/fares/
```

ولو:

```text
../../../
```

نصبح:

```text
/home/
```

ولو:

```text
../../../../
```

نصل إلى:

```text
/
```

الفكرة:

```text
../ = خطوة للخلف
```

---

# 8. Relative Path

Relative Path يعني:

> المسار محسوب بالنسبة لمكان معين.

مثلاً لو الـ current directory:

```text
/home/fares/
```

وعندنا:

```text
documents/test.txt
```

فالمسار الكامل:

```text
/home/fares/documents/test.txt
```

لكن المستخدم كتب:

```text
documents/test.txt
```

لأنه Relative Path.

---

# 9. Absolute Path

Absolute Path هو المسار الكامل.

Linux:

```text
/etc/passwd
```

Windows:

```text
C:\Windows\win.ini
```

مثال:

```text
/home/fares/project/test.txt
```

ده Absolute Path.

---

# 10. الآن: Path Traversal

## التعريف البسيط

Path Traversal يعني:

> المستخدم يستطيع التلاعب بمسار الملف والخروج من الـ directory الذي كان التطبيق يقصده.

مثال:

التطبيق يسمح:

```text
/var/www/files/
```

المستخدم المفروض يختار:

```text
report.pdf
```

لكن بدل ذلك يرسل:

```text
../secret.txt
```

فيحاول التطبيق الوصول إلى:

```text
/var/www/files/../secret.txt
```

والـ filesystem يحلها إلى:

```text
/var/www/secret.txt
```

المستخدم خرج من:

```text
files/
```

وده هو جوهر Path Traversal.

---

# 11. لماذا اسمها Traversal؟

Traversal معناها تقريبًا:

> التنقل / العبور خلال المسارات.

المهاجم بيتنقل من:

```text
Directory
```

إلى:

```text
Parent Directory
```

ثم:

```text
Parent Directory
```

حتى يصل لمكان لم يكن المفروض أن يصل إليه.

---

# 12. الشكل الأساسي للثغرة

التطبيق:

```text
User Input
     ↓
File Path
     ↓
Filesystem
```

التطبيق الآمن:

```text
User Input
     ↓
Validation
     ↓
Safe File Mapping
     ↓
Filesystem
```

التطبيق الضعيف:

```text
User Input
     ↓
Filesystem
```

---

# 13. مثال Web Application

لنفترض عندنا:

```http
GET /download?file=report.pdf
```

الـ backend ممكن يعمل:

```text
base_directory + user_input
```

فتصبح:

```text
/var/www/files/report.pdf
```

لو الـ input:

```text
report.pdf
```

تمام.

لكن لو المستخدم يستطيع إدخال:

```text
../secret.txt
```

فالمسار يصبح:

```text
/var/www/files/../secret.txt
```

وبعد normalization:

```text
/var/www/secret.txt
```

---

# 14. يعني إيه Normalization؟

دي نقطة مهمة جدًا.

الـ filesystem أو application قد يقوم بتبسيط المسار.

مثلاً:

```text
/a/b/../c
```

معناها:

```text
/a/c
```

ليه؟

لأن:

```text
b/..
```

يعني:

> ادخل b ثم ارجع منه.

فتلغي بعضهما.

مثال:

```text
/home/fares/docs/../file.txt
```

تصبح:

```text
/home/fares/file.txt
```

---

# 15. هنا تظهر المشكلة

لو التطبيق يعمل:

```text
/allowed/directory/
+
user input
```

والمستخدم يتحكم في input:

```text
../
```

فالـ normalization قد يخرج المسار من الـ allowed directory.

لذلك:

```text
Path Traversal
```

تعتمد بشكل أساسي على:

```text
Path manipulation
+
Path resolution
```

---

# 16. LFI

## Local File Inclusion

LFI =

```text
Local File Inclusion
```

معناها:

> التطبيق يسمح للمستخدم بالتأثير على ملف محلي يتم تحميله أو تضمينه.

مثال PHP:

```php
<?php
$page = $_GET['page'];
include($page);
?>
```

عندنا URL:

```text
/index.php?page=home.php
```

الـ application يأخذ:

```text
home.php
```

ويضعه في:

```php
include()
```

---

# 17. ليه ده خطر؟

لأن:

```text
page
```

جاي من المستخدم.

يعني:

```text
User
 ↓
page
 ↓
include()
```

بدل أن يكون:

```text
Server-controlled value
 ↓
include()
```

---

# 18. مثال طبيعي

الموقع:

```text
/index.php?page=home.php
```

عنده:

```text
pages/
├── home.php
├── about.php
└── contact.php
```

المفروض المستخدم يختار واحدة منهم.

مثلاً:

```text
?page=about.php
```

---

# 19. أين الخطأ؟

لو التطبيق لا يتحقق من قيمة:

```text
page
```

فالمستخدم قد يحاول تغيير المسار.

مثلاً بشكل تعليمي داخل Lab:

```text
?page=../../../../some/local/file
```

لو التطبيق قام بتحميل ملف محلي خارج المكان المتوقع، فهنا ظهر تأثير LFI.

---

# 20. الفرق بين File Read و File Inclusion

دي نقطة لازم تفهمها.

## File Read

التطبيق يسمح لك بقراءة ملف:

```text
secret.txt
```

فتشوف:

```text
file contents
```

---

## File Inclusion

التطبيق يقوم بتحميل الملف ضمن آلية الـ application.

خصوصًا في لغات مثل PHP، `include()` و`require()` لهما سلوك مختلف عن مجرد قراءة bytes من ملف.

لذلك:

```text
Arbitrary File Read
```

و:

```text
LFI
```

مصطلحان مرتبطان، لكن ليسا متطابقين في كل حالة.

---

# 21. هل Path Traversal = LFI؟

لا.

احفظ:

```text
Path Traversal ≠ LFI
```

Path Traversal:

> التلاعب بالـ path.

LFI:

> التحكم في local file يتم تحميله أو تضمينه.

لكن ممكن يجتمعوا:

```text
LFI
+
Path Traversal
```

---

# 22. مثال يجمع الاثنين

عندنا:

```text
/index.php?page=home.php
```

والتطبيق يعمل:

```php
include($_GET['page']);
```

لو المستخدم استطاع استخدام traversal للوصول إلى ملف محلي آخر:

```text
../../some/file
```

فهنا:

```text
LFI
```

استخدم:

```text
Path Traversal
```

كوسيلة للوصول إلى الملف.

---

# 23. RFI

RFI:

```text
Remote File Inclusion
```

الفرق:

```text
LFI
→ Local

RFI
→ Remote
```

LFI:

```text
Local filesystem
```

RFI:

```text
Remote resource
```

RFI يعتمد على إعدادات التطبيق واللغة والبيئة، وليس كل تطبيق يسمح به.

---

# 24. أشهر Parameters

أثناء اختبار Web Application، ابحث عن:

```text
file=
filename=
path=
filepath=
page=
template=
include=
document=
folder=
directory=
download=
image=
img=
attachment=
resource=
```

مثلاً:

```text
/download?file=
```

أو:

```text
/view?page=
```

أو:

```text
/image?filename=
```

لكن وجود parameter لا يعني وجود vulnerability.

---

# 25. كيف تفكر كمختبر؟

لما تشوف:

```text
/download?file=report.pdf
```

اسأل:

### السؤال 1

هل `file` يتحكم في ملف؟

### السؤال 2

هل التطبيق يبني filesystem path باستخدامه؟

### السؤال 3

ما هو الـ intended directory؟

### السؤال 4

هل توجد validation؟

### السؤال 5

هل أستطيع تغيير المسار؟

### السؤال 6

هل أستطيع الوصول خارج المكان المسموح؟

### السؤال 7

ما هو تأثير ذلك؟

دي طريقة التفكير الصحيحة.

---

# 26. Burp Suite

لما تلاقي request:

```http
GET /download?file=report.pdf HTTP/1.1
Host: lab.example
```

اعمل:

```text
Proxy
 ↓
Capture Request
 ↓
Send to Repeater
```

ثم في Repeater:

```text
Original
file=report.pdf
```

ابدأ الاختبار داخل الـ Lab.

الهدف ليس تجربة payloads عشوائية.

الهدف:

> معرفة كيف يتعامل التطبيق مع path.

---

# 27. لماذا Repeater؟

لأنك تحتاج تقارن:

```text
Normal Request
```

مع:

```text
Modified Request
```

وتشوف الفرق.

مثلاً:

```text
Original:
file=image.jpg
```

ثم قيمة traversal مناسبة للـ Lab.

وتقارن:

```text
Status
Length
Response
Error
Behavior
```

---

# 28. Status Code

قد ترى:

```text
200 OK
```

أو:

```text
403 Forbidden
```

أو:

```text
404 Not Found
```

أو:

```text
500 Internal Server Error
```

لكن:

> Status code وحده لا يثبت vulnerability.

---

# 29. Response Body

ده أهم أحيانًا.

مثلاً الطلب الطبيعي يرجع:

```text
Image content
```

والطلب المعدل يرجع:

```text
Unexpected file content
```

هنا أصبح عندك دليل أقوى.

---

# 30. Response Length

في Burp:

```text
Length: 1250
```

ثم:

```text
Length: 1850
```

الاختلاف ممكن يكون مهم.

لكن:

> اختلاف الـ length وحده ليس إثباتًا.

لازم تعرف لماذا اختلف response.

---

# 31. Error Messages

مثلاً:

```text
File not found
```

أو:

```text
No such file or directory
```

أو:

```text
Permission denied
```

هذه الأخطاء قد تكشف معلومات عن:

```text
Filesystem
Directory structure
Operating system
Application behavior
```

لكن error وحده لا يعني أن الثغرة confirmed.

---

# 32. `../` بالتفصيل

لو current directory:

```text
/var/www/app/uploads/
```

فـ:

```text
../
```

يعني:

```text
/var/www/app/
```

و:

```text
../../
```

يعني:

```text
/var/www/
```

و:

```text
../../../
```

يعني:

```text
/var/
```

وهكذا.

---

# 33. ليه عدد `../` مهم؟

لأنك لازم تعرف:

```text
Current Location
```

مثلاً:

```text
/var/www/app/uploads/
```

لو تريد الوصول إلى:

```text
/var/www/app/config.txt
```

فأنت تحتاج الرجوع من:

```text
uploads/
```

إلى:

```text
app/
```

أي:

```text
../
```

ثم:

```text
config.txt
```

فتصبح الفكرة:

```text
../config.txt
```

---

# 34. Encoding

أحيانًا التطبيق يعمل filtering على:

```text
../
```

وهنا قد يكون الـ URL encoded representation مختلفًا.

مثلاً:

```text
.
```

يمكن تمثيله URL encoded كـ:

```text
%2e
```

و:

```text
/
```

كـ:

```text
%2f
```

فتصبح:

```text
%2e%2e%2f
```

تمثيلًا encoded لـ:

```text
../
```

---

# 35. لماذا قد يعمل Encoding؟

لأن الطلب يمر بمراحل:

```text
HTTP Request
      ↓
Web Server
      ↓
Decoder
      ↓
Framework
      ↓
Application
      ↓
Filesystem
```

إذا تم:

```text
Validation
```

في مرحلة، ثم:

```text
Decoding
```

في مرحلة أخرى، فقد تظهر اختلافات في السلوك.

وده سبب دراسة:

```text
Encoding
Decoding
Normalization
```

في Path Traversal.

---

# 36. Double Encoding

المفهوم:

```text
Encoding
```

مرة.

ثم:

```text
Encoding
```

مرة أخرى.

لكن لا تحفظ payload معين كأنه قاعدة.

اسأل:

> كم مرة يقوم التطبيق بالـ decoding؟

لو:

```text
Decode مرة واحدة
```

فطريقة معينة قد تتصرف بشكل مختلف عن:

```text
Decode مرتين
```

وده يظهر كثيرًا في Labs التعليمية.

---

# 37. Windows Path Traversal

في Windows يوجد:

```text
..\
```

مثال:

```text
..\secret.txt
```

لأن Windows يستخدم غالبًا:

```text
\
```

كـ separator.

وقد تتعامل بعض التطبيقات أيضًا مع:

```text
/
```

لأسباب مرتبطة بالـ framework أو API.

---

# 38. Linux Path Traversal

في Linux:

```text
../
```

مثال:

```text
../../etc/passwd
```

---

# 39. Absolute Path Testing

بعض التطبيقات تسمح بالـ absolute path.

Linux:

```text
/etc/passwd
```

Windows:

```text
C:\Windows\win.ini
```

إذا التطبيق يقبل absolute path مباشرة، قد لا تحتاج traversal أصلًا.

---

# 40. لماذا `/etc/passwd` مشهور في Labs؟

لأنه:

- موجود عادةً على Linux
    
- معروف
    
- نصي
    
- سهل التحقق من محتواه
    
- لا يحتوي عادةً على كلمات مرور النظام الفعلية
    

مثال:

```text
root:x:0:0:root:/root:/bin/bash
```

---

# 41. Windows `win.ini`

في بعض Labs الخاصة بـ Windows يمكن استخدام ملف معروف مثل:

```text
C:\Windows\win.ini
```

للتأكد أن قراءة الملف المحلي نجحت.

الفكرة ليست الملف نفسه.

الفكرة:

> إثبات أن التطبيق خرج من المكان المقصود ووصل إلى ملف آخر.

---

# 42. Sensitive Files

لو confirmed file read، التأثير يعتمد على الملف.

أمثلة عامة:

```text
Configuration
Source Code
Environment Files
Credentials
API Keys
Private Documents
Application Secrets
```

لكن لا تفترض أن كل ملف موجود قابل للقراءة.

الصلاحيات مهمة.

---

# 43. File Permissions

حتى لو عندك Path Traversal:

```text
../../secret
```

قد يرفض النظام:

```text
Permission denied
```

لأن الـ Web Application يعمل بحساب معين.

مثلاً:

```text
Web Application
     ↓
www-data
```

قد يستطيع قراءة بعض الملفات وليس كلها.

إذن:

```text
Vulnerability
≠
Unlimited access
```

---

# 44. Arbitrary File Read

لو المهاجم يستطيع اختيار ملف ويقرأه:

```text
Arbitrary File Read
```

يعني:

> التطبيق يسمح بقراءة ملفات خارج ما كان المفروض للمستخدم الوصول إليه.

وده ممكن يكون نتيجة:

```text
Path Traversal
```

أو:

```text
LFI
```

حسب السياق.

---

# 45. Arbitrary File Write

نفس الفكرة ولكن بدل:

```text
Read
```

عندنا:

```text
Write
```

مثلاً:

```text
User-controlled filename
+
Upload
+
Weak path validation
```

قد يؤدي إلى:

```text
Writing file outside intended directory
```

وده يسمى:

```text
Arbitrary File Write
```

---

# 46. لماذا File Write خطيرة؟

لأن الكتابة في مكان غير مسموح قد تؤدي إلى:

```text
Overwrite configuration
Modify application files
Create unexpected files
Potentially enable further attacks
```

لكن التأثير النهائي يعتمد على:

- permissions
    
- file type
    
- server configuration
    
- execution behavior
    

---

# 47. LFI و Source Code

لو التطبيق يستخدم PHP مثلًا:

```text
page=about.php
```

عادةً:

```text
PHP
```

يتم تنفيذه.

لكن في بعض السيناريوهات التعليمية يمكن استخدام آليات تجعل التطبيق يعرض source code بدل تنفيذه.

وده مهم لأن source code قد يكشف:

```text
Database configuration
API keys
Internal endpoints
Application logic
Secrets
```

---

# 48. PHP Wrappers

في PHP يوجد مفهوم:

```text
Stream Wrappers
```

ومن أشهر الأشياء التي تظهر في LFI Labs:

```text
php://
```

مثل:

```text
php://filter
```

الفكرة الأساسية:

> بدل التعامل مع الملف بالطريقة العادية، PHP تستخدم wrapper لتغيير طريقة التعامل مع الـ stream.

في Labs، هذا قد يساعد في **قراءة source code** بطريقة مختلفة.

---

# 49. `php://filter`

مثال تعليمي شائع في Labs يكون بالشكل العام:

```text
php://filter/...
```

والهدف:

```text
Read source code
```

بدل:

```text
Execute PHP
```

لا تحفظ syntax فقط.

افهم:

```text
Normal include
→ PHP may execute

Filter wrapper
→ Can transform/read stream
```

---

# 50. LFI و Log Poisoning

موضوع متقدم.

الفكرة:

```text
Attacker-controlled input
        ↓
Web Server Log
        ↓
Malicious content stored in log
        ↓
LFI reads/includes log
        ↓
Possible code execution
```

لكن ده مش:

```text
LFI = RCE
```

بل:

```text
LFI
+
Writable/controlled file
+
Execution context
+
Suitable configuration
```

---

# 51. هل LFI دائمًا تؤدي إلى RCE؟

لا.

احفظ:

```text
LFI ≠ RCE
```

ممكن تكون:

```text
LFI
 ↓
File Read
```

فقط.

أو:

```text
LFI
 ↓
Source Code Disclosure
```

وفي سيناريو خاص:

```text
LFI
 ↓
Controlled File
 ↓
Execution
```

فتصبح Code Execution ممكنة.

---

# 52. RFI

RFI:

```text
Remote File Inclusion
```

يعني التطبيق يسمح بتحميل/تضمين resource من مصدر remote.

المفهوم:

```text
LFI:
Application → Local File

RFI:
Application → Remote Resource
```

RFI أقل شيوعًا في البيئات الحديثة بسبب إعدادات الأمان الحديثة، لكنه مهم كمفهوم.

---

# 53. Path Traversal في File Upload

تخيل:

```text
POST /upload
```

والـ application يستقبل:

```text
filename
```

ثم يستخدم:

```text
upload_directory + filename
```

لو الـ filename يتحكم في path بدون حماية، ممكن تظهر:

```text
Path Traversal
```

لكن هنا بدل:

```text
Read
```

قد يصبح:

```text
Write
```

---

# 54. الفرق بين Read و Write

```text
Path Traversal
       │
       ├── Read
       │    └── Arbitrary File Read
       │
       └── Write
            └── Arbitrary File Write
```

والـ impact مختلف حسب الحالة.

---

# 55. Path Traversal vs IDOR

### IDOR

```text
/account?id=100
```

تغيير ID:

```text
/account?id=101
```

المشكلة:

```text
Authorization
```

---

### Path Traversal

```text
/download?file=../../secret.txt
```

المشكلة:

```text
Filesystem Path
```

---

# 56. Path Traversal vs SSRF

### SSRF

```text
User Input
 ↓
URL
 ↓
Server makes network request
```

### Path Traversal

```text
User Input
 ↓
Filesystem path
 ↓
Server accesses file
```

---

# 57. Path Traversal vs SQL Injection

### SQL Injection

```text
Input
 ↓
SQL Query
 ↓
Database
```

### Path Traversal

```text
Input
 ↓
File Path
 ↓
Filesystem
```

---

# 58. Path Traversal vs Command Injection

### Command Injection

```text
Input
 ↓
OS Command
 ↓
Shell
```

### Path Traversal

```text
Input
 ↓
Path
 ↓
Filesystem
```

---

# 59. كيف تكتشفها في التطبيق؟

ابدأ بالـ attack surface:

```text
Download
Upload
Image Viewer
Document Viewer
Template Loader
Language Selector
Theme Selector
Page Loader
Backup Viewer
Export/Import
```

أي feature تتعامل مع:

```text
Files
```

تستحق الفحص.

---

# 60. مثال Language Selector

أحيانًا الموقع يعمل:

```text
?lang=en
```

والـ backend يستخدم:

```text
/languages/en.php
```

فالسؤال:

> هل `lang` مجرد ID داخلي، أم يتم استخدامه كـ path؟

لو المستخدم يستطيع التأثير في filesystem path:

```text
Path Traversal / LFI
```

قد يصبح احتمالًا يستحق الاختبار.

---

# 61. مثال Template Loader

مثلاً:

```text
?template=home
```

والـ backend يقرأ:

```text
templates/home.html
```

هنا عندك:

```text
User Input
      ↓
Template Path
```

وده attack surface مهم.

---

# 62. مثال Image Loader

```text
/image?filename=logo.png
```

المستخدم يطلب:

```text
logo.png
```

السيرفر يبحث عن الملف.

السؤال:

> هل filename مقيد داخل images directory؟

لو لا، فهنا نبحث عن Path Traversal.

---

# 63. Blacklist

Blacklist معناها:

> امنع قيمًا معينة.

مثلاً:

```text
if "../" exists:
    reject
```

المشكلة إن blacklist قد تفشل بسبب اختلاف:

```text
Encoding
Normalization
Separators
Case handling
Decoding order
```

لذلك:

> لا تعتمد على blacklist وحدها.

---

# 64. Allowlist

Allowlist:

> اسمح فقط بالقيم المطلوبة.

مثلاً:

```text
Allowed files:
home.html
about.html
contact.html
```

لو المستخدم أرسل:

```text
../../secret
```

يرفضه التطبيق لأنه ليس ضمن القائمة.

دي طريقة أقوى عندما يكون عدد الموارد معروفًا.

---

# 65. Server-Side Mapping

حل ممتاز:

بدل:

```text
/download?file=report.pdf
```

استخدم:

```text
/download?id=15
```

ثم:

```text
15 → report.pdf
```

الـ user لا يعرف ولا يتحكم في filesystem path الحقيقي.

---

# 66. Canonicalization

المبدأ:

```text
User Input
 ↓
Resolve/Clean Path
 ↓
Compare against Allowed Directory
 ↓
Allow/Reject
```

مثلاً:

```text
Base:
 /var/www/files/
```

بعد حل الـ path، لازم النتيجة تظل داخل:

```text
/var/www/files/
```

لو أصبحت:

```text
/var/www/secret.txt
```

ترفض.

---

# 67. Directory Boundary

دي من أهم الحمايات.

المطلوب:

```text
Resolved Path
```

يظل داخل:

```text
Allowed Directory
```

مش مجرد فحص:

```text
"../" not found
```

---

# 68. Secure Flow

التطبيق الآمن:

```text
User
 ↓
Input
 ↓
Validate
 ↓
Normalize/Canonicalize
 ↓
Resolve Path
 ↓
Check Boundary
 ↓
Authorization
 ↓
Read File
```

---

# 69. Vulnerable Flow

```text
User
 ↓
Filename
 ↓
Concatenate String
 ↓
Filesystem
```

مثلاً:

```text
"/var/www/files/" + user_input
```

بدون validation مناسب.

---

# 70. كيف تكتب Finding في تقرير Pentest؟

مثال:

### Title

```text
Path Traversal Allows Unauthorized File Access
```

### Description

> The application uses user-controlled input to construct a filesystem path without sufficient validation, allowing access outside the intended directory.

### Affected Parameter

```text
file
```

### Impact

حسب ما أثبت الاختبار:

```text
Unauthorized file read
```

أو:

```text
Sensitive information disclosure
```

### Remediation

```text
Use allowlists where possible.
Canonicalize paths.
Enforce directory boundaries.
Avoid direct use of user-controlled filesystem paths.
Use server-side file identifiers.
```

---

# 71. منهجية اختبار كاملة

```text
1. Find file-related functionality
        ↓
2. Identify parameter
        ↓
3. Understand normal behavior
        ↓
4. Identify intended directory
        ↓
5. Send request to Repeater
        ↓
6. Test path manipulation
        ↓
7. Compare responses
        ↓
8. Confirm access outside intended location
        ↓
9. Determine Read/Write
        ↓
10. Determine sensitivity
        ↓
11. Document evidence
        ↓
12. Recommend remediation
```

---

# 72. مثال كامل

عندنا:

```http
GET /download?file=report.pdf
```

التطبيق يتعامل مع:

```text
/var/www/uploads/report.pdf
```

أنت تعرف أن:

```text
uploads/
```

هي الـ intended directory.

تبدأ تفكيرك:

```text
file
 ↓
filesystem
 ↓
uploads/
 ↓
Can path escape?
```

لو استطعت الوصول إلى ملف خارج:

```text
uploads/
```

أصبح عندك دليل على Path Traversal.

---

# 73. لماذا لا نقول فورًا "LFI"؟

لأننا لازم نعرف **كيف** التطبيق يتعامل مع الملف.

هل:

```text
read()
```

فقط؟

أم:

```text
include()
```

؟

أم:

```text
template loader
```

؟

أم:

```text
download response
```

؟

التسمية الدقيقة مهمة.

---

# 74. أهم المصطلحات

## Path

عنوان الملف.

## Directory

Folder.

## Root

أعلى filesystem في Linux:

```text
/
```

## Parent Directory

المجلد الأعلى:

```text
..
```

## Relative Path

Path يعتمد على current location.

## Absolute Path

Path كامل يبدأ من root/drive.

## Traversal

التنقل عبر directories.

## Canonicalization

تحويل path إلى representation موحد/فعلي.

## Normalization

تبسيط path وحل عناصر مثل:

```text
.
..
```

## LFI

Local File Inclusion.

## RFI

Remote File Inclusion.

## Arbitrary File Read

قراءة ملف يختاره المهاجم خارج المسموح.

## Arbitrary File Write

كتابة ملف خارج المكان المسموح.

---

# 75. أشهر الأخطاء التي يقع فيها المبتدئ

### ❌ الخطأ 1

يفكر:

```text
../ = LFI
```

الصحيح:

```text
../ = Parent Directory traversal sequence
```

---

### ❌ الخطأ 2

يفكر:

```text
LFI = RCE
```

الصحيح:

```text
LFI may have different impacts.
RCE requires additional conditions.
```

---

### ❌ الخطأ 3

يفكر:

```text
200 = Vulnerable
```

الصحيح:

> لازم تفهم محتوى الـ response.

---

### ❌ الخطأ 4

يحفظ:

```text
../../../../etc/passwd
```

بدون فهم.

الصحيح:

> افهم current directory والـ target path.

---

### ❌ الخطأ 5

يخلط:

```text
IDOR
```

مع:

```text
Path Traversal
```

الصحيح:

```text
IDOR → Authorization
Traversal → Filesystem path
```

---

# 76. Cheat Sheet

## Core

```text
.   = Current Directory
..  = Parent Directory
/   = Linux Path Separator
\   = Windows Path Separator
```

## Concepts

```text
Path Traversal
Directory Traversal
LFI
RFI
Arbitrary File Read
Arbitrary File Write
```

## Linux

```text
/
etc/
home/
var/
tmp/
```

## Windows

```text
C:\
C:\Windows\
C:\Users\
```

## Encoding Concepts

```text
. → %2e
/ → %2f
```

---

# 77. Mind Map

```text
                 FILE-BASED VULNERABILITIES
                            │
             ┌──────────────┴──────────────┐
             │                             │
       PATH TRAVERSAL                     LFI
             │                             │
       Path Manipulation             Local File Loading
             │                             │
       ┌─────┴─────┐               ┌──────┴──────┐
       │           │               │             │
      Read        Write          File Read    Source Disclosure
       │           │               │             │
       └───────────┴───────────────┴─────────────┘
                            │
                          Impact
                            │
              ┌─────────────┴─────────────┐
              │                           │
       Information Disclosure       Further Attack
```

---

# 78. طريقة مذاكرة الموضوع

رتب مذاكرتك كالتالي:

```text
1. Filesystem
       ↓
2. File Path
       ↓
3. Absolute vs Relative
       ↓
4. . and ..
       ↓
5. Path Traversal
       ↓
6. Path Normalization
       ↓
7. URL Encoding
       ↓
8. LFI
       ↓
9. File Read
       ↓
10. PHP include()
       ↓
11. PHP Wrappers
       ↓
12. Advanced LFI
       ↓
13. Burp Repeater
       ↓
14. PortSwigger Labs
       ↓
15. Prevention
```

---

# ⭐ الخلاصة النهائية

## Path Traversal

```text
User Input
    ↓
File Path
    ↓
../
    ↓
Parent Directory
    ↓
Escape Intended Directory
    ↓
Unauthorized File Access
```

## LFI

```text
User Input
    ↓
Local File Parameter
    ↓
Application loads/includes file
    ↓
Unexpected Local File
    ↓
Possible File Read / Source Disclosure
```

## العلاقة

```text
Path Traversal
     ↓
طريقة للتلاعب بالـ path

LFI
     ↓
نوع من مشاكل Local File Loading/Including
```

وقد تستخدم:

```text
LFI + Path Traversal
```

معًا.

---

# 🧠 أهم 15 نقطة تحفظهم

```text
1. Filesystem = نظام تنظيم الملفات.

2. Directory = Folder.

3. Path = عنوان الملف.

4. . = Current Directory.

5. .. = Parent Directory.

6. ../ = الرجوع Directory واحد.

7. Path Traversal = الخروج من intended directory.

8. Absolute Path = المسار الكامل.

9. Relative Path = المسار بالنسبة لمكان معين.

10. LFI = Local File Inclusion.

11. Path Traversal ≠ LFI.

12. LFI ≠ RCE.

13. Arbitrary File Read = قراءة ملفات خارج المسموح.

14. أفضل حماية ليست blacklist فقط؛
    استخدم allowlist + canonicalization +
    boundary validation.

15. في الـ Pentest لا تحفظ payload فقط؛
    افهم:
    أين أنت؟
    أين الملف؟
    كيف التطبيق يبني الـ path؟
    وما الذي يحدث بعد إرسال الـ input؟
```

# 🎯 السؤال الذي لازم تسأله نفسك عند أي Lab

لو شفت:

```text
?page=
```

أو:

```text
?file=
```

أو:

```text
?filename=
```

ما تقولش:

> "دي LFI."

اسأل:

```text
ما الذي يتحكم فيه الـ parameter؟
        ↓
هل له علاقة بملف؟
        ↓
كيف يبني التطبيق الـ path؟
        ↓
ما هو الـ intended directory؟
        ↓
هل أستطيع التأثير على المسار؟
        ↓
هل أستطيع الوصول خارج المكان المسموح؟
        ↓
هل التطبيق يقرأ الملف أم يضمنه؟
        ↓
ما هو التأثير الحقيقي؟
```

**لو فهمت سلسلة الأسئلة دي، هتكون فهمت LFI وPath Traversal فعلًا، مش مجرد حافظ Payloads.**