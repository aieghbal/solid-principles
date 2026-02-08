# 🧱 Single Responsibility Principle (SRP)

## اصل اول SOLID با مثال واقعی در Laravel

> **هر کلاس باید فقط یک دلیل برای تغییر داشته باشد.**
> یعنی هر کلاس فقط باید **یک مسئولیت مشخص** داشته باشد.

---

## 🎯 تعریف ساده SRP

اصل SRP می‌گوید:

> یک کلاس فقط باید **یک کار مشخص** انجام دهد، نه چند کار متفاوت.

اگر یک کلاس:

* دیتا بگیرد
* اعتبارسنجی کند
* ذخیره کند
* ایمیل بفرستد

یعنی **چند مسئولیت دارد** و SRP را نقض کرده است.

---

## ❌ مثال غلط در Laravel (نقض SRP)

```php
class UserController extends Controller
{
    public function store(Request $request)
    {
        // 1. اعتبارسنجی
        $request->validate([
            'email' => 'required|email',
            'password' => 'required|min:8'
        ]);

        // 2. ساخت کاربر
        $user = User::create([
            'email' => $request->email,
            'password' => bcrypt($request->password),
        ]);

        // 3. ارسال ایمیل
        Mail::to($user->email)->send(new WelcomeMail($user));

        // 4. لاگ گرفتن
        Log::info("New user created: {$user->email}");

        return response()->json(['status' => 'ok']);
    }
}
```

### 🔴 مشکل چیست؟

این متد 4 مسئولیت دارد:

1. اعتبارسنجی
2. ساخت کاربر
3. ارسال ایمیل
4. لاگ

اگر یکی تغییر کند، باید کل متد را تغییر بدهی → نقض SRP

---

## ✅ نسخه صحیح با رعایت SRP

### 1️⃣ سرویس ثبت کاربر

```php
class RegisterUserService
{
    public function execute(array $data): User
    {
        return User::create([
            'email' => $data['email'],
            'password' => bcrypt($data['password']),
        ]);
    }
}
```

---

### 2️⃣ سرویس ارسال ایمیل

```php
class SendWelcomeEmailService
{
    public function send(User $user): void
    {
        Mail::to($user->email)->send(new WelcomeMail($user));
    }
}
```

---

### 3️⃣ سرویس لاگ

```php
class UserLoggerService
{
    public function log(User $user): void
    {
        Log::info("New user created: {$user->email}");
    }
}
```

---

### 4️⃣ کنترلر تمیز

```php
class UserController extends Controller
{
    public function __construct(
        private RegisterUserService $registerService,
        private SendWelcomeEmailService $mailService,
        private UserLoggerService $loggerService,
    ) {}

    public function store(Request $request)
    {
        $request->validate([
            'email' => 'required|email',
            'password' => 'required|min:8'
        ]);

        $user = $this->registerService->execute($request->all());
        $this->mailService->send($user);
        $this->loggerService->log($user);

        return response()->json(['status' => 'ok']);
    }
}
```

---

## 🧠 مزایای رعایت SRP

| مزیت            | توضیح                               |
| --------------- | ----------------------------------- |
| 🧪 تست‌پذیری    | هر کلاس جدا تست می‌شود              |
| 🔧 نگهداری آسان | تغییر در یک بخش، بقیه را نمی‌شکند   |
| ♻️ استفاده مجدد | سرویس‌ها قابل استفاده در جاهای دیگر |
| 🚀 مقیاس‌پذیری  | توسعه بدون ترس از خراب شدن سیستم    |

---

## 🛑 نشانه‌های نقض SRP

اگر در کلاس:

* چند نوع لاجیک دارید
* if زیاد دارید
* تغییرات مکرر دارد
* بیش از 200 خط است

احتمالاً SRP را نقض کرده‌اید.

---

## 📌 جمع‌بندی

> **یک کلاس = یک مسئولیت = یک دلیل برای تغییر**

اگر این جمله را همیشه جلوی چشم داشته باشی،
کدت تمیزتر، تست‌پذیرتر و حرفه‌ای‌تر می‌شود. 💪

