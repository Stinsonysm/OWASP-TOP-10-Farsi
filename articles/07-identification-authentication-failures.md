# 🎭🔑 آسیب‌پذیری شماره ۷: شکست شناسایی و احراز هویت (Identification and Authentication Failures)

## 🤔 این چیه به زبان ساده؟

تصور کن یه باشگاه خیلی مهم داری که فقط اعضا می‌تونن بیان تو.

**سناریو ۱:** در ورودی نگهبانی نیست! هر کسی میگه "من عضوم" میره تو! 🚪👤

**سناریو ۲:** نگهبان هست ولی فقط میگه "اسمتون؟" و بدون چک کردن کارت شناسایی راهت میده! 🤷

**سناریو ۳:** برای ورود باید رمز عبور بگی ولی رمزش "123456" هست و همه می‌دونن! 🤦

همه این‌ها مثال‌هایی از مشکلات شناسایی و احراز هویت هستن!

## 🎮 مثال‌های بامزه

### مثال ۱: پسوردهای ضعیف مجازن! 💩

```javascript
// ثبت‌نام کاربر
app.post('/register', (req, res) => {
    const { username, password } = req.body;

    // هیچ چک کردنی نداریم! 😱
    db.createUser(username, password);

    res.send('ثبت‌نام موفق!');
});

// کاربر ثبت‌نام می‌کنه با:
// Username: admin
// Password: 123
// و سیستم قبول می‌کنه! 🤦
```

**نتیجه:** هکر با ۱۰ تا تلاش می‌تونه پسورد رو حدس بزنه:
```
123 ❌
1234 ❌
12345 ❌
123456 ✅ وارد شد! 😱
```

### مثال ۲: Session که هیچ‌وقت منقضی نمیشه! ⏰

```javascript
// ورود به سیستم
app.post('/login', (req, res) => {
    if (checkPassword(req.body.username, req.body.password)) {
        // Session برای همیشه! 😱
        req.session.userId = user.id;
        res.send('خوش اومدی!');
    }
});

// کاربر از کافی‌نت لاگین می‌کنه و میره
// ۱ سال بعد هنوز لاگینه! 💀
// نفر بعدی میاد و می‌تونه به حساب اون دسترسی داشته باشه!
```

### مثال ۳: "فراموشی رمز" بدون امنیت 🔓

```javascript
// فراموشی رمز
app.post('/forgot-password', (req, res) => {
    const user = db.findUser(req.body.email);

    if (user) {
        // سوال امنیتی!
        res.json({
            question: user.securityQuestion,
            // "اسم مادرتون؟" 🤦
            // که از فیسبوک پیداست!
        });
    }
});

// هکر:
// اسم مادرش: Googling... "فاطمه" ✅
// وارد شد! 😱
```

### مثال ۴: Brute Force بدون محدودیت 🔨

```javascript
// ورود
app.post('/login', (req, res) => {
    if (checkPassword(req.body.username, req.body.password)) {
        res.send('ورود موفق! ✅');
    } else {
        res.send('پسورد اشتباهه! ❌');
    }
});

// هکر یه اسکریپت می‌نویسه:
for password in common_passwords:
    response = login(username, password)
    if "موفق" in response:
        print("پیداش کردم! 🎉")

// و میلیون‌ها بار امتحان می‌کنه!
// سیستم هیچ محدودیتی نداره! 😱
```

## 🎪 انواع حملات

### ۱. Credential Stuffing 🗃️

```python
# هکر یه لیست بزرگ از username/password های لو رفته داره
# (از هک‌های قبلی سایت‌های دیگه)

leaked_credentials = [
    ("ali@email.com", "password123"),
    ("sara@email.com", "qwerty"),
    ("reza@email.com", "123456"),
    # میلیون‌ها تا! 😱
]

# حالا همه رو امتحان می‌کنه تو سایت تو!
for email, password in leaked_credentials:
    try_login(email, password)

# چون خیلیا پسورد یکسان تو سایت‌های مختلف دارن،
# خیلیاشون موفق میشه! 💀
```

### ۲. Session Hijacking 🏴‍☠️

```javascript
// Session ID تو URL
https://bank.com/account?session=abc123

// کاربر این لینک رو برای دوستش می‌فرسته
// دوستش کلیک می‌کنه و... وارد حساب کاربر میشه! 😱

// یا اینکه Session ID توی Cookie بدون Secure flag
// هکر از طریق WiFi عمومی Cookie رو می‌دزده!
```

### ۳. Account Enumeration 🔍

```javascript
// لاگین
app.post('/login', (req, res) => {
    const user = db.findUser(req.body.username);

    if (!user) {
        return res.send('کاربر پیدا نشد! ❌');
        // اوه اوه! یعنی اگه این پیام نیومد، کاربر وجود داره! 🚨
    }

    if (user.password !== req.body.password) {
        return res.send('رمز عبور اشتباهه! ❌');
    }

    // ورود موفق...
});

// هکر:
# تست: username=admin
# پاسخ: "رمز عبور اشتباهه"
# یعنی admin وجود داره! 🎯
# حالا فقط باید پسوردش رو پیدا کنه!
```

