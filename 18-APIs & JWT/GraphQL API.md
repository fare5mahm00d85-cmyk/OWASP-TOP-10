
----
# 1. ما هو GraphQL؟

**GraphQL**  هو لغة استعلام (Query Language) خاصة بالـ APIs، وبيئة تشغيل (Runtime) بتنفّذ الاستعلامات اعتمادًا على تعريف مسبق للبيانات اسمه Schema.

بمعنى أبسط:

GraphQL بيسمح للـ Client (المتصفح أو التطبيق) إنه يحدد بنفسه البيانات اللي محتاجها من السيرفر، والسيرفر بيرجع له الحقول المطلوبة فقط.

تم تطوير GraphQL في Facebook (Meta حاليًا)، وأصبح مشروعًا مفتوحًا.

### الفرق بين REST API و GraphQL API

| REST API                             | GraphQL API                             |
| ------------------------------------ | --------------------------------------- |
| غالبًا بيستخدم Endpoints متعددة      | غالبًا بيستخدم Endpoint واحد            |
| السيرفر بيحدد شكل الاستجابة          | العميل بيحدد الحقول المطلوبة            |
| ممكن يرجع بيانات مش محتاجها المستخدم | العميل بيحدد البيانات اللي محتاجها      |
| ممكن تحتاج عدة Requests              | ممكن تجمع بيانات مترابطة في Query واحدة |
| بيعتمد على HTTP Methods              | غالبًا بيستخدم POST وأحيانًا GET        |

مثال باستخدام REST:

```
GET /api/users/1
GET /api/users/1/orders
GET /api/users/1/profile
```

هنا محتاجين 3 Requests علشان نجيب بيانات المستخدم وطلباته وملفه الشخصي.

نفس الفكرة باستخدام GraphQL:

```
query {
  user(id: "1") {
    name
    email
    orders {
      id
      total
    }
  }
}
```

هنا طلبنا بيانات المستخدم والطلبات المرتبطة بيه في Query واحدة.

**من منظور الأمن:** تقليل عدد Requests مش معناه إن GraphQL أكثر أمانًا. بالعكس، لازم نفحص الصلاحيات والحقول والعمليات المتاحة.

---

# 2. GraphQL بيشتغل إزاي؟

GraphQL بيعتمد على 3 مكونات أساسية:

1. Schema
    
2. Query
    
3. Resolver
    

## A. Schema – مخطط البيانات

الـ Schema هو تعريف شكل الـ API، وبيحدد البيانات والعمليات اللي يقدر العميل يتعامل معاها.

بيحدد:

- أنواع البيانات الموجودة.
    
- الحقول المتاحة لكل نوع.
    
- العلاقات بين البيانات.
    
- العمليات المتاحة.
    
- أنواع المدخلات والمخرجات.
    

مثال:

```
type User {
  id: ID!
  username: String!
  email: String!
  age: Int
}

type Query {
  user(id: ID!): User
}
```

شرح الكود:

- `type User`: بنعرّف نوع بيانات اسمه User.
    
- `id`: معرف المستخدم.
    
- `ID`: نوع بيانات مخصص للمعرفات.
    
- `username`: اسم المستخدم.
    
- `String`: قيمة نصية.
    
- `Int`: رقم صحيح.
    
- `!`: معناها إن القيمة مطلوبة ولا تقبل Null.
    
- `Query`: العمليات المستخدمة لجلب البيانات.
    
- `user`: اسم العملية.
    
- `(id: ID!)`: العملية محتاجة ID إجباري.
    
- `: User`: النتيجة المتوقعة هي بيانات من نوع User.
    

**Security Point:**

الـ Schema ممكن يكشف معلومات مهمة عن تصميم التطبيق والحقول والعمليات الموجودة فيه، وده بيساعدنا أثناء اختبار الاختراق.

## B. Query – الاستعلام

الـ Query هي عملية قراءة البيانات من السيرفر، من غير ما نغيّرها.

مثال:

```
query {
  user(id: "1") {
    id
    username
    email
  }
}
```

معنى الطلب:

هات المستخدم اللي رقمه 1، وارجع لي:

- ID
    
- Username
    
- Email
    

مثال على الاستجابة:

```
{
  "data": {
    "user": {
      "id": "1",
      "username": "fares",
      "email": "fares@example.com"
    }
  }
}
```

