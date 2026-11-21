
---
1) # Lab: File path traversal, simple case 
	 انتا هنا لما تيجى تعرض محتوى الملف هتلاقى مش راضى لازم تخرج من المكن الاول باستخدام /../../../etc/passwd  
---
2) Lab: File path traversal, traversal sequences blocked with absolute path bypass 
	انتا هنا هتقرا الملف دايركت /etc/passwd 
---
3) Lab: File path traversal, traversal sequences stripped non-recursively 
	هنا الفكره ان هو بيمسح /.. فلازم تعملها دبل 
	//....//....//....//etc/passwd 
---
4) # Lab: File path traversal, traversal sequences stripped with superfluous URL-decode
	انتا اولا منتش فى المكان هتيجى ترجع ورا بس تعمل دبل encoode 
---
5) Lab: File path traversal, validation of start of path
	انتا هنا مش هينفع تمسح كله هتمسح الصور بس وهترجع وتكتب الملف 
	 /var/www/images/../../../etc/passwd
---
6) # Lab: File path traversal, validation of file extension with null byte bypass
	 الفكره هنا هو بيناكد من امتداد لو موجود مش موجود بيضيفه لو موجود بيعديه فهتعمله كومنت وتضيف لامتداد ```  etc/passwd%00.jpg/../../../../```
---
