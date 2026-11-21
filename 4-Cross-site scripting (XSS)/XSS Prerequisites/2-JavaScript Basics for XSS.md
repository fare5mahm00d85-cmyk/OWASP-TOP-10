# الجزء الأول: Variables (المتغيرات)

## ما هو المتغير؟

المتغير هو مكان في الذاكرة لتخزين قيمة يمكن استخدامها أو تغييرها لاحقًا.

مثال:

```
let name = "Fares";
```

هنا:

```
name  → اسم المتغير

=     → إسناد قيمة

"Fares" → القيمة
```

يمكن استخدامه بعد ذلك:

```
let name = "Fares";

console.log(name);
```

الناتج

```
Fares
```

---

# أنواع المتغيرات

يوجد 3 أنواع يجب معرفتها.

---

# 1) var

**الشرح**

أقدم طريقة لإنشاء متغير.

**مثال**

```
var name = "Fares";

console.log(name);
```

الناتج

```
Fares
```

يمكن تغيير القيمة

```
var name = "Fares";

name = "Ali";

console.log(name);
```

الناتج

```
Ali
```

---

## مشكلة var

```
var x = 10;

var x = 20;

console.log(x);
```

الناتج

```
20
```

لأن `var` يسمح بإعادة تعريف المتغير، لذلك أصبح استخدامه أقل في JavaScript الحديثة.

---

# 2) let ⭐⭐⭐⭐⭐

**الشرح**

أفضل طريقة لإنشاء متغير يمكن تغيير قيمته لاحقًا، ولا يسمح بإعادة تعريفه في نفس النطاق (Scope).

**مثال**

```
let age = 22;

console.log(age);
```

الناتج

```
22
```

---

تغيير القيمة

```
let age = 22;

age = 23;

console.log(age);
```

الناتج

```
23
```

---

لا يسمح بإعادة التعريف

```
let age = 22;

let age = 30;
```

الناتج

```
Syntax Error
```

---

# 3) const ⭐⭐⭐⭐⭐

**الشرح**

يستخدم عندما تكون القيمة ثابتة ولا تريد تغييرها.

**مثال**

```
const pi = 3.14;

console.log(pi);
```

الناتج

```
3.14
```

---

إذا حاولت تغييرها

```
const pi = 3.14;

pi = 5;
```

الناتج

```
TypeError
```

---

# مقارنة بين var و let و const

|الخاصية|var|let|const|
|---|---|---|---|
|يمكن تغيير القيمة|✅|✅|❌|
|يمكن إعادة التعريف|✅|❌|❌|
|الاستخدام حاليًا|قليل|⭐ الأفضل|⭐ للثوابت|

---

# قواعد تسمية المتغيرات

✅ صحيح

```
let username;

let userName;

let user_name;

let age1;
```

---

❌ خطأ

```
let 1age;

let user-name;

let let;
```

---

# أنواع البيانات (Data Types)

---

## 1) String

نص.

```
let name = "Fares";
```

---

## 2) Number

رقم.

```
let age = 22;
```

---

## 3) Boolean

قيمة منطقية.

```
let isAdmin = true;
```

أو

```
let isAdmin = false;
```

---

## 4) Null

يعني لا توجد قيمة.

```
let user = null;
```

---

## 5) Undefined

يعني المتغير موجود ولكن لم يتم إعطاؤه قيمة.

```
let user;

console.log(user);
```

الناتج

```
undefined
```

---

# معرفة نوع المتغير

باستخدام

```
typeof
```

مثال

```
let name = "Fares";

console.log(typeof name);
```

الناتج

```
string
```

---

مثال

```
let age = 22;

console.log(typeof age);
```

الناتج

```
number
```

---

مثال

```
let admin = true;

console.log(typeof admin);
```

الناتج

```
boolean
```

---

# مثال شامل

```
let username = "Fares";
let age = 22;
let isStudent = true;

console.log(username);
console.log(age);
console.log(isStudent);

console.log(typeof username);
console.log(typeof age);
console.log(typeof isStudent);
```

**الناتج:**

```
Fares
22
true
string
number
boolean
```

---
# الجزء الثاني: Operators (المعاملات)