لاحظ إن السيرفر رجّع الحقول اللي طلبناها فقط.

## C. Resolver – منفّذ العملية

الـ Resolver هو Function في الـ Backend مسؤولة عن تنفيذ العملية وإرجاع البيانات المطلوبة.

مثال باستخدام JavaScript:

```
const resolvers = {
  Query: {
    user: async (_, args, context) => {
      return await database.users.findById(args.id);
    }
  }
};
```

شرح:

- `Query`: العمليات الخاصة بقراءة البيانات.
    
- `user`: الـ Resolver المسؤول عن جلب المستخدم.
    
- `args`: المدخلات اللي أرسلها المستخدم.
    
- `context`: معلومات الطلب، زي المستخدم الحالي وصلاحياته.
    
- `database`: قاعدة البيانات.
    

**أهم نقطة أمنية:**

مش كفاية إن التطبيق يخفي زرار أو صفحة من المستخدم. لازم الـ Resolver نفسه يتحقق إن المستخدم مسموح له بالوصول للبيانات.

---

# 3. أنواع GraphQL Operations

GraphQL فيه 3 أنواع أساسية من العمليات:

## A. Query – قراءة البيانات

بتستخدم لجلب البيانات دون تعديلها.

مثال:

```
query {
  users {
    id
    username
  }
}
```

الطلب ده بيجيب قائمة المستخدمين مع الـ ID والـ Username.

## B. Mutation – تعديل البيانات

بتستخدم لتنفيذ عمليات تغيير البيانات، زي:

- إضافة مستخدم.
    
- تعديل بيانات.
    
- حذف سجل.
    
- تغيير كلمة مرور.
    

مثال:

```
mutation {
  updateUser(id: "1", username: "admin") {
    id
    username
  }
}
```

المعنى: عدّل اسم المستخدم صاحب ID رقم 1 إلى admin، وارجع بياناته.

مثال آخر:

```
mutation {
  deleteUser(id: "5") {
    id
    username
  }
}
```

المعنى: احذف المستخدم صاحب ID رقم 5.

**Security Point:**

لازم نختبر هل المستخدم العادي يقدر ينفّذ Mutations مخصصة للإدارة، وهل يقدر يعدّل أو يحذف بيانات مستخدمين تانيين.

## C. Subscription – الاشتراك في التحديثات

بتسمح للعميل باستقبال تحديثات من السيرفر بشكل مستمر عند حدوث أحداث معينة.

مثال:

```
subscription {
  messageAdded {
    id
    content
  }
}
```

المعنى: استقبل رسالة جديدة كل ما يتم إضافتها.

غالبًا بتستخدم WebSocket.

**Security Point:**

لازم نختبر هل المستخدم يقدر يشترك في بيانات أو أحداث تخص مستخدمين آخرين.

---

# 4. GraphQL Data Types – أنواع البيانات

الـ Schema بيستخدم أنواع مختلفة لتحديد شكل البيانات.

|النوع|وظيفته|
|---|---|
|String|نص|
|Int|رقم صحيح|
|Float|رقم عشري|
|Boolean|True أو False|
|ID|معرف فريد|
|Object Type|نوع بيانات مخصص|
|Enum|مجموعة قيم محددة|
|List|قائمة من القيم|
|Input Type|نوع مدخلات|

### مثال:

```
type User {
  id: ID!
  username: String!
  age: Int
  active: Boolean
}
```

### فهم علامة !

```
username: String!
```

معناها إن username لازم يكون موجود.

أما:

```
age: Int
```

فممكن يكون Null.

### فهم List

```
users: [User]
```

معناها إن النتيجة قائمة من المستخدمين.

```
users: [User!]!
```

معناها إن القائمة نفسها مطلوبة، وكل عنصر بداخلها مطلوب وغير قابل لـ Null.

---

# 5. GraphQL Endpoint

الـ Endpoint هو المسار اللي التطبيق بيستقبل عليه طلبات GraphQL.

أمثلة شائعة:

```
/graphql
/api/graphql
/graphql/
```

مش شرط كل التطبيقات تستخدم نفس المسار.

أثناء اختبار الاختراق بنحاول نكتشف الـ Endpoint من خلال:

- ملفات JavaScript.
    
- Network Tab في المتصفح.
    
