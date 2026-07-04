# DbContext و مدل‌سازی رابطه در EF Core

## 1. Interview Relevance Summary

این فایل **مفاهیم مدل و رابطه در EF Core** رو پوشش می‌ده: Principal/Depended Entity، Navigation Property، کلید خارجی، **Conventional** در برابر **Fluent API**، رفتار حذف (OnDelete)، Owned Type و استراتژی‌های ارث‌بری (TPH, TPT) و Table Splitting. برای مصاحبهٔ Backend که با EF Core کار می‌کنن مهمه بدونن چطور رابطهٔ یک‑به‑یک و یک‑به‑چند و چند‑به‑چند تعریف می‌شه و رفتار Cascade/SetNull چیه. برای دیتابیس و طراحی جدول به **[[Database]]** مراجعه کن.

---

## 2. Principal Key و Alternate Key

- **Principal Key**: کلیدی که طرف **والد** در رابطهٔ پدر–فرزند با آن شناخته می‌شه. معمولاً همان **Primary Key** جدول والد است؛ می‌تونه در موارد خاص **Alternate Key** هم باشه (مثلاً برای User به‌جای Id از کد ملی به‌عنوان Principal Key استفاده کنیم).
- **Alternate Key**: کلیدی که همهٔ ویژگی‌های کلید اصلی رو داره (یکتا و معتبر) ولی **کلید اصلی جدول نیست**.

---

## 3. Principal Entity و Dependent Entity

- **Principal Entity**: در رابطهٔ پدر–فرزند، طرف **مالک** رابطه (والد).
- **Dependent Entity**: طرف **وابسته** (فرزند) که کلید خارجی داره.

---

## 4. Navigation Property و Foreign Key

**Navigation Property** پراپرتیی است که در یک entity به entity دیگر (یا به مجموعهٔ فرزندان) اشاره می‌کنه. در **والد** معمولاً یک **Collection** از فرزندان؛ در **فرزند** یک پراپرتی به والد (با مقدار Principal Key). بودن یا نبودن Navigation بسته به **نیاز بیزنس** است: مثلاً Course باید لیست Commentها رو داشته باشه برای گزارش؛ در عوض Comment لزوماً به Course نیاز نداره. برای رابطهٔ **چند‑به‑چند** معمولاً هر دو طرف Navigation دارن؛ برای **یک‑به‑یک** هر دو طرف باید داشته باشن تا **Conventional** درست کار کنه، وگرنه با **Configuration** رابطه رو تعریف می‌کنی.

**Foreign Key**: پراپرتی در طرف فرزند که مقدار **Principal Key** والد رو نگه می‌داره.

- **Required Relationship**: ارتباط اجباری — Navigation در طرف فرزند **nullable نیست**.
- **Optional Relationship**: ارتباط اختیاری — Navigation در طرف فرزند **nullable** است.

اگه خودت FK رو به‌صورت پراپرتی تعریف نکنی، EF Core می‌تونه به‌صورت **Shadow Property** یک FK با نامی مثل `ParentEntityId` اضافه کنه و بر اساس Nullability همین Navigation، رابطه رو Required یا Optional در نظر بگیره.

---

## 5. برقراری ارتباط: Conventional و یافتن FK

تنظیم رابطه‌ها در EF Core به‌صورت **Conventional** انجام می‌شه و در خیلی موارد به **Configuration** نیاز نیست. EF از **DbSet**ها entityها رو پیدا می‌کنه، روی Navigationها لوپ می‌زنه و کلیدها رو تشخیص می‌ده. برای پیدا کردن **کلید خارجی**: نوع پراپرتی FK باید با **نوع Principal Key** والد یکی باشه و نام هم مناسب باشه (مثلاً `ParentId`). با **HasPrincipalKey** می‌تونی مشخص کنی فرزند به **Alternate Key** والد وابسته است (در این حالت آن Principal Key قابل تغییر نیست).

**FK Nullability**: اگه رابطه **Required** باشه، پیش‌فرض **OnDelete Cascade** است؛ اگه **Optional** باشه، **ClientSetNull** (در حافظه فرزند را null می‌کنه؛ در دیتابیس بسته به تنظیم دیتابیس).

---

## 6. Data Annotations و Fluent API

می‌تونی رابطه و FK را با **Data Annotations** (مثل `[ForeignKey]`, `[InverseProperty]`) یا با **Fluent API** تعریف کنی. وقتی **چند رفرنس** از یک والد به نوع فرزند داری (مثلاً دو Collection در والد و دو پراپرتی والد در فرزند)، باید با **InverseProperty** یا Fluent مشخص کنی کدوم به کدوم وصل است.

**Fluent API** برای رابطهٔ **یک‑به‑یک**: `HasOne`, `WithOne`, `HasForeignKey`. برای **یک‑به‑چند**: `HasOne`/`WithMany` یا `HasMany`/`WithOne` و `HasForeignKey`. برای **چند‑به‑چند**: با یک **جدول واسط (Join Table)** و دو رابطهٔ یک‑به‑چند از دو طرف.

