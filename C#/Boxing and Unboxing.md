به فرایند تبدیل یک Value-Type مانند int که بصورت پیشفرض داخل Stack ذخیره میشود، به یک object که در داخل Heap ذخیره میشود، **Boxing** گفته میشود. انجام این عمل باعث allocation بر روی memory میشود که سربار زیادی دارد.

```csharp
public static void Boxing()
{
    const int number = 5;

    object boxedNumber = number;          // implicit boxing using implicit cast
    object boxedNumber = (object)number;  // explicit boxing using direct cast
}
```

در ابتدا عدد 5 روی Stack ذخیره شده بود، اما با Box کردن آن، یعنی قرار دادن مقدار آن داخل یک object، مقدار از Stack به Heap انتقال داده شده و allocation اتفاق خواهد افتاد. [[Stack & Heap]]

به عکس این عمل، یعنی تبدیل یک Reference-Type به یک Value-Type، اصطلاحا **Unboxing** گفته میشود:

```csharp
public static void Unboxing()
{
    object boxedNumber = 5;

    int number = (int)boxedNumber;
}
```

تلاش تیم‌های مایکروسافت طی سال‌های اخیر، باعث افزایش Performance فوق العاده در NET Core. و ASP.NET Core شده است. یکی از دلایل این Performance، جلوگیری بسیار زیاد از allocation در کدهای خود NET. است، که این امر به واسطه اولویت قرار دادن استفاده از Structها میسر گردیده است.

برخلاف Stack که طول عمر متغیرهای موجود در آن، در انتهای یک متد پایان می‌یابند، متغیرهای allocate شده‌ی در Heap به این شکل نبوده و در صورت حذف نکردن آنها بصورت دستی، تا پایان طول عمر اجرای برنامه داخل memory باقی خواهند ماند. اینجا، جاییست که
**[[Garbage Collector]]** در NET. وارد عمل میشود.