- Burp Proxy → HTTP History.
    
- مصادر التوثيق.
    
- مسارات التطبيق الظاهرة في الصفحات.
    

مثال على HTTP Request:

```
POST /graphql HTTP/1.1
Host: example.com
Content-Type: application/json
```

Body:

```
{
  "query": "query { user(id: \"1\") { username email } }"
}
```

الطلب بيحتوي على:

- HTTP Method: POST
    
- Endpoint: /graphql
    
- Content-Type: application/json
    
- Query: الاستعلام المطلوب تنفيذه.
    

---

# 6. GraphQL Introspection

دي من أهم النقاط في اختبار GraphQL.

**Introspection** هي خاصية بتسمح للعميل يسأل الـ API عن الـ Schema الخاص بيه.

يعني ممكن نعرف:

- أنواع البيانات الموجودة.
    
- أسماء الحقول.
    
- أسماء Queries.
    
- أسماء Mutations.
    
- العلاقات بين أنواع البيانات.
    
- المدخلات المطلوبة.
    

### ليه Introspection مهمة في Pentesting؟

لأنها بتساعدنا نكتشف العمليات والحقول اللي ممكن تكون مش ظاهرة في واجهة التطبيق.

مثال:

```
query {
  __schema {
    types {
      name
    }
  }
}
```

الاستعلام ده بيطلب أسماء أنواع البيانات الموجودة في الـ Schema.

مثال آخر:

```
query {
  __type(name: "User") {
    name
    fields {
      name
    }
  }
}
```

ده بيطلب أسماء الحقول الموجودة في نوع User.

### أشهر Introspection Fields

|Field|وظيفته|
|---|---|
|__schema|استعراض الـ Schema|
|__type|معرفة تفاصيل نوع معين|
|__typename|معرفة نوع العنصر الحالي|

**ملاحظة أمنية:**

تعطيل Introspection ممكن يقلل المعلومات المتاحة للمهاجم، لكنه مش حل أمني كافي. لازم التطبيق يحمي البيانات والعمليات نفسها بصلاحيات صحيحة.

---

# 7. GraphQL Security Vulnerabilities

هنا نبدأ الجزء الأهم بالنسبة لمجال Web Penetration Testing.

## A. Broken Access Control

معناها إن التطبيق مش بيتحقق بشكل صحيح من صلاحيات المستخدم.

مثال:

مستخدم عادي مسموح له يشوف بياناته فقط، لكنه يقدر يطلب بيانات مستخدم تاني عن طريق تغيير ID.

```
query {
  user(id: "2") {
    username
    email
  }
}
```

لو المستخدم الحالي ID بتاعه 1، والسيرفر رجّع بيانات المستخدم 2 من غير التحقق من الصلاحية، يبقى عندنا مشكلة Access Control.

النتيجة المحتملة:

- قراءة بيانات مستخدمين آخرين.
    
- الوصول لبيانات خاصة.
    
- تجاوز صلاحيات المستخدم.
    

## B. IDOR

اختصار:

**Insecure Direct Object Reference**

بتحصل لما التطبيق يعتمد على معرف الكائن من غير ما يتحقق إن المستخدم مسموح له بالوصول ليه.

مثال:

```
query {
  order(id: "1005") {
    id
    total
    address
  }
}
```

لو غيرت رقم الطلب من 1005 إلى 1006 وظهرت بيانات طلب مستخدم آخر، ممكن تكون ثغرة IDOR.

اختبر دائمًا باستخدام حسابين تملكهما داخل بيئة الاختبار.

## C. Excessive Data Exposure

معناها إن السيرفر بيرجع بيانات حساسة أكثر من اللازم.

مثال:

```
query {
  user(id: "1") {
    username
    email
    phone
    address
    role
  }
}
```

لو التطبيق بيسمح للمستخدم العادي بالوصول لحقول حساسة زي بيانات خاصة أو صلاحيات إدارية، يبقى فيه مشكلة.

ملحوظة: GraphQL مش بيكشف الحقول تلقائيًا؛ لازم الـ Backend يكون بيسمح بالوصول ليها.

## D. Mass Assignment

بتحصل لما التطبيق يسمح بتعديل خصائص داخلية أو حساسة من غير التحقق من صلاحية المستخدم.

مثال:

