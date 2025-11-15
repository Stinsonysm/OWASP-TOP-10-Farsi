# ⚙️🔧 آسیب‌پذیری شماره ۵: پیکربندی نادرست امنیتی (Security Misconfiguration)

## 🤔 این چیه به زبان ساده؟

تصور کن یه ماشین لوکس و گرون‌قیمت خریدی با بهترین سیستم امنیتی دنیا! قفل هوشمند، دزدگیر، دوربین، و همه چی! 🚗✨

ولی... یادت رفت در عقب رو ببندی! 🚪😅

یا این که سیستم دزدگیر رو روی رمز پیش‌فرض "0000" گذاشتی و عوضش نکردی!

همین اتفاق توی وب‌سایت‌ها هم می‌افته! همه چیز درست نصب شده ولی **تنظیماتش غلطه!**

## 🎮 مثال‌های بامزه

### مثال ۱: پنل ادمین که فراموش شده 🎛️

```javascript
// برنامه نویس یه پنل دیباگ درست کرده برای خودش
app.get('/debug/info', (req, res) => {
    res.json({
        environment: process.env,
        database: db.config,
        secrets: {
            apiKey: "sk-1234567890",
            dbPassword: "super_secret_password"
        }
    });
});

// بعد یادش رفت قبل از دپلوی حذفش کنه! 😱
// حالا همه می‌تونن ببینن!
```

### مثال ۲: پیام‌های خطای پرحرف 🗣️

```javascript
// ❌ بد - خطا خیلی چیزا رو لو میده!
app.post('/login', (req, res) => {
    try {
        const user = db.query(
            'SELECT * FROM users WHERE username = ? AND password = ?',
            [req.body.username, req.body.password]
        );

        if (!user) {
            throw new Error('User not found in database table "users"');
        }
    } catch (error) {
        // همه چی رو به کاربر نشون میده! 😱
        res.status(500).json({
            error: error.message,
            stack: error.stack,
            query: 'SELECT * FROM users WHERE...',
            database: 'MySQL 8.0.27',
            server: 'Ubuntu 20.04, Node.js 16.3.0'
        });
    }
});

// هکر الان می‌دونه:
// ✅ اسم جدول (users)
// ✅ نوع دیتابیس (MySQL)
// ✅ نسخه دیتابیس (8.0.27)
// ✅ سیستم عامل سرور (Ubuntu 20.04)
// حالا راحت‌تر می‌تونه حمله کنه! 🎯
```

### مثال ۳: فهرست فایل‌های باز 📂

```
# سرور Apache پیکربندی نشده:
https://mysite.com/uploads/

نتیجه:
Index of /uploads
📁 invoices/
📁 contracts/
📁 secrets/
📄 passwords.txt
📄 database_backup.sql

# اوه اوه! همه چی رو می‌شه دید! 😱
```

## 🎪 انواع پیکربندی نادرست

### ۱. تنظیمات پیش‌فرض 🏭

```bash
# MongoDB با تنظیمات پیش‌فرض
# بدون پسورد! بدون احراز هویت! 😱

mongo
> use admin
> db.users.find()  # همه کاربرا!
> db.dropDatabase()  # حذف کل دیتابیس! 💀

# چرا کار کرد؟ چون پیکربندی نشده بود!
```

**راه حل:**
```javascript
// mongod.conf
security:
  authorization: enabled

net:
  bindIp: 127.0.0.1  # فقط localhost
```

### ۲. پورت‌های اضافی باز 🚪🚪🚪

```bash
# چک کردن پورت‌های باز
nmap myserver.com

PORT      STATE SERVICE
22/tcp    open  ssh         ✅ خوبه
80/tcp    open  http        ✅ خوبه
443/tcp   open  https       ✅ خوبه
3306/tcp  open  mysql       ❌ بد! چرا MySQL از اینترنت قابل دسترسیه؟!
6379/tcp  open  redis       ❌ بد! Redis هم باز؟!
27017/tcp open  mongodb     ❌ بد! MongoDB هم؟!

# هکر: "یعنی چی این همه در باز؟! 🎉"
```

**راه حل:**
```bash
# Firewall تنظیم کن
ufw allow 22/tcp    # SSH
ufw allow 80/tcp    # HTTP
ufw allow 443/tcp   # HTTPS
ufw deny 3306/tcp   # MySQL رو ببند
ufw enable
```

