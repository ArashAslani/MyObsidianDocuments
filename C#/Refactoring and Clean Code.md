# Refactoring: از کد کثیف به کد تمیز

Refactoring یکی از **مهم‌ترین مهارت‌های** یک توسعه‌دهنده Senior است. درک اصول Refactoring و توانایی تشخیص و رفع Code Smell در مصاحبه‌ها بسیار مهم است.

---

## فهرست مطالب

1. [Refactoring چیست؟](#refactoring-چیست)
2. [کد کثیف (Dirty Code)](#کد-کثیف-dirty-code)
3. [ویژگی‌های کد تمیز (Clean Code)](#ویژگیهای-کد-تمیز-clean-code)
4. [Code Smell (بوی بد کد)](#code-smell-بوی-بد-کد)
5. [Technical Debt (بدهی فنی)](#technical-debt-بدهی-فنی)
6. [فرآیند Refactoring](#فرآیند-refactoring)
7. [تکنیک‌های Refactoring](#تکنیکهای-refactoring)
8. [نکات مصاحبه](#نکات-مصاحبه)

---

## Refactoring چیست؟

### تعریف

**Refactoring فرآیند اصلاح کد است** بدون تغییر رفتار خارجی آن.

### هدف اصلی

**هدف اصلی Refactoring جنگیدن با بدهی فنی (Technical Debt) یا پرداختن آن است.**

### ویژگی‌های Refactoring

1. **بدون تغییر رفتار**: رفتار خارجی کد تغییر نمی‌کند
2. **بهبود ساختار**: ساختار داخلی کد بهبود می‌یابد
3. **تدریجی**: به صورت تدریجی انجام می‌شود
4. **با تست**: همیشه با Test Coverage انجام می‌شود

### مثال

```csharp
// ❌ قبل از Refactoring
public int Calculate(int a, int b, int c)
{
    return a * b + a * c + b * c;
}

// ✅ بعد از Refactoring
public int Calculate(int a, int b, int c)
{
    int ab = Multiply(a, b);
    int ac = Multiply(a, c);
    int bc = Multiply(b, c);
    return Add(Add(ab, ac), bc);
}

private int Multiply(int x, int y) => x * y;
private int Add(int x, int y) => x + y;
```

---

## کد کثیف (Dirty Code)

### تعریف

**کد کثیف مربوط به زبانی که کار می‌کنیم نیست و حتی مربوط به تخصص هم نیست.**

کد کثیف می‌تواند در هر زبانی و هر تخصصی وجود داشته باشد.

### علائم کد کثیف

1. **نام‌گذاری بد**: نام‌های نامفهوم و غیرقابل فهم
2. **توابع بزرگ**: توابع با صدها خط کد
3. **تکرار**: کد تکراری در چندین جا
4. **وابستگی زیاد**: وابستگی‌های پیچیده بین کلاس‌ها
5. **کامنت‌های زیاد**: نیاز به کامنت زیاد برای توضیح کد

### مثال: کد کثیف

```csharp
// ❌ کد کثیف
public void Process(int x, int y, int z)
{
    int a = x * y;
    int b = x * z;
    int c = y * z;
    int d = a + b + c;
    Console.WriteLine(d);
    // ... 100 خط دیگر
}
```

---

## ویژگی‌های کد تمیز (Clean Code)

### 1. وضوح و خوانایی

**کد تمیز برای بقیه برنامه‌نویسان واضح است.**

بحث سر الگوریتم‌های پیچیده نیست - حتی الگوریتم‌های پیچیده باید به صورت واضح نوشته شوند.

```csharp
// ✅ کد تمیز - واضح و خوانا
public decimal CalculateTotalPrice(Order order)
{
    decimal subtotal = CalculateSubtotal(order.Items);
    decimal tax = CalculateTax(subtotal, order.TaxRate);
    decimal discount = CalculateDiscount(order, subtotal);
    
    return subtotal + tax - discount;
}
```

### 2. عدم تکرار (DRY Principle)

**کد تمیز هیچ قسمت تکراری ندارد:**

اگر منطق تکراری در برنامه وجود داشته باشد، هر بار و پس از هر تغییری باید همه جا Update کنیم که:
- بار ذهنی روی برنامه‌نویس را افزایش می‌دهد
- پیشرفت کد را کند می‌کند
- احتمال Bug را افزایش می‌دهد

```csharp
// ❌ کد تکراری
public void ProcessOrder1(Order order)
{
    if (order.Status == "Pending")
    {
        // Logic
    }
}

public void ProcessOrder2(Order order)
{
    if (order.Status == "Pending") // تکرار!
    {
        // Logic
    }
}

// ✅ کد تمیز - بدون تکرار
public void ProcessOrder(Order order)
{
    if (order.IsPending())
    {
        // Logic
    }
}
```

### 3. حداقل پیچیدگی

**کد تمیز شامل حداقل تعداد کلاس و قسمت‌های متحرک است:**

- هر چه حجم کد کمتر باشد، چیزهایی که باید در ذهن نگه دارید کمتر می‌شود
- هر چه کد کمتر شود، هزینه نگهداری هم کمتر است
- **کد شبیه مسئولیت است - آن را کوتاه (Small) و ساده (Simple) نگه دارید**

```csharp
// ❌ پیچیده - کلاس‌های زیاد
public class OrderProcessor { }
public class OrderValidator { }
public class OrderCalculator { }
public class OrderNotifier { }
public class OrderLogger { }
// ... 10 کلاس دیگر

// ✅ ساده - مسئولیت‌های منطقی
public class OrderService
{
    private readonly IOrderValidator _validator;
    private readonly IOrderCalculator _calculator;
    // ...
}
```

### 4. Test Coverage کامل

**کد تمیز شامل تمام تست‌های برنامه را با موفقیت طی می‌کند:**

- اگر فقط 95 درصد تست‌های شما Pass شده‌اند، یعنی کد هنوز کثیف است
- تمام قسمت‌های کد جدید هم باید داخل تست‌ها ایفای نقش کنند
- **Full Coverage**: Coverage کامل تست

```csharp
// ✅ کد با Test Coverage
[Fact]
public void CalculateTotal_ShouldReturnCorrectTotal()
{
    // Arrange
    var order = new Order { /* ... */ };
    var service = new OrderService();
    
    // Act
    var result = service.CalculateTotal(order);
    
    // Assert
    Assert.Equal(expectedTotal, result);
}
```

### 5. نگهداری آسان

**نگهداری از کد تمیز آسان‌تر و ارزان‌تر است:**

- تغییرات سریع‌تر انجام می‌شوند
- Bug‌ها سریع‌تر پیدا می‌شوند
- هزینه توسعه کاهش می‌یابد

---

## Code Smell (بوی بد کد)

### تعریف

**Code Smell نشانه‌هایی هستند که نشان می‌دهند کد نیاز به Refactoring دارد.**

### فرآیند شناسایی

**کد کثیف وقتی وارد چرخه می‌شود، در مرحله اول باید چیزهایی که به دردش نمی‌خورد ازش جدا کنیم. به اصطلاح Bad Smell‌ها را جدا کنیم.**

**ما باید این‌ها را شناسایی کنیم و Sign‌ها یا نشانه‌های آن را جدا کنیم و حذف و اضافه کنیم تا با تکنیک‌های Refactoring و تکرار این چرخه به کد تمیز برسیم.**

### انواع Code Smell

#### 1. Long Method (متد طولانی)

```csharp
// ❌ Code Smell: Long Method
public void ProcessOrder(Order order)
{
    // 200 خط کد
    // ...
}

// ✅ Refactored: Extract Method
public void ProcessOrder(Order order)
{
    ValidateOrder(order);
    CalculateTotal(order);
    ApplyDiscount(order);
    SendNotification(order);
}
```

#### 2. Duplicate Code (کد تکراری)

```csharp
// ❌ Code Smell: Duplicate Code
public void Method1()
{
    if (condition) { /* ... */ }
}

public void Method2()
{
    if (condition) { /* ... */ } // تکرار
}

// ✅ Refactored: Extract Method
private void CommonLogic()
{
    if (condition) { /* ... */ }
}
```

#### 3. Large Class (کلاس بزرگ)

```csharp
// ❌ Code Smell: Large Class
public class OrderProcessor
{
    // 50 متد و 20 Property
}

// ✅ Refactored: Extract Class
public class OrderValidator { }
public class OrderCalculator { }
public class OrderNotifier { }
```

#### 4. Feature Envy (حسادت به Feature)

```csharp
// ❌ Code Smell: Feature Envy
public class OrderService
{
    public decimal CalculateTotal(Order order)
    {
        return order.Items.Sum(i => i.Price * i.Quantity) 
             + order.Tax 
             - order.Discount; // استفاده زیاد از order
    }
}

// ✅ Refactored: Move Method
public class Order
{
    public decimal CalculateTotal()
    {
        return Items.Sum(i => i.Price * i.Quantity) 
             + Tax 
             - Discount;
    }
}
```

---

## Technical Debt (بدهی فنی)

### تعریف

**هیچ کس عمداً کد کثیف نمی‌زند. کد به مرور کثیف می‌شود.**

### تشبیه وام بانکی

**برای مثال شما از بانک وام می‌گیرید تا بتوانید چیزی را سریع‌تر بخرید:**

- در ادامه بازپرداخت وام از مبلغ اصلی بیشتر می‌شود
- هر چه بازپرداخت بلندمدت‌تر باشد، بهره بانک بیشتر می‌شود

**Technical Debt هم همینطور است:**

- کد کثیف را سریع می‌نویسیم (وام می‌گیریم)
- بعداً باید Refactor کنیم (بازپرداخت)
- هر چه دیرتر Refactor کنیم، هزینه بیشتر می‌شود (بهره بیشتر)

### مثال: Technical Debt

```csharp
// ❌ Technical Debt: کد سریع نوشته شده
public void ProcessOrder(Order order)
{
    // TODO: Refactor this later
    // Quick fix for now
    if (order.Status == "P")
    {
        // Magic numbers and strings
        order.Total = order.Items.Sum(x => x.Price) * 1.1m;
    }
}

// ✅ بعد از Refactoring: کد تمیز
public void ProcessOrder(Order order)
{
    if (order.IsPending())
    {
        order.CalculateTotalWithTax();
    }
}
```

### مدیریت Technical Debt

1. **شناسایی**: شناسایی Technical Debt
2. **اولویت‌بندی**: اولویت‌بندی بر اساس Impact
3. **برنامه‌ریزی**: برنامه‌ریزی برای Refactoring
4. **اجرا**: اجرای تدریجی Refactoring

---

## فرآیند Refactoring

### چرخه Refactoring

**فرآیند تغییر کد کثیف (Dirty Code) به کد تمیز (Clean Code):**

1. **شناسایی Code Smell**: شناسایی نشانه‌های کد کثیف
2. **انتخاب تکنیک**: انتخاب تکنیک Refactoring مناسب
3. **نوشتن تست**: نوشتن تست برای اطمینان از رفتار
4. **اجرای Refactoring**: اجرای تکنیک Refactoring
5. **اجرای تست**: اجرای تست‌ها برای اطمینان
6. **تکرار**: تکرار چرخه تا رسیدن به کد تمیز

### مراحل Refactoring

```
کد کثیف
  ↓
شناسایی Code Smell
  ↓
انتخاب تکنیک Refactoring
  ↓
نوشتن/اجرای تست
  ↓
اجرای Refactoring
  ↓
اجرای تست (Pass)
  ↓
کد تمیز
```

---

## تکنیک‌های Refactoring

### 1. Extract Method

**استخراج یک متد از کد موجود:**

```csharp
// ❌ قبل
public void ProcessOrder(Order order)
{
    // 50 خط کد
    decimal total = 0;
    foreach (var item in order.Items)
    {
        total += item.Price * item.Quantity;
    }
    // ...
}

// ✅ بعد
public void ProcessOrder(Order order)
{
    decimal total = CalculateSubtotal(order.Items);
    // ...
}

private decimal CalculateSubtotal(List<OrderItem> items)
{
    decimal total = 0;
    foreach (var item in items)
    {
        total += item.Price * item.Quantity;
    }
    return total;
}
```

### 2. Extract Class

**استخراج یک کلاس از کلاس موجود:**

```csharp
// ❌ قبل: کلاس بزرگ
public class Order
{
    public void Validate() { }
    public void Calculate() { }
    public void SendEmail() { }
    public void Log() { }
}

// ✅ بعد: Extract Class
public class Order
{
    private readonly OrderValidator _validator;
    private readonly OrderCalculator _calculator;
    // ...
}
```

### 3. Rename

**تغییر نام برای وضوح بیشتر:**

```csharp
// ❌ قبل
public void Proc(Order o) { }

// ✅ بعد
public void ProcessOrder(Order order) { }
```

### 4. Move Method

**انتقال متد به کلاس مناسب:**

```csharp
// ❌ قبل: Feature Envy
public class OrderService
{
    public decimal GetTotal(Order order)
    {
        return order.Items.Sum(i => i.Price);
    }
}

// ✅ بعد: Move Method
public class Order
{
    public decimal GetTotal()
    {
        return Items.Sum(i => i.Price);
    }
}
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. Refactoring چیست؟

**پاسخ**:
- فرآیند اصلاح کد بدون تغییر رفتار خارجی
- بهبود ساختار داخلی کد
- هدف: کاهش Technical Debt

#### 2. چه زمانی Refactoring انجام می‌دهیم؟

**پاسخ**:
- وقتی Code Smell شناسایی می‌کنیم
- قبل از اضافه کردن Feature جدید
- به صورت مداوم (Continuous Refactoring)
- **نکته**: نه فقط وقتی کد کثیف است!

#### 3. ویژگی‌های کد تمیز چیست؟

**پاسخ**:
- وضوح و خوانایی
- عدم تکرار (DRY)
- حداقل پیچیدگی
- Test Coverage کامل
- نگهداری آسان

#### 4. Technical Debt چیست؟

**پاسخ**:
- بدهی فنی که با نوشتن کد سریع ایجاد می‌شود
- باید بعداً پرداخت شود (Refactoring)
- هر چه دیرتر پرداخت شود، هزینه بیشتر می‌شود

#### 5. چه تکنیک‌های Refactoring می‌شناسید؟

**پاسخ**:
- Extract Method
- Extract Class
- Rename
- Move Method
- Replace Magic Number with Constant
- و بسیاری دیگر

### نکات مهم برای مصاحبه

1. **بدون تغییر رفتار**: Refactoring رفتار را تغییر نمی‌دهد
2. **با تست**: همیشه با Test Coverage انجام می‌شود
3. **تدریجی**: به صورت تدریجی انجام می‌شود
4. **مداوم**: Continuous Refactoring بهتر از Big Bang Refactoring است

---

## منابع

- **Dive into Refactoring**: https://www.youtube.com/watch?v=3ihcGo0tziA&list=PLx0fUAU-hxaH9Fo1oQhceLgaHM-nRtO9D
- **Refactoring Book** (Martin Fowler)
- **Clean Code** (Robert C. Martin)

---

## خلاصه

### Refactoring
- فرآیند اصلاح کد بدون تغییر رفتار
- هدف: کاهش Technical Debt
- با تست انجام می‌شود

### کد تمیز
- واضح و خوانا
- بدون تکرار
- حداقل پیچیدگی
- Test Coverage کامل

### Technical Debt
- بدهی فنی که باید پرداخت شود
- هر چه دیرتر، هزینه بیشتر

**موفق باشید!** 🚀

کد تمیز چه ویژگی هایی دارد:

1- کد تمیز برای بقیه برنویسان واضح است.
بحث سر الگریتم های پیچیده نیست.

2- کد تمیز هیچ قسمت تکراری ندارد:
اگر منطق تکراری در برنامه وجود داشته باشد هربا و پس از هر تغییری باید همه جا آپدیت کنیم که این بار ذهنی روی برنامه نویس رو افزایش میده و پیشرفت کد رو کند میکنه.

3- کد تمیز شامل حداقل تعداد کلاس و قسمت های متحرک است:
هر چه حجم کد کمتر باشد، چیز هایی که باید در ذهن نگه دارید کمتر میشود. هرچه کد کمتر میشود هزینه نگهداری هم کمتر است.
کد شبیه مسئولیت است، اون رو کوتاه Small و ساده Simple نگه دارید.

4- کد تمیز شامل تمام تست های برنامه را با موفقیت طی pass میکند.
اگر فقط 95 درصد تست های شما pass شده اند یعنی کد هنوز کثیف هست.
تمام قسمت های کد جدید هم باید داخل تست ها ایفای نقش کنند. Full Coverage

5- نگهداری از کد تمیز آسان تر و ارزان تر است

![[Pasted image 20240123215452.png]]

بدهی فنی چیست؟
هیچ کس عمدا کد کثیف نمیزند. کد به مرور کثیف میشود.
برای مثال شما از بانک وام میگیرید تا بتوانید چیزی را سریع تر بخرید. و در ادامه باز پرداخت وام از مبلغ اصلی بیشتر میشود. و هرچه بازپرداخت بلند مدت تر باشد بهر بانگ بیشتر میشود.

