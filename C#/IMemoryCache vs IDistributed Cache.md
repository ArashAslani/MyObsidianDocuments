# IMemoryCache vs IDistributedCache در ASP.NET Core

Caching یکی از مهم‌ترین تکنیک‌های بهینه‌سازی Performance در ASP.NET Core است. درک تفاوت بین `IMemoryCache` و `IDistributedCache` و انتخاب صحیح بین آن‌ها برای هر توسعه‌دهنده Backend ضروری است. این موضوع در مصاحبه‌های فنی به صورت مستقیم و غیرمستقیم (از طریق سوالات Performance و Scalability) بسیار پرسیده می‌شود.

---

## فهرست مطالب

- [[#IMemoryCache چیست؟]]
- [[#IDistributedCache چیست؟]]
- [[#تفاوت‌های کلیدی]]
- [[#پیاده‌سازی‌های IDistributedCache]]
- [[#مثال‌های عملی]]
- [[#چه زمانی کدام را استفاده کنیم؟]]
- [[#Trade-offs و محدودیت‌ها]]
- [[#نکات مصاحبه]]

---

## IMemoryCache چیست؟

### تعریف

**`IMemoryCache` یک Interface برای Cache کردن داده‌ها در حافظه (Memory) همان سرور است.**

`IMemoryCache` داده‌ها را به صورت مستقیم در حافظه سرور ذخیره می‌کند و برای دسترسی سریع به داده‌های پرتکرار استفاده می‌شود.

### ویژگی‌های کلیدی

- **In-Memory Storage**: داده‌ها در حافظه RAM سرور ذخیره می‌شوند
- **سریع**: دسترسی به داده‌ها بسیار سریع است (در حد میکروثانیه)
- **بدون Serialization**: داده‌ها به صورت Object ذخیره می‌شوند، نیاز به Serialization ندارند
- **Single Server**: فقط در همان سروری که Application اجرا می‌شود در دسترس است
- **Volatile**: با Restart شدن Application یا Server، داده‌های Cache از بین می‌روند

### مثال: استفاده از IMemoryCache

```csharp
public class ProductService
{
    private readonly IMemoryCache _cache;
    private readonly IProductRepository _repository;
    
    public ProductService(IMemoryCache cache, IProductRepository repository)
    {
        _cache = cache;
        _repository = repository;
    }
    
    public async Task<Product> GetProductAsync(int id)
    {
        // تلاش برای دریافت از Cache
        if (_cache.TryGetValue($"product_{id}", out Product cachedProduct))
        {
            return cachedProduct;
        }
        
        // اگر در Cache نبود، از Database دریافت کن
        var product = await _repository.GetByIdAsync(id);
        
        // در Cache ذخیره کن
        var cacheOptions = new MemoryCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(30),
            SlidingExpiration = TimeSpan.FromMinutes(10),
            Priority = CacheItemPriority.Normal
        };
        
        _cache.Set($"product_{id}", product, cacheOptions);
        
        return product;
    }
}
```

### تنظیمات در Startup

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // افزودن IMemoryCache
    services.AddMemoryCache();
    
    // یا با تنظیمات سفارشی
    services.AddMemoryCache(options =>
    {
        options.SizeLimit = 1024; // محدودیت اندازه Cache
    });
    
    services.AddScoped<IProductService, ProductService>();
}
```

---

## IDistributedCache چیست؟

### تعریف

**`IDistributedCache` یک Interface برای Cache کردن داده‌ها در یک Storage توزیع‌شده است که بین چندین سرور قابل اشتراک است.**

`IDistributedCache` برای محیط‌های توزیع‌شده (Distributed Systems) طراحی شده است که چندین Instance از Application روی سرورهای مختلف اجرا می‌شوند.

### ویژگی‌های کلیدی

- **Distributed Storage**: داده‌ها در یک Storage خارجی (Redis، SQL Server، etc.) ذخیره می‌شوند
- **Shared Across Servers**: چندین سرور می‌توانند به همان Cache دسترسی داشته باشند
- **Serialization Required**: داده‌ها باید به Byte Array تبدیل شوند (Serialization)
- **Async Operations**: تمام عملیات به صورت Async هستند
- **Network Latency**: به دلیل ارتباط با Storage خارجی، ممکن است کندتر باشد
- **Persistent**: با Restart شدن Application، داده‌های Cache حفظ می‌شوند (بسته به Implementation)

### مثال: استفاده از IDistributedCache

```csharp
public class ProductService
{
    private readonly IDistributedCache _cache;
    private readonly IProductRepository _repository;
    private readonly ILogger<ProductService> _logger;
    
    public ProductService(
        IDistributedCache cache, 
        IProductRepository repository,
        ILogger<ProductService> logger)
    {
        _cache = cache;
        _repository = repository;
        _logger = logger;
    }
    
    public async Task<Product> GetProductAsync(int id)
    {
        var cacheKey = $"product_{id}";
        
        // تلاش برای دریافت از Cache
        var cachedData = await _cache.GetStringAsync(cacheKey);
        if (cachedData != null)
        {
            return JsonSerializer.Deserialize<Product>(cachedData);
        }
        
        // اگر در Cache نبود، از Database دریافت کن
        var product = await _repository.GetByIdAsync(id);
        
        // در Cache ذخیره کن
        var serializedProduct = JsonSerializer.Serialize(product);
        var cacheOptions = new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(30),
            SlidingExpiration = TimeSpan.FromMinutes(10)
        };
        
        await _cache.SetStringAsync(cacheKey, serializedProduct, cacheOptions);
        
        return product;
    }
}
```

### تنظیمات در Startup

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // استفاده از Redis
    services.AddStackExchangeRedisCache(options =>
    {
        options.Configuration = "localhost:6379";
        options.InstanceName = "MyApp_";
    });
    
    // یا استفاده از SQL Server
    services.AddDistributedSqlServerCache(options =>
    {
        options.ConnectionString = _configuration.GetConnectionString("DefaultConnection");
        options.SchemaName = "dbo";
        options.TableName = "CacheTable";
    });
    
    services.AddScoped<IProductService, ProductService>();
}
```

---

## تفاوت‌های کلیدی

### جدول مقایسه

| ویژگی | IMemoryCache | IDistributedCache |
|-------|--------------|-------------------|
| **Storage Location** | حافظه RAM همان سرور | Storage خارجی (Redis، SQL Server، etc.) |
| **Serialization** | ❌ نیاز ندارد | ✅ نیاز دارد (Byte Array) |
| **Multi-Server** | ❌ فقط یک سرور | ✅ چندین سرور |
| **Performance** | ⚡ بسیار سریع (میکروثانیه) | 🐢 کندتر (میلی‌ثانیه) |
| **Network Latency** | ❌ ندارد | ✅ دارد |
| **Async Operations** | ❌ ندارد (Sync) | ✅ دارد (Async) |
| **Persistence** | ❌ با Restart از بین می‌رود | ✅ حفظ می‌شود (بسته به Implementation) |
| **Use Case** | Single Server Applications | Distributed Systems |
| **Memory Usage** | از حافظه Application استفاده می‌کند | از حافظه خارجی استفاده می‌کند |

### تفاوت در Serialization

```csharp
// IMemoryCache - بدون Serialization
var product = new Product { Id = 1, Name = "Laptop" };
_cache.Set("product_1", product); // Object مستقیماً ذخیره می‌شود

// IDistributedCache - نیاز به Serialization
var product = new Product { Id = 1, Name = "Laptop" };
var serialized = JsonSerializer.Serialize(product); // باید Serialize شود
await _cache.SetStringAsync("product_1", serialized); // به صورت String ذخیره می‌شود
```

### تفاوت در Performance

```csharp
// IMemoryCache - بسیار سریع
var stopwatch = Stopwatch.StartNew();
var product = _memoryCache.Get<Product>("product_1");
stopwatch.Stop();
// زمان: ~0.001ms (میکروثانیه)

// IDistributedCache - کندتر (Network Latency)
var stopwatch = Stopwatch.StartNew();
var cachedData = await _distributedCache.GetStringAsync("product_1");
var product = JsonSerializer.Deserialize<Product>(cachedData);
stopwatch.Stop();
// زمان: ~1-5ms (میلی‌ثانیه) - بسته به Network Latency
```

---

## پیاده‌سازی‌های IDistributedCache

در حال حاضر، ASP.NET Core چهار پیاده‌سازی برای `IDistributedCache` ارائه می‌دهد:

### 1. Distributed Redis Cache

**محبوب‌ترین و پرکاربردترین پیاده‌سازی:**

```csharp
services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = "localhost:6379";
    options.InstanceName = "MyApp_";
});
```

**مزایا**:
- ⚡ بسیار سریع
- 🔄 پشتیبانی از Replication و Clustering
- 📊 پشتیبانی از Data Structures مختلف
- 💾 می‌تواند Persistent باشد (با AOF یا RDB)

**معایب**:
- نیاز به نصب و نگهداری Redis Server
- نیاز به Memory اضافی

### 2. Distributed SQL Server Cache

**استفاده از SQL Server به عنوان Cache Store:**

```csharp
services.AddDistributedSqlServerCache(options =>
{
    options.ConnectionString = _configuration.GetConnectionString("DefaultConnection");
    options.SchemaName = "dbo";
    options.TableName = "CacheTable";
});
```

**مزایا**:
- ✅ استفاده از Infrastructure موجود (SQL Server)
- 💾 Persistent و Reliable
- 🔒 امنیت SQL Server

**معایب**:
- 🐢 کندتر از Redis
- 📊 فشار اضافی به Database
- 💰 هزینه‌بر برای Cache

### 3. Distributed NCache Cache

**یک Commercial Cache Solution:**

```csharp
services.AddNCacheDistributedCache(options =>
{
    options.CacheName = "myCache";
    options.EnableLogs = true;
});
```

**مزایا**:
- ⚡ بسیار سریع
- 🔄 پشتیبانی از Clustering
- 📊 Monitoring Tools

**معایب**:
- 💰 Commercial (نیاز به License)
- 🔧 پیچیدگی Setup

### 4. Distributed Memory Cache

**استفاده از Memory همان سرور (مشابه IMemoryCache):**

```csharp
services.AddDistributedMemoryCache();
```

> [!warning] نکته مهم
> `DistributedMemoryCache` از حافظه همان سرور استفاده می‌کند و **توزیع‌شده نیست**. این فقط برای Testing یا Development مناسب است و در Production نباید استفاده شود.

**مزایا**:
- ✅ ساده برای Testing
- ⚡ سریع

**معایب**:
- ❌ واقعاً Distributed نیست
- ❌ با Restart از بین می‌رود
- ❌ فقط برای یک سرور کار می‌کند

---

## مثال‌های عملی

### مثال 1: استفاده ترکیبی (Hybrid Approach)

```csharp
public class HybridCacheService
{
    private readonly IMemoryCache _memoryCache;
    private readonly IDistributedCache _distributedCache;
    private readonly ILogger<HybridCacheService> _logger;
    
    public HybridCacheService(
        IMemoryCache memoryCache,
        IDistributedCache distributedCache,
        ILogger<HybridCacheService> logger)
    {
        _memoryCache = memoryCache;
        _distributedCache = distributedCache;
        _logger = logger;
    }
    
    public async Task<T> GetOrSetAsync<T>(
        string key, 
        Func<Task<T>> factory,
        TimeSpan? expiration = null) where T : class
    {
        // ابتدا از Memory Cache بررسی کن (سریع‌تر)
        if (_memoryCache.TryGetValue(key, out T cachedValue))
        {
            return cachedValue;
        }
        
        // سپس از Distributed Cache بررسی کن
        try
        {
            var distributedData = await _distributedCache.GetStringAsync(key);
            if (distributedData != null)
            {
                var value = JsonSerializer.Deserialize<T>(distributedData);
                
                // در Memory Cache هم ذخیره کن برای دسترسی سریع‌تر
                _memoryCache.Set(key, value, new MemoryCacheEntryOptions
                {
                    AbsoluteExpirationRelativeToNow = expiration ?? TimeSpan.FromMinutes(30)
                });
                
                return value;
            }
        }
        catch (Exception ex)
        {
            _logger.LogWarning(ex, "Failed to get from distributed cache");
        }
        
        // اگر در هیچ Cache نبود، از Factory دریافت کن
        var value = await factory();
        
        // در هر دو Cache ذخیره کن
        var serialized = JsonSerializer.Serialize(value);
        var cacheOptions = new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = expiration ?? TimeSpan.FromMinutes(30)
        };
        
        await _distributedCache.SetStringAsync(key, serialized, cacheOptions);
        _memoryCache.Set(key, value, new MemoryCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = expiration ?? TimeSpan.FromMinutes(30)
        });
        
        return value;
    }
}
```

### مثال 2: Cache Invalidation Pattern

```csharp
public class CacheInvalidationService
{
    private readonly IMemoryCache _memoryCache;
    private readonly IDistributedCache _distributedCache;
    
    public async Task InvalidateAsync(string key)
    {
        // از Memory Cache حذف کن
        _memoryCache.Remove(key);
        
        // از Distributed Cache حذف کن
        await _distributedCache.RemoveAsync(key);
    }
    
    public async Task InvalidateByPatternAsync(string pattern)
    {
        // برای IMemoryCache - باید تمام Keys را بررسی کنیم
        // (این یک محدودیت IMemoryCache است)
        
        // برای IDistributedCache با Redis - می‌توان از SCAN استفاده کرد
        // اما در Interface استاندارد این امکان وجود ندارد
    }
}
```

---

## چه زمانی کدام را استفاده کنیم؟

### استفاده از IMemoryCache

> [!success] استفاده کنید وقتی
> - Application روی یک سرور اجرا می‌شود (Single Server)
> - نیاز به Performance بسیار بالا دارید
> - داده‌های Cache کوچک هستند
> - از دست رفتن Cache با Restart مشکلی ایجاد نمی‌کند
> - نیاز به Serialization ندارید

**مثال‌های Use Case**:
- Application های کوچک و متوسط
- Development و Testing
- Cache کردن Configuration
- Cache کردن داده‌های Static

### استفاده از IDistributedCache

> [!success] استفاده کنید وقتی
> - Application روی چندین سرور اجرا می‌شود (Load Balancing)
> - نیاز به Shared Cache بین سرورها دارید
> - نیاز به Persistence Cache دارید
> - می‌خواهید Cache را بین چندین Application به اشتراک بگذارید

**مثال‌های Use Case**:
- Microservices Architecture
- Application های با High Availability
- Session State Management
- Cache کردن داده‌های مشترک بین چندین Service

### استفاده ترکیبی (Hybrid)

> [!tip] بهترین رویکرد
> در بسیاری از موارد، استفاده ترکیبی از هر دو بهترین نتیجه را می‌دهد:
> - از `IMemoryCache` برای داده‌های پرتکرار و کوچک
> - از `IDistributedCache` برای داده‌های مشترک بین سرورها

---

## Trade-offs و محدودیت‌ها

### IMemoryCache

**مزایا**:
- ⚡ بسیار سریع
- 🔧 ساده برای استفاده
- 💰 بدون هزینه اضافی
- 🚫 بدون نیاز به Serialization

**معایب**:
- ❌ فقط برای یک سرور
- ❌ با Restart از بین می‌رود
- 📊 محدودیت حافظه
- 🔄 مشکل در Load Balancing

### IDistributedCache

**مزایا**:
- ✅ Multi-Server Support
- 💾 Persistent (بسته به Implementation)
- 🔄 مناسب برای Load Balancing
- 📊 Scalable

**معایب**:
- 🐢 کندتر (Network Latency)
- 🔧 پیچیدگی بیشتر
- 💰 هزینه Infrastructure
- 📦 نیاز به Serialization

### چرا نمی‌توان آن‌ها را یکپارچه کرد؟

> [!important] نکته مهم
> `IMemoryCache` و `IDistributedCache` نمی‌توانند یکپارچه شوند چون:
> 
> 1. **Semantics متفاوت**: `IMemoryCache` می‌تواند Object های زنده را ذخیره کند، اما `IDistributedCache` باید Serialize شود
> 2. **Performance**: `IMemoryCache` همیشه در Memory و سریع است، اما `IDistributedCache` ممکن است Fail کند یا کند باشد
> 3. **Async Operations**: `IDistributedCache` باید Async باشد چون ممکن است Network Call کند، اما `IMemoryCache` Sync است
> 4. **Error Handling**: `IDistributedCache` باید با Disconnection از Store مقابله کند

**مثال**: مثل این است که بخواهیم `IDictionary<object, object>` را با Redis Client Interface یکپارچه کنیم - این دو کاملاً متفاوت هستند.

---

## نکات مصاحبه

### سوالات رایج

#### 1. تفاوت IMemoryCache و IDistributedCache چیست؟

**پاسخ Senior-Level**:
- **IMemoryCache**: In-Memory Cache در همان سرور، سریع، بدون Serialization، فقط یک سرور
- **IDistributedCache**: Distributed Cache در Storage خارجی، کندتر، نیاز به Serialization، چندین سرور
- **Trade-off**: Performance vs Scalability

#### 2. چه زمانی از IMemoryCache استفاده کنیم؟

**پاسخ**:
- Single Server Applications
- نیاز به Performance بسیار بالا
- داده‌های کوچک
- از دست رفتن Cache با Restart مشکلی ندارد

#### 3. چه زمانی از IDistributedCache استفاده کنیم؟

**پاسخ**:
- Multi-Server Applications (Load Balancing)
- نیاز به Shared Cache
- نیاز به Persistence
- Microservices Architecture

#### 4. کدام پیاده‌سازی IDistributedCache بهتر است؟

**پاسخ**:
- **Redis**: برای اکثر موارد بهترین انتخاب است (سریع، Reliable، Feature-Rich)
- **SQL Server**: وقتی Infrastructure موجود است و نیاز به Persistence داریم
- **Memory Cache**: فقط برای Testing

#### 5. چگونه می‌توانیم از هر دو استفاده کنیم؟

**پاسخ**:
- Hybrid Approach: از `IMemoryCache` برای L1 Cache و `IDistributedCache` برای L2 Cache
- Pattern: ابتدا از Memory Cache بررسی کنیم، سپس از Distributed Cache

### Common Mistakes & Red Flags

> [!error] اشتباه
> استفاده از `DistributedMemoryCache` در Production
> 
> [!success] درست
> `DistributedMemoryCache` فقط برای Testing است. در Production از Redis یا SQL Server استفاده کنید.

> [!error] اشتباه
> استفاده از `IMemoryCache` در Load Balanced Environment
> 
> [!success] درست
> در Load Balanced Environment از `IDistributedCache` استفاده کنید تا Cache بین سرورها به اشتراک گذاشته شود.

> [!error] اشتباه
> عدم مدیریت Memory در `IMemoryCache`
> 
> [!success] درست
> از `MemoryCacheEntryOptions` برای محدود کردن اندازه و Expiration استفاده کنید.

> [!error] اشتباه
> عدم Handle کردن Exception در `IDistributedCache`
> 
> [!success] درست
> همیشه Exception Handling داشته باشید چون Network Call ممکن است Fail کند.

---

## خلاصه

### مفاهیم کلیدی

- **IMemoryCache**: In-Memory Cache برای Single Server، سریع، بدون Serialization
- **IDistributedCache**: Distributed Cache برای Multi-Server، کندتر، نیاز به Serialization
- **Trade-off**: Performance vs Scalability
- **Hybrid Approach**: استفاده ترکیبی از هر دو برای بهترین نتیجه

### Best Practices

- از `IMemoryCache` برای Single Server Applications استفاده کنید
- از `IDistributedCache` برای Distributed Systems استفاده کنید
- از Redis برای `IDistributedCache` در Production استفاده کنید
- Exception Handling برای `IDistributedCache` داشته باشید
- از Cache Expiration و Size Limits استفاده کنید
- از Hybrid Approach برای بهینه‌سازی Performance استفاده کنید

**موفق باشید!** 🚀