### ۳. نسخه‌های نرم‌افزار رو نشون دادن 🏷️

```bash
# درخواست HTTP:
GET / HTTP/1.1
Host: mysite.com

# پاسخ سرور:
HTTP/1.1 200 OK
Server: Apache/2.4.29 (Ubuntu)
X-Powered-By: PHP/7.2.10

# هکر الان می‌دونه چه نسخه‌هایی داری!
# میره سرچ می‌کنه: "Apache 2.4.29 vulnerability"
# و باگ‌ها رو پیدا می‌کنه! 🔍
```

**راه حل:**
```javascript
// Express.js
app.disable('x-powered-by');

// Apache
# httpd.conf
ServerTokens Prod
ServerSignature Off

// Nginx
# nginx.conf
server_tokens off;
```

### ۴. CORS بدون محدودیت 🌍

```javascript
// ❌ بد - همه می‌تونن API تو رو صدا بزنن!
app.use(cors({
    origin: '*'  // همه! 😱
}));

// سایت هکر:
// https://evil-site.com
fetch('https://your-api.com/api/users')
    .then(r => r.json())
    .then(data => {
        // همه کاربرا رو دزدید! 💰
        sendToHacker(data);
    });
```

**راه حل:**
```javascript
// ✅ خوب - فقط سایت خودت
app.use(cors({
    origin: 'https://your-site.com',
    credentials: true
}));
```

### ۵. فایل‌های حساس قابل دسترس 📄

```
# این فایل‌ها نباید قابل دسترسی باشن:
https://mysite.com/.env
https://mysite.com/.git/config
https://mysite.com/config/database.yml
https://mysite.com/phpinfo.php
https://mysite.com/admin/config.php
```

**محتوای .env که لو رفته:**
```bash
DB_HOST=localhost
DB_USER=admin
DB_PASSWORD=SuperSecret123!
API_KEY=sk-1234567890abcdef
AWS_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCY
```

هکر: "ممنون که گذاشتی! 😈"

## 🛡️ چطور درستش کنیم؟

### ۱. چک‌لیست قبل از دپلوی 📋

```markdown
☐ تمام پسوردهای پیش‌فرض رو عوض کردم
☐ Debug mode رو خاموش کردم
☐ فایل‌های حساس (.env, .git) رو مخفی کردم
☐ HTTPS رو فعال کردم
☐ Firewall رو تنظیم کردم
☐ پورت‌های غیرضروری رو بستم
☐ Directory listing رو خاموش کردم
☐ پیام‌های خطا رو کلی کردم
☐ نسخه نرم‌افزار رو مخفی کردم
☐ CORS رو محدود کردم
☐ Security headers رو اضافه کردم
```

### ۲. محیط‌های مختلف جدا کن 🏗️

```javascript
// ❌ بد - همه چی تو یه محیط
const config = {
    debug: true,
    database: {
        host: 'production-db.com',
        password: 'real_password'
    }
};

// ✅ خوب - محیط‌های جدا
// config/development.js
module.exports = {
    debug: true,
    database: {
        host: 'localhost',
        password: 'dev_password'
    }
};

// config/production.js
module.exports = {
    debug: false,  // خاموش!
    database: {
        host: process.env.DB_HOST,
        password: process.env.DB_PASSWORD
    }
};

// استفاده:
const config = require(`./config/${process.env.NODE_ENV}`);
```

### ۳. Security Headers اضافه کن 🛡️

```javascript
// استفاده از Helmet برای Express
const helmet = require('helmet');

app.use(helmet());

// یا دستی:
app.use((req, res, next) => {
    // جلوگیری از XSS
    res.setHeader('X-XSS-Protection', '1; mode=block');

    // جلوگیری از clickjacking
    res.setHeader('X-Frame-Options', 'DENY');

    // Content type sniffing رو خاموش کن
    res.setHeader('X-Content-Type-Options', 'nosniff');

    // فقط HTTPS
    res.setHeader('Strict-Transport-Security', 'max-age=31536000');

    next();
});
```

### ۴. خطاها رو درست مدیریت کن ⚠️

