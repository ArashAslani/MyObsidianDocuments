سناریو :
1.کاربران به شما اعلام می کنند که ما کند هستیم؟
2.مدیر IT مجموعه اعالم میکند ما کند هستیم؟

علت کند شدن سیستم میتواند به عوامل مختلف زیری ساخت، تنظیمات SQL Server، OS و کوئری ها بستگی دارد.
سناریو های کاربردی:
SQL Server Profiler :
یک ابزار گرافیکی که کارهای Debug و Troubleshooting و Debug  (Developer) را انجام میدهیم.
همچنین کارهای Monitoring & Performance (DBA) را انجام میدهیم.
Tools> Sql Server profiler
 شروع کار Profiler بررسی مفهومی به اسم Trace هست که نمایش دستورات ارسالی + مدت زمان اجرا را نمایش میدهد.
نحوه قاپیدن کوئری ها توسط Profiler :
![[Pasted image 20240426131756.png]]
![[Pasted image 20240426132808.png]]
Event Class
Event Category
 Data Column
 Template 
 Trace
 Filter
زمان ایجاد پروفایل در Trace Property برای Trace هر کانسپت یک Template وجود دارد مثلا برای Tuning, Lock, Duration و.. که میتوان از آنها استفاده کنیم.
در قسمت Event Selection
هر Template شامل چند Column هست که میتوانیم شخصی سازی بسیاری از جمله تشخیص نوع اپلیکشن ساعت شروع و پایان و .. را انتخاب کرد.
میتوانیم دیتابیسم مورد نظرمان را Filter بکنیم (Column Filter btn)و فقط از دیتابیس مورد نظر (Database Name) اطلاعات Trace بشود.

spId = Session Process Id نوشته روی تب کوئری
گذاشتن Application Name در Config برای Trace کردن بسیار مهم هست.
Duration =Time  ms

Batch -> TSql
useful Events : show all events
Starting - Completed 

useful Events :
RPC -> Sps
Sp.smtCompleted  
Starting - Completed 

بیشتر سناریو ها با حالت Completed شناسایی و رفع میشوند.
محتوای Sp معرفی شده را نمایش میدهد.
SP_HELPTEXT sp_name
GO

نرم افزار Query Stress نرم افزار کوچکی امکان این را میدهد که Session ها متفاوتی را ایجاد کنیم و تعداد کوئری های بسیاری بفرستیم. test work load

Tuning Template بهترین راه برای Trace کوئری ها
در Event ها میتوانیم Exaction Plan  را از قسمت Performance > show plan xml فعال کرد.

-در محیط عملیاتی Production استفاده از شکل گرافیکی مناسب نیست چرا که هنگام اجرا سیستم کاربر ها کند میشود.

استفاده از Extended event هم  بسیار خوب هست.

Server side render :
ایجاد اسکریپت و اجرا روی سرور عملیاتی

Select all from sys.traces
go
SP_CONFIGURE 'default trace enabled
'
got

RoleOver اگر فایلی حجم مورد نظر را داشت یکی دیگر ایجاد میکند.
دقیقه 45 تا 50 استارت و استاپ Trace روی محیط عملیاتی.


Query Store
ابزاری گرفیکی برای بررسی کارایی کوئری ها روی دیتابیس Execution Plan هارا بررسی میکند.
و امار  Statistics را ذخیره میکند
![[Pasted image 20240426151736.png]]

![[Pasted image 20240426151835.png]]

داخل جداول Query Store
Plan cache 
Regressed Queries پسرفت کوئری
نمایش تکامل وضعیت اجرای کوئری

در پراپرتیز Datbase Query storm را On میکنیم و پوشه Querysore را فعال میکنیم.

پیدا کدرن کوئری های عمل کاهش کارایی
پیدا کردن مشکلات Parameter Sniffing
بررسی سوابق تغییرات پلن کوئری ها
شناسایی تعداد N کوئری اول بر اساس زمان CPu
تحلیل میزان استفاده از منابع CPU, IO, Ram

 ![[Pasted image 20240426152652.png]]

Automathic Database tuning
1:12:00 فیلم دیتابیس

![[Pasted image 20240426160919.png]]