> **ما هي الـ Operators؟**
> 
> هي رموز تُستخدم لإجراء عمليات على المتغيرات أو القيم، مثل الجمع، المقارنة، أو التحقق من الشروط.

---

# 1) Arithmetic Operators (العمليات الحسابية)

## `+` (الجمع)

**الشرح:** يجمع رقمين أو يدمج نصين.

**مثال (أرقام):**

```
let a = 10;
let b = 5;

console.log(a + b);
```

**الناتج:**

```
15
```

**مثال (نصوص):**

```
let first = "Cyber";
let second = "Security";

console.log(first + " " + second);
```

**الناتج:**

```
Cyber Security
```

---

## `-` (الطرح)

**الشرح:** يطرح رقمًا من رقم.

**مثال:**

```
let a = 20;
let b = 7;

console.log(a - b);
```

**الناتج:**

```
13
```

---

## `*` (الضرب)

**مثال:**

```
let a = 6;
let b = 5;

console.log(a * b);
```

**الناتج:**

```
30
```

---

## `/` (القسمة)

**مثال:**

```
let a = 20;
let b = 4;

console.log(a / b);
```

**الناتج:**

```
5
```

---

## `%` (باقي القسمة)

**الشرح:** يعيد باقي قسمة رقم على آخر.

**مثال:**

```
console.log(10 % 3);
```

**الناتج:**

```
1
```

---

## `**` (الأس)

**مثال:**

```
console.log(2 ** 3);
```

**الناتج:**

```
8
```

---

# 2) Assignment Operators (معاملات الإسناد)

## `=`

**الشرح:** إسناد قيمة لمتغير.

```
let age = 22;
```

---

## `+=`

**مثال:**

```
let x = 10;

x += 5;

console.log(x);
```

**الناتج:**

```
15
```

تعادل:

```
x = x + 5;
```

---

## `-=`

```
let x = 10;

x -= 3;

console.log(x);
```

**الناتج:**

```
7
```

---

## `*=`

```
let x = 5;

x *= 4;

console.log(x);
```

**الناتج:**

```
20
```

---

## `/=`

```
let x = 20;

x /= 4;

console.log(x);
```

**الناتج:**

```
5
```

---

# 3) Comparison Operators (المقارنة)

ترجع دائمًا:

```
true
```

أو

```
false
```

---

## `==`

**الشرح:** يقارن القيمة فقط.

**مثال:**

```
console.log(5 == "5");
```

**الناتج:**

```
true
```

لأن JavaScript حولت `"5"` إلى رقم.

---

## `===` ⭐⭐⭐⭐⭐

**الشرح:** يقارن القيمة والنوع معًا.

**مثال:**

```
console.log(5 === "5");
```

**الناتج:**

```
false
```

لأن:

```
5 → Number

"5" → String
```

> **نصيحة:** استخدم `===` دائمًا في JavaScript الحديثة لأنه أكثر أمانًا ودقة.

---

## `!=`

**مثال:**

```
console.log(10 != 5);
```

**الناتج:**

```
true
```

---

## `!==`

**مثال:**

```
console.log(5 !== "5");
```

**الناتج:**

```
true
```

---

## `>`

```
console.log(20 > 10);
```

الناتج

```
true
```

---

## `<`

```
console.log(10 < 5);
```

الناتج

```
false
```

---

## `>=`

```
console.log(10 >= 10);
```

الناتج

```
true
```

---

## `<=`

```
console.log(5 <= 8);
```

الناتج

```
true
```

---

# 4) Logical Operators (المنطقية)

---

## `&&` (AND)

**الشرح:** يرجع `true` إذا كان الشرطان صحيحين.

```
let age = 20;

console.log(age > 18 && age < 30);
```

**الناتج:**

```
true
```

---

## `||` (OR)

**الشرح:** يكفي أن يكون أحد الشرطين صحيحًا.

```
let age = 15;

console.log(age > 18 || age == 15);
```

**الناتج:**

```
true
```

---

## `!` (NOT)

**الشرح:** يعكس القيمة المنطقية.

```
let admin = false;

console.log(!admin);
```

**الناتج:**

```
true
```

---

# 5) Increment & Decrement

---

## `++`

يزيد المتغير بمقدار 1.

```
let x = 5;

x++;

console.log(x);
```