## 🛡️ چطور درستش کنیم؟

### ۱. پسورد قوی اجباری کن! 💪

```javascript
function validatePassword(password) {
    // حداقل ۸ کاراکتر
    if (password.length < 8) {
        return 'پسورد باید حداقل ۸ کاراکتر باشه!';
    }

    // حداقل یه حرف بزرگ
    if (!/[A-Z]/.test(password)) {
        return 'باید یه حرف بزرگ داشته باشه!';
    }

    // حداقل یه حرف کوچیک
    if (!/[a-z]/.test(password)) {
        return 'باید یه حرف کوچیک داشته باشه!';
    }

    // حداقل یه عدد
    if (!/[0-9]/.test(password)) {
        return 'باید یه عدد داشته باشه!';
    }

    // حداقل یه کاراکتر خاص
    if (!/[!@#$%^&*]/.test(password)) {
        return 'باید یه کاراکتر خاص داشته باشه!';
    }

    // پسوردهای معروف رو رد کن
    const commonPasswords = ['123456', 'password', 'qwerty', 'admin'];
    if (commonPasswords.includes(password.toLowerCase())) {
        return 'این پسورد خیلی ضعیفه!';
    }

    return null; // قبوله! ✅
}
```

### ۲. Multi-Factor Authentication (MFA) 📱

```javascript
// مرحله ۱: ورود با پسورد
app.post('/login', async (req, res) => {
    const user = await checkPassword(req.body.username, req.body.password);

    if (user) {
        // مرحله ۲: ارسال کد به موبایل
        const code = generateRandomCode(); // 123456
        await sendSMS(user.phone, `کد تایید: ${code}`);

        // ذخیره موقت
        tempCodes[user.id] = {
            code: code,
            expires: Date.now() + 5 * 60 * 1000 // ۵ دقیقه
        };

        res.json({ needsVerification: true, userId: user.id });
    }
});

// مرحله ۳: تایید کد
app.post('/verify', (req, res) => {
    const saved = tempCodes[req.body.userId];

    if (!saved || Date.now() > saved.expires) {
        return res.send('کد منقضی شده! ❌');
    }

    if (saved.code === req.body.code) {
        // حالا واقعا لاگین کن
        req.session.userId = req.body.userId;
        delete tempCodes[req.body.userId];
        res.send('ورود موفق! ✅');
    } else {
        res.send('کد اشتباهه! ❌');
    }
});
```

### ۳. Rate Limiting (محدودیت تعداد تلاش) 🚦

```javascript
const rateLimit = require('express-rate-limit');

// حداکثر ۵ تلاش در ۱۵ دقیقه
const loginLimiter = rateLimit({
    windowMs: 15 * 60 * 1000, // ۱۵ دقیقه
    max: 5, // ۵ تلاش
    message: 'خیلی زیاد تلاش کردی! ۱۵ دقیقه صبر کن! ⏰',
    standardHeaders: true,
    legacyHeaders: false,
});

app.post('/login', loginLimiter, (req, res) => {
    // لاگین...
});

// یا با Redis برای چند سرور:
const RedisStore = require('rate-limit-redis');

const limiter = rateLimit({
    store: new RedisStore({
        client: redisClient,
        prefix: 'login_attempts:'
    }),
    max: 5,
    windowMs: 15 * 60 * 1000
});
```

### ۴. Session امن 🔐

```javascript
const session = require('express-session');

app.use(session({
    secret: process.env.SESSION_SECRET, // کلید قوی!
    resave: false,
    saveUninitialized: false,
    cookie: {
        secure: true, // فقط HTTPS
        httpOnly: true, // جلوگیری از دسترسی JavaScript
        maxAge: 30 * 60 * 1000, // ۳۰ دقیقه
        sameSite: 'strict' // جلوگیری از CSRF
    }
}));

// و Session رو منظم Rotate کن
app.use((req, res, next) => {
    if (req.session.userId) {
        // هر ۱۰ دقیقه Session جدید
        if (!req.session.rotatedAt || Date.now() - req.session.rotatedAt > 10 * 60 * 1000) {
            req.session.regenerate(() => {
                req.session.rotatedAt = Date.now();
                next();
            });
        } else {
            next();
        }
    } else {
        next();
    }
});
```

### ۵. رمز فراموشی امن 🔑

```javascript
app.post('/forgot-password', async (req, res) => {
    const user = await db.findUser(req.body.email);

    // همیشه همین پیام رو بده (حتی اگه کاربر نباشه!)
    // تا هکر نفهمه کاربر وجود داره یا نه
    res.send('اگه این ایمیل تو سیستم باشه، لینک ریست پسورد فرستادیم! ✅');

    if (user) {
        // توکن یکبار مصرف بساز
        const token = crypto.randomBytes(32).toString('hex');

        await db.saveResetToken({
            userId: user.id,
            token: token,
            expires: Date.now() + 60 * 60 * 1000 // ۱ ساعت
        });

        // ایمیل بفرست
        await sendEmail(user.email, `
            برای ریست پسورد روی این لینک کلیک کن:
            https://mysite.com/reset-password?token=${token}

            این لینک فقط ۱ ساعت اعتبار داره!
        `);
    }
});

// ریست کردن پسورد
app.post('/reset-password', async (req, res) => {
    const resetData = await db.getResetToken(req.body.token);

    if (!resetData || Date.now() > resetData.expires) {
        return res.send('توکن نامعتبره! ❌');
    }

    // پسورد جدید رو ذخیره کن
    await db.updatePassword(resetData.userId, req.body.newPassword);

    // توکن رو حذف کن (یکبار مصرف!)
    await db.deleteResetToken(req.body.token);

    res.send('پسورد تغییر کرد! ✅');
});
```

