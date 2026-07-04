# SQL پیشرفته: تراکنش، ACID، کوئری و دستورات

## 1. Interview Relevance Summary

این فایل **مفاهیم تراکنش و ACID**، **Full Join و Union**، **ALTER و Truncate**، **HAVING در برابر WHERE** و **Subquery** رو پوشش می‌ده. برای مصاحبهٔ Backend مهمه تراکنش و ACID رو بدونیش و بتونی تفاوت WHERE و HAVING، Truncate و Delete، و کاربرد Union و Subquery رو بگی. برای JOINهای پایه به **[[SQL]]** و برای طراحی دیتابیس به **[[Database]]** مراجعه کن.

---

## 2. تراکنش (Transaction) و ACID

**Transaction** یعنی مجموعه‌ای از کارها که با هم یه **واحد منطقی** رو تشکیل می‌دن: یا **همه** انجام می‌شن یا **هیچ‌کدوم**. این مفهوم از بیزنس وارد دیتابیس شده و با **Begin / Commit / Rollback** مدیریت می‌شه.

- **Begin**: شروع تراکنش؛ بعد از آن تا Commit هیچ‌چیز روی دیتابیس قطعی نمی‌شه.
- **Commit**: تأیید همهٔ تغییرات؛ از این لحظه تغییرات روی دیتابیس اعمال می‌شن.
- **Rollback**: در صورت خطا همهٔ تغییرات همان تراکنش برگردونده می‌شن.