الناتج

```
6
```

---

## `--`

ينقص المتغير بمقدار 1.

```
let x = 5;

x--;

console.log(x);
```

الناتج

```
4
```

---

# مثال شامل

```
let age = 22;
let score = 80;

console.log(age + score);
console.log(score > 50);
console.log(age >= 18 && score >= 50);
```

**الناتج**

```
102
true
true
```

---

# ⭐ الفرق المهم جدًا بين `==` و `===`

```
console.log(5 == "5");
```

الناتج

```
true
```

---

```
console.log(5 === "5");
```

الناتج

```
false
```

---
# الجزء الثالث: Conditions & Loops (الشروط والحلقات)

> **لماذا هذا الجزء مهم في XSS؟**
> 
> لأن معظم أكواد JavaScript التي ستقرأها داخل صفحات الويب تعتمد على الشروط (`if`) والحلقات (`for` و`while`) لاتخاذ قرارات أو تكرار العمليات.

---

# أولًا: if

## ما هي؟

تنفذ كودًا إذا كان الشرط صحيحًا (`true`).

**الصيغة:**

```
if (condition) {

    // Code

}
```

---

### مثال

```
let age = 22;

if (age >= 18) {

    console.log("Adult");

}
```

الناتج

```
Adult
```

لأن

```
22 >= 18
```

تساوي

```
true
```

---

### مثال آخر

```
let age = 15;

if (age >= 18) {

    console.log("Adult");

}
```

الناتج

```
لا يوجد شيء
```

لأن الشرط

```
false
```

---

# ثانيًا: if...else

## ما هي؟

إذا كان الشرط صحيحًا نفذ الجزء الأول، وإذا كان خطأ نفذ الجزء الثاني.

**الصيغة**

```
if (condition){

}
else{

}
```

---

### مثال

```
let age = 16;

if (age >= 18){

    console.log("Adult");

}
else{

    console.log("Child");

}
```

الناتج

```
Child
```

---

### مثال آخر

```
let loggedIn = true;

if (loggedIn){

    console.log("Welcome");

}
else{

    console.log("Login First");

}
```

الناتج

```
Welcome
```

---

# ثالثًا: else if

## ما هي؟

تستخدم عندما يوجد أكثر من احتمال.

**الصيغة**

```
if(){

}
else if(){

}
else{

}
```

---

### مثال

```
let score = 85;

if(score >=90){

console.log("Excellent");

}

else if(score >=70){

console.log("Good");

}

else{

console.log("Fail");

}
```

الناتج

```
Good
```

---

### مثال آخر

```
let hour = 21;

if(hour <12){

console.log("Morning");

}

else if(hour <18){

console.log("Afternoon");

}

else{

console.log("Night");

}
```

الناتج

```
Night
```

---

# رابعًا: switch

## ما هي؟

بديل لـ if عندما توجد قيم كثيرة للمقارنة.

---

### الصيغة

```
switch(value){

case قيمة:

break;

default:

}
```

---

### مثال

```
let day = 3;

switch(day){

case 1:
console.log("Saturday");
break;

case 2:
console.log("Sunday");
break;

case 3:
console.log("Monday");
break;

default:
console.log("Unknown");

}
```

الناتج

```
Monday
```

---

# خامسًا: for Loop

## ما هي؟

تكرر الكود عددًا محددًا من المرات.

---

### الصيغة

```
for(initialization;condition;increment){

}
```

---

### مثال

```
for(let i=1;i<=5;i++){

console.log(i);

}
```

الناتج

```
1
2
3
4
5
```

---

### مثال آخر

```
for(let i=0;i<3;i++){

console.log("Cyber Security");

}
```

الناتج

```
Cyber Security
Cyber Security
Cyber Security
```

---

# شرح for

```
for(let i=0;i<5;i++)
```

تنقسم إلى:

```
let i=0
```

بداية العد

---

```
i<5
```

استمر طالما الشرط صحيح.

---

```
i++
```

زد واحدًا بعد كل دورة.

---

# سادسًا: while

## ما هي؟

تكرر الكود طالما الشرط صحيح.

---

### الصيغة

```
while(condition){

}
```

---

### مثال

