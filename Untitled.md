عالی! فاز 0 تمام شد.  
حالا وارد **فاز 1: ساختار Layout اصلی و مسیریابی** می‌شویم.

قدم اول از این فاز را خیلی ریز برمی‌داریم: **ساخت کامپوننت Header (فقط لوگو و یک پس‌زمینه ساده، بدون دکمه و جستجو)**.

---

## قدم 1-1: ایجاد کامپوننت Header ساده

**هدف:** یک هدر در بالای صفحه ببینیم که فقط یک لوگوی متنی دارد.  
بعداً جستجو، دکمه‌ها و چسبندگی (sticky) را اضافه می‌کنیم.

### دستورالعمل گام به گام:

1. در پوشه `src/components` یک فایل جدید بسازید به نام `Header.jsx`

2. کد زیر را در `Header.jsx` قرار دهید:

```jsx
// src/components/Header.jsx
function Header() {
  return (
    <header className="bg-white shadow-md py-4 px-6">
      <div className="container mx-auto">
        <div className="flex justify-between items-center">
          {/* لوگو */}
          <div className="text-2xl font-bold text-blue-600">
            آهن‌مارکت
          </div>
          {/* فعلاً جای خالی برای منوی دسکتاپ */}
          <div>
            {/* بعداً دکمه‌ها و سرچ اینجا می‌آیند */}
          </div>
        </div>
      </div>
    </header>
  );
}

export default Header;
```

3. حالا فایل `src/App.jsx` را باز کنید و آن را تغییر دهید تا هدر را نمایش دهد:

```jsx
// src/App.jsx
import Header from './components/Header';

function App() {
  return (
    <div className="min-h-screen bg-gray-100">
      <Header />
      <main className="container mx-auto py-8 px-4">
        <h1 className="text-3xl font-bold text-center">
          صفحه اصلی - در حال ساخت
        </h1>
        <p className="text-center text-gray-600 mt-4">
          محتوای اصلی اینجا قرار می‌گیرد.
        </p>
      </main>
    </div>
  );
}

export default App;
```

4. مطمئن شوید پروژه در حال اجراست (`npm run dev`). اگر خاموش بود دوباره اجرا کنید.

**نتیجه مطلوب:**  
در مرورگر یک هدر سفید با نوشته «آهن‌مارکت» در سمت چپ (در راستای راست برای فارسی؟ فعلاً متن انگلیسی است، بعداً راست‌چین می‌کنیم) و زیر آن متن «صفحه اصلی - در حال ساخت» ببینید.

---

## اگر خطایی دیدید

- خطای `Failed to resolve import` → یعنی مسیر `./components/Header` اشتباه است. مطمئن شوید فایل دقیقاً در `src/components/Header.jsx` است.
- صفحه سفید شد → ترمینال را ببینید، خطا را کپی کنید.

---

## بعد از موفقیت

به من بگویید: **«هدر ساده را می‌بینم»**  
و من قدم 1-2 (ساخت Footer ساده) را به شما می‌دهم.

---

**یادآوری موقعیت:**  
فاز 1 – قدم 1-1 (هدر ساده) – در انتظار تأیید شما.