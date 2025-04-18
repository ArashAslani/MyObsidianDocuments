In-Memory Oltp
ابتدا در Sql server 2014 ارائه شد که باعث میشوند جدول در مموری قرار بگیرد و سرعت بسیار زیادی دارند.
 که جداول Memory Optimized  هستند.
بررسی Memory Optimized Table
![[Pasted image 20240512212700.png]]
ویژگی ها :
بالا بودن سرعت کار
Lock Free
Latch Free
قابلیت ذخیره / عدم ذخیره داده در حافظه

مزایا :
Reduce Contention
Reduce Logging
Lower Latency 
Minimally Execution Time

این جداول با اینکه در رم هستند میتوانند روی Disk هم ذخیره سازی شوند.

![[Pasted image 20240512214000.png]]

Buffer pool
بزرگترین حافظه از 800+ قسمت رم هست که اسکیوال سرور میگیرد. که داده های روی جداول عادی ذخیره شده را از دیسک به رم میاورد و هنگام عملیات CU ابتدا روی این حافظه تغییر میکند و با استفاده از Checkpoint تغییرات به سمت دیسک می آید.

![[Pasted image 20240512214900.png]]

![[Pasted image 20240512221424.png]]

 چطور استفاده کنیم ؟
 سه گام باید برداریم
 1. ساخت File Group 
 2. ساخت جدول Memory Optimized
 3. ساخت Natively Compiled SP
 
 ایجاد File Groups
 1.Root File : مربوط به Meta Data File
 2. Data File :رکورد های درج در Memory Optimized Table
 3. Delta File : رکور های حذف شده از Memory Optimized Table
 4.  Log File: استفاده از سیستم Log File مربوط به Filestream
 ![[Pasted image 20240512223312.png]]

Native Compiled Sp : 
SP هایی هستند که به زبان ماشین ترجمه میشوند و یک DDL  مخصوص به خود را دارند که به C هم ترجمه شده و در MEMORY Optimized Tables ها کاربرد دارند و فوق العاده سریع هستند.

اگر انتظار پرفرمنس داریم باید دیتابیس تخصصی استفاده کنیم.

Temporal Table 2016
![[Pasted image 20240512231534.png]]

چک نویس دیتابیس Temp db از این جدول مجزا است.

ثبت سوابق تغییرات : یک روش آن است که در یک جدول این کار را بکنیم. که با بالا رفتن تعداد رکورد ها نیاز به شرط گذاری بسیار است و ایندکسینگ را عملا بی فایده میکند.
روش دیگر استفاده از تریگر هست که با بالا رفتن تعداد کاربران کندی به بار می آورد.
روش دیگر استفاده از CDC است که خوب هست اما نیاز مند یک متخصص برای نگهداری هست.
روش دوم کنترل در اپلیکشن و ثبت داده ها در دیتابیس NoSql است. که در صورتی که به صورت دستی تغییراتی در دیتابیس اعمال شود ورژن های دیتابیس هماهنگ نمی شوند.

روش بعدی استفاده از Temporal Table هست
که EF هم آن را ساپورت میکند.
Time Travel - Data Audit - Slowly Changing Dimensions - Repair record-level corruptions 
که برای بازیابی رکورد های اسیب دیده بسیار مناسب است.

نحوه پیکربندی :
![[Pasted image 20240512232653.png]]

![[Pasted image 20240512232838.png]]


![[Pasted image 20240512233156.png]]
 زمانی که ایندکس ریبیلد میکنیم باید Temporal Table هم Rebuild شود.
 