```
let i=1;

while(i<=5){

console.log(i);

i++;

}
```

الناتج

```
1
2
3
4
5
```

---

# سابعًا: do...while

## ما هي؟

تشبه while، لكنها تنفذ الكود مرة واحدة على الأقل حتى لو كان الشرط غير صحيح.

---

### مثال

```
let i=6;

do{

console.log(i);

i++;

}
while(i<=5);
```

الناتج

```
6
```

رغم أن الشرط خطأ، إلا أن الكود نُفذ مرة واحدة.

---

# break

## ما هي؟

توقف الحلقة فورًا.

### مثال

```
for(let i=1;i<=10;i++){

if(i==5){

break;

}

console.log(i);

}
```

الناتج

```
1
2
3
4
```

---

# continue

## ما هي؟

تتخطى الدورة الحالية وتكمل الحلقة.

### مثال

```
for(let i=1;i<=5;i++){

if(i==3){

continue;

}

console.log(i);

}
```

الناتج

```
1
2
4
5
```

---

# مثال شامل

```
let score = 75;

if(score>=90){

console.log("Excellent");

}

else if(score>=70){

console.log("Good");

}

else{

console.log("Fail");

}

for(let i=1;i<=3;i++){

console.log("Lesson "+i);

}
```

الناتج

```
Good
Lesson 1
Lesson 2
Lesson 3
```

---

# ⭐ أهم ما ستراه كثيرًا في JavaScript

```
if(condition)
```

اتخاذ قرار.

---

```
for(...)
```

تكرار.

---

```
while(...)
```

تكرار حتى يتحقق شرط.

---

```
switch(...)
```

اختيار بين عدة قيم.

---
# الجزء الرابع: Functions, Arrays, Objects

---

# أولًا: Functions (الدوال)

## ما هي الـ Function؟

هي **مجموعة أوامر** تكتبها مرة واحدة ثم تستدعيها كلما احتجتها.

بدلًا من كتابة نفس الكود كل مرة.

---

## الصيغة

```
function functionName(){

}
```

---

## مثال

```
function hello(){

console.log("Hello");

}

hello();
```

الناتج

```
Hello
```

---

## مثال آخر

```
function welcome(){

console.log("Welcome Fares");

}

welcome();
welcome();
welcome();
```

الناتج

```
Welcome Fares
Welcome Fares
Welcome Fares
```

---

# Function مع Parameters

## ما هي؟

هي متغيرات تستقبل قيمًا عند استدعاء الدالة.

---

### مثال

```
function hello(name){

console.log(name);

}

hello("Fares");
```

الناتج

```
Fares
```

---

### مثال

```
function sum(a,b){

console.log(a+b);

}

sum(5,10);
```

الناتج

```
15
```

---

## return

### ما هو؟

يرجع قيمة من الدالة.

---

### مثال

```
function sum(a,b){

return a+b;

}

let result = sum(10,20);

console.log(result);
```

الناتج

```
30
```

---

## Function Expression

```
const hello = function(){

console.log("Hello");

}

hello();
```

---

## Arrow Function ⭐⭐⭐⭐⭐

الصيغة الحديثة.

```
const hello = () => {

console.log("Hello");

}

hello();
```

---

مع بارامتر

```
const sum = (a,b)=>{

return a+b;

}

console.log(sum(5,8));
```

الناتج

```
13
```

---

# ثانيًا: Arrays

## ما هو الـ Array؟

هو متغير يحتوي على أكثر من قيمة.

---

### مثال

```
let names = ["Ali","Ahmed","Fares"];
```

---

### الوصول للعناصر

```
console.log(names[0]);
```

الناتج

```
Ali
```

---

```
console.log(names[2]);
```

الناتج

```
Fares
```

---

## length

عدد العناصر.

```
let names=["Ali","Ahmed","Fares"];

console.log(names.length);
```

الناتج

```
3
```

---

## push()

إضافة عنصر.

```
let names=["Ali","Ahmed"];

names.push("Fares");

console.log(names);
```

الناتج

```
["Ali","Ahmed","Fares"]
```

---

## pop()

يحذف آخر عنصر.

```
let names=["Ali","Ahmed","Fares"];

names.pop();

console.log(names);
```

الناتج

