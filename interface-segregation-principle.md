# 🧩 Interface Segregation Principle (ISP)

## اصل چهارم SOLID با مثال واقعی در Laravel

> **هیچ کلاسی نباید مجبور شود متدهایی را پیاده‌سازی کند
> که به آن‌ها نیازی ندارد.**

یعنی:

> اینترفیس‌های بزرگ و چاق ❌
> اینترفیس‌های کوچک و تخصصی ✅

---

## 🎯 تعریف ساده ISP

اگر یک اینترفیس:

* متدهای زیادی دارد
* ولی یک کلاس فقط به بعضی از آن‌ها نیاز دارد

➡️ یعنی ISP نقض شده است.

---

## ❌ مثال غلط (نقض ISP)

فرض کن اینترفیس زیر را داریم:

```php
interface UserActions
{
    public function createUser();
    public function deleteUser();
    public function banUser();
    public function exportUsers();
}
```

### پیاده‌سازی:

```php
class AdminUserService implements UserActions
{
    public function createUser() {}
    public function deleteUser() {}
    public function banUser() {}
    public function exportUsers() {}
}
```

```php
class CustomerUserService implements UserActions
{
    public function createUser() {}
    public function deleteUser() {
        throw new Exception("Not allowed!");
    }
    public function banUser() {
        throw new Exception("Not allowed!");
    }
    public function exportUsers() {
        throw new Exception("Not allowed!");
    }
}
```

❌ این یعنی کلاس دوم مجبور شده متدهایی را پیاده کند که به آن‌ها نیاز ندارد.

---

## ✅ نسخه صحیح با رعایت ISP

### 1️⃣ اینترفیس‌های کوچک

```php
interface CanCreateUser
{
    public function createUser();
}
```

```php
interface CanDeleteUser
{
    public function deleteUser();
}
```

```php
interface CanBanUser
{
    public function banUser();
}
```

```php
interface CanExportUsers
{
    public function exportUsers();
}
```

---

### 2️⃣ پیاده‌سازی دقیق

```php
class AdminUserService implements 
    CanCreateUser,
    CanDeleteUser,
    CanBanUser,
    CanExportUsers
{
    public function createUser() {}
    public function deleteUser() {}
    public function banUser() {}
    public function exportUsers() {}
}
```

```php
class CustomerUserService implements CanCreateUser
{
    public function createUser() {}
}
```

---

## 🧠 حالا چه شد؟

هر کلاس:

* فقط اینترفیس‌هایی را پیاده می‌کند که نیاز دارد
* هیچ متد بی‌مصرفی ندارد

➡️ این یعنی رعایت واقعی ISP

---

## 🛑 نشانه‌های نقض ISP

اگر:

* اینترفیس‌ها بیش از 5–6 متد دارند
* کلاس‌ها متدهای خالی یا Exception دارند
* متدهایی هست که هرگز استفاده نمی‌شوند

➡️ ISP نقض شده است.

---

## 📌 جمع‌بندی

| اصل   | معنی                                |
| ----- | ----------------------------------- |
| ISP   | اینترفیس‌ها باید کوچک و تخصصی باشند |
| هدف   | جلوگیری از وابستگی غیرضروری         |
| نتیجه | کد تمیز، منعطف، قابل نگهداری        |
