# SQL: JOINها

## 1. Interview Relevance Summary

**JOIN** یعنی ترکیب دو (یا چند) جدول بر اساس یه شرط مشترک، تا ستون‌های مربوط از جداول مختلف رو یه‌جا توی نتیجه ببینی. تو مصاحبهٔ Backend حتماً ازت می‌پرسن **تفاوت Inner، Left و Right Join** رو بدونی و بتونی با یه مثال بگی کی کدوم رو استفاده می‌کنی. Self Join هم برای مقایسهٔ رکوردهای داخل یک جدول به‌کار می‌ره.

---

## 2. JOIN به‌طور کلی

وقتی می‌خوای چند ستون از **جداول مختلف** رو با هم نمایش بدی، آن‌ها رو با شرط روی فیلد مشترک **Join** می‌کنی. شرط می‌تونه با `=`, `<`, `>`, `<>`, `<=`, `>=`, `BETWEEN`, `LIKE`, `NOT` و غیره نوشته بشه.

![[Pasted image 20240202161608.png]]

```sql
SELECT ID, NAME, AGE, AMOUNT
FROM CUSTOMERS, ORDERS
WHERE CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```

![[Pasted image 20240202161637.png]]

---

## 3. Inner Join

فقط **رکوردهایی** که در **هر دو** جدول با شرط ON مطابقت دارن توی خروجی می‌آن. یعنی اشتراک دو جدول بر اساس آن شرط.

```sql
SELECT table1.column1, table2.column2
FROM table1
INNER JOIN table2
ON table1.common_field = table2.common_field;
```

![[Pasted image 20240202165306.png]]

---

## 4. Left Join (Left Outer Join)

**همهٔ رکوردهای جدول سمت چپ** به‌اضافهٔ رکوردهای مطابق در جدول راست. اگه برای یه رکورد چپ مطابقی در راست نباشه، ستون‌های جدول راست **NULL** می‌شن.

```sql
SELECT ID, NAME, AMOUNT, DATE
FROM CUSTOMERS
LEFT JOIN ORDERS
ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```

![[Pasted image 20240202165204.png]]

---

## 5. Right Join (Right Outer Join)

**همهٔ رکوردهای جدول سمت راست** به‌اضافهٔ مطابق‌های جدول چپ. اگه برای یه رکورد راست مطابقی در چپ نباشه، ستون‌های جدول چپ **NULL** می‌شن.

```sql
SELECT table1.column1, table2.column2
FROM table1
RIGHT JOIN table2
ON table1.common_field = table2.common_field;
```

![[Pasted image 20240202165229.png]]

---

## 6. Self Join

وقتی **همان جدول** رو با خودش Join می‌کنی تا رکوردها رو با هم مقایسه کنی (مثلاً مقایسهٔ تاریخ ورود یا حقوق افراد در یک جدول). برای این کار جدول رو با **Alias** دو بار می‌آوری و شرط Join رو روی فیلد مشترک می‌نویسی.

```sql
SELECT a.column_name, b.column_name
FROM table1 a, table1 b
WHERE a.common_field = b.common_field;
```

مثال: مقایسهٔ حقوق افراد در یک جدول  
![[Pasted image 20240202170021.png]]

مقایسهٔ یک فرد نسبت به بقیه  
![[Pasted image 20240202170212.png]]

---

## 7. Key Interview Talking Points

- **Inner Join**: فقط رکوردهای مشترک هر دو جدول.
- **Left Join**: همهٔ چپ + مطابق راست؛ عدم تطابق → NULL در ستون‌های راست.
- **Right Join**: همهٔ راست + مطابق چپ؛ عدم تطابق → NULL در ستون‌های چپ.
- **Self Join**: همان جدول با دو Alias برای مقایسهٔ رکوردها با هم.

---

## 8. Common Mistakes & Red Flags

- فراموش کردن شرط **ON** یا گذاشتن شرط در **WHERE** به‌جای ON در JOINهای صریح (خوانایی و گاهی رفتار متفاوت).
- اشتباه گرفتن Left و Right؛ کافی است بدونی «کدوم جدول باید حتماً کامل بیاد» و همان را سمت چپ (Left) یا راست (Right) بذاری.

---

این فایل برای **JOIN**هاست. برای طراحی دیتابیس و ایندکس به **[[Database]]** و برای کوئری پیشرفته به **[[Advanced Query]]** و **[[database more]]** مراجعه کن.