```
["Ali","Ahmed"]
```

---

## for مع Array

```
let names=["Ali","Ahmed","Fares"];

for(let i=0;i<names.length;i++){

console.log(names[i]);

}
```

الناتج

```
Ali
Ahmed
Fares
```

---

# ثالثًا: Objects

## ما هو الـ Object؟

هو متغير يحتوي على بيانات في صورة:

```
Key : Value
```

---

### مثال

```
let user={

name:"Fares",

age:22,

city:"Tanta"

};
```

---

## الوصول للبيانات

```
console.log(user.name);
```

الناتج

```
Fares
```

---

```
console.log(user.age);
```

الناتج

```
22
```

---

### أو

```
console.log(user["city"]);
```

الناتج

```
Tanta
```

---

## تعديل البيانات

```
user.age=23;

console.log(user.age);
```

الناتج

```
23
```

---

## إضافة خاصية

```
user.job="Pentester";
```

---

## حذف خاصية

```
delete user.city;
```

---

# مثال شامل

```
let user={

name:"Fares",

age:22,

skills:["HTML","JavaScript","Burp Suite"]

};

console.log(user.name);

console.log(user.skills[1]);
```

الناتج

```
Fares
JavaScript
```

---

# ⭐ لماذا Arrays و Objects مهمان في XSS؟

لأن JavaScript يخزن معظم البيانات بهما.

مثل:

```
document.forms
```

يرجع Array-like Object.

---

```
document.links
```

يرجع جميع الروابط.

---

```
document.images
```

يرجع جميع الصور.

---

وأيضًا عند استخدام API ستجد البيانات غالبًا بهذا الشكل:

```
let user={

username:"admin",

email:"admin@test.com",

role:"administrator"

};
```

---
# الجزء الخامس: DOM (Document Object Model)

> **ما هو الـ DOM؟**
> 
> الـ DOM هو تمثيل الصفحة على شكل **شجرة (Tree)** يستطيع JavaScript قراءة عناصرها وتعديلها.

مثال لصفحة HTML:

```
<html>
    <body>

        <h1>Hello</h1>

        <p>Welcome</p>

    </body>
</html>
```

شكلها في الـ DOM:

```
Document
│
└── html
    │
    ├── head
    │
    └── body
        │
        ├── h1
        │
        └── p
```

كل عنصر يسمى **Node**.

---

# أولًا: document

هو الكائن الذي يمثل الصفحة بالكامل.

```
console.log(document);
```

يعرض كل الصفحة داخل الـ Console.

---

## document.title

يعرض عنوان الصفحة.

```
console.log(document.title);
```

لو كان

```
<title>Cyber Security</title>
```

الناتج

```
Cyber Security
```

---

تغيير العنوان

```
document.title = "XSS Lab";
```

---

# document.body

يمثل عنصر `<body>`.

```
console.log(document.body);
```

---

# document.head

يمثل عنصر `<head>`.

```
console.log(document.head);
```

---

# ثانيًا: getElementById()

يبحث عن عنصر باستخدام الـ id.

## الصيغة

```
document.getElementById("id");
```

---

### مثال

HTML

```
<h1 id="title">
Welcome
</h1>
```

JavaScript

```
let x = document.getElementById("title");

console.log(x);
```

الناتج

```
<h1 id="title">Welcome</h1>
```

---

## تغيير النص

```
document.getElementById("title").innerHTML="Hello";
```

سيصبح

```
<h1>Hello</h1>
```

---

# ثالثًا: getElementsByClassName()

يبحث باستخدام الـ Class.

HTML

```
<p class="user">Ali</p>

<p class="user">Ahmed</p>
```

JavaScript

```
let users=document.getElementsByClassName("user");

console.log(users);
```

يعيد مجموعة عناصر.

---

# رابعًا: getElementsByTagName()

يبحث باستخدام اسم الـ Tag.

```
document.getElementsByTagName("p");
```

يعيد جميع عناصر

```
<p>
```

---

# خامسًا: querySelector() ⭐⭐⭐⭐⭐

من أكثر الدوال استخدامًا.

ترجع **أول عنصر** يطابق المحدد (Selector).

---

### حسب الـ id

```
document.querySelector("#title");
```

---

### حسب الـ class

