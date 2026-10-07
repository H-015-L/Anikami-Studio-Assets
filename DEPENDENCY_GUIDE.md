# راهنمای مدیریت وابستگی‌های Anikami Studio

این Repository عمومی محل نگهداری Assetهای موردنیاز Anikami Studio است؛ به‌خصوص Python Wheelهایی که برای نصب آفلاین، ساخت برنامه و بازیابی وابستگی‌های ناقص استفاده می‌شوند.

این راهنما مربوط به **Repository عمومی Assets** است و بخشی از سورس کد خصوصی Anikami Studio نیست.

---

## ساختار کلی

وابستگی‌های Python در قالب Wheel نگهداری می‌شوند.

روند کلی:

```text
requirements.txt
        ↓
Wheelhouse
        ↓
SHA-256 Manifest
        ↓
GitHub Release Assets
        ↓
Anikami Studio Builder
```

Repository اصلی سورس کد جدا از این Repository است.

---

## Wheelhouse

Wheelهای Python در GitHub Release Assets قرار می‌گیرند.

Wheelها مستقیماً به‌عنوان فایل‌های عادی داخل Repository اصلی Git نگهداری نمی‌شوند؛ زیرا حجم بعضی از آن‌ها زیاد است.

هر مجموعهٔ Wheelها در یک GitHub Release قرار می‌گیرد.

---

## Manifest و SHA-256

برای مجموعهٔ Wheelها یک Manifest با نام:

```text
offline_packages.sha256
```

وجود دارد.

این فایل SHA-256 هر Wheel را ثبت می‌کند.

روند اعتبارسنجی:

```text
Wheel
  ↓
محاسبه SHA-256
  ↓
مقایسه با Manifest
  ↓
تأیید یا رد فایل
```

قبل از استفاده از Wheel، SHA-256 فایل باید با مقدار ثبت‌شده در Manifest مقایسه شود.

اگر مقدارها یکسان نباشند، فایل معتبر در نظر گرفته نمی‌شود.

---

## Release

Wheelهای قابل دریافت در GitHub Release Assets منتشر می‌شوند.

یک Release می‌تواند شامل:

```text
Wheelها
+
offline_packages.sha256
```

باشد.

### آیا برای هر Dependency جدید Release جدید لازم است؟

خیر.

اگر فقط یک یا چند Wheel جدید به همان مجموعهٔ Wheelhouse اضافه شوند، می‌توان آن‌ها را به **Release موجود** اضافه کرد.

مثلاً:

```text
wheelhouse-2026.10.0
├── wheel-1.whl
├── wheel-2.whl
├── ...
├── wheel-117.whl
└── wheel-118.whl
```

در این حالت لازم نیست فقط به دلیل اضافه‌شدن Wheel جدید، Release جدید ساخته شود.

### چه زمانی Release جدید مناسب است؟

وقتی بخواهیم یک Snapshot یا مجموعهٔ جدید از Wheelhouse منتشر کنیم، می‌توان Release جدید ایجاد کرد.

مثلاً:

```text
wheelhouse-2026.10.0
        ↓
wheelhouse-2026.11.0
```

Release جدید برای ایجاد یک مجموعهٔ نسخه‌بندی‌شده و مشخص مفید است.

بنابراین:

```text
Dependency جدید بدون تغییر Snapshot
        ↓
همان Release

مجموعهٔ جدید / Snapshot جدید
        ↓
Release جدید
```

---

## اضافه‌کردن یک Dependency جدید

هنگامی که Anikami Studio به یک کتابخانهٔ Python جدید نیاز پیدا می‌کند، فقط اضافه‌کردن نام آن به `requirements.txt` کافی نیست.

مراحل کلی:

```text
1. تعیین نسخهٔ دقیق
2. تهیهٔ Wheel مناسب
3. بررسی Dependencyهای آن
4. تهیهٔ Wheelهای موردنیاز Dependencyهای غیرمستقیم
5. قرار دادن Wheelها در Wheelhouse محلی
6. محاسبهٔ SHA-256
7. به‌روزرسانی offline_packages.sha256
8. بررسی کامل Wheelhouse
9. انتشار Wheel جدید در GitHub Release
10. در صورت تغییر Manifest، جایگزینی Manifest در Release
11. بررسی نهایی
```

---

## نسخهٔ دقیق

Dependency باید با نسخهٔ دقیق مشخص شود.

نمونه:

```text
some-library==1.2.3
```

استفاده از نسخهٔ بدون محدودیت برای Buildهای قابل تکرار توصیه نمی‌شود.

---

## Dependencyهای غیرمستقیم

یک کتابخانه ممکن است خودش به کتابخانه‌های دیگری نیاز داشته باشد.

مثال:

