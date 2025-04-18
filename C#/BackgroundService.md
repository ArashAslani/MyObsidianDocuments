IHostedService and BackgroundService in Dotnet

بکگراند سرویس ها به دو نوع تبدیل میشوند :
short runing : Register - message - seed data

long runing :

کنسول host و وب web host هستند.
![[Pasted image 20240222143905.png]]

.net یک اینترفیس تهیه کرده به نام IHostedService
که دو متد start و stop دارد.
یک ابسترکت کلاس دیگر داریم به نام Background Service که  از IHostedService  ارث بری کرده.
BackgroundService که یک تمپلیت پترن هست که longRuning را هندل میکند که فقط ان را اگزکیوت میکند و متد های استارت استاپ را نداریم مگر اینکه اوراید کنیم.
IhostedService مسئولیت انجام ShortRuning ها را دارد.

![[Pasted image 20240222144326.png]]

زمانی که در IHostedService یک لوپ از فعالیت ها را داشته باشیم یا long runing داشته باشیم.
برنامه اجرا نمیشود و عملیات های بکگراند اجرا میشوند.
![[Pasted image 20240222145454.png]]
![[Pasted image 20240222145513.png]]
که میتوان با Task.Run میتوان این را کنترل کرد.

![[Pasted image 20240222145242.png]]
![[Pasted image 20240222145424.png]]



اما با استفاده از BackgroundService بدون نیاز به پیاده سازی های دیگر از اینکه جهت که یک تمپلیت هست و استارت استاپ را خودش کنترل میکند این کار را برای ما انجام میدهد.
![[Pasted image 20240222145549.png]]
![[Pasted image 20240222145608.png]]

رجیستر کردن سرویس های بکگراند :
![[Pasted image 20240222152357.png]]

in .net core 8

![[Pasted image 20240415203032.png]]
 در حالت کلی دو host داریم که 
 .net core Host که بیشتر اپلیکشن های کنسولی رو در بر میگیره 
 و .asp.net core host که هر چاچوبی که روی پلتفرم asp.net core میشینه را در بر میگیره.
درواقع یک ابجکت هست که مسئول زنده نگهداشتن اپلیکشن هست و Encapsolate میکنه تمام لایف تایم اپلیکشن ما.
زمانی که اپلیکشن اجرا میشود تا پایان آن asp.net core host دارد کپسوله میکند امکاناتی مثل Configuration - di - hosted services 
در اصل وظیفه استارت اپلیکشن و کپسوله کردن امکانات و شات دان کردن اپلیکشن را دارد.
پس مدیرید HostedService , BackgroundServices به دست Host اتفاق می افتد.
زمانی که یه کپسوله را معرفی میکنیم Host سرویس هارا در Hosted Service قرار میدهد و مدیریت میکند.
دقیقا در .net core Host هم همین کار را میکند.

معماری در هر دو تقریبا یک شکل هست.

Asp.net core Host :
قبل .net 8
زمانی که یک IHostedServise به Host اصلی معرفی میشود ابجکت ها به صورت Fifo و یا به صورت Sequential یا ترتیبی اون ها رو اضافه میکنه.
و اونها رو پشت سرهم اجرا و صدا میکنه و به صورت برعکس هم زمان پایان دادن به برنامه اینکارو انجام میدهد.
![[Pasted image 20240415204112.png]]
اما حالا یکسری Config و امکانات داریم که میتوانیم به صورت Concurrent این کار را انجام دهیم.
اما در .net 8
یک Interface اضافه شده به نام IHostedLifecycleService interface که برای مدیریت بیشتر HostedServices هست.
یعنی میگه قبل اینکه یک سرویس استارت بشه یک کاری رو انجام بده و اگر استارت شده یک کار دیگر انجام بده.

![[Pasted image 20240415204310.png]]
کد :
![[Pasted image 20240415204638.png]]![[Pasted image 20240415204714.png]]
ابتدا Worker های معرفی شده در Hsot اجرا میشوند و بعد اپلیکشن اجرا میشود. به خاطر همین دلیل هم هست که باید Worker های ما ShortRuning باشند.
![[Pasted image 20240415205039.png]]
پیاده سازی Worker ها به یک شکل هست.
فقط WorkerFour از BackgroundServide ارث بری کرده و Long Running هست.
برای مثال این ورکر ها وظیفه دارند که فرضا هر 2 ثانیه یک بار دیتابیس را Pull کنند و اگر مسیجی جدیدی اومده اون رو ارسال کنه. یا چک کنه در outbox اگر چیز جدیدی هست ارسال کنیم.

به این ترتیب میتوانی ترتیب اجرا و Fifo را در زمان اجرای ورکر ها باهم ببینیم:

![[Pasted image 20240415210017.png]]
![[Pasted image 20240415210237.png]]

با اضافه کردن اپشن های ServicesStartCocurrently , ServicesStopConcurrently میتوانیم سرویس هارا به صورت Concurrent اجرا و Execute کنیم.
![[Pasted image 20240415210453.png]]که باعث میشود استارت اولیه با سرعت بیشتر اجرا شود.

IHostedLifecycleService  از IHostedService ارث بری کرده با این تفاوت که شامل چند متد جدید شامل StartingAsync - StartedAsync - StopingAsync - StopedAsync  هست.

WorkerThree از نوع IHostedLifecycleService هست که به صورت زیر هاست اجرا میشود.
![[Pasted image 20240415210934.png]]
Woker های از جنس IHostedLifecycleService باشند اول از همه اجرا میشوند که باید توجه داشت که اگر در زمان StartingAsync یک عملیات LongRunning باشد دقیقا مشکلی که در حالت Fifo داشتیم اتفاق میافتد و ورکر های بعدی تا تمام نشدن آن اجرا نمیشوند.