```javascript
// ❌ بد - خطای کامل
app.use((err, req, res, next) => {
    res.status(500).json({
        error: err.message,
        stack: err.stack,
        details: err
    });
});

// ✅ خوب - خطای کلی برای کاربر
app.use((err, req, res, next) => {
    // لاگ کامل برای خودمون
    console.error('Error:', err);

    // پیام کلی برای کاربر
    res.status(500).json({
        error: 'مشکلی پیش اومد! لطفا بعدا تلاش کنید.'
    });
});
```

### ۵. از ابزارهای خودکار استفاده کن 🤖

```bash
# اسکن امنیتی خودکار
npm audit

# یا
npm audit fix

# برای Docker:
docker scan myimage:latest

# برای وب:
# استفاده از Mozilla Observatory
# https://observatory.mozilla.org/
```

## 🎯 چک‌لیست کامل امنیتی

### سرور:
- ✅ Firewall فعال و تنظیم شده
- ✅ فقط پورت‌های لازم باز
- ✅ SSH با کلید (نه پسورد)
- ✅ Fail2ban نصب شده
- ✅ آپدیت‌های امنیتی خودکار

### دیتابیس:
- ✅ پسورد قوی
- ✅ فقط از localhost قابل دسترسی
- ✅ User با کمترین دسترسی
- ✅ بکاپ منظم

### اپلیکیشن:
- ✅ Debug mode خاموش
- ✅ HTTPS فعال
- ✅ Security headers
- ✅ CORS محدود
- ✅ Rate limiting

### فایل‌ها:
- ✅ .env در .gitignore
- ✅ .git غیرقابل دسترس
- ✅ Directory listing خاموش
- ✅ فایل‌های حساس حذف شده

## 🎓 تمرین: پیدا کردن مشکل!

این تنظیمات Nginx رو ببین:

```nginx
server {
    listen 80;
    server_name mysite.com;

    # مشکل ۱: HTTPS نیست! 😱

    root /var/www/html;

    location / {
        try_files $uri $uri/ =404;
    }

    # مشکل ۲: Directory listing روشنه!
    autoindex on;

    # مشکل ۳: نسخه رو نشون میده!
    server_tokens on;

    # مشکل ۴: دسترسی به .git
    location ~ /\.git {
        # هیچی! یعنی قابل دسترسیه! 😱
    }
}
```

<details>
<summary>نسخه درست شده</summary>

```nginx
server {
    # ریدایرکت به HTTPS
    listen 80;
    server_name mysite.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name mysite.com;

    # SSL Certificate
    ssl_certificate /etc/ssl/certs/mysite.crt;
    ssl_certificate_key /etc/ssl/private/mysite.key;

    root /var/www/html;

    # مخفی کردن نسخه
    server_tokens off;

    # خاموش کردن directory listing
    autoindex off;

    # مسدود کردن فایل‌های حساس
    location ~ /\. {
        deny all;
        return 404;
    }

    location / {
        try_files $uri $uri/ =404;
    }

    # Security Headers
    add_header X-Frame-Options "DENY";
    add_header X-Content-Type-Options "nosniff";
    add_header X-XSS-Protection "1; mode=block";
    add_header Strict-Transport-Security "max-age=31536000";
}
```

</details>

## 🎬 داستان واقعی

سال ۲۰۱۷، یه شرکت بزرگ یه سرور AWS راه‌اندازی کرد. برنامه‌نویس تنظیمات رو روی "public" گذاشت فقط برای تست...

و یادش رفت برگردونه به "private"! 😱

نتیجه؟ **۱۴۳ میلیون رکورد مشتری لو رفت!** 💀

همش به خاطر یه تنظیم ساده! 🤦

## 📚 ابزارهای مفید

**برای چک کردن امنیت:**
- [Mozilla Observatory](https://observatory.mozilla.org/)
- [Security Headers](https://securityheaders.com/)
- [SSL Labs](https://www.ssllabs.com/ssltest/)

**برای Node.js:**
- `helmet` - Security headers
- `express-rate-limit` - Rate limiting
- `hpp` - جلوگیری از HTTP Parameter Pollution

---

**یادت باشه: بهترین قفل دنیا هم اگه باز بذاریش، فایده نداره! 🔒🔓**

**"خوب نوشتن کافی نیست، خوب تنظیم کردن هم مهمه!" ⚙️✨**
