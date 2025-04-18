IMemoryCache : داده ها درون مموری ذخیره میکند و سرعت دستیابی به اطلاعات بالا هست. 
داده ها نیاز به سریالایز ندارند و فقط درون یک سرور ذخیره میشوند.
و پس از ریستارت شدن سرور یا اپلیکیشن دیتای Cache شده از بین میرود.

IDistributedCache : داده به صورت Byte Array ذخیره میشوند و نیاز به سریالایز دارند. به صورت توزیع شده هستند یعنی چند سرور قابل دستری هستند.
At the moment there are 4 implementations of this interface:

- Distributed SQL Server Cache
- Distributed Redis Cache
- Distributed NCache Cache
- **Distributed Memory Cache**

DistributedCache uses web server’s memory as a cache store. It’s just like `IMemoryCache`we have seen above.





They can't be unified, that would be a breaking change. They shouldn't be unified, they have fundamentally different semantics. MemoryCache can store live objects, the distributed cache can't, objects have to be serialized. The distributed cache can be off box and calls to it may fail or take a long time so getting and setting should be async, the MemoryCache is always in memory and fast. The distributed cache can be disconnected from the store so the interface should account for that.

It's like trying to unify `IDictionary<object, object>` with a redis client interface. It's apples and oranges.