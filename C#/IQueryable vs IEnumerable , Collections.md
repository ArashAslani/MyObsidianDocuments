# Collections، IEnumerable و IQueryable در C#

Collections، IEnumerable و IQueryable از مفاهیم پایه‌ای و مهم در C# هستند که در مصاحبه‌های Backend Developer بسیار پرسیده می‌شوند. درک تفاوت‌های دقیق بین این مفاهیم، Deferred Execution، Lazy Evaluation و Expression Trees برای هر توسعه‌دهنده C# ضروری است.

---

## فهرست مطالب

- [[#Collections چیست؟]]
- [[#IEnumerable و IEnumerator]]
- [[#IQueryable چیست؟]]
- [[#تفاوت‌های کلیدی IEnumerable vs IQueryable]]
- [[#Deferred Execution اجرای معوق]]
- [[#Expression Trees]]
- [[#مثال‌های عملی]]
- [[#نکات مصاحبه]]

---

## Collections چیست؟

### تعریف

**Collections (مجموعه‌ها) امکان ذخیره و مدیریت تعداد زیادی از داده‌ها را به ما می‌دهند.**

در C#، هر چیزی که بتوان روی آن `foreach` زد، یک Collection محسوب می‌شود. Collections پایه‌ای‌ترین ساختار داده در .NET هستند و برای کار با مجموعه‌ای از اشیاء استفاده می‌شوند.

### انواع Collections

```csharp
// Array - ساده‌ترین نوع Collection
int[] numbers = { 1, 2, 3, 4, 5 };

// List<T> - Generic List
List<string> names = new List<string> { "Ali", "Reza", "Sara" };

// Dictionary<TKey, TValue> - Key-Value Pairs
Dictionary<int, string> students = new Dictionary<int, string>
{
    { 1, "Ali" },
    { 2, "Reza" }
};

// HashSet<T> - مجموعه بدون تکرار
HashSet<int> uniqueNumbers = new HashSet<int> { 1, 2, 3, 2 }; // فقط {1, 2, 3}
```

### سلسله مراتب Collections در .NET

```
Object
  └── IEnumerable<T> (Base Interface)
       └── ICollection<T>
            └── IList<T>
                 └── List<T>
```

> [!important] نکته مهم
> `List<T>` از `IEnumerable<T>` **ارث‌بری نمی‌کند**، بلکه آن را **implement می‌کند**. در C#، کلاس‌ها فقط از یک کلاس می‌توانند ارث‌بری کنند، اما می‌توانند چندین Interface را implement کنند.

---

## IEnumerable و IEnumerator

### IEnumerable<T>

**`IEnumerable<T>` پایه‌ای‌ترین Interface برای iteration (تکرار) در .NET است.**

هر کلاسی که `IEnumerable<T>` را implement کند، می‌تواند در `foreach` استفاده شود و از LINQ به Objects بهره ببرد.

```csharp
public interface IEnumerable<out T> : IEnumerable
{
    IEnumerator<T> GetEnumerator();
}
```

**ویژگی‌های کلیدی**:
- **Read-Only Iteration**: فقط امکان خواندن دارد، نمی‌تواند تغییر دهد
- **Forward-Only**: فقط به جلو حرکت می‌کند
- **Lazy Evaluation**: تا زمانی که iterate نشود، اجرا نمی‌شود

### IEnumerator<T>

**`IEnumerator<T>` رابط پایه برای iteration است که توسط `IEnumerable<T>` استفاده می‌شود.**

```csharp
public interface IEnumerator<out T> : IDisposable, IEnumerator
{
    T Current { get; }
    bool MoveNext();
    void Reset();
}
```

> [!note] نکته مهم
> `IEnumerator` **جزوی از `IEnumerable` نیست**. در واقع، `IEnumerable` از `IEnumerator` استفاده می‌کند. رابطه این است:
> 
> - `IEnumerable<T>` دارای متد `GetEnumerator()` است که `IEnumerator<T>` برمی‌گرداند
> - `IEnumerator<T>` مسئول iteration واقعی است

### مثال: پیاده‌سازی Custom Collection

```csharp
public class MyCollection<T> : IEnumerable<T>
{
    private readonly T[] _items;
    
    public MyCollection(T[] items)
    {
        _items = items;
    }
    
    public IEnumerator<T> GetEnumerator()
    {
        foreach (var item in _items)
        {
            yield return item; // Yield Return برای Lazy Evaluation
        }
    }
    
    IEnumerator IEnumerable.GetEnumerator() => GetEnumerator();
}

// استفاده
var collection = new MyCollection<int>(new[] { 1, 2, 3, 4, 5 });
foreach (var item in collection)
{
    Console.WriteLine(item);
}
```

### گردش روی لیست بدون استفاده از foreach

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };

// روش 1: استفاده از IEnumerator
IEnumerator<int> enumerator = numbers.GetEnumerator();
while (enumerator.MoveNext())
{
    Console.WriteLine(enumerator.Current);
}
enumerator.Dispose();

// روش 2: استفاده از LINQ
numbers.ForEach(n => Console.WriteLine(n));

// روش 3: استفاده از Index (فقط برای IList)
for (int i = 0; i < numbers.Count; i++)
{
    Console.WriteLine(numbers[i]);
}
```

---

## IQueryable چیست؟

### تعریف

**`IQueryable<T>` از `IEnumerable<T>` ارث‌بری می‌کند و برای Query Providers (مثل LINQ to SQL، Entity Framework) طراحی شده است.**

```csharp
public interface IQueryable<out T> : IEnumerable<T>, IQueryable
{
    // ...
}
```

**ویژگی‌های کلیدی**:
- **Query Provider**: برای Query کردن منابع خارجی (Database، XML، Web Service)
- **Expression Trees**: Query را به Expression Tree تبدیل می‌کند
- **Deferred Execution**: Query تا زمانی که iterate نشود اجرا نمی‌شود
- **Translation**: Query به زبان منبع (مثل SQL) ترجمه می‌شود

### تفاوت مفهومی

**`IEnumerable`**: "من یک مجموعه در Memory هستم، روی من iterate کن"

**`IQueryable`**: "من یک Query هستم که می‌تواند به زبان دیگری (مثل SQL) ترجمه شود"

---

## تفاوت‌های کلیدی IEnumerable vs IQueryable

### جدول مقایسه

| ویژگی | IEnumerable<T> | IQueryable<T> |
|-------|----------------|---------------|
| **Namespace** | `System.Collections.Generic` | `System.Linq` |
| **Execution** | In-Memory (LINQ to Objects) | Query Provider (LINQ to SQL/EF) |
| **Deferred Execution** | ✅ دارد | ✅ دارد |
| **Expression Type** | `Func<T, bool>` (Delegate) | `Expression<Func<T, bool>>` (Expression Tree) |
| **Where Clause** | روی Memory اجرا می‌شود | به SQL/Query ترجمه می‌شود |
| **Performance** | تمام داده‌ها Load می‌شوند | فقط Query اجرا می‌شود |
| **Use Case** | Collections در Memory | Database، External Sources |

### مثال تفاوت در اجرا

```csharp
// IEnumerable - LINQ to Objects
List<Student> students = GetStudentsFromMemory(); // 1000 رکورد در Memory

var result1 = students
    .Where(s => s.Age > 18)        // روی Memory اجرا می‌شود
    .Where(s => s.Name.StartsWith("A"))
    .ToList(); // تمام 1000 رکورد فیلتر می‌شوند در Memory

// IQueryable - LINQ to SQL/EF
IQueryable<Student> studentsQuery = _context.Students; // Query، نه داده

var result2 = studentsQuery
    .Where(s => s.Age > 18)        // به SQL ترجمه می‌شود
    .Where(s => s.Name.StartsWith("A"))
    .ToList(); // فقط Query به SQL تبدیل می‌شود و در Database اجرا می‌شود
    // SQL: SELECT * FROM Students WHERE Age > 18 AND Name LIKE 'A%'
```

> [!tip] نکته مهم
> در `IQueryable`، Query تا زمانی که `ToList()`, `ToArray()`, `First()`, یا `foreach` صدا زده نشود، اجرا نمی‌شود (Deferred Execution).

---

## Deferred Execution (اجرای معوق)

### تعریف

**Deferred Execution به معنی این است که Query تا زمانی که نتیجه واقعاً نیاز نباشد، اجرا نمی‌شود.**

هر دو `IEnumerable` و `IQueryable` از Deferred Execution پشتیبانی می‌کنند، اما به روش‌های مختلف:

### IEnumerable - Deferred Execution

```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

// Query تعریف می‌شود اما اجرا نمی‌شود
var query = numbers.Where(n => n % 2 == 0);

// هنوز اجرا نشده - numbers تغییر می‌کند
numbers.Add(12);

// حالا اجرا می‌شود - نتیجه شامل 12 هم می‌شود
foreach (var num in query)
{
    Console.WriteLine(num); // 2, 4, 6, 8, 10, 12
}
```

### IQueryable - Deferred Execution

```csharp
// Query تعریف می‌شود اما به SQL ترجمه نمی‌شود
var query = _context.Students
    .Where(s => s.Age > 18)
    .OrderBy(s => s.Name);

// هنوز Query به Database ارسال نشده
// می‌توانیم Query را ادامه دهیم
var finalQuery = query
    .Where(s => s.IsActive)
    .Take(10);

// حالا Query به SQL ترجمه و اجرا می‌شود
var result = finalQuery.ToList();
// SQL: SELECT TOP 10 * FROM Students 
//      WHERE Age > 18 AND IsActive = 1 
//      ORDER BY Name
```

### Immediate Execution (اجرای فوری)

**برخی متدها Deferred Execution را می‌شکنند و Query را فوراً اجرا می‌کنند:**

```csharp
// Immediate Execution - فوراً اجرا می‌شود
var list = query.ToList();
var array = query.ToArray();
var first = query.First();
var count = query.Count();
var any = query.Any();
```

---

## Expression Trees

### تعریف

**Expression Tree نمایش درختی از کد است که می‌تواند در Runtime تجزیه و تحلیل شود.**

### تفاوت در LINQ

```csharp
// IEnumerable - Delegate (Func)
IEnumerable<Student> students = GetStudents();
var result1 = students.Where(s => s.Age > 18);
// s => s.Age > 18 یک Delegate است که به IL تبدیل می‌شود

// IQueryable - Expression Tree
IQueryable<Student> studentsQuery = _context.Students;
var result2 = studentsQuery.Where(s => s.Age > 18);
// s => s.Age > 18 یک Expression Tree است که می‌تواند به SQL ترجمه شود
```

### چرا Expression Tree؟

**Expression Tree** به Query Provider (مثل Entity Framework) اجازه می‌دهد:
1. Query را تجزیه و تحلیل کند
2. Query را به زبان منبع (SQL، XML، etc.) ترجمه کند
3. Query را بهینه‌سازی کند

```csharp
// Expression Tree می‌تواند تجزیه شود
Expression<Func<Student, bool>> expression = s => s.Age > 18;

// می‌توانیم Expression را تجزیه کنیم
var parameter = expression.Parameters[0]; // s
var body = expression.Body; // s.Age > 18
// می‌توانیم به SQL ترجمه کنیم: WHERE Age > 18
```

> [!warning] نکته مهم
> اگر از `Func` استفاده کنیم، نمی‌توانیم Query را تجزیه کنیم:

```csharp
// ❌ نمی‌تواند به SQL ترجمه شود
Func<Student, bool> predicate = s => s.Age > 18;
var result = studentsQuery.Where(predicate); // Error!

// ✅ می‌تواند به SQL ترجمه شود
Expression<Func<Student, bool>> expression = s => s.Age > 18;
var result = studentsQuery.Where(expression); // OK
```

---

## مثال‌های عملی

### مثال 1: کار با IEnumerable

```csharp
// داده‌ها در Memory هستند
List<Order> orders = GetOrdersFromMemory(); // 10000 رکورد

// فیلتر در Memory انجام می‌شود
var filteredOrders = orders
    .Where(o => o.Total > 1000)
    .Where(o => o.Status == "Completed")
    .OrderBy(o => o.Date)
    .ToList(); // تمام 10000 رکورد در Memory فیلتر می‌شوند
```

### مثال 2: کار با IQueryable

```csharp
// Query به Database
IQueryable<Order> ordersQuery = _context.Orders;

// Query به SQL ترجمه می‌شود
var filteredOrders = ordersQuery
    .Where(o => o.Total > 1000)
    .Where(o => o.Status == "Completed")
    .OrderBy(o => o.Date)
    .ToList(); // فقط Query به SQL تبدیل و در Database اجرا می‌شود

// SQL Generated:
// SELECT * FROM Orders 
// WHERE Total > 1000 AND Status = 'Completed' 
// ORDER BY Date
```

### مثال 3: تبدیل IEnumerable به IQueryable

```csharp
// ❌ نمی‌توان IEnumerable را به IQueryable تبدیل کرد
List<Student> students = GetStudents();
IQueryable<Student> query = students.AsQueryable(); // Warning: In-Memory Query

// این کار Query را در Memory اجرا می‌کند، نه در Database
var result = query.Where(s => s.Age > 18).ToList();
```

### مثال 4: تبدیل IQueryable به IEnumerable

```csharp
// ✅ می‌توان IQueryable را به IEnumerable تبدیل کرد
IQueryable<Student> query = _context.Students;
IEnumerable<Student> enumerable = query.AsEnumerable();

// حالا LINQ to Objects اجرا می‌شود، نه LINQ to SQL
var result = enumerable.Where(s => s.Age > 18).ToList();
// تمام داده‌ها از Database Load می‌شوند، سپس در Memory فیلتر می‌شوند
```

---

## نکات مصاحبه

### سوالات رایج

#### 1. تفاوت IEnumerable و IQueryable چیست؟

**پاسخ**:
- **IEnumerable**: برای Collections در Memory (LINQ to Objects)
- **IQueryable**: برای Query Providers (LINQ to SQL/EF)
- **IEnumerable**: از `Func<T, bool>` استفاده می‌کند
- **IQueryable**: از `Expression<Func<T, bool>>` استفاده می‌کند
- **IQueryable**: Query را به زبان منبع (SQL) ترجمه می‌کند

#### 2. Deferred Execution چیست؟

**پاسخ**:
- Query تا زمانی که نتیجه نیاز نباشد اجرا نمی‌شود
- هر دو `IEnumerable` و `IQueryable` از Deferred Execution پشتیبانی می‌کنند
- متدهایی مثل `ToList()`, `ToArray()`, `First()` Deferred Execution را می‌شکنند

#### 3. Expression Tree چیست و چرا مهم است؟

**پاسخ**:
- Expression Tree نمایش درختی از کد است
- به Query Provider اجازه می‌دهد Query را تجزیه و به SQL ترجمه کند
- `IQueryable` از Expression Tree استفاده می‌کند
- `IEnumerable` از Delegate استفاده می‌کند

#### 4. چه زمانی از IEnumerable استفاده کنیم و چه زمانی از IQueryable؟

**پاسخ**:
- **IEnumerable**: وقتی داده‌ها در Memory هستند
- **IQueryable**: وقتی با Database یا External Source کار می‌کنیم
- **IQueryable**: برای بهینه‌سازی Query و کاهش Load از Database

#### 5. مشکل تبدیل IEnumerable به IQueryable چیست؟

**پاسخ**:
- `AsQueryable()` روی IEnumerable، Query را در Memory اجرا می‌کند
- نمی‌تواند به SQL ترجمه شود
- Performance بدتر می‌شود چون تمام داده‌ها Load می‌شوند

### نکات مهم برای مصاحبه

1. **همیشه بگویید**: "IQueryable برای Query Providers است، نه فقط Database"
2. **Deferred Execution**: همیشه ذکر کنید که Query تا iterate شدن اجرا نمی‌شود
3. **Expression Tree**: تفاوت بین `Func` و `Expression<Func>` را توضیح دهید
4. **Performance**: IQueryable برای بهینه‌سازی Query در Database است
5. **مثال بزنید**: همیشه با مثال SQL توضیح دهید

### Common Mistakes & Red Flags

> [!error] اشتباه
> "IQueryable فقط برای Database است"
> 
> [!success] درست
> "IQueryable برای هر Query Provider است (Database، XML، Web Service)"

> [!error] اشتباه
> "IEnumerable از IEnumerator ارث‌بری می‌کند"
> 
> [!success] درست
> "IEnumerable از IEnumerator استفاده می‌کند"

> [!error] اشتباه
> "List از IEnumerable ارث‌بری می‌کند"
> 
> [!success] درست
> "List، IEnumerable را implement می‌کند"

> [!error] اشتباه
> استفاده از `AsQueryable()` روی List برای بهینه‌سازی
> 
> [!success] درست
> `AsQueryable()` فقط Interface را تغییر می‌دهد، Query را در Memory اجرا می‌کند

---

## خلاصه

### مفاهیم کلیدی

- **Collections**: ساختارهای داده برای نگهداری مجموعه‌ای از اشیاء
- **IEnumerable**: Interface پایه برای iteration در Memory
- **IQueryable**: Interface برای Query Providers با Expression Trees
- **Deferred Execution**: Query تا iterate شدن اجرا نمی‌شود
- **Expression Tree**: نمایش درختی Query که می‌تواند به SQL ترجمه شود

### Best Practices

- از `IQueryable` برای Database Queries استفاده کنید
- از `IEnumerable` برای In-Memory Collections استفاده کنید
- از Deferred Execution برای بهینه‌سازی استفاده کنید
- از `ToList()` یا `ToArray()` برای Immediate Execution استفاده کنید
- از Expression Tree برای Query Translation استفاده کنید

**موفق باشید!** 🚀