```text
Library A
   ├── Library B
   └── Library C
```

در این حالت فقط Wheel مربوط به `Library A` کافی نیست.

Wheelهای لازم برای `Library B` و `Library C` نیز باید در مجموعهٔ مناسب موجود باشند.

---

## سازگاری Wheel

Wheel باید با محیط هدف سازگار باشد.

محیط فعلی Anikami Studio:

```text
Python: 3.10.11
Platform: Windows x64
```

Wheel نامناسب نباید در مجموعه منتشر شود.

---

## بررسی Wheel

برای هر Wheel این موارد باید بررسی شوند:

```text
[ ] فایل وجود دارد
[ ] نام فایل صحیح است
[ ] با محیط هدف سازگار است
[ ] SHA-256 صحیح است
[ ] در Manifest ثبت شده است
[ ] Dependencyهای لازم آن موجود هستند
```

---

## به‌روزرسانی Manifest

هر زمان که Wheelی اضافه، حذف یا با نسخهٔ دیگری جایگزین می‌شود، Manifest باید با مجموعهٔ واقعی Wheelها هماهنگ باشد.

یعنی:

```text
Wheelhouse
      =
Manifest
```

از نظر فایل‌های مورد انتظار.

اگر Wheel جدیدی اضافه شود:

```text
Wheel جدید
    ↓
افزودن SHA-256 به Manifest
```

اگر Wheelی حذف شود:

```text
Wheel حذف‌شده
    ↓
حذف ورودی مربوطه از Manifest
```

اگر نسخهٔ یک Wheel عوض شود:

```text
Wheel قدیمی
    ↓
Wheel جدید
    ↓
SHA-256 جدید
    ↓
Manifest جدید
```

---

## انتشار Manifest در Release

اگر `offline_packages.sha256` تغییر کرده باشد، فقط تغییر فایل محلی کافی نیست.

Manifest به‌روز‌شده باید در GitHub Release نیز قرار بگیرد.

بنابراین پس از تغییر Manifest:

```text
offline_packages.sha256
        ↓
Commit در Repository
        +
Release Asset به‌روز
```

هر دو باید هماهنگ باشند.

اگر Release قبلاً یک فایل با نام `offline_packages.sha256` داشته باشد، نسخهٔ قبلی باید با نسخهٔ جدید جایگزین شود تا Release همچنان با Manifest جدید هماهنگ باشد.

---

## رفتار Builder

Builder ابتدا Wheelhouse محلی را بررسی می‌کند.

اگر Wheel مفقود یا خراب باشد:

```text
Wheelhouse محلی
        ↓
Missing / Invalid
        ↓
پرسش از کاربر
```

Builder نباید بدون اجازهٔ کاربر دانلود کند.

در صورت پاسخ `Y`:

```text
GitHub Release Assets
        ↓
دانلود فقط فایل‌های موردنیاز
        ↓
SHA-256 Verification
        ↓
Wheelhouse محلی
        ↓
ادامهٔ Build
```

در صورت پاسخ `N`:

```text
Build متوقف می‌شود.
```

---

## Builder چگونه Wheel موردنیاز را پیدا می‌کند؟

در Builder، نام فایل Wheel از Manifest محلی مشخص می‌شود.

مثلاً اگر Manifest شامل:

```text
altgraph-0.17.5-py2.py3-none-any.whl
```

باشد، Builder دقیقاً همین فایل را به‌عنوان Wheel موردنیاز شناسایی می‌کند.

سپس Builder فایل را از Release موردنظر دریافت می‌کند.

### وضعیت فعلی Builder

در نسخهٔ فعلی Builder، Repository و Release Wheelhouse در تنظیمات Builder مشخص شده‌اند.

به‌صورت فعلی:

```text
Repository:
H-015-L/Anikami-Studio-Assets

Release:
wheelhouse-2026.10.0
```

بنابراین Builder فعلی عملاً این رابطه را دارد:

```text
Manifest
   ↓
نام دقیق Wheel
   ↓
Release مشخص
   ↓
GitHub Release Asset
```

مثلاً:

```text
altgraph-0.17.5-py2.py3-none-any.whl
```

از Release مشخص Wheelhouse درخواست می‌شود.

### نکتهٔ مهم

اگر در آینده Release فعال تغییر کند، Builder نیز باید از Release جدید مطلع شود.

برای همین، در نسخه‌های آینده می‌توان اطلاعات Release فعال را به یک Manifest یا فایل نسخه‌بندی عمومی منتقل کرد تا Release مستقیماً داخل کد Builder ثابت نباشد.

---

## منبع دانلود

منبع رسمی Wheelها این Repository و Releaseهای آن است:

```text
H-015-L/Anikami-Studio-Assets
```

