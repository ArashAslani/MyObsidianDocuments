# Message Queue Internals: زیر پوستِ RabbitMQ و Kafka

## چرا این فایل؟

تا الان از RabbitMQ به‌عنوان یه جعبهٔ سیاه استفاده کردیم: پیام می‌فرستیم، صف می‌گیره، Consumer می‌خونه. این فایل می‌ره **زیر پوست** این ابزارها: چطور یه Exchange واقعاً روتینگ رو انجام می‌ده، چرا Kafka اینقدر سریعه، replication بین بروکرها دقیقاً چطور کار می‌کنه، و چرا Consumer Group Rebalancing می‌تونه کل یه سیستم تولید رو برای چند دقیقه فلج کنه. فهمیدن این internals دقیقاً همون چیزیه که جواب «چرا» رو به جواب «چطور» تبدیل می‌کنه — و تو مصاحبه، سؤال‌های سطح بالا معمولاً دقیقاً همینجاها متمرکزن.

---

## بخش اول: RabbitMQ Internals

### 1.1. AMQP: چرا Exchange و Queue دو مفهوم جدا هستن

خیلی از توسعه‌دهنده‌ها فکر می‌کنن پیام مستقیم «به یه صف» فرستاده می‌شه. واقعیت اینه: پیام همیشه اول به یه **Exchange** می‌ره، و این Exchange بر اساس نوعش و یه **Binding** (قانون اتصال بین Exchange و Queue)، تصمیم می‌گیره پیام به کدوم صف(ها) کپی بشه.

| نوع Exchange | نحوهٔ روتینگ | کاربرد |
|---|---|---|
| **Direct** | پیام به صف‌هایی می‌ره که Routing Key‌شون **دقیقاً** با Routing Key پیام یکی باشه | مسیریابی ساده (مثلاً بر اساس نوع رویداد) |
| **Topic** | تطبیق الگو با wildcard (`*` یه کلمه، `#` صفر یا چند کلمه) | مثلاً `order.created.*` به همهٔ زیرشاخه‌ها |
| **Fanout** | پیام به **همهٔ** صف‌های متصل کپی می‌شه، Routing Key اصلاً چک نمی‌شه | Broadcast (مثلاً اطلاع‌رسانی به چند سرویس مستقل هم‌زمان) |
| **Headers** | تطبیق بر اساس header پیام به‌جای Routing Key | سناریوهای پیچیده‌تر روتینگ |

**نکتهٔ مهم که خیلی‌ها نمی‌دونن**: وقتی می‌گی `channel.QueueDeclare()` یه صف پیش‌فرض به‌نام `""` (Default Exchange) وجود داره که هر صف رو خودکار با یه Binding مستقیم (اسم صف = Routing Key) بهش وصل می‌کنه — به همین خاطر خیلی از مثال‌های ساده انگار مستقیم به صف می‌فرستن، ولی در واقع از Default Exchange رد می‌شن.

### 1.2. Connection در برابر Channel: چرا نباید برای هر عملیات یه Connection جدید باز کنی

یه **Connection** یه TCP Socket واقعیه بین کلاینت و بروکر — ساختنش گرون است (TLS Handshake، احراز هویت). یه **Channel** یه «کانال مجازی» داخل همون Connection هست که سبک‌تره و برای انجام عملیات‌های واقعی (publish، consume، ack) استفاده می‌شه.

**اشتباه رایج**: باز کردن یه Connection جدید برای هر Request HTTP که می‌خواد یه پیام بفرسته. این هم throughput رو به‌شدت پایین میاره (چون هر بار TCP Handshake) و هم می‌تونه به‌سرعت به سقف تعداد Connection مجاز بروکر برخورد کنه. **راه‌حل درست**: یه Connection واحد (یا یه Connection Pool کوچیک) در طول عمر اپلیکیشن نگه‌داری بشه، و برای هر Thread/Task از یه Channel جدا (که سبکه) استفاده بشه — چون Channelها thread-safe نیستن ولی Connection معمولاً هست.

### 1.3. Publisher Confirms و Consumer Ack: دو مکانیزم متفاوت که با هم اشتباه گرفته می‌شن

- **Consumer Ack** (که قبلاً دیدیم): Consumer به بروکر می‌گه «این پیام رو کامل پردازش کردم، حذفش کن».
- **Publisher Confirm**: این یکی برعکسه — **بروکر** به Publisher می‌گه «پیامت رو با موفقیت گرفتم و (اگه durable باشه) روی دیسک نوشتم».

