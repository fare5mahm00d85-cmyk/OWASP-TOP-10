
---

https://sa.my/quickjack/quickjack.html  css ده موقع بيكتب جزء   فى الموقع 

---
1) Lab: Basic clickjacking with CSRF token protection 
	 هنا الثغره فى حذف لاكونت 
---
2) # Lab: Clickjacking with form input data prefilled from a URL parameter 
	 هنا الثغره فى تجديد الاكونت 
---
3) # Lab: Clickjacking with a frame buster script 
	هنا الازم نضيف ```<iframe sandbox="allow-forms"> ``` علشان نعدى الحمايه 
---
4) # Lab: Exploiting clickjacking vulnerability to trigger DOM-based XSS
	اللاب بيجمع بين **Clickjacking** و **DOM-based XSS** لاستغلال زر أو عنصر في صفحة الهدف لتنفيذ JavaScript في متصفح الضحية.  
	عند الضغط، الضحية يضغط على العنصر الحقيقي داخل الـ iframe، فيتم تمرير الـ payload للـ DOM ويتنفذ الـ XSS.  
	الهدف من اللاب هو استغلال الـ Clickjacking كوسيلة لإجبار الضحية على تنفيذ الـ DOM XSS بدون ما يعرف.
---
5) 