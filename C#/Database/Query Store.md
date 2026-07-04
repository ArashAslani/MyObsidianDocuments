# Query Store و تشخیص کندی در SQL Server

## 1. Interview Relevance Summary

وقتی کاربران یا مدیر IT **کندی** گزارش می‌دن، علت می‌تونه از **SQL Server** (تنظیمات، کوئری‌ها)، **سیستم‌عامل** یا **کوئری‌های بی‌رویه** باشه. این فایل دو ابزار رو خلاصه می‌کنه: **SQL Server Profiler** (برای trace و عیب‌یابی، با توجه به deprecated بودن) و **Query Store** (برای بررسی کارایی و تاریخچهٔ پلن و آمار). برای DMVها و تیونینگ عمیق‌تر به **[[Tuning Database]]** مراجعه کن.

---

## 2. سناریو: وقتی سیستم «کند» گزارش می‌شه

- کاربران می‌گن کند هستیم.
- مدیر IT می‌گه کند هستیم.

علت می‌تونه **ساختار، تنظیمات SQL Server، OS یا خود کوئری‌ها** باشه. برای تشخیص باید کوئری‌های سنگین، رویدادهای lock و مدت زمان اجرا رو ببینی و با **Execution Plan** و آمار I/O تحلیل کنی.

---

## 3. SQL Server Profiler (و جایگزینش)

**Profiler** یه ابزار گرافیکی برای **Trace** کردن دستورات ارسالی به سرور و **مدت زمان اجرا** است. برای **Debug و Troubleshooting** (Developer) و **Monitoring و Performance** (DBA) استفاده می‌شه. مسیر: Tools → SQL Server Profiler.

مفهوم اصلی **Trace** است: یه سری **رویداد (Event)** و **ستون (Column)** انتخاب می‌کنی و هر دستور ارسالی (و زمان اجرا و غیره) ثبت می‌شه.

![[Pasted image 20240426131756.png]]
![[Pasted image 20240426132808.png]]

- **Event Class / Event Category / Data Column**: هر قالب (Template) یه مجموعه رویداد داره (مثلاً Tuning، Lock، Duration). از **Event Selection** می‌تونی ستون‌ها رو شخصی‌سازی کنی (نوع اپلیکیشن، ساعت شروع/پایان و غیره).
- **Filter**: می‌تونی روی **Database Name** یا ستون دیگه فیلتر بذاری تا فقط دیتابیس یا session موردنظر trace بشه.
- **SPID** = Session Process ID (روی تب کوئری نشون داده می‌شه). گذاشتن **Application Name** در connection string برای تشخیص اپلیکیشن توی trace خیلی به‌درد می‌خوره.
- **Duration** معمولاً به **میلی‌ثانیه** است.
- **Batch** → T-SQL؛ برای SPS از **RPC** و **SP:StmtCompleted** و **Starting/Completed** استفاده می‌کنن. بیشتر سناریوها با حالت **Completed** شناسایی و رفع می‌شن.
- برای دیدن متن Stored Procedure: `SP_HELPTEXT sp_name`.

**نرم‌افزار Query Stress**: برای شبیه‌سازی **بار (workload)** با sessionها و تعداد زیاد کوئری برای تست.

**نکتهٔ مهم**: در **Production** اجرای Profiler به‌صورت گرافیکی و با trace سنگین می‌تونه خودش سیستم رو کند کنه. **Extended Events** روش سبک‌تر و توصیه‌شدهٔ فعلی است؛ Profiler در نسخه‌های جدید **deprecated** است.

برای محیط عملیاتی بهتره **اسکریپت trace** رو روی سرور اجرا کنی (server-side) یا از **Extended Events** استفاده کنی. برای دیدن traceهای فعال: `SELECT * FROM sys.traces`. برای تنظیم trace پیش‌فرض: `SP_CONFIGURE 'default trace enabled'`. با **Rollover** وقتی فایل به حجم مشخص برسه، فایل جدید ساخته می‌شه. معمولاً برای استارت/استاپ trace روی Production زمان کوتاه (مثلاً چند دقیقه) در نظر می‌گیرن تا overhead کم باشه.

---

## 4. Query Store

**Query Store** ابزاری برای **بررسی کارایی کوئری‌ها** روی دیتابیس است: **Execution Plan**ها و **آمار (Statistics)** اجرا رو ذخیره می‌کنه و می‌تونی ببینی کدوم کوئری‌ها سنگین شدن یا پلنشون عوض شده.

![[Pasted image 20240426151736.png]]
![[Pasted image 20240426151835.png]]

در **Properties** دیتابیس، **Query Store** رو روشن می‌کنی و پوشهٔ **Query Store** در SSMS بهت اجازه می‌ده:

- **Regressed Queries**: کوئری‌هایی که کاراییشون پسرفت کرده (مثلاً به‌خاطر تغییر پلن یا داده).
- **تکامل وضعیت اجرا** و **سوابق تغییرات پلن**.
- **Plan cache** و مقایسهٔ پلن‌ها.
- پیدا کردن **کوئری‌های با کاهش کارایی** و **Parameter Sniffing**.
- **Top N** کوئری بر اساس زمان CPU یا مصرف منابع (CPU, I/O, Memory).

![[Pasted image 20240426152652.png]]

**Automatic Database Tuning** (در نسخه‌های جدید) می‌تونه پیشنهادهایی برای ایندکس و اصلاح پلن بده.

![[Pasted image 20240426160919.png]]

---

## 5. Key Interview Talking Points

- **تشخیص کندی**: علت می‌تونه کوئری، تنظیمات SQL Server یا OS باشه؛ با Trace یا Extended Events و Query Store و DMVها تحلیل می‌کنیم.
- **Profiler**: برای trace و عیب‌یابی؛ در Production با احتیاط و ترجیحاً کوتاه‌مدت؛ **Extended Events** جایگزین توصیه‌شده.
- **Query Store**: ذخیرهٔ پلن و آمار اجرا؛ پیدا کردن Regressed Queries و تغییر پلن و Parameter Sniffing؛ Top N بر اساس CPU/I/O.

---

## 6. Common Mistakes & Red Flags

- اجرای **Profiler با trace سنگین** روی Production بدون محدودیت زمان یا فیلتر → overhead و کندی.
- **Query Store** رو روشن نکردن روی دیتابیس‌های مهم تا وقتی که کندی پیش بیاد و تاریخچهٔ پلن نباشه.

---

این فایل **خلاصه** است. برای آمار I/O، Execution Plan و TempDB به **[[Tuning Database]]** مراجعه کن.