نکته: اگه به **ترتیب** فرزندان نیاز داری، از `List<>` استفاده کن؛ اگه فقط Eager Loading بدون ترتیب مهمه و OrderBy نمی‌زنی، `HashSet<>` می‌تونه از نظر عملکرد بهتر باشه. برای مرتب‌سازی از مجموعه‌ای که ترتیب نگه می‌داره استفاده کن.

---

## 7. DeleteBehavior (OnDelete)

- **Restrict (No Action)**: وقتی والد حذف می‌شه، اگه فرزند داشته باشه اجازهٔ حذف نمی‌ده.
- **SetNull**: در رابطهٔ **اختیاری**؛ با حذف والد، **کلید خارجی در فرزند null** می‌شه (ستون FK باید nullable باشه).
- **Cascade**: حذف **آبشاری** — با حذف والد، فرزندان هم حذف می‌شن.
- **ClientSetNull**: در حافظه فرزند لود‌شده را null می‌کنه؛ برای فرزند لود‌نشده دیتابیس طبق تنظیمات خودش عمل می‌کنه.
- **ClientCascade**: مشابه ClientSetNull ولی با رفتار شبیه Cascade در سمت client.

با **HasConstraintName** می‌تونی نام constraint کلید خارجی را در دیتابیس عوض کنی تا خطاهای واضح‌تری بگیری.

---

## 8. Owned Entity Types

وقتی می‌خوای **بخشی از یک entity** را جدا و گروه‌بندی کنی (مثل Value Object) بدون اینکه جدول جدا بسازی، از **Owned Type** استفاده می‌کنی. به‌جای ذخیرهٔ چند فیلد به‌صورت مسطح یا JSON، آن بخش را در یک نوع «مالک‌شده» تعریف می‌کنی.

- **OwnedOne**: یک بخش تک‌شیء (مثلاً آدرس).
- **OwnedMany**: یک مجموعه از اشیاء مالک‌شده.

Owned typeها همراه با entity اصلی واکشی می‌شن و در همان جدول (یا جدول مرتبط طبق تنظیم) ذخیره می‌شن.

---

## 9. استراتژی‌های ارث‌بری (Inheritance)

### Table per Hierarchy (TPH)

همهٔ انواع ارث‌بری در **یک جدول** با یک ستون **Discriminator** که نوع رکورد (کدام کلاس فرزند) را مشخص می‌کنه. **پیش‌فرض** EF Core است. وقتی کلاس‌های فرزند فیلدهای خیلی متفاوت دارن، جدول **sparse** (ستون‌های زیاد با مقدار null) می‌شه. نام و مقدار Discriminator را می‌شه تنظیم کرد.

### Table per Type (TPT)

برای هر **کلاس** (والد و هر فرزند) یک **جدول** جدا؛ فیلدهای مشترک در جدول والد و فیلدهای خاص در جدول هر فرزند. واکشی با **JOIN** انجام می‌شه. جداول مطابق سلسله‌مراتب کلاس‌های اپلیکیشن هستن.

![[Pasted image 20240512203559.png]]

---

## 10. Table Splitting

وقتی **یک جدول** را به **چند کلاس entity** مپ می‌کنی (هر دو به همان جدول اشاره می‌کنن)، به آن **Table Splitting** می‌گیم. معمولاً برای این استفاده می‌شه که بخشی از ستون‌ها در یک entity و بخش دیگر در entity دوم باشه تا در کوئری فقط همان بخشی که لازم است واکشی بشه (مثلاً برای لیست خبر فقط عنوان و خلاصه، بدون بدنه). هر دو کلاس می‌تونن Id داشته باشن و پراپرتی از نوع entity دوم در entity اصلی قرار بگیره؛ هر دو به **همان جدول** مپ می‌شن.

![[Pasted image 20240512204418.png]]

---

## 11. Key Interview Talking Points

- **Principal/Dependent** و **Navigation Property**؛ Required vs Optional با nullable بودن Navigation و FK.
- **Conventional** برای تشخیص رابطه و FK؛ **Fluent API** برای کنترل دقیق و نام constraint.
- **OnDelete**: Restrict، SetNull (FK در فرزند null)، Cascade (حذف آبشاری)، ClientSetNull/ClientCascade.
- **Owned Type** برای گروه‌بندی بخشی از entity بدون جدول جدا.
- **TPH** (یک جدول + Discriminator) در برابر **TPT** (یک جدول per نوع)؛ **Table Splitting** (یک جدول، چند entity).

---

## 12. Common Mistakes & Red Flags

- اشتباه گرفتن **SetNull** با **Cascade**: SetNull یعنی FK در فرزند null می‌شه، نه حذف فرزند.
- تعریف نکردن **InverseProperty** یا Fluent وقتی چند Navigation به یک نوع داریم → رابطهٔ اشتباه.
- استفادهٔ **HashSet** برای مجموعه‌ای که ترتیبش مهمه (مثلاً با OrderBy).

---

این فایل **مکمل** **[[Database]]** و **[[Entity Framework]]** است. برای کوئری و دیتابیس به فایل‌های پوشهٔ Database مراجعه کن.