Builder باید فقط Asset موردنیاز را دریافت کند، نه اینکه کل Wheelhouse را دوباره دانلود کند.

---

## PyPI

Builder برای Repair وابستگی‌ها نباید به‌صورت خودکار از PyPI استفاده کند.

اصل موردنظر:

```text
Missing Wheel
      ↓
GitHub Release Asset
```

نه:

```text
Missing Wheel
      ↓
PyPI
```

در نتیجه، Repair وابستگی‌ها باید بر اساس Assetهای منتشرشده در این Repository انجام شود.

---

## تغییر نسخهٔ یک کتابخانه

مثلاً:

```text
some-library==1.2.3
```

به:

```text
some-library==1.3.0
```

تغییر کند.

در این حالت باید:

```text
1. Wheel نسخهٔ جدید تهیه شود
2. Dependencyهای تغییرکرده بررسی شوند
3. Wheel جدید وارد Wheelhouse شود
4. SHA-256 جدید محاسبه شود
5. Manifest به‌روزرسانی شود
6. Manifest جدید در Release قرار بگیرد
7. Wheel جدید در Release قرار بگیرد
8. بررسی نهایی انجام شود
```

اگر Wheel قدیمی دیگر موردنیاز نیست، می‌توان آن را از مجموعه حذف کرد.

---

## اضافه‌کردن Wheel به Release موجود

برای یک Dependency جدید لازم نیست حتماً Release جدید ساخته شود.

می‌توان Wheel جدید را به Release موجود اضافه کرد:

```text
Release موجود
    ↓
Wheel جدید
    ↓
Manifest به‌روز
```

این روش زمانی مناسب است که همچنان همان مجموعهٔ Wheelhouse را ادامه می‌دهیم.

مثال:

```text
قبل:

wheelhouse-2026.10.0
├── 117 wheels
└── offline_packages.sha256


بعد:

wheelhouse-2026.10.0
├── 118 wheels
└── offline_packages.sha256
```

در این حالت Release همان Release قبلی است.

---

## چه زمانی Release جدید بسازیم؟

در صورت نیاز به Snapshot جدید:

```text
wheelhouse-2026.10.0
        ↓
wheelhouse-2026.11.0
```

Release جدید ساخته می‌شود.

Release جدید باید شامل مجموعهٔ هماهنگ Wheelها و Manifest همان مجموعه باشد.

این روش زمانی مناسب است که بخواهیم یک نسخهٔ مشخص و جداگانه از Wheelhouse داشته باشیم.

---

## ارتباط با Repository سورس

این Repository مسئول Assetهای وابستگی است.

Repository خصوصی سورس کد مسئول:

```text
کد برنامه
requirements.txt
Builder
ساختار پروژه
```

است.

این Repository مسئول:

```text
Wheelهای Python
Manifestها
GitHub Release Assets
راهنمای وابستگی‌ها
```

است.

---

## قوانین Git

فایل‌هایی مانند:

```text
DEPENDENCY_GUIDE.md
offline_packages.sha256
manifests/
```

می‌توانند در Repository Git نگهداری شوند.

Wheelهای حجیم بهتر است به‌عنوان GitHub Release Assets نگهداری شوند و مستقیماً وارد Git History نشوند.

---

## چک‌لیست انتشار Dependency جدید

قبل از انتشار یک Dependency جدید:

```text
[ ] نسخهٔ دقیق مشخص شده
[ ] Wheel اصلی آماده است
[ ] Dependencyهای غیرمستقیم بررسی شده‌اند
[ ] Wheelهای لازم آماده هستند
[ ] Wheel در Wheelhouse محلی قرار گرفته
[ ] SHA-256 محاسبه شده
[ ] Manifest به‌روز شده
[ ] Manifest محلی با Wheelhouse هماهنگ است
[ ] Wheel در GitHub Release قرار گرفته
[ ] Manifest جدید در صورت تغییر در Release قرار گرفته
[ ] SHA-256 فایل Release با Manifest یکسان است
[ ] Builder قادر به دریافت Wheel است
```

پس از انجام این مراحل، Dependency برای استفاده توسط ابزارهای Anikami Studio آماده است.

---

## خلاصهٔ فرآیند

برای یک Dependency جدید:

```text
requirements.txt
        ↓
Wheel
        ↓
Dependencyهای غیرمستقیم
        ↓
Wheelhouse محلی
        ↓
offline_packages.sha256
        ↓
GitHub Release
        ↓
Builder
        ↓
Download در صورت Missing / Invalid
        ↓
SHA-256 Verification
        ↓
.venv
        ↓
Build
```

اصل مهم این است که **Wheelhouse محلی، Manifest و GitHub Release باید همیشه با یکدیگر هماهنگ باشند**.