بدون Publisher Confirm، وقتی `channel.BasicPublish()` رو صدا می‌زنی، هیچ تضمینی نداری که پیام واقعاً به بروکر رسیده — چون AMQP روی TCP هست و TCP هم می‌تونه بی‌صدا fail کنه (مثلاً بستهٔ آخر گم بشه). برای پیام‌های واقعاً حیاتی، باید صریحاً منتظر Confirm بمونی:

```csharp
channel.ConfirmSelect(); // فعال‌سازی Publisher Confirms روی این channel
channel.BasicPublish(exchange: "orders", routingKey: "created", body: messageBody);
var confirmed = channel.WaitForConfirms(TimeSpan.FromSeconds(5));
if (!confirmed)
{
    // پیام تأیید نشد؛ retry یا هشدار بده
}
```

### 1.4. Flow Control: وقتی بروکر خودش Publisherها رو بلاک می‌کنه

یکی از رفتارهای internal که خیلی‌ها موقع دیباگ یه «کندی عجیب» باهاش برخورد می‌کنن: RabbitMQ یه آستانهٔ حافظه (`vm_memory_high_watermark`، پیش‌فرض ۴۰٪ از حافظهٔ کل سیستم) و یه آستانهٔ فضای دیسک (`disk_free_limit`) داره. وقتی از این آستانه‌ها رد بشه، بروکر وارد حالت **Flow Control** می‌شه: به‌جای رد کردن پیام یا crash کردن، به‌سادگی **دیگه به Publisherها TCP ACK نمی‌ده** — یعنی از دید اپلیکیشن، `BasicPublish` (یا معادل async‌ش) به‌طرز عجیبی کند یا معلق می‌مونه، بدون هیچ Exception واضحی.

**درسی که می‌گیریم**: اگه یه سیستم Producer-Consumer ناگهان بدون خطای مشخص کند بشه، همیشه اول متریک‌های حافظه و دیسک خود بروکر RabbitMQ رو چک کن، نه فقط کد اپلیکیشن — این دقیقاً یکی از اون «باگ‌های نامرئی» هست که فقط زیر بار واقعی خودش رو نشون می‌ده.

### 1.5. Classic Mirrored Queues در برابر Quorum Queues: چرا RabbitMQ کل مدل Replication‌ش رو عوض کرد

این شاید مهم‌ترین internal‌ای باشه که باید بدونی، و مستقیماً به بحث **Split-Brain** که قبلاً مفصل توضیح دادیم وصل می‌شه.

**مدل قدیمی (Classic Mirrored Queues)**: یه صف روی یه node اصلی (master) بود و چند node دیگه یه «آینه» (mirror) ازش نگه می‌داشتن، با یه پروتکل replication به‌نام Guaranteed Multicast (یه نسخهٔ سفارشی از Paxos).

**مشکل مستند و رسمی**: طبق مستندات و مقالات فنی خود تیم RabbitMQ و شرکای رسمی‌شون (CloudAMQP)، وقتی یه Network Partition بین nodeها اتفاق می‌افتاد، Mirrored Queues می‌تونستن وارد حالت **Split-Brain** بشن: nodeهایی که از master جدا شدن، یه master جدید برای خودشون انتخاب می‌کردن، و حالا کلاستر **دو تا master** داشت که هر دو هم‌زمان read/write قبول می‌کردن — دقیقاً همون الگوی Split-Brain که در فایل قبلی (با نمونهٔ GitHub) دیدیم. نکتهٔ مهم دیگه: توی RabbitMQ، حتی یه «خوندن» ساده هم یه نوع «نوشتن» محسوب می‌شه (چون پیام رو از صف حذف می‌کنه)، پس این ریسک حتی برای عملیات خوندن هم وجود داشت. نتیجهٔ رسمی این بود که Classic Mirrored Queues حتی در ساده‌ترین تست‌های Jepsen (همون ابزار معروفی که در فایل قبلی دیدیم) **رد می‌شدن**.

