[gitmag-group-admin/SQL-Advanced: md file of sql advanced course in gitmag youtube chennel (github.com)](https://github.com/gitmag-group-admin/SQL-Advanced?tab=readme-ov-file)

[آموزش پیشرفته SQL - sub queries (youtube.com)](https://www.youtube.com/watch?v=mmijvRWozqw&list=PL1xdRbCBrpoel9XleokPKxF0mRzc3YLB2&index=15)

[(60) جلسه 1: Course Overview - (DB) - YouTube](https://www.youtube.com/watch?v=ix5nDOfT1Kk&list=PLKwsMoYJqrLIUfclTe45Q_WFfyelJQbs4)
transaction
: مجموعه ای از task ها است.
مفهومی در بیزنس بوده که در پایگاه داده نیز قالب شده.
در دل دیتابیس transaction management هستند.
transaction life cycle :
برای مثال زمانی که در یک متد هستیم. در جایی از بلاک Trans.Begin() بگین میشود. در ادامه مجموعه کار هایی از  Begin انجام میشوند - میتواند حذف اپدیت گرفتن دیتا یا هرچیزی باشد - در این لحظه در دیتابیس اتفاقی ایجاد نمیشود. در صورتی نیز که یک عملیات درست انجام نشود عملیات های دیگر اعمال نمیشود.
در اصطلاح Transaction انجام نمیشود. 
حالا فرض میکنیم عملیات ها خطایی ندارند پس باید دیتابیس تمام کار های مد نظر را انجام دهد. در اینجا به اصطلاح ما Transaction.Commit() میکنیم.
و عملیات روی دیتابیس در صورت درست بودن تمام عملیات اعمال میشود.
اگر موفق نبود در catch باید 
transaction.RollBack() اتفاق میوفتد همان برگرداندن تمام تغییرات اتفاق میوفتد.
![[Pasted image 20240201025230.png]]

Transaction در لایه بیزنس نوشته میشود و در دیتابیس انجام میشود.

برای منیج کردن و مطمئن شدن از روند Transaction ها اصولی طراحی شد به نام ACID.
ASID :
ATOMICITY : اصل غیر قابل تجذیه بودن
به معنی اینکه کل ترزشکن تجزیه ناپذیر است یا تمام عملیات انجام میشوند یا انجام نمیشوند. همه یا هیچ - دارای یکپارچه گی

Consistency : اصل پایداری
قبل از انجام transaction  یک پایگاه داده یا dbms در حالت پایدار است. و بعد از انجام transaction باید دوباره به حالت پایدار برگردد.  قوانین** جامعیت داده نباید نقض شود.

Isolation : اصل انزوا
زمانی که یک transaction با جداول خاصی کار میکنند نباید transaction دومی باشد که با همان جداول کار کند. پس باید صبر کند تا تراکنش قبلی تمام شود و بعدی انجام شود.
و در عمل هم این اتفاق نمی افتد و عملیات مشابه منجر به خطا میشود.
البته خارج از transaction عملیات read میتواند صورت بگیرد که احتمال دریافت دیتای قدیمی وجود دارد.

Durability :  هر سیستمی که transaction داشته باشد باید تضمین کند که بعد از commit داده ها از بین نمیروند.
که اگر دیتا از بین برود باید مکانیسم recovery management

تراکنش = بار

NOSQL ها تراکنش ساپورت نمیکنند. فقط یکی از آن ها ساپورت میکند.

Data integrity :
در طول عمر یک داده
هر dbms باید این هارا ضمانت کند.

Maintenance نگهداشت و امنیت داده
Consistency پایداری داده ها
Accuracy صحت داده ها
Assurance ضمانت داده ها

دو نوع جامعیت داریم :
- فیزیکی : سخت افزار باید ضمانت نگهدای و امنیت را به ما بدهد.
- منطقی : 
	- یک بخش مربوط به اپلیکیشن هست
		- یک بخش مربوط به قوانین dbms هست. مانند :
			- data type
			- Constraints
			- P.K -> Unique and not null
			- F.K
![[Pasted image 20240201045409.png]]

جامعیت داده با تراکنش نباید بهم بریزد. اصول acid



Full join
تمام رکورد های دو جدول با هم به اشتراک گذاشته میشوند. درصورتی که معادلی برای فیلد هایی نباشد طبق شرط مورد نظر دیتا مقدار null میگیرد.

```sql
SQL> SELECT  ID, NAME, AMOUNT, DATE
   FROM CUSTOMERS
   FULL JOIN ORDERS
   ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```

![[Pasted image 20240202190725.png]]

این دستور در mysql وجود ندارد و معادل آن union هست در mysql

Union clause :
برای ترکیب کردن و اجتمال سلک ها و کوئری های مختلف میگیریم از union استفاده میکنیم.
شرایط : باید از هر دو جدول به تعداد مساوی رکورد گرفته شود.
این شرایط برای نوع دیتا هم صدق میکنند.
نکته : در این روش دیتای تکراری حذف میشوند.

```sql
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

UNION

SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
```

![[Pasted image 20240202191419.png]]

نتیجه:
![[Pasted image 20240202191438.png]]

Union All :
امکان اینکه دیتای تکراری هم داشته باشیم به ما میدهد.


Alias :
زمانی که ستون هایی با نام مشابه داریم به کمک Alias میتوانیم بین دو اسم تمایز قائل شویم.
این اسامی به صورت موقت هست و بعد از بسته شدن در دسترس نیستند.
برای  نام جداول هم میتوانیم استفاده کنیم.
``` sql
SELECT column1, column2....
FROM table_name AS alias_name
WHERE [condition];
```

```sql
SELECT column_name AS alias_name
FROM table_name
WHERE [condition];
```

Alter :
در شرایطی که در امکان تغییرات روی جداول را بخواهیم از این مورد استفاده میکنیم.
مثلا ستونی حذف شود اضافه شود تایپ آن تغییر کند و...
از alter استفاده میکنیم.

## Syntax

The basic syntax of an ALTER TABLE command to add a **New Column** in an existing table is as follows.

```
ALTER TABLE table_name ADD column_name datatype;
```

The basic syntax of an ALTER TABLE command to **DROP COLUMN** in an existing table is as follows.

```
ALTER TABLE table_name DROP COLUMN column_name;
```

The basic syntax of an ALTER TABLE command to change the **DATA TYPE** of a column in a table is as follows.

```
ALTER TABLE table_name MODIFY COLUMN column_name datatype;
```

The basic syntax of an ALTER TABLE command to add a **NOT NULL** constraint to a column in a table is as follows.

```
ALTER TABLE table_name MODIFY column_name datatype NOT NULL;
```

The basic syntax of ALTER TABLE to **ADD UNIQUE CONSTRAINT** to a table is as follows.

```
ALTER TABLE table_name 
ADD CONSTRAINT MyUniqueConstraint UNIQUE(column1, column2...);
```

The basic syntax of an ALTER TABLE command to **ADD CHECK CONSTRAINT** to a table is as follows.

```
ALTER TABLE table_name 
ADD CONSTRAINT MyUniqueConstraint CHECK (CONDITION);
```

The basic syntax of an ALTER TABLE command to **ADD PRIMARY KEY** constraint to a table is as follows.

```
ALTER TABLE table_name 
ADD CONSTRAINT MyPrimaryKey PRIMARY KEY (column1, column2...);
```

The basic syntax of an ALTER TABLE command to **DROP CONSTRAINT** from a table is as follows.

```
ALTER TABLE table_name 
DROP CONSTRAINT MyUniqueConstraint;
```

If you're using MySQL, the code is as follows.

```
ALTER TABLE table_name 
DROP INDEX MyUniqueConstraint;
```

The basic syntax of an ALTER TABLE command to **DROP PRIMARY KEY** constraint from a table is as follows.

```
ALTER TABLE table_name 
DROP CONSTRAINT MyPrimaryKey;
```

If you're using MySQL, the code is as follows.

```
ALTER TABLE table_name 
DROP PRIMARY KEY;
```



Truncate :
زمانی که لازم هست دیتای داخل یک جدول به صورت کلی پاک شود تا تغییراتی درون آن اتفاق بیوفتد. از این دستور استفاده میکنیم.
غیر معقولانه ترین حالت استفاده از drop table یا delete table هست.
میتوانیم برای دستور delete شرط بزاریم و بگیم کدوم ستون را پاک کند.
اما truncate شرط پذیر نیست و همه رکورد هارا پاک میکند.
زمانی که از delete استفاده میکنیم فضایی که جدول و رکورد های ان اشغال کرده بعد از حذف شدن آزاد نمیشود. اما truncate فضای قبل را آزاد میکند و مجددا قابل استفاده است.
Truncate به مراتب سریع تر از delete هست.
در کل زمانی که میخواهیم ساختار یک جدول رو تغییر بدیم اما تمام رکورد هارا به علاوه فصایی که اشغال کردن رو پاک کنیم از truncate استفاده میکنیم.

Having :
زمانی که یک شرط و در کوئری بخواهیم بیاریم از having استفاده میکنیم.
تفاوت آن با where این هست که دستور where جایی اعمال میشود که مقداری در جداول select شده هستند و شرایط خاصی برای واکشی داریم.
اما having زمانی استفاده میشود که میخواهیم دیتا های مد نظرمون رو گروه بندی کنیم و شرایطی روی گروهی از اطاعات قرار دهیم.
where روی تمام داده اعمال میشود. اما having بعد از گروه بندی یا عبارت Group By صدا زده میشود.

```sql
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
```

```sql
SELECT column1, column2
FROM table1, table2
WHERE [ conditions ]
GROUP BY column1, column2
HAVING [ conditions ]
ORDER BY column1, column2
```

![[Pasted image 20240202202716.png]]

Transactions :
زمانی که میخواهیم مجموعه از دستورات مربط را بخواهیم اجرا کنیم تا به یک عملیات واحد برسیم.
Transaction control
commit -> To save the changes
RollBack -> To roll back the changes
SavePoint -> Creates points within the groups of transactions in wich RollBack

SetTransaction -> Places a name on transaction


## Properties of Transactions

Transactions have the following four standard properties, usually referred to by the acronym **ACID**.

- **Atomicity** − ensures that all operations within the work unit are completed successfully. Otherwise, the transaction is aborted at the point of failure and all the previous operations are rolled back to their former state.
    
- **Consistency** − ensures that the database properly changes states upon a successfully committed transaction.
    
- **Isolation** − enables transactions to operate independently of and transparent to each other.
    
- **Durability** − ensures that the result or effect of a committed transaction persists in case of a system failure.
سینتکس ها :







Sub queries :
زمانی که دو جدول داریم و میخواهیم روی جدول اول عملیاتی انجام دهیم که تحت تاثیر جدول دوم هست.
مثلا از دیتابیس بک اپی گرفتیم و حالا دیتابیس پاک شده حالا اطلاعات جدول دیتابیس پاک شده میتوان با استفاده از بک آپ باز گردانی کنیم.

```sql
SELECT column_name [, column_name ]
FROM   table1 [, table2 ]
WHERE  column_name OPERATOR
   (SELECT column_name [, column_name ]
   FROM table1 [, table2 ]
   [WHERE])
```


![[Pasted image 20240203234105.png]]


insert 
```
INSERT INTO CUSTOMERS_BKP
   SELECT * FROM CUSTOMERS 
   WHERE ID IN (SELECT ID 
   FROM CUSTOMERS) ;
```

UPDATE :
```
UPDATE CUSTOMERS
   SET SALARY = SALARY *  0.25 WHERE AGE IN (SELECT AGE FROM CUSTOMERS_BKP
      WHERE AGE >=  27  );
```

DELETE :
```
DELETE FROM CUSTOMERS
   WHERE AGE IN (SELECT AGE FROM CUSTOMERS_BKP
      WHERE AGE >=  27  );
```


Full join
تمام رکورد های دو جدول با هم به اشتراک گذاشته میشوند. درصورتی که معادلی برای فیلد هایی نباشد طبق شرط مورد نظر دیتا مقدار null میگیرد.

```sql
SQL> SELECT  ID, NAME, AMOUNT, DATE
   FROM CUSTOMERS
   FULL JOIN ORDERS
   ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```

![[Pasted image 20240202190725.png]]

این دستور در mysql وجود ندارد و معادل آن union هست در mysql

Union clause :
برای ترکیب کردن و اجتمال سلک ها و کوئری های مختلف میگیریم از union استفاده میکنیم.
شرایط : باید از هر دو جدول به تعداد مساوی رکورد گرفته شود.
این شرایط برای نوع دیتا هم صدق میکنند.
نکته : در این روش دیتای تکراری حذف میشوند.

```sql
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]

UNION

SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
```

![[Pasted image 20240202191419.png]]

نتیجه:
![[Pasted image 20240202191438.png]]

Union All :
امکان اینکه دیتای تکراری هم داشته باشیم به ما میدهد.


Alias :
زمانی که ستون هایی با نام مشابه داریم به کمک Alias میتوانیم بین دو اسم تمایز قائل شویم.
این اسامی به صورت موقت هست و بعد از بسته شدن در دسترس نیستند.
برای  نام جداول هم میتوانیم استفاده کنیم.
``` sql
SELECT column1, column2....
FROM table_name AS alias_name
WHERE [condition];
```

```sql
SELECT column_name AS alias_name
FROM table_name
WHERE [condition];
```

Alter :
در شرایطی که در امکان تغییرات روی جداول را بخواهیم از این مورد استفاده میکنیم.
مثلا ستونی حذف شود اضافه شود تایپ آن تغییر کند و...
از alter استفاده میکنیم.

## Syntax

The basic syntax of an ALTER TABLE command to add a **New Column** in an existing table is as follows.

```
ALTER TABLE table_name ADD column_name datatype;
```

The basic syntax of an ALTER TABLE command to **DROP COLUMN** in an existing table is as follows.

```
ALTER TABLE table_name DROP COLUMN column_name;
```

The basic syntax of an ALTER TABLE command to change the **DATA TYPE** of a column in a table is as follows.

```
ALTER TABLE table_name MODIFY COLUMN column_name datatype;
```

The basic syntax of an ALTER TABLE command to add a **NOT NULL** constraint to a column in a table is as follows.

```
ALTER TABLE table_name MODIFY column_name datatype NOT NULL;
```

The basic syntax of ALTER TABLE to **ADD UNIQUE CONSTRAINT** to a table is as follows.

```
ALTER TABLE table_name 
ADD CONSTRAINT MyUniqueConstraint UNIQUE(column1, column2...);
```

The basic syntax of an ALTER TABLE command to **ADD CHECK CONSTRAINT** to a table is as follows.

```
ALTER TABLE table_name 
ADD CONSTRAINT MyUniqueConstraint CHECK (CONDITION);
```

The basic syntax of an ALTER TABLE command to **ADD PRIMARY KEY** constraint to a table is as follows.

```
ALTER TABLE table_name 
ADD CONSTRAINT MyPrimaryKey PRIMARY KEY (column1, column2...);
```

The basic syntax of an ALTER TABLE command to **DROP CONSTRAINT** from a table is as follows.

```
ALTER TABLE table_name 
DROP CONSTRAINT MyUniqueConstraint;
```

If you're using MySQL, the code is as follows.

```
ALTER TABLE table_name 
DROP INDEX MyUniqueConstraint;
```

The basic syntax of an ALTER TABLE command to **DROP PRIMARY KEY** constraint from a table is as follows.

```
ALTER TABLE table_name 
DROP CONSTRAINT MyPrimaryKey;
```

If you're using MySQL, the code is as follows.

```
ALTER TABLE table_name 
DROP PRIMARY KEY;
```



Truncate :
زمانی که لازم هست دیتای داخل یک جدول به صورت کلی پاک شود تا تغییراتی درون آن اتفاق بیوفتد. از این دستور استفاده میکنیم.
غیر معقولانه ترین حالت استفاده از drop table یا delete table هست.
میتوانیم برای دستور delete شرط بزاریم و بگیم کدوم ستون را پاک کند.
اما truncate شرط پذیر نیست و همه رکورد هارا پاک میکند.
زمانی که از delete استفاده میکنیم فضایی که جدول و رکورد های ان اشغال کرده بعد از حذف شدن آزاد نمیشود. اما truncate فضای قبل را آزاد میکند و مجددا قابل استفاده است.
Truncate به مراتب سریع تر از delete هست.
در کل زمانی که میخواهیم ساختار یک جدول رو تغییر بدیم اما تمام رکورد هارا به علاوه فصایی که اشغال کردن رو پاک کنیم از truncate استفاده میکنیم.

Having :
زمانی که یک شرط و در کوئری بخواهیم بیاریم از having استفاده میکنیم.
تفاوت آن با where این هست که دستور where جایی اعمال میشود که مقداری در جداول select شده هستند و شرایط خاصی برای واکشی داریم.
اما having زمانی استفاده میشود که میخواهیم دیتا های مد نظرمون رو گروه بندی کنیم و شرایطی روی گروهی از اطاعات قرار دهیم.
where روی تمام داده اعمال میشود. اما having بعد از گروه بندی یا عبارت Group By صدا زده میشود.

```sql
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
```

```sql
SELECT column1, column2
FROM table1, table2
WHERE [ conditions ]
GROUP BY column1, column2
HAVING [ conditions ]
ORDER BY column1, column2
```

![[Pasted image 20240202202716.png]]

Transactions :
زمانی که میخواهیم مجموعه از دستورات مربط را بخواهیم اجرا کنیم تا به یک عملیات واحد برسیم.
Transaction control
commit -> To save the changes
RollBack -> To roll back the changes
SavePoint -> Creates points within the groups of transactions in wich RollBack

SetTransaction -> Places a name on transaction


## Properties of Transactions

Transactions have the following four standard properties, usually referred to by the acronym **ACID**.

- **Atomicity** − ensures that all operations within the work unit are completed successfully. Otherwise, the transaction is aborted at the point of failure and all the previous operations are rolled back to their former state.
    
- **Consistency** − ensures that the database properly changes states upon a successfully committed transaction.
    
- **Isolation** − enables transactions to operate independently of and transparent to each other.
    
- **Durability** − ensures that the result or effect of a committed transaction persists in case of a system failure.
سینتکس ها :







Sub queries :
زمانی که دو جدول داریم و میخواهیم روی جدول اول عملیاتی انجام دهیم که تحت تاثیر جدول دوم هست.
مثلا از دیتابیس بک اپی گرفتیم و حالا دیتابیس پاک شده حالا اطلاعات جدول دیتابیس پاک شده میتوان با استفاده از بک آپ باز گردانی کنیم.

```sql
SELECT column_name [, column_name ]
FROM   table1 [, table2 ]
WHERE  column_name OPERATOR
   (SELECT column_name [, column_name ]
   FROM table1 [, table2 ]
   [WHERE])
```


![[Pasted image 20240203234105.png]]


insert 
```
INSERT INTO CUSTOMERS_BKP
   SELECT * FROM CUSTOMERS 
   WHERE ID IN (SELECT ID 
   FROM CUSTOMERS) ;
```

UPDATE :
```
UPDATE CUSTOMERS
   SET SALARY = SALARY *  0.25 WHERE AGE IN (SELECT AGE FROM CUSTOMERS_BKP
      WHERE AGE >=  27  );
```

DELETE :
```
DELETE FROM CUSTOMERS
   WHERE AGE IN (SELECT AGE FROM CUSTOMERS_BKP
      WHERE AGE >=  27  );
```


