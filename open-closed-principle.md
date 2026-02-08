# 🔓 Open–Closed Principle (OCP)

## اصل دوم SOLID با مثال واقعی در Laravel

> **کلاس‌ها باید برای توسعه (Extension) باز باشند
> ولی برای تغییر (Modification) بسته باشند.**

یعنی بتوانیم قابلیت جدید اضافه کنیم
**بدون اینکه کدهای موجود را دست بزنیم.**

---

## 🎯 تعریف ساده OCP

اگر برای اضافه کردن یک قابلیت جدید:

* مجبور شوی `if/else` جدید اضافه کنی
* یا کلاس قبلی را تغییر بدهی

➡️ یعنی اصل OCP را نقض کرده‌ای.

---

## ❌ مثال غلط در Laravel (نقض OCP)

فرض کن سیستم پرداخت داری:

```php
class PaymentService
{
    public function pay(string $method, int $amount)
    {
        if ($method === 'zarinpal') {
            // پرداخت با زرین‌پال
        } elseif ($method === 'paypal') {
            // پرداخت با پی‌پال
        } elseif ($method === 'stripe') {
            // پرداخت با استرایپ
        }
    }
}
```

### 🔴 مشکل چیست؟

هر بار درگاه جدید اضافه شود:

* باید این کلاس را **تغییر بدهی**
* ریسک باگ بالا می‌رود
* تست‌ها می‌شکنند

---

## ✅ نسخه صحیح با رعایت OCP

### 1️⃣ اینترفیس مشترک

```php
interface PaymentGateway
{
    public function pay(int $amount): bool;
}
```

---

### 2️⃣ پیاده‌سازی درگاه‌ها

```php
class ZarinpalGateway implements PaymentGateway
{
    public function pay(int $amount): bool
    {
        return true;
    }
}
```

```php
class PaypalGateway implements PaymentGateway
{
    public function pay(int $amount): bool
    {
        return true;
    }
}
```

```php
class StripeGateway implements PaymentGateway
{
    public function pay(int $amount): bool
    {
        return true;
    }
}
```

---

### 3️⃣ سرویس پرداخت اصلی

```php
class PaymentService
{
    public function __construct(private PaymentGateway $gateway) {}

    public function pay(int $amount)
    {
        return $this->gateway->pay($amount);
    }
}
```

---

### 4️⃣ انتخاب درگاه در Laravel (Service Container)

```php
app()->bind(PaymentGateway::class, function () {
    return match(config('payment.driver')) {
        'zarinpal' => new ZarinpalGateway(),
        'paypal'   => new PaypalGateway(),
        'stripe'   => new StripeGateway(),
    };
});
```

---

## 🧠 حالا چه اتفاقی افتاد؟

برای اضافه کردن درگاه جدید:

1. فقط یک کلاس جدید می‌سازیم
2. به کدهای قبلی دست نمی‌زنیم

➡️ این یعنی رعایت واقعی OCP

---

## 🛑 نشانه‌های نقض OCP

اگر در کلاس:

* if/else برای نوع رفتار دارید
* سوئیچ روی type دارید
* با اضافه شدن فیچر، فایل قبلی تغییر می‌کند

➡️ احتمالاً OCP رعایت نشده است.

---

## 📌 جمع‌بندی

| ویژگی    | توضیح                             |
| -------- | --------------------------------- |
| 🎯 هدف   | توسعه بدون تغییر کد موجود         |
| 🔧 ابزار | Interface + Polymorphism          |
| 🚀 نتیجه | سیستم پایدار، قابل رشد و تست‌پذیر |

---
