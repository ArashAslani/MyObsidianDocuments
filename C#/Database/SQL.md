Join :
ترکیب دو جدول است. زمانی که میخواهیم چندین ستون از جداول مختلف دیتابیس بگیریم و به صورت واحد یکجا نمایش دهیم.

![[Pasted image 20240202161608.png]]
```
 SELECT ID, NAME, AGE, AMOUNT
   FROM CUSTOMERS, ORDERS
   WHERE  CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```
![[Pasted image 20240202161637.png]]
 همچنین میتوانیم از  =, <, >, <>, <=, >=, !=, BETWEEN, LIKE, and NOT استفاده کنیم.


Inner join :
فرض کنید داده های دو جدول را باهم ترکیب کنیم.
رکورد های مشترک بین دو جدول خروجی میشوند.

```sql
SELECT table1.column1, table2.column2
FROM table1
INNER JOIN table2
ON table1.common_field = table2.common_field;
```

![[Pasted image 20240202165306.png]]

Left join :
اطلاعات جدول اول به علاوه اشتراکات آن (شرط روی فیلد مورد نظر) با جدول دوم در خروجی نمایش داده میشود.
به ازای اطلاعاتی که اشتراکی بین جدول اول و دوم نیست فیلد ها Null میشوند.

```sql
SQL> SELECT  ID, NAME, AMOUNT, DATE
   FROM CUSTOMERS
   LEFT JOIN ORDERS
   ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```
![[Pasted image 20240202165204.png]]

Right join :
اطلاعات جدول دوم به علاوه اشتراکات آن (شرط روی فیلد مورد نظر) با جدول اول در خروجی نمایش داده میشود.
به ازای اطلاعاتی که اشتراکی بین جدول اول و دوم نیست فیلد ها Null میشوند.

```sql
SELECT table1.column1, table2.column2...
FROM table1
RIGHT JOIN table2
ON table1.common_field = table2.common_field;
```

![[Pasted image 20240202165229.png]]

Self join :
روی یک جدول اثر میکند. وضعیت یک رکورد را با رکورد های دیگر مقایسه میکند.
مثلا یک کامند خاص کی به داخل شرکت اومده و از چه کسانی زودتر یا دیر تر وارد شده است.
چون این جدول با خودش مقایسه میشود لازم هست از آن دو نمونه ساخته شود تا باهم مقایسه شوند.

```sql
SELECT a.column_name, b.column_name...
FROM table1 a, table1 b
WHERE a.common_field = b.common_field;
```
مثال مقایسه حقوق افراد در یک جدول
![[Pasted image 20240202170021.png]]
مقایسه یک فرد نسبت به بقیه
![[Pasted image 20240202170212.png]]