### ۶. Password Hashing درست 🔨

```javascript
const bcrypt = require('bcrypt');
const saltRounds = 12; // بالاتر = امن‌تر ولی کندتر

// ذخیره پسورد
async function savePassword(username, password) {
    const hashedPassword = await bcrypt.hash(password, saltRounds);

    await db.createUser({
        username: username,
        password: hashedPassword // هش شده!
    });
}

// چک کردن پسورد
async function checkPassword(username, password) {
    const user = await db.findUser(username);

    if (!user) {
        // هش یه چیز فیک کن تا زمان یکسان بگذره
        // (جلوگیری از Timing Attack)
        await bcrypt.compare(password, '$2b$12$fake...');
        return null;
    }

    const isValid = await bcrypt.compare(password, user.password);
    return isValid ? user : null;
}
```

## 🎯 چک‌لیست کامل

**پسوردها:**
- ✅ حداقل ۸ کاراکتر (بهتره ۱۲+)
- ✅ ترکیبی از حروف، اعداد، کاراکترهای خاص
- ✅ پسوردهای معروف رو رد کن
- ✅ با bcrypt/Argon2 هش کن
- ✅ نمایش قدرت پسورد

**احراز هویت:**
- ✅ Multi-Factor Authentication (MFA)
- ✅ Rate limiting روی لاگین
- ✅ Captcha بعد از چند تلاش ناموفق
- ✅ تاخیر بین تلاش‌های ناموفق

**Session:**
- ✅ Session timeout (۳۰ دقیقه)
- ✅ Secure و HttpOnly cookies
- ✅ Session rotation
- ✅ Logout روی همه دستگاه‌ها

**رمز فراموشی:**
- ✅ توکن یکبار مصرف
- ✅ انقضای زمانی (۱ ساعت)
- ✅ ارسال به ایمیل/موبایل تایید شده
- ✅ عدم افشای اطلاعات کاربری

## 🎓 تمرین: این کدا امنن یا نه؟

```javascript
// مثال ۱
app.post('/login', (req, res) => {
    const user = users.find(u => u.username === req.body.username);

    if (user && user.password === req.body.password) {
        req.session.user = user;
        res.send('ورود موفق!');
    } else {
        res.send('اطلاعات اشتباهه!');
    }
});
```

<details>
<summary>جواب</summary>

❌ مشکلات:

1. پسورد بدون هش مقایسه میشه!
2. پسورد به صورت plain text ذخیره شده!
3. هیچ rate limiting نیست!
4. Session config نداره (secure, httpOnly)
5. همه اطلاعات user توی session میره!

✅ نسخه درست:

```javascript
const bcrypt = require('bcrypt');
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 5
});

app.post('/login', limiter, async (req, res) => {
    const user = await db.findUser(req.body.username);

    if (!user) {
        await bcrypt.compare(req.body.password, '$2b$12$fake...');
        return res.send('اطلاعات اشتباهه!');
    }

    const isValid = await bcrypt.compare(req.body.password, user.passwordHash);

    if (isValid) {
        req.session.userId = user.id; // فقط ID
        res.send('ورود موفق!');
    } else {
        res.send('اطلاعات اشتباهه!');
    }
});
```

</details>

## 🎬 داستان واقعی

یه شرکت بزرگ یه باگ داشت که اگه پسورد رو اشتباه وارد می‌کردی، می‌گفت: "پسورد اشتباهه!"

ولی اگه username اشتباه می‌زدی، می‌گفت: "کاربر پیدا نشد!"

یه هکر از این استفاده کرد و لیست همه کاربرای سیستم رو استخراج کرد! 😱

بعد با Brute Force پسوردای ضعیف رو شکست و وارد شد!

**درس:** حتی یه تفاوت کوچیک تو پیام خطا می‌تونه خطرناک باشه!

## 📚 کتابخونه‌های مفید

**Node.js:**
- `bcrypt` - پسورد هشینگ
- `express-session` - مدیریت Session
- `express-rate-limit` - محدودیت تعداد درخواست
- `passport` - فریمورک احراز هویت
- `speakeasy` - 2FA/TOTP

**Python:**
- `bcrypt` - پسورد هشینگ
- `django.contrib.auth` - احراز هویت Django
- `flask-login` - احراز هویت Flask

---

**یادت باشه: بهترین قفل هم اگه کلیدش ساده باشه، بی‌فایده است! 🔐✨**

**"پسورد قوی + MFA = خیال راحت! 💪📱"**