**راه‌حل: Quorum Queues (از نسخهٔ ۳.۸ به بعد)**: RabbitMQ کل مدل replication رو عوض کرد و از الگوریتم **Raft** (همون الگوریتم Consensus که در بخش Split-Brain توضیح دادیم) استفاده کرد. حالا هر صف یه leader و چند follower داره؛ هر نوشته باید توسط **اکثریت (quorum)** replica‌ها تأیید بشه قبل از این‌که به Client گفته بشه «موفق بود». چون نوشتن نیازمند اکثریته، اگه یه Partition اتفاق بیفته، طرف اقلیت اصلاً نمی‌تونه یه leader جدید انتخاب کنه و می‌ایسته — دقیقاً به‌جای این‌که به اشتباه ادامه بده و داده رو خراب کنه. طبق گزارش‌های رسمی، Quorum Queues نسخهٔ سخت‌گیرانه‌تری از تست Jepsen رو با موفقیت پاس کردن، درحالی‌که Classic Mirrored Queues حتی از نسخهٔ ساده‌ترش هم رد می‌شدن. این موفقیت اونقدر واضح بود که Classic Mirrored Queues از نسخهٔ ۳.۹ منسوخ (deprecated) و در نسخهٔ ۴.۰ به‌طور کامل **حذف** شدن.

**درسی که می‌گیریم برای دفاع از عنوانت**: این یه مثال عالیه از این‌که چطور یه مشکل تئوریک (Split-Brain که در فایل قبلی خوندی) دقیقاً باعث شده یکی از پرکاربردترین Message Brokerهای دنیا، کل معماری replication خودش رو از پایه بازطراحی کنه. اگه بتونی این ارتباط رو تو مصاحبه نشون بدی، یعنی داری تئوری رو با تصمیم‌های واقعی مهندسی وصل می‌کنی.

---

## بخش دوم: Kafka Internals

### 2.1. چرا Kafka اینقدر سریعه: Log-Structured Storage و Zero-Copy

برخلاف RabbitMQ که پیام رو بعد از مصرف شدن حذف می‌کنه، Kafka هر Partition رو به‌صورت یه **Append-Only Log** روی دیسک نگه می‌داره — پیام‌های جدید همیشه به **انتهای** فایل اضافه می‌شن (Sequential Write)، نه این‌که در جای دلخواه دیسک نوشته بشن (Random Write). نوشتن Sequential روی دیسک‌های معمولی (حتی HDD) می‌تونه به‌اندازهٔ نوشتن Random روی SSD سریع باشه، چون دیسک مجبور نیست هد خودش رو مدام جابه‌جا کنه.

**Zero-Copy**: وقتی یه Consumer داده می‌خواد، Kafka از یه system call به‌نام `sendfile` استفاده می‌کنه که داده رو مستقیماً از **Page Cache سیستم‌عامل** به Network Socket منتقل می‌کنه، بدون این‌که از دیسک به فضای حافظهٔ خود پروسهٔ Kafka کپی بشه. این یعنی Kafka عمداً **به GC خود JVM تکیه نمی‌کنه** برای caching — به‌جاش می‌ذاره سیستم‌عامل (که برای این کار خیلی بهینه‌تره) این کار رو انجام بده.

**درسی که می‌گیریم**: این دقیقاً همون فلسفهٔ «سبک نگه داشتن حافظهٔ اپلیکیشن و تکیه به سیستم‌عامل» هست که در بحث GC/OOMKilled در فایل قبلی هم بهش اشاره کردیم — فقط اینجا Kafka عمداً طراحی شده که ازش سود ببره.

### 2.2. Partition، Leader، و ISR: مکانیزم واقعی replication در Kafka

هر Topic به چند **Partition** تقسیم می‌شه؛ هر Partition چند **Replica** داره که روی Brokerهای مختلف پخش می‌شن. یکی از این Replicaها **Leader** است (همهٔ خواندن/نوشتن از اینجا رد می‌شه)، بقیه **Follower** هستن که مدام از Leader داده می‌کشن (fetch می‌کنن).

**ISR (In-Sync Replicas)**: مجموعه‌ای از Followerهایی که به‌اندازهٔ کافی با Leader هماهنگ (sync) هستن. وقتی یه Producer با تنظیم `acks=all` پیام می‌فرسته، پیام فقط زمانی «موفق» اعلام می‌شه که **همهٔ اعضای فعلی ISR** (نه لزوماً همهٔ Replicaهای تعریف‌شده) اون رو دریافت کرده باشن.

### 2.3. اتفاق مستند: کوچک شدن ISR در یه Network Brownout