```
document.querySelector(".user");
```

---

### حسب الـ tag

```
document.querySelector("p");
```

---

# سادسًا: querySelectorAll()

ترجع **كل العناصر** المطابقة.

```
document.querySelectorAll("p");
```

---

مثال

```
let p=document.querySelectorAll("p");

console.log(p.length);
```

---

# سابعًا: innerHTML ⭐⭐⭐⭐⭐

يعرض أو يغير محتوى العنصر على أنه **HTML**.

HTML

```
<div id="box">

Hello

</div>
```

---

JavaScript

```
document.getElementById("box").innerHTML="<b>Cyber</b>";
```

الناتج

```
<div>

<b>Cyber</b>

</div>
```

سيظهر النص **Cyber** بالخط العريض.

> **مهم جدًا:** `innerHTML` يفسر HTML، لذلك إذا وُضعت فيه بيانات مستخدم غير مُعالجة فقد يؤدي ذلك إلى ثغرات XSS.

---

# ثامنًا: innerText

يعرض النص الظاهر فقط.

```
document.getElementById("box").innerText;
```

---

تغيير النص

```
document.getElementById("box").innerText="Hello";
```

---

# تاسعًا: textContent

يعرض النص الموجود داخل العنصر حتى لو كان مخفيًا.

```
console.log(element.textContent);
```

---

## الفرق بين innerHTML و innerText و textContent

|الخاصية|تقرأ HTML؟|تعرض النص فقط؟|
|---|---|---|
|`innerHTML`|✅ نعم|❌|
|`innerText`|❌|✅|
|`textContent`|❌|✅ (بما في ذلك النصوص المخفية)|

---

# عاشرًا: value

تقرأ قيمة عناصر الإدخال.

HTML

```
<input id="username" value="Fares">
```

JavaScript

```
let user=document.getElementById("username").value;

console.log(user);
```

الناتج

```
Fares
```

---

# الحادي عشر: setAttribute()

يضيف أو يغير Attribute.

```
let img=document.getElementById("photo");

img.setAttribute("width","300");
```

---

# الثاني عشر: getAttribute()

يقرأ قيمة Attribute.

```
let link=document.getElementById("myLink");

console.log(link.getAttribute("href"));
```

---

# الثالث عشر: removeAttribute()

يحذف Attribute.

```
button.removeAttribute("disabled");
```

---

# الرابع عشر: addEventListener() ⭐⭐⭐⭐⭐

الطريقة الحديثة لإضافة Events.

بدلًا من كتابة:

```
<button onclick="...">
```

نكتب:

```
button.addEventListener("click",function(){

console.log("Clicked");

});
```

---

# الخامس عشر: console.log()

يعرض بيانات داخل Developer Tools.

```
console.log("Hello");
```

---

عرض متغير

```
let name="Fares";

console.log(name);
```

---

# السادس عشر: alert()

يعرض نافذة تنبيه.

```
alert("Hello");
```

الناتج

```
+------------+
|   Hello    |
|     OK     |
+------------+
```

> **ملاحظة:** سترى `alert()` كثيرًا في مختبرات XSS لأنه وسيلة بسيطة لإثبات أن JavaScript تم تنفيذه. في التطبيقات الحقيقية، الهدف يكون فهم تأثير التنفيذ وليس مجرد إظهار نافذة تنبيه.

---

# السابع عشر: prompt()

يعرض مربع إدخال.

```
let name=prompt("Enter Name");
```

---

# الثامن عشر: confirm()

يعرض نافذة نعم/إلغاء.

```
confirm("Delete?");
``` 

---
# الجزء السادس: Browser Objects

> **ما هي Browser Objects؟**
> 
> هي كائنات (Objects) يوفرها المتصفح للتعامل مع الصفحة، والرابط، والتخزين المحلي، ومعلومات المتصفح.

---

# أولًا: window ⭐⭐⭐⭐⭐

هو الكائن الرئيسي في المتصفح، وكل شيء تقريبًا يتبعه.

```
console.log(window);
```

---

### مثال

```
window.alert("Hello");
```

وتساوي

```
alert("Hello");
```

لأن `window` هو الكائن الافتراضي.

---

## window.location

يمثل عنوان الصفحة الحالي.