تراکنش در **لایهٔ بیزنس** (مثلاً کد C#) شروع و تمام می‌شه و روی **دیتابیس** اجرا می‌شه. برای اطمینان از درست بودن تراکنش‌ها اصول **ACID** تعریف شده:

![[Pasted image 20240201025230.png]]

- **Atomicity (اتمیک بودن)**: کل تراکنش تجزیه‌ناپذیر است؛ یا همهٔ عملیات انجام می‌شن یا هیچ‌کدوم. همه یا هیچ.
- **Consistency (پایداری)**: قبل و بعد از تراکنش دیتابیس در حالت **پایدار** و معتبر است؛ قوانین جامعیت داده نقض نمی‌شن.
- **Isolation (انزوا)**: تراکنش‌ها طوری اجرا می‌شن که روی هم اثر نامرئی بذارن؛ در عمل با قفل یا سطح ایزوله، تراکنش دوم ممکنه صبر کنه یا دیتای قدیمی بخونه (بسته به سطح ایزوله).
- **Durability (ماندگاری)**: بعد از **Commit** داده از بین نمی‌ره؛ با مکانیزم **Recovery** در صورت خرابی قابل بازیافت است.

**نکته**: خیلی از **NoSQL**ها تراکنش به‌معنای کامل ACID ندارن؛ فقط بعضی (مثل بعضی حالت‌های MongoDB) پشتیبانی محدود دارن.

---

## 3. جامعیت داده (Data Integrity)

در طول عمر داده، DBMS باید **نگهداری و امنیت**، **پایداری**، **صحت** و **ضمانت** داده رو تأمین کنه. دو نوع جامعیت داریم:

- **فیزیکی**: سخت‌افزار و ذخیره‌سازی (بکاپ، recovery).
- **منطقی**: قوانین در **اپلیکیشن** و در **خود DBMS** — مثل **Data Type**، **Constraints**، **Primary Key** (Unique و Not Null)، **Foreign Key**. جامعیت منطقی با تراکنش و ACID نباید به‌هم بریزه.

![[Pasted image 20240201045409.png]]

---

## 4. Full Join

**Full (Outer) Join**: همهٔ رکوردهای **هر دو** جدول در خروجی هستن؛ اگه مطابقی برای یه رکورد نباشه، ستون‌های طرف مقابل **NULL** می‌شن.

```sql
SELECT ID, NAME, AMOUNT, DATE
FROM CUSTOMERS
FULL JOIN ORDERS
ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```

![[Pasted image 20240202190725.png]]

**نکته**: در **MySQL** FULL JOIN وجود نداره؛ می‌تونی با ترکیب **LEFT و RIGHT و UNION** معادلش رو بسازی.

---

## 5. Union و Union All

**UNION** برای **ترکیب نتیجهٔ دو (یا چند) SELECT** به‌صورت یک مجموعهٔ یک‌جا است. شرط: تعداد و **نوع** ستون‌های هر دو SELECT یکی باشه. با **UNION** رکوردهای **تکراری** حذف می‌شن؛ با **UNION ALL** تکراری هم می‌مونن (و معمولاً سریع‌تره چون حذف تکراری انجام نمی‌شه).

```sql
SELECT column1 [, column2 ] FROM table1 [WHERE condition]
UNION
SELECT column1 [, column2 ] FROM table2 [WHERE condition];
```

![[Pasted image 20240202191419.png]]
![[Pasted image 20240202191438.png]]

---

## 6. Alias

برای **تفاوت گذاشتن** بین ستون‌ها یا جدول‌هایی که نام یکسان دارن از **Alias** استفاده می‌کنی. این نام‌ها **موقت** هستن و فقط در همون کوئری معتبرن.

```sql
SELECT column_name AS alias_name FROM table_name WHERE condition;

SELECT t1.col1, t2.col2
FROM table1 AS t1, table2 AS t2
WHERE t1.id = t2.id;
```

---

## 7. ALTER TABLE

وقتی می‌خوای **ساختار جدول** رو عوض کنی (اضافه/حذف ستون، تغییر نوع، اضافه/حذف Constraint) از **ALTER TABLE** استفاده می‌کنی. سینتکس دقیق بین **SQL Server** و **MySQL** فرق داره؛ نمونه‌های متداول:

**اضافه کردن ستون:**
```sql
ALTER TABLE table_name ADD column_name datatype;
```

**حذف ستون:**
```sql
ALTER TABLE table_name DROP COLUMN column_name;
```

**تغییر نوع ستون:** در SQL Server معمولاً `ALTER COLUMN`؛ در MySQL `MODIFY COLUMN`.

**اضافه کردن Constraint (UNIQUE, CHECK, PRIMARY KEY):**
```sql
ALTER TABLE table_name ADD CONSTRAINT constraint_name UNIQUE(column1, column2);
ALTER TABLE table_name ADD CONSTRAINT constraint_name CHECK (condition);
ALTER TABLE table_name ADD CONSTRAINT pk_name PRIMARY KEY (column1, column2);
```

**حذف Constraint:** در SQL Server `DROP CONSTRAINT name`؛ در MySQL برای ایندکس/unique گاهی `DROP INDEX name` یا `DROP PRIMARY KEY`. مستندات موتور خودت رو چک کن.

---

## 8. Truncate در برابر Delete

- **DELETE**: رکوردها رو با **شرط** (یا بدون شرط) حذف می‌کنه؛ **Trigger** و **Log** اجرا می‌شن؛ فضای آزاد شده لزوماً فوراً به جدول برنمی‌گرده (بسته به موتور).
- **TRUNCATE**: **همهٔ** رکوردهای جدول رو یکجا حذف می‌کنه؛ **شرط نمی‌گیره**؛ معمولاً **سریع‌تر** از Delete است و فضای جدول آزاد می‌شه. برای خالی کردن کامل جدول و شروع مجدد مناسب است.

**نکته**: حذف کل جدول با **DROP TABLE** ساختار رو هم از بین می‌بره؛ وقتی فقط می‌خوای داده و فضای جدول رو خالی کنی و ساختار بمونه از **TRUNCATE** استفاده کن (با توجه به محدودیت‌های FK و موتور).

---

## 9. HAVING در برابر WHERE

- **WHERE**: روی **هر رکورد** قبل از گروه‌بندی اعمال می‌شه؛ یعنی فیلتر روی دادهٔ خام.
- **HAVING**: بعد از **GROUP BY** اعمال می‌شه؛ یعنی شرط روی **گروه‌ها** (یا روی توابع تجمعی مثل COUNT, SUM).

ترتیب منطقی: `FROM` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY`.

```sql
SELECT column1, column2
FROM table1, table2
WHERE conditions
GROUP BY column1, column2
HAVING conditions
ORDER BY column1, column2;
```

![[Pasted image 20240202202716.png]]

---

## 10. Subquery (زیرپرسش)

وقتی نتیجهٔ **یک SELECT** داخل SELECT، WHERE یا دستور دیگه (مثلاً INSERT/UPDATE/DELETE) استفاده می‌شه، به آن **Subquery** می‌گیم. مثال: واکشی از جدولی که تحت تأثیر شرطی از جدول دیگر است (مثلاً بازگردوندن از بکاپ بر اساس لیست ID).

```sql
SELECT column_name
FROM table1
WHERE column_name OPERATOR
   (SELECT column_name FROM table2 WHERE condition);
```

![[Pasted image 20240203234105.png]]

**INSERT با Subquery:**
```sql
INSERT INTO CUSTOMERS_BKP
SELECT * FROM CUSTOMERS WHERE ID IN (SELECT ID FROM CUSTOMERS);
```

**UPDATE با Subquery:**
```sql
UPDATE CUSTOMERS
SET SALARY = SALARY * 0.25
WHERE AGE IN (SELECT AGE FROM CUSTOMERS_BKP WHERE AGE >= 27);
```

**DELETE با Subquery:**
```sql
DELETE FROM CUSTOMERS
WHERE AGE IN (SELECT AGE FROM CUSTOMERS_BKP WHERE AGE >= 27);
```

---

## 11. کنترل تراکنش در SQL (خلاصه)

- **COMMIT**: ذخیرهٔ تغییرات.
- **ROLLBACK**: برگردوندن تغییرات تراکنش جاری.
- **SAVEPOINT**: نقطهٔ ذخیره داخل تراکنش که بعداً بتوانی تا همان نقطه Rollback کنی.
- **SET TRANSACTION**: نام‌گذاری یا تنظیمات تراکنش (سینتکس بسته به موتور فرق می‌کنه).

---

## 12. Key Interview Talking Points

- **Transaction و ACID**: Atomicity (همه یا هیچ)، Consistency (پایدار قبل و بعد)، Isolation (انزوا)، Durability (ماندگاری بعد از Commit). تراکنش در بیزنس Begin/Commit/Rollback و روی دیتابیس اجرا می‌شه.
- **Full Join**: همهٔ رکوردهای هر دو جدول؛ عدم تطابق → NULL. در MySQL با UNION شبیه‌سازی می‌شه.
- **UNION** تکراری حذف می‌کنه؛ **UNION ALL** تکراری نگه می‌داره و معمولاً سریع‌تره.
- **WHERE** روی رکوردها؛ **HAVING** روی گروه‌ها (بعد از GROUP BY).
- **TRUNCATE** همهٔ رکوردها بدون شرط، سریع و آزاد کردن فضا؛ **DELETE** با شرط و با لاگ/Trigger.
- **Subquery**: یک SELECT داخل شرط یا داخل دستور دیگر؛ برای INSERT/UPDATE/DELETE شرط‌دار بر اساس نتیجهٔ کوئری دیگر.

---

## 13. When to Use / When NOT to Use

- **Transaction**: وقتی چند دستور باید با هم همه اجرا بشن یا هیچ‌کدوم؛ بدون تراکنش احتمال نیمه‌کاره موندن است.
- **Full Join**: وقتی می‌خوای همهٔ رکوردهای هر دو طرف را ببینی حتی بدون تطابق؛ در MySQL از UNION استفاده کن.
- **UNION ALL** وقتی تکراری برایت مهم نیست و می‌خوای سریع‌تر باشه؛ **UNION** وقتی یکتا بودن نتیجه مهمه.
- **TRUNCATE** برای خالی کردن کامل جدول؛ برای حذف با شرط از **DELETE** استفاده کن.
- **HAVING** وقتی شرط روی گروه یا تابع تجمعی است؛ برای فیلتر روی رکورد از **WHERE** استفاده کن.

---

## منابع

- [SQL-Advanced (GitHub)](https://github.com/gitmag-group-admin/SQL-Advanced?tab=readme-ov-file)
- [آموزش پیشرفته SQL - sub queries (YouTube)](https://www.youtube.com/watch?v=mmijvRWozqw&list=PL1xdRbCBrpoel9XleokPKxF0mRzc3YLB2&index=15)
- [Course Overview - DB (YouTube)](https://www.youtube.com/watch?v=ix5nDOfT1Kk&list=PLKwsMoYJqrLIUfclTe45Q_WFfyelJQbs4)

---

این فایل **مکمل** **[[SQL]]** و **[[Database]]** است. برای تیونینگ و Plan به **[[Tuning Database]]** و **[[Explain Plan]]** مراجعه کن.
