به صورت ساده با رجیستر کردن AddApiVersioning() و اضافه کردن اتریبیوت [Route("api/v{version:apiVersion}/[controller]")]
apiVersion یک Root constrain هست که میگوید ورژن باید تحت فرمت خاصی مقدار داده شود.
virsion کلیدی هست که عدد ورژن بعد v در ان جایگذاری میشود.
[Microsoft.AspNetCore.Mvc.ApiVersion("1")]
به بالای controller میتوان به ورژنینگ دست پیدا کرد.
که به صورت دیفالت Url segment پیاده سازی میشود. api/v1/Auth 

در Oveload دوم AddApiVerssion میتوانیم شخصی سازی هایی انجام دهیم.

options.AssumeDefaultVersionWhenUnspecified = true; 
مقدار دیفالت این اپشن false هست.
این اتریوبیوت زمانی که کنترلر ما ورژن خاصی برایش مشخص نشده یک مقدار پیشفرض برایش مشخص میکند.

و زمانی که ست میشود باید حتما 
            options.DefaultApiVersion = new ApiVersion(1, 0); //v1.0 == v1
تنظیم شود.
به صورت پیشفرض مقدار 1 دارد.
که به آن یک شی از ApiVersion پاس میدهیم که Overload های مختلفی دارد. میتوانند عدد یا حتی تاریخ باشند. 

options.ReportApiVersions = true;
یک ریپورت از ورژن های api میدهد.



میتوان استراتژی های خواندن ورژن متفاوتی داشته باشیم:
options.ApiVersionReader = new QueryStringApiVersionReader("api-version");
 api/posts?api-version=1

options.ApiVersionReader = new UrlSegmentApiVersionReader();
 api/v1/posts حالت دیفالت

options.ApiVersionReader = new HeaderApiVersionReader(new[] { "Api-Version" });
 header => Api-Version : 1

options.ApiVersionReader = new MediaTypeApiVersionReader()

options.ApiVersionReader = ApiVersionReader.Combine(new QueryStringApiVersionReader("api-version"), new UrlSegmentApiVersionReader())
 combine of [querystring] & [urlsegment]
