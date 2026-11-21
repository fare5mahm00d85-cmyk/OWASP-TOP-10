
---
1) # Lab: Authentication bypass via OAuth implicit flow
	 1. اعمل OAuth Login بحسابك، ومن **Burp → HTTP History** راقب الـ OAuth response وخد الـ `access_token`.
	2.
	استغل إن التطبيق مش بيربط الـ OAuth response بالـ session بشكل صحيح، وأعد استخدام الـ response في جلسة تانية.
    
	2. لو اتقبل، هتدخل بحساب **Carlos** وبالتالي يتحل اللاب.
---
2) # Lab: SSRF via OpenID dynamic client registration 
	
---
3) Forced OAuth profile linking
	انتا هنا هتسجل عادى بالاكونت وتسجل ب اكونت الشوشيال بس هتعترض الطلب وتوقعه وتنسخ url الكود وتحطه فى  ifram 
	```
	<iframe src="https://YOUR-LAB-ID.web-security-academy.net/oauth-linking?  code=STOLEN-CODE"></iframe>`
	```
---
4) Lab: OAuth account hijacking via redirect_uri
	
---
5)  Lab: Stealing OAuth access tokens via an open redirect
	