```
mutation {
  updateUser(
    id: "1"
    input: {
      username: "fares"
      role: "ADMIN"
    }
  ) {
    id
    username
    role
  }
}
```

لو المستخدم العادي قدر يغير دوره إلى ADMIN، فده Broken Access Control وقد يكون مرتبطًا بـ Mass Assignment.

## E. Information Disclosure

بتحصل لما الـ API يكشف معلومات داخلية مش المفروض تكون متاحة.

أمثلة:

- تفاصيل الـ Schema.
    
- أسماء الحقول الداخلية.
    
- Stack Traces.
    
- Database Errors.
    
- معلومات عن Backend.
    
- بيانات المستخدمين.
    

## F. GraphQL Injection

بتحصل لما التطبيق يبني استعلامات أو يتعامل مع مدخلات المستخدم بطريقة غير آمنة.

لازم نفرّق بين GraphQL نفسها وبين SQL Injection:

GraphQL Injection مش معناها إن كل GraphQL API معرضة لـ SQL Injection.

لو الـ Resolver بيستخدم Database Query بطريقة غير آمنة، ممكن يحصل SQL Injection داخل التطبيق.

اختبر مدخلات المستخدم وابحث عن:

- أخطاء غير متوقعة.
    
- اختلاف الاستجابة مع المدخلات.
    
- تجاوز التحقق من البيانات.
    
- تأثير المدخلات على استعلامات قاعدة البيانات.
    

## G. Denial of Service (DoS)

GraphQL بتسمح بطلب بيانات مترابطة ومتداخلة، وده ممكن يسبب ضغطًا على السيرفر لو مفيش حدود مناسبة.

مثال توضيحي:

```
query {
  users {
    posts {
      comments {
        author {
          posts {
            comments {
              id
            }
          }
        }
      }
    }
  }
}
```

الاستعلامات العميقة والمعقدة ممكن تستهلك موارد كبيرة.

طرق الحماية:

- Query Depth Limits.
    
- Query Complexity Limits.
    
- Rate Limiting.
    
- Request Timeouts.
    
- Pagination.
    

لا تنفّذ اختبارات ضغط على أنظمة حقيقية بدون تصريح ونطاق اختبار واضح.

---

# 8. GraphQL Testing Using Burp Suite

ده الجزء العملي.

### الخطوة 1: اكتشاف GraphQL Endpoint

افتح التطبيق باستخدام المتصفح من خلال Burp Proxy.

بعد كده:

1. افتح Proxy.
    
2. افتح HTTP History.
    
3. راقب Requests.
    
4. ابحث عن مسار مثل `/graphql`.
    

### الخطوة 2: إرسال الطلب إلى Repeater

1. اختار طلب GraphQL.
    
2. اضغط كليك يمين.
    
3. اختار Send to Repeater.
    
4. افتح Repeater.
    

### الخطوة 3: فهم الطلب

مثال:

```
POST /graphql HTTP/2
Host: target.example
Content-Type: application/json

{
  "query": "query { user(id: \"1\") { username email } }"
}
```

هنفحص:

- Endpoint.
    
- Headers.
    
- Authentication.
    
- Cookies.
    
- Query.
    
- Variables.
    
- Response.
    

### الخطوة 4: تجربة Introspection

جرّب الاستعلام التالي في بيئة اختبار مصرح بها:

```
{
  "query": "{ __schema { types { name } } }"
}
```

لو السيرفر رجع أنواع البيانات، يبقى Introspection متاحة.

### الخطوة 5: اكتشاف Queries وMutations

بعد معرفة الـ Schema، دور على:

- عمليات قراءة البيانات.
    
- عمليات تعديل البيانات.
    
- عمليات الحذف.
    
- العمليات الخاصة بالإدارة.
    
- الحقول الحساسة.
    

### الخطوة 6: اختبار Authorization

جرّب الوصول إلى بيانات مستخدم آخر بحساب اختبار تملكه.

اختبر:

- هل المستخدم يقدر يقرأ بيانات غيره؟
    
- هل يقدر يعدّل بيانات غيره؟
    
- هل يقدر يحذف بيانات غيره؟
    
- هل يقدر ينفّذ Admin Mutations؟
    

### الخطوة 7: تحليل Response

راجع:

- Status Code.
    
- JSON Response.
    
- Error Messages.
    
- البيانات الراجعة.
    
