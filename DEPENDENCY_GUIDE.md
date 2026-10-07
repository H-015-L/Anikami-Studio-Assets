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

هر مجموعهٔ Wheelها در یک Release مشخص قرار می‌گیرد.

---

## Manifest و SHA-256

برای هر مجموعهٔ Wheelها یک Manifest با نام:

```text
offline_packages.sha256
```

وجود دارد.

این فایل SHA-256 هر Wheel را ثبت می‌کند.

مثال:

```text
Wheel
  ↓
SHA-256
  ↓
Manifest
```

قبل از استفاده از Wheel، دریافت‌کننده باید SHA-256 فایل را با مقدار ثبت‌شده در Manifest مقایسه کند.

اگر مقدارها یکسان نباشند، فایل نباید معتبر در نظر گرفته شود.

---

## Release

هر نسخهٔ Wheelhouse در یک GitHub Release منتشر می‌شود.

Release شامل Wheelها و Manifest مربوط به همان مجموعه است.

بنابراین یک Release باید به‌عنوان یک مجموعهٔ هماهنگ در نظر گرفته شود.

---

## اضافه‌کردن یک Dependency جدید

هنگامی که Anikami Studio به یک کتابخانهٔ Python جدید نیاز پیدا می‌کند، فقط اضافه‌کردن نام آن به `requirements.txt` کافی نیست.

مراحل کلی:

```text
1. تعیین نسخهٔ دقیق
2. تهیهٔ Wheel مناسب
3. بررسی Dependencyهای آن
4. تهیهٔ Wheelهای موردنیاز Dependencyهای غیرمستقیم
5. محاسبهٔ SHA-256
6. به‌روزرسانی Manifest
7. بررسی کامل مجموعه
8. انتشار Wheelها در GitHub Release
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

## رفتار Builder

Builder ابتدا مجموعهٔ محلی Wheelها را بررسی می‌کند.

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
3. SHA-256 جدید ثبت شود
4. Manifest به‌روزرسانی شود
5. مجموعهٔ جدید در Release منتشر شود
```

Wheel قدیمی فقط زمانی حذف شود که دیگر موردنیاز نباشد.

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
```

است.

---

## چک‌لیست انتشار Dependency

قبل از انتشار یک Dependency جدید:

```text
[ ] نسخهٔ دقیق مشخص شده
[ ] Wheel اصلی آماده است
[ ] Dependencyهای غیرمستقیم بررسی شده‌اند
[ ] Wheelهای لازم آماده هستند
[ ] SHA-256 محاسبه شده
[ ] Manifest به‌روز شده
[ ] Wheel در Release قرار گرفته
[ ] نام فایل با Manifest مطابقت دارد
[ ] SHA-256 فایل Release با Manifest یکسان است
```

پس از انجام این مراحل، Dependency برای استفاده توسط ابزارهای Anikami Studio آماده است.
