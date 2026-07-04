# Exception Handling در C#: try-catch-finally

Exception Handling یکی از **مهم‌ترین مفاهیم** در C# است که در مصاحبه‌ها بسیار پرسیده می‌شود. درک صحیح نحوه استفاده از try-catch-finally، throw، و Best Practices برای نوشتن کد قابل اعتماد ضروری است.

---

## فهرست مطالب

1. [try-catch-finally](#try-catch-finally)
2. [throw vs throw ex](#throw-vs-throw-ex)
3. [Exception Filtering](#exception-filtering)
4. [using Statement](#using-statement)
5. [Best Practices](#best-practices)
6. [نکات مصاحبه](#نکات-مصاحبه)

---

## try-catch-finally

### ساختار کلی

```csharp
try
{
    // کدهایی که مشکوک به خطا هستند
}
catch (ExceptionType ex)
{
    // مدیریت خطا
}
finally
{
    // کدهایی که همیشه اجرا می‌شوند
}
```

### try Block

**کدهایی که مشکوک به خطا هستند داخل `try` قرار می‌گیرند:**

```csharp
try
{
    int result = 10 / 0; // Division by zero
    string text = null;
    int length = text.Length; // NullReferenceException
}
```

### catch Block

**`catch` زمانی که برنامه در `try` به مشکل بر می‌خورد، صدا زده می‌شود:**

```csharp
try
{
    int result = 10 / 0;
}
catch (DivideByZeroException ex)
{
    Console.WriteLine($"خطا: {ex.Message}");
    // مدیریت خطا
}
```

### throw

**`throw` برنامه را از داخل Stack خارج می‌کند و به خروجی پرت می‌کند:**

```csharp
try
{
    // کد
}
catch (Exception ex)
{
    // Log error
    throw; // Exception را دوباره throw می‌کند
}
```

### finally

**`finally` در نهایت صدا زده می‌شود، چه برنامه به خطا بخورد چه نخورد:**

```csharp
try
{
    // کد
}
catch (Exception ex)
{
    // مدیریت خطا
}
finally
{
    // این کد همیشه اجرا می‌شود
    Console.WriteLine("Finally block executed");
}
```

### مثال: Database Connection

**مانند زمانی که یک Connection از Database ایجاد می‌شود و در نهایت باید بسته شود، چه عملیات به خطا بخورد چه نخورد:**

```csharp
SqlConnection connection = null;
try
{
    connection = new SqlConnection(connectionString);
    connection.Open();
    // عملیات Database
}
catch (SqlException ex)
{
    Console.WriteLine($"Database error: {ex.Message}");
    throw;
}
finally
{
    // همیشه Connection را ببند
    connection?.Close();
    connection?.Dispose();
}
```

### try-finally (بدون catch)

**می‌توان Block `try` را به تنهایی استفاده کنیم اما حتماً باید `finally` داشته باشیم:**

```csharp
try
{
    // کد
}
finally
{
    // Cleanup code
    // این کد همیشه اجرا می‌شود
}
```

**نکته**: در این حالت، Exception به Caller پرتاب می‌شود اما `finally` همیشه اجرا می‌شود.

---

## throw vs throw ex

### تفاوت کلیدی

**`throw` به صورت خالی دارای جزئیات بیشتر از `throw ex` است:**

```csharp
// ❌ بد - Stack Trace از دست می‌رود
try
{
    // کد
}
catch (Exception ex)
{
    // Log
    throw ex; // Stack Trace از این نقطه شروع می‌شود!
}

// ✅ خوب - Stack Trace حفظ می‌شود
try
{
    // کد
}
catch (Exception ex)
{
    // Log
    throw; // Stack Trace کامل حفظ می‌شود
}
```

### مثال: تفاوت در Stack Trace

```csharp
public void Method1()
{
    try
    {
        Method2();
    }
    catch (Exception ex)
    {
        // throw ex; // Stack Trace: Method1 -> (Method2 از دست می‌رود)
        throw; // Stack Trace: Method1 -> Method2 -> Method3 (کامل)
    }
}

public void Method2()
{
    Method3();
}

public void Method3()
{
    throw new InvalidOperationException("Error occurred");
}
```

### Best Practice

**همیشه از `throw` استفاده کنید، نه `throw ex`:**

```csharp
// ✅ صحیح
catch (Exception ex)
{
    _logger.LogError(ex, "Error occurred");
    throw; // Stack Trace کامل
}

// ❌ غلط
catch (Exception ex)
{
    _logger.LogError(ex, "Error occurred");
    throw ex; // Stack Trace از دست می‌رود
}
```

---

## Exception Filtering

### تعریف

**همچنین می‌توان روی Exception‌ها شرط گذاشت که مثلاً در صورتی که Timeout اتفاق افتاد، وارد یک Exception خاص شود:**

```csharp
try
{
    // کد
}
catch (TimeoutException ex) when (ex.Message.Contains("Connection"))
{
    // فقط اگر Timeout مربوط به Connection باشد
    Console.WriteLine("Connection timeout");
}
catch (TimeoutException ex)
{
    // سایر Timeout‌ها
    Console.WriteLine("General timeout");
}
```

### مثال: Exception Filtering

```csharp
try
{
    await httpClient.GetAsync(url);
}
catch (HttpRequestException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // فقط برای 404
    return NotFound();
}
catch (HttpRequestException ex) when (ex.StatusCode == HttpStatusCode.Unauthorized)
{
    // فقط برای 401
    return Unauthorized();
}
catch (HttpRequestException ex)
{
    // سایر HttpRequestException‌ها
    return BadRequest();
}
```

### مزایای Exception Filtering

1. **Selective Handling**: فقط Exception‌های خاص را Handle می‌کنیم
2. **Cleaner Code**: کد تمیزتر و خوانا‌تر
3. **Performance**: Exception را دوباره Throw نمی‌کنیم

---

## using Statement

### استفاده از using به جای try-catch

**در C# می‌توان از `using` Statement برای مدیریت منابع استفاده کرد:**

```csharp
// ❌ روش قدیمی
SqlConnection connection = null;
try
{
    connection = new SqlConnection(connectionString);
    connection.Open();
    // استفاده از connection
}
finally
{
    connection?.Dispose();
}

// ✅ روش جدید - using Statement
using (var connection = new SqlConnection(connectionString))
{
    connection.Open();
    // استفاده از connection
} // به صورت خودکار Dispose می‌شود
```

### شکل ساده‌تر: using Declaration (C# 8.0+)

**در C# 8.0، می‌توان از using Declaration استفاده کرد:**

```csharp
// ✅ ساده‌تر - C# 8.0+
using var connection = new SqlConnection(connectionString);
connection.Open();
// استفاده از connection
// در انتهای Scope به صورت خودکار Dispose می‌شود
```

**در این حالت، در انتهای Block Function، Object مورد نظر Dispose می‌شود.**

### مثال: File Operations

```csharp
// ✅ using Statement
using (var file = new FileStream("data.txt", FileMode.Open))
{
    // استفاده از file
} // Dispose می‌شود

// ✅ using Declaration (C# 8.0+)
using var file = new FileStream("data.txt", FileMode.Open);
// استفاده از file
// در انتهای Scope Dispose می‌شود
```

### مزایای using

1. **Automatic Dispose**: به صورت خودکار Dispose می‌شود
2. **Cleaner Code**: کد تمیزتر و کوتاه‌تر
3. **Exception Safe**: حتی در صورت Exception، Dispose می‌شود
4. **Less Error-Prone**: احتمال فراموش کردن Dispose کمتر است

### Helper Methods در C# جدید

**در C# جدید می‌توان به جای استفاده از Condition `if`، از Helper Methods استفاده کرد:**

```csharp
// ❌ روش قدیمی
if (user == null)
    throw new NullReferenceException();

// ✅ روش جدید - C# 10+
ArgumentNullException.ThrowIfNull(user);
```

### Helper Methods موجود

```csharp
// ArgumentNullException
ArgumentNullException.ThrowIfNull(user);
ArgumentNullException.ThrowIfNullOrEmpty(name);

// ArgumentException
ArgumentException.ThrowIfNullOrWhiteSpace(text);

// ObjectDisposedException
ObjectDisposedException.ThrowIf(_disposed, this);
```

### مزایای Helper Methods

1. **Cleaner Code**: کد تمیزتر و خوانا‌تر
2. **Consistent**: یکنواخت‌تر
3. **Less Boilerplate**: کد تکراری کمتر
4. **Better Performance**: بهینه‌تر

---

## Best Practices

### 1. Catch Specific Exceptions

```csharp
// ❌ بد - Catch همه Exception‌ها
try
{
    // کد
}
catch (Exception ex)
{
    // همه Exception‌ها را catch می‌کند
}

// ✅ خوب - Catch Exception‌های خاص
try
{
    // کد
}
catch (FileNotFoundException ex)
{
    // فقط FileNotFoundException
}
catch (UnauthorizedAccessException ex)
{
    // فقط UnauthorizedAccessException
}
```

### 2. Log Before Re-throwing

```csharp
try
{
    // کد
}
catch (Exception ex)
{
    _logger.LogError(ex, "Error occurred in Method1");
    throw; // Stack Trace حفظ می‌شود
}
```

### 3. Use finally for Cleanup

```csharp
FileStream file = null;
try
{
    file = new FileStream("data.txt", FileMode.Open);
    // استفاده
}
finally
{
    file?.Dispose(); // همیشه Dispose می‌شود
}
```

### 4. Avoid Empty catch Blocks

```csharp
// ❌ بد - Exception را نادیده می‌گیرد
try
{
    // کد
}
catch (Exception)
{
    // خالی - Exception نادیده گرفته می‌شود
}

// ✅ خوب - حداقل Log کنید
try
{
    // کد
}
catch (Exception ex)
{
    _logger.LogWarning(ex, "Expected exception occurred");
}
```

### 5. Don't Catch What You Can't Handle

```csharp
// ❌ بد - Exception را catch می‌کنید اما نمی‌توانید Handle کنید
try
{
    ProcessCriticalData();
}
catch (Exception ex)
{
    // نمی‌دانید چه کار کنید
}

// ✅ خوب - فقط Exception‌هایی که می‌توانید Handle کنید
try
{
    ProcessCriticalData();
}
catch (ExpectedException ex)
{
    // می‌دانید چه کار کنید
    HandleExpectedException(ex);
}
// سایر Exception‌ها به Caller پرتاب می‌شوند
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. تفاوت throw و throw ex چیست؟

**پاسخ**:
- `throw`: Stack Trace کامل حفظ می‌شود
- `throw ex`: Stack Trace از این نقطه شروع می‌شود (از دست می‌رود)
- **همیشه از `throw` استفاده کنید**

#### 2. finally چه زمانی اجرا می‌شود؟

**پاسخ**:
- `finally` **همیشه** اجرا می‌شود
- چه Exception رخ دهد چه رخ ندهد
- حتی اگر `return` در try یا catch باشد
- **مثال**: برای Cleanup منابع (Database Connection، File Stream)

#### 3. آیا می‌توان try بدون catch استفاده کرد؟

**پاسخ**:
- بله، اما باید `finally` داشته باشد
- Exception به Caller پرتاب می‌شود
- `finally` همیشه اجرا می‌شود

#### 4. Exception Filtering چیست؟

**پاسخ**:
- استفاده از `when` برای شرط‌گذاری روی Exception
- فقط Exception‌های خاص را Handle می‌کنیم
- **مثال**: `catch (Exception ex) when (ex.Message.Contains("Timeout"))`

#### 5. using Statement چگونه کار می‌کند؟

**پاسخ**:
- برای مدیریت منابع که `IDisposable` هستند
- به صورت خودکار `Dispose` می‌شود
- حتی در صورت Exception، Dispose می‌شود
- **مثال**: `using var file = new FileStream(...);`

#### 6. چه زمانی از Exception Handling استفاده می‌کنیم؟

**پاسخ**:
- برای خطاهای **Expected** (مثل File Not Found)
- برای Cleanup منابع
- **نکته**: برای خطاهای **Unexpected** (مثل Null Reference)، بهتر است کد را Fix کنیم

### نکات مهم برای مصاحبه

1. **Stack Trace**: همیشه از `throw` استفاده کنید، نه `throw ex`
2. **Specific Exceptions**: Exception‌های خاص را Catch کنید
3. **finally**: برای Cleanup منابع استفاده کنید
4. **using**: برای مدیریت منابع از `using` استفاده کنید
5. **Don't Swallow**: Exception‌ها را نادیده نگیرید

---

## خلاصه

### try-catch-finally
- `try`: کد مشکوک به خطا
- `catch`: مدیریت خطا
- `finally`: همیشه اجرا می‌شود

### throw vs throw ex
- `throw`: Stack Trace کامل
- `throw ex`: Stack Trace از دست می‌رود

### using Statement
- Automatic Dispose
- Exception Safe
- Cleaner Code

### Best Practices
- Catch Specific Exceptions
- Log Before Re-throwing
- Use finally for Cleanup
- Don't Swallow Exceptions

**موفق باشید!** 🚀