- الحقول التي تم قبولها أو رفضها.
    

---

# 9. GraphQL Variables

بدل ما نكتب القيم مباشرة داخل Query، نقدر نستخدم Variables.

مثال:

```
query GetUser($id: ID!) {
  user(id: $id) {
    username
    email
  }
}
```

والـ Variables:

```
{
  "id": "1"
}
```

الطلب كاملًا باستخدام JSON:

```
{
  "query": "query GetUser($id: ID!) { user(id: $id) { username email } }",
  "variables": {
    "id": "1"
  }
}
```

ليه Variables مهمة؟

- بتخلي الاستعلامات منظمة.
    
- بتفصل البيانات عن الاستعلام.
    
- بتساعد على إعادة استخدام Queries.
    
- بتقلل الحاجة لبناء الاستعلام كنص متغير.
    

**Security Point:**

استخدام Variables مش ضمان للأمان وحده. لازم الـ Backend يتحقق من المدخلات والصلاحيات ويستخدم Database Queries آمنة.

---

# 10. GraphQL Aliases

الـ Alias بيسمح بتسمية نتيجة الحقل باسم مختلف داخل الاستجابة.

مثال:

```
query {
  firstUser: user(id: "1") {
    username
  }

  secondUser: user(id: "2") {
    username
  }
}
```

بدل ما نطلب `user` مرتين بنفس الاسم، استخدمنا:

- firstUser
    
- secondUser
    

### أهميتها في Security Testing

ممكن تساعدنا نفهم طريقة تعامل السيرفر مع الطلبات المتعددة.

بعض التطبيقات بتطبق Rate Limiting على عدد HTTP Requests، لكن مش بتتعامل بشكل صحيح مع عدد العمليات داخل GraphQL Request واحد.

لازم نختبر هل حدود المعدل مطبقة على كل عملية فعلية، مش مجرد عدد الطلبات.

---

# 11. GraphQL Batching

الـ Batching هو إرسال أكثر من عملية GraphQL داخل HTTP Request واحد، إذا كان السيرفر يدعم ذلك.

الفكرة:

بدل ما نرسل عدة HTTP Requests، ممكن نجمع عدة عمليات في Request واحد.

الأهمية الأمنية:

- اختبار Rate Limiting.
    
- اختبار حدود عدد العمليات.
    
- اكتشاف ضعف الحماية من محاولات تسجيل الدخول المتكررة.
    
- قياس تأثير تعدد العمليات على موارد السيرفر.
    

لازم نختبر في بيئة مصرح بها، ونتجنب إرسال طلبات ضخمة أو ضغط غير مصرح به.

---

# 12. أهم HTTP Methods في GraphQL

الأكثر شيوعًا:

### POST

غالبًا بيستخدم لإرسال Query أو Mutation.

### GET

بعض التطبيقات بتسمح بإرسال Queries باستخدام GET عن طريق Query Parameters.

لكن دعم GET يختلف حسب التطبيق والإعدادات، وغالبًا لا يُسمح باستخدامه للـ Mutations.

### OPTIONS

ممكن يساعد في معرفة إعدادات HTTP والـ Methods المدعومة، لكنه مش دليل كافي على وجود GraphQL.

---

# 13. Authentication vs Authorization

دي نقطة مهمة جدًا.

**Authentication:**

السيرفر بيتأكد من أنت مين؟

مثال: تسجيل الدخول باستخدام Session Cookie أو JWT.

**Authorization:**

السيرفر بيتأكد هل مسموح لك تنفّذ العملية دي؟

مثال:

- المستخدم يقدر يشوف بياناته.
    
- المدير يقدر يشوف بيانات الإدارة.
    
- المستخدم العادي لا يقدر يحذف مستخدمين.
    

ممكن يكون المستخدم عامل Login بنجاح، لكن ده مش معناه إن عنده صلاحية لكل Query أو Mutation.

---

# 14. GraphQL Security Checklist

استخدم القائمة دي أثناء اختبار أي GraphQL API مصرح لك باختبارها:

## Discovery

- تحديد GraphQL Endpoint.
    
- فحص JavaScript Files.
    
- مراقبة HTTP Requests.
    
- تحديد Authentication Mechanism.
    
- معرفة هل Introspection مفعلة.
    

## Schema

- اكتشاف Query Types.
    