```
console.log(window.location);
```

مثال

إذا كنت داخل

```
https://example.com/login?id=5
```

سيطبع معلومات الرابط بالكامل.

---

# ثانيًا: location ⭐⭐⭐⭐⭐

يستخدم للتعامل مع عنوان الصفحة.

---

## location.href

يعرض الرابط الحالي.

```
console.log(location.href);
```

الناتج

```
https://example.com/login?id=5
```

---

### تغيير الصفحة

```
location.href = "https://google.com";
```

سينتقل المتصفح إلى الصفحة الجديدة.

---

## location.hostname

يعرض اسم الموقع.

```
console.log(location.hostname);
```

الناتج

```
example.com
```

---

## location.pathname

يعرض مسار الصفحة.

```
https://example.com/admin/login
```

الناتج

```
console.log(location.pathname);
```

```
/admin/login
```

---

## location.search ⭐⭐⭐⭐⭐

يعرض Query String.

الرابط

```
https://site.com?id=10&name=Ali
```

الكود

```
console.log(location.search);
```

الناتج

```
?id=10&name=Ali
```

---

## location.hash

الرابط

```
https://site.com/page#about
```

```
console.log(location.hash);
```

الناتج

```
#about
```

---

## location.reload()

يعيد تحميل الصفحة.

```
location.reload();
```

---

# ثالثًا: history

يتعامل مع سجل التصفح.

---

## الرجوع للخلف

```
history.back();
```

---

## الذهاب للأمام

```
history.forward();
```

---

## إعادة صفحة للخلف

```
history.go(-1);
```

---

# رابعًا: navigator

يعرض معلومات عن المتصفح.

---

## اسم المتصفح

```
console.log(navigator.userAgent);
```

يعرض معلومات مثل

```
Chrome
Windows
```

وغيرها.

---

## اللغة

```
console.log(navigator.language);
```

الناتج

```
en-US
```

---

# خامسًا: document.cookie ⭐⭐⭐⭐⭐

يعرض الـ Cookies التي يسمح JavaScript بالوصول إليها (أي التي **ليست** موسومة بـ `HttpOnly`).

```
console.log(document.cookie);
```

إذا كانت هناك Cookies قابلة للقراءة، سيظهر شيء مثل:

```
theme=dark; lang=en
```

> إذا كانت الـ Cookie تحمل الخاصية `HttpOnly` فلن يستطيع JavaScript قراءتها.

---

### إنشاء Cookie

```
document.cookie = "theme=dark";
```

---

# سادسًا: localStorage ⭐⭐⭐⭐⭐

يحفظ البيانات داخل المتصفح حتى بعد إغلاقه.

---

## حفظ

```
localStorage.setItem("username","Fares");
```

---

## قراءة

```
console.log(localStorage.getItem("username"));
```

الناتج

```
Fares
```

---

## حذف

```
localStorage.removeItem("username");
```

---

## حذف الكل

```
localStorage.clear();
```

---

# سابعًا: sessionStorage

يشبه `localStorage`.

لكن البيانات تُحذف عند إغلاق التبويب.

---

## حفظ

```
sessionStorage.setItem("token","123");
```

---

## قراءة

```
console.log(sessionStorage.getItem("token"));
```

---

# ثامنًا: encodeURIComponent()

يحوّل النص إلى صيغة مناسبة للاستخدام داخل URL.

---

### مثال

```
let name="Ali Mohamed";

console.log(encodeURIComponent(name));
```

الناتج

```
Ali%20Mohamed
```

---

# تاسعًا: decodeURIComponent()

يعيد النص إلى حالته الطبيعية.

```
console.log(decodeURIComponent("Ali%20Mohamed"));
```

الناتج

```
Ali Mohamed
```

---

# عاشرًا: JSON.stringify()

يحوّل Object إلى JSON String.

```
let user={

name:"Fares",

age:22

};

console.log(JSON.stringify(user));
```

الناتج

```
{"name":"Fares","age":22}
```

---

# الحادي عشر: JSON.parse()

يحوّل JSON String إلى Object.

```
let data='{"name":"Fares","age":22}';

let user=JSON.parse(data);

console.log(user.name);
```

الناتج

```
Fares
```