یه سناریوی مستند در تحلیل‌های تولیدی Kafka این‌طور توصیف شده: طی یه افت موقتی کیفیت شبکه (network brownout)، Followerها حدود ۵ ثانیه از Leader عقب افتادن. در نتیجه، ISR روی حدود ۲۰٪ از Partitionها به فقط ۱ عضو (خود Leader) کاهش پیدا کرد. چون تنظیم `acks=all` بود، نوشتن‌ها منتظر تأیید اکثریت ISR موندن و درخواست‌های ثبت سفارش با Timeout مواجه شدن. راه‌حل اضطراری این بود که با استفاده از Dynamic Quota، نرخ ارسال Producerها رو ۵۰٪ محدود کردن تا Followerها فرصت کنن ظرف حدود ۹۰ ثانیه به Leader برسن و ISR دوباره سالم بشه.

**نکتهٔ کلیدی**: این دقیقاً همون **trade-off بین Consistency و Availability** هست که در بحث CAP Theorem دیدیم — `acks=all` + `min.insync.replicas` بالا یعنی صریحاً Consistency رو بیشتر از Availability اولویت دادی؛ وقتی شبکه مشکل پیدا کنه، سیستم به‌جای پذیرفتن ریسک از‌دست‌رفتن داده، ترجیح می‌ده کند بشه یا اصلاً ننویسه.

### 2.4. Consumer Group Rebalancing: «دکمهٔ Emergency Stop» که خیلی‌ها بی‌دلیل فشارش می‌دن

وقتی یه Consumer به یه Group می‌پیوندن یا ازش خارج می‌شن (چه با crash چه با یه دیپلوی معمولی)، Kafka باید Partitionها رو دوباره بین اعضای باقی‌مونده تقسیم کنه — این فرآیند **Rebalancing** نام داره.

**مدل قدیمی (Eager Rebalancing)**: کل Group متوقف می‌شه («Stop the World»)، همهٔ Partitionها آزاد می‌شن، و بعد دوباره از صفر تخصیص داده می‌شن — حتی برای Consumerهایی که اصلاً تغییری نکردن.

**مدل جدید (Cooperative/Incremental Rebalancing)**: فقط Partitionهایی که واقعاً باید جابه‌جا بشن آزاد می‌شن؛ بقیهٔ Consumerها بدون وقفه به کارشون ادامه می‌دن.

### 2.5. اتفاق مستند: یه Rolling Restart که به یه قطعی ۴۵ دقیقه‌ای تبدیل شد

یه گزارش تحلیلی از یه تیم مهندسی، یه سناریوی کاملاً واقعی و آموزنده رو مستند کرده: یه تیم با یه Consumer Group شامل ۱۰۰ instance، یه Rolling Deployment معمولی انجام داد. تنظیمات Kubernetes به‌گونه‌ای بود که تا **۲۵ Pod هم‌زمان** می‌تونستن ری‌استارت بشن (`maxUnavailable` بیش از حد بالا). هر موج از ری‌استارت یه Rebalance ایجاد می‌کرد، ولی قبل از این‌که Rebalance قبلی کامل تموم بشه، موج بعدی هم شروع می‌شد — نتیجه یه **حلقهٔ بازخورد (Feedback Loop)** بود که هیچ‌وقت به حالت پایدار نمی‌رسید و کل پردازش برای **۴۵ دقیقه** کاملاً متوقف شد، تا وقتی که خود Deployment رو کاملاً متوقف کردن و اجازه دادن سیستم آرام بگیره.

یه نمونهٔ دیگهٔ مشابه و دقیق‌تر: یه Rolling Restart با موج‌های ۸تایی از یه Group ۲۴۰ نفره، با فاصلهٔ ۳۰ ثانیه بین موج‌ها طراحی شده بود. مشکل این بود که Consumerهای تازه‌راه‌اندازی‌شده، به‌طور میانگین ۳۸ ثانیه طول می‌کشید تا اولین Heartbeat‌شون رو بفرستن (به‌خاطر JVM warmup، اتصال به Schema Registry، و بارگذاری state محلی) — درحالی‌که `session.timeout.ms` روی ۳۰ ثانیه تنظیم شده بود. یعنی هر Consumer تازه، قبل از این‌که حتی بتونه اولین Heartbeat رو بفرسته، به‌عنوان «مرده» از Group حذف می‌شد و یه Rebalance جدید راه می‌افتاد. این حلقه ۹ دقیقهٔ کامل ادامه پیدا کرد و throughput به صفر رسید.