- اكتشاف Mutation Types.
    
- معرفة Object Types.
    
- فحص الحقول الحساسة.
    
- فحص العلاقات بين البيانات.
    

## Authorization

- اختبار الوصول لبيانات مستخدم آخر.
    
- اختبار IDOR.
    
- اختبار Admin Mutations.
    
- اختبار تعديل Role.
    
- اختبار حذف بيانات مستخدم آخر.
    
- اختبار الصلاحيات على مستوى كل Field.
    

## Input Validation

- اختبار أنواع البيانات.
    
- اختبار القيم غير المتوقعة.
    
- فحص أخطاء السيرفر.
    
- فحص SQL Injection في Resolvers عند وجود مؤشرات.
    
- التأكد من استخدام Variables والتحقق من المدخلات.
    

## DoS Protection

- اختبار Query Depth.
    
- اختبار Query Complexity.
    
- فحص Pagination.
    
- فحص Rate Limiting.
    
- فحص Batching Limits.
    

## Information Disclosure

- فحص Error Messages.
    
- فحص Stack Traces.
    
- فحص البيانات الحساسة.
    
- فحص تفاصيل الـ Schema.
    

---

# 15. أهم مصطلحات GraphQL

|المصطلح|المعنى|
|---|---|
|GraphQL|لغة استعلام للـ APIs|
|Schema|تعريف شكل الـ API|
|Query|قراءة البيانات|
|Mutation|تعديل البيانات|
|Subscription|استقبال التحديثات|
|Resolver|الدالة التي تنفذ العملية|
|Endpoint|مسار استقبال الطلب|
|Introspection|استكشاف الـ Schema|
|Object Type|نوع بيانات|
|Input Type|نوع مدخلات|
|Field|حقل داخل نوع بيانات|
|Arguments|مدخلات العملية|
|Variables|متغيرات الاستعلام|
|Alias|اسم بديل للحقل|
|Batching|تجميع عدة عمليات في طلب|
|Authorization|التحقق من الصلاحيات|
|Authentication|التحقق من الهوية|
|IDOR|الوصول غير المصرح به لكائن|
|DoS|تعطيل الخدمة|

---

# 16. الفرق بين GraphQL و REST من منظور Pentesting

|نقطة الاختبار|REST|GraphQL|
|---|---|---|
|Discovery|اكتشاف Endpoints|اكتشاف GraphQL Endpoint والـ Schema|
|Data Access|Parameters وIDs|Queries وFields وArguments|
|Modification|POST/PUT/PATCH/DELETE|Mutations|
|IDOR|تغيير Object ID|تغيير ID داخل Arguments|
|Access Control|Endpoint Authorization|Query/Mutation/Field Authorization|
|Information Disclosure|API Responses|Schema وFields وErrors|
|DoS|Rate Limiting|Depth وComplexity وBatching|
|Injection|حسب Backend|حسب Resolver وBackend|

---

# 17. أفضل طريقة لتعلم GraphQL في مجال Web Pentesting

رتّب مذاكرتك بالشكل ده:

1. افهم REST API وHTTP Methods.
    
2. افهم GraphQL Schema وTypes.
    
3. اتعلم Queries وMutations وVariables.
    
4. افهم Introspection.
    
5. اتعلم استخدام Burp Suite مع GraphQL.
    
6. اختبر Broken Access Control وIDOR.
    
7. ادرس Batching وAliases وDoS.
    
8. حل لابات PortSwigger الخاصة بـ GraphQL.
    

## الخلاصة

GraphQL مش مجرد طريقة مختلفة لكتابة API Requests؛ هو نظام له Schema وQueries وMutations وResolvers.

كمختبر اختراق، أهم حاجة مش إنك تعرف تبعت Query فقط، لكن تعرف:

- إيه العمليات المتاحة؟
    
- إيه البيانات اللي ممكن الوصول ليها؟
    
- هل فيه صلاحيات ناقصة؟
    
- هل المستخدم يقدر يعدّل بيانات غيره؟
    
- هل فيه معلومات حساسة مكشوفة؟
    
- هل السيرفر محمي من الاستعلامات المعقدة؟
    

**الهدف النهائي:** تقدر تكتشف GraphQL API، تفهم بنيته، وتختبر أمانه باستخدام Burp Suite بشكل احترافي.