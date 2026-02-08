# 🔌 Dependency Inversion Principle (DIP)

## اصل پنجم SOLID با مثال واقعی در Laravel

> **ماژول‌های سطح بالا نباید به ماژول‌های سطح پایین وابسته باشند.
> هر دو باید به انتزاع (Interface) وابسته باشند.**

> **انتزاع نباید به جزئیات وابسته باشد،
> جزئیات باید به انتزاع وابسته باشند.**

---

## 🎯 تعریف ساده DIP

به‌جای اینکه کلاس مستقیماً یک کلاس دیگر را بسازد:

```php
new MysqlUserRepository();
```

باید به **Interface** وابسته باشد:

```php
UserRepositoryInterface
```

---

## ❌ مثال غلط (نقض DIP)

```php
class UserService
{
    public function __construct()
    {
        $this->repo = new MysqlUserRepository();
    }

    public function getUsers()
    {
        return $this->repo->all();
    }
}
```

❌ این کلاس به دیتابیس خاص وابسته است
اگر بخواهی Redis یا API جایگزین کنی، باید این کلاس را تغییر بدهی.

---

## ✅ نسخه صحیح با رعایت DIP

### 1️⃣ اینترفیس

```php
interface UserRepositoryInterface
{
    public function all();
}
```

---

### 2️⃣ پیاده‌سازی‌ها

```php
class MysqlUserRepository implements UserRepositoryInterface
{
    public function all()
    {
        return User::all();
    }
}
```

```php
class ApiUserRepository implements UserRepositoryInterface
{
    public function all()
    {
        return Http::get('https://api.test/users')->json();
    }
}
```

---

### 3️⃣ سرویس سطح بالا

```php
class UserService
{
    public function __construct(
        private UserRepositoryInterface $repo
    ) {}

    public function getUsers()
    {
        return $this->repo->all();
    }
}
```

---

### 4️⃣ Bind در Service Container لاراول

```php
app()->bind(
    UserRepositoryInterface::class,
    MysqlUserRepository::class
);
```

---

## 🧠 حالا چه شد؟

* سرویس دیگر به دیتابیس خاص وابسته نیست
* می‌توانیم ریپازیتوری را عوض کنیم بدون تغییر سرویس
* تست بسیار ساده‌تر می‌شود

➡️ این یعنی رعایت واقعی DIP

---

## 🛑 نشانه‌های نقض DIP

اگر:

* new داخل سرویس‌ها زیاد است
* کلاس‌ها به پیاده‌سازی وابسته‌اند
* نتوانی راحت mock بسازی

➡️ DIP رعایت نشده است.

---

## 📌 جمع‌بندی نهایی SOLID

| اصل | مفهوم              |
| --- | ------------------ |
| SRP | هر کلاس یک مسئولیت |
| OCP | توسعه بدون تغییر   |
| LSP | جایگزینی امن       |
| ISP | اینترفیس‌های کوچک  |
| DIP | وابستگی به انتزاع  |