**درسی که می‌گیریم**:
- بین `session.timeout.ms` و زمان واقعی startup اپلیکیشن (شامل JVM/CLR warmup، اتصال به وابستگی‌ها) باید یه حاشیهٔ امن واقعی وجود داشته باشه، نه فقط مقدار پیش‌فرض کتابخانه.
- توی Kubernetes، `maxUnavailable` یه Consumer Deployment باید طوری تنظیم بشه که **موج‌های ری‌استارت به‌اندازهٔ کافی فاصله داشته باشن** تا Rebalance قبلی کاملاً تثبیت بشه قبل از شروع بعدی.
- استفاده از **Static Membership** (`group.instance.id` ثابت برای هر Consumer) به Kafka می‌گه «این یه Consumer قدیمیه که موقتاً غیب شده، نه یه عضو جدید» — و در نتیجه یه ری‌استارت معمولی، اصلاً باعث Rebalance نمی‌شه (فقط اگه ظرف یه بازهٔ زمانی مشخص برگرده).
- **Cooperative Sticky Assignor** به‌جای Assignor پیش‌فرض، دامنهٔ آسیب هر Rebalance رو محدود می‌کنه.

```java
// تنظیمات پیشنهادی برای کاهش ریسک Rebalance Storm
props.put("partition.assignment.strategy", "org.apache.kafka.clients.consumer.CooperativeStickyAssignor");
props.put("group.instance.id", Environment.MachineName); // Static Membership
props.put("session.timeout.ms", 45000); // بیشتر از زمان واقعی startup
props.put("max.poll.interval.ms", 300000); // برای کارهای پردازشی طولانی
```

### 2.6. `__consumer_offsets`: چرا مشکل Rebalancing می‌تونه کل بروکر رو تحت فشار بذاره

Kafka آفست‌های هر Consumer Group رو در یه Topic داخلی به‌نام `__consumer_offsets` نگه می‌داره. وقتی یه Rebalance Storm اتفاق می‌افته، هر Rebalance یعنی نوشتن‌های زیاد و متوالی به همین Topic. در یه گزارش مستند، یه تیم متوجه شد که CPU و ترافیک شبکهٔ یه Broker خاص (که Leader Partitionهای `__consumer_offsets` روش بود) به‌طرز غیرعادی بالا رفته؛ ریشهٔ مشکل، چند Consumer Group داخلی مربوط به **Kafka Connect** بودن که به‌طور مداوم flap می‌کردن (join/leave می‌شدن) و هر بار یه سیل نوشتن به این Topic داخلی ایجاد می‌کردن — درحالی‌که این Consumer Groupها با ابزارهای مانیتورینگ معمولی اصلاً دیده نمی‌شدن، چون Kafka Connect گروه‌های داخلی خودش رو مخفی نگه می‌داره مگر این‌که صریحاً درخواستشون کنی.

**درسی که می‌گیریم**: مشکلات Kafka همیشه از جایی که انتظارش رو داری نمیان — گاهی «کندی کل بروکر» ریشه‌ش نه در Producer/Consumer اصلی، بلکه در یه Consumer Group داخلی و نامرئی (مثل Kafka Connect) است که باید صریحاً باهاش مانیتور بشه.

### 2.7. چطور Kafka به «Exactly-Once» نزدیک می‌شه (نه این‌که واقعاً تضمینش کنه)

طبق قضیهٔ Two Generals Problem که در فایل قبلی دیدیم، Exactly-Once واقعی بین دو سیستم مستقل از نظر تئوری غیرممکنه. Kafka دو مکانیزم داره که این رو **در سطح عملی** (نه تئوریک مطلق) به‌شدت بهش نزدیک می‌کنه:

- **Idempotent Producer**: هر Producer یه شناسهٔ منحصربه‌فرد (PID) و یه شمارندهٔ ترتیبی برای هر Partition داره. اگه به‌خاطر یه Timeout، همون پیام دوباره retry بشه، بروکر بر اساس PID و شماره ترتیب تشخیص می‌ده که این پیام تکراریه و دوباره ذخیره‌ش نمی‌کنه.
- **Transactional Producer**: چند نوشته به چند Partition مختلف (یا حتی خوندن از یه Topic و نوشتن به Topic دیگه، الگویی به‌نام "Read-Process-Write") رو می‌تونی توی یه تراکنش atomic بپیچی — یا همه‌ش commit می‌شه یا هیچ‌کدوم دیده نمی‌شه (با فیلتر کردن پیام‌های commit‌نشده در سمت Consumer، اگه `isolation.level=read_committed` تنظیم شده باشه).

**نکتهٔ صادقانه**: این‌ها Exactly-Once رو **بین Kafka خودش** تضمین می‌کنن (producer→broker، یا broker→broker در یه تراکنش). به محض این‌که یه سیستم بیرونی (مثل یه دیتابیس یا یه API خارجی) وارد ماجرا بشه، دوباره به همون بحث **Idempotent Consumer** و **Outbox Pattern** برمی‌گردیم که قبلاً مفصل توضیح دادیم — چون Kafka نمی‌تونه exactly-once رو به یه سیستم خارج از خودش تعمیم بده.

---

## جمع‌بندی: جدول کامل Internals برای مرور سریع

| مفهوم | چیه؟ | اتفاق واقعی/ریسک مستند | راه‌حل |
|---|---|---|---|
| AMQP Exchange | روتینگ پیام قبل از رسیدن به صف | استفادهٔ اشتباه از Exchange نوع اشتباه → پیام به صف اشتباه یا اصلاً نمی‌رسه | انتخاب درست Direct/Topic/Fanout/Headers |
| Connection vs Channel | Connection سنگین، Channel سبک | باز کردن Connection به‌ازای هر Request → throughput پایین و سقف اتصال بروکر | یه Connection طولانی‌عمر + Channel به‌ازای هر Thread |
| Publisher Confirm | تأیید بروکر که پیام واقعاً رسید | بدون Confirm، پیام می‌تونه بی‌صدا در شبکه گم بشه | `ConfirmSelect()` + `WaitForConfirms()` |
| Flow Control | بروکر Publisherها رو موقع فشار حافظه/دیسک بلاک می‌کنه | کندی عجیب بدون Exception مشخص | مانیتور `vm_memory_high_watermark` و `disk_free_limit` |
| Mirrored → Quorum Queues | تغییر کامل مدل Replication در RabbitMQ | Split-Brain مستند و رد شدن از تست Jepsen در Mirrored Queues | استفادهٔ Quorum Queues (مبتنی بر Raft) |
| Log-Structured Storage | نوشتن Sequential + Zero-Copy در Kafka | — (این خودش راه‌حل throughput بالاست) | — |
| ISR (In-Sync Replicas) | مجموعهٔ Followerهای هماهنگ با Leader | کوچک شدن ISR در یه Network Brownout → Timeout در نوشتن‌های `acks=all` | Dynamic Quota برای کنترل نرخ Producer در بحران |
| Consumer Group Rebalancing | تخصیص مجدد Partition بین Consumerها | Rebalance Storm مستند: قطعی ۴۵ دقیقه‌ای از یه Rolling Deployment بد-تنظیم‌شده | Cooperative Rebalancing + Static Membership + `maxUnavailable` محدود |
| `__consumer_offsets` | Topic داخلی نگه‌داری آفست‌ها | فشار غیرمنتظره روی بروکر از Consumer Groupهای داخلی نامرئی (Kafka Connect) | مانیتورینگ صریح گروه‌های داخلی |
| Idempotent/Transactional Producer | نزدیک‌شدن عملی به Exactly-Once | فقط داخل اکوسیستم Kafka معتبره، نه به سیستم‌های بیرونی | ترکیب با Outbox Pattern و Idempotent Consumer برای مرز بیرونی |

**جمع‌بندی نهایی برای دفاع از عنوانت**: تفاوت کسی که فقط API یه Message Broker رو بلده با کسی که واقعاً «Specialist» هست، دقیقاً همین internals هستن. وقتی بتونی توضیح بدی که چرا RabbitMQ کل مدل replication‌ش رو عوض کرد (نه فقط این‌که Quorum Queues «بهتره»)، یا چرا یه Rolling Deployment معمولی می‌تونه یه Consumer Group رو برای ۴۵ دقیقه فلج کنه (نه فقط این‌که Rebalancing «وجود داره»)، داری نشون می‌دی که مسائل رو در سطح ریشه فهمیدی، نه فقط در سطح مستندات API.
