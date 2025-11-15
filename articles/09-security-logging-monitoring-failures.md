# 📹🙈 آسیب‌پذیری شماره ۹: شکست لاگ‌گیری و نظارت امنیتی (Security Logging and Monitoring Failures)

## 🤔 این چیه به زبان ساده؟

تصور کن یه بانک بزرگ داری ولی... **هیچ دوربین امنیتی نداری!** 📹❌

یه دزد میاد، گاوصندوق رو می‌شکونه، پول رو برمی‌داره و میره.

فردا که میای می‌بینی پول نیست! 😱

ولی:
- نمی‌دونی کی اومده
- نمی‌دونی چجوری اومده
- نمی‌دونی چی برده
- نمی‌دونی چند نفر بودن
- اصلا نمی‌دونی کی این اتفاق افتاده!

همین اتفاق وقتی می‌افته که سایتت **لاگ نداره** یا **مانیتور نمیشه**!

## 🎮 مثال‌های بامزه

### مثال ۱: سایت بی‌خبر! 📱

```javascript
// سیستم لاگین
app.post('/login', async (req, res) => {
    const user = await checkPassword(req.body.username, req.body.password);

    if (user) {
        req.session.userId = user.id;
        res.send('خوش اومدی! ✅');
    } else {
        res.send('اطلاعات اشتباهه! ❌');
        // و... هیچی دیگه! 🤷
        // هیچ لاگی نمی‌گیریم!
    }
});

// هکر داره ۱۰,۰۰۰ بار سعی می‌کنه لاگین کنه
// و ما اصلا نمی‌دونیم! 😴
```

**چی میشه؟**
- هکر ۱۰,۰۰۰ بار Brute Force می‌کنه
- موفق میشه و وارد حساب کاربری میشه
- فایل‌ها رو می‌دزده
- پسورد کاربرا رو عوض می‌کنه
- و تو اصلا خبر نداری تا وقتی کاربر شکایت کنه! 😱

### مثال ۲: لاگ‌های بی‌فایده 📝

```javascript
// لاگ گرفتن اشتباه!
app.post('/login', async (req, res) => {
    console.log('Someone tried to login');
    // همین! 🤷

    // این لاگ چه فایده‌ای داره؟
    // - کی؟
    // - از کجا؟
    // - موفق بود یا نه؟
    // - چند بار تلاش کرده؟
    // هیچکدوم رو نمی‌دونیم! 😅
});
```

### مثال ۳: لاگ‌های پر از اطلاعات حساس! 🔓

```javascript
// ❌ خطرناک!
app.post('/login', async (req, res) => {
    console.log('Login attempt:', {
        username: req.body.username,
        password: req.body.password,  // 😱😱😱
        ip: req.ip
    });

    // حالا پسوردها توی لاگ فایل نوشته شده!
    // اگه کسی لاگ‌ها رو ببینه، همه پسوردا رو داره! 💀
});
```

### مثال ۴: Alert نداریم! 🔕

```javascript
// فایل‌های حساس حذف شدن!
app.delete('/api/users/all', (req, res) => {
    db.deleteAllUsers();  // همه کاربرا پاک شدن!
    res.send('Done!');

    // ولی هیچ Alert یا Notification نفرستادیم! 🤷
    // شاید چند روز طول بکشه تا متوجه بشیم!
});
```

## 🎪 چرا خطرناکه؟

### ۱. نمی‌دونی هک شدی! 🙈

```
Day 1: هکر وارد سیستم شد
Day 5: هکر داره دیتا می‌دزده
Day 30: هکر Backdoor گذاشت
Day 60: همه چی رو کپی کرد
Day 90: کاربر شکایت کرد که حسابش خالی شده!
Day 91: تو متوجه شدی! 😱

متوسط زمان تشخیص هک: ۲۸۰ روز! 🤯
```

### ۲. نمی‌تونی تحقیق کنی (Forensics) 🔍

```javascript
// مدیر: "کی این فایل رو حذف کرد؟"
// تو: "نمی‌دونم! 🤷"

// مدیر: "چجوری هک شدیم؟"
// تو: "نمی‌دونم! 🤷"

// مدیر: "کی این اتفاق افتاد؟"
// تو: "نمی‌دونم! 🤷"

// مدیر: "پس چیکار کنیم؟"
// تو: "نمی‌دونم! 🤷"

// مدیر: "اخراجی! 💼"
```

### ۳. نمی‌تونی جلوشو بگیری ⛔

بدون Monitoring:
- نمی‌دونی الان داره چی میشه
- نمی‌تونی فوری عکس‌العمل نشون بدی
- نمی‌تونی هکر رو Block کنی
- نمی‌تونی ضرر رو کم کنی

## 🛡️ چطور درستش کنیم؟

### ۱. لاگ کامل و مفید بگیر! 📝

```javascript
const winston = require('winston');

// Logger تنظیم کن
const logger = winston.createLogger({
    level: 'info',
    format: winston.format.json(),
    transports: [
        new winston.transports.File({ filename: 'error.log', level: 'error' }),
        new winston.transports.File({ filename: 'combined.log' })
    ]
});

// لاگین
app.post('/login', async (req, res) => {
    const startTime = Date.now();

    try {
        const user = await checkPassword(req.body.username, req.body.password);

        if (user) {
            // ✅ موفق
            logger.info('Login successful', {
                event: 'login_success',
                username: req.body.username,
                userId: user.id,
                ip: req.ip,
                userAgent: req.get('user-agent'),
                timestamp: new Date().toISOString(),
                duration: Date.now() - startTime
            });

            req.session.userId = user.id;
            res.send('خوش اومدی! ✅');
        } else {
            // ❌ ناموفق
            logger.warn('Login failed', {
                event: 'login_failed',
                username: req.body.username,
                // توجه: پسورد رو لاگ نمی‌کنیم! 🚫
                ip: req.ip,
                userAgent: req.get('user-agent'),
                timestamp: new Date().toISOString(),
                duration: Date.now() - startTime
            });

            res.send('اطلاعات اشتباهه! ❌');
        }
    } catch (error) {
        // 🚨 خطا
        logger.error('Login error', {
            event: 'login_error',
            username: req.body.username,
            error: error.message,
            stack: error.stack,
            ip: req.ip,
            timestamp: new Date().toISOString()
        });

        res.status(500).send('مشکلی پیش اومد!');
    }
});
```

### ۲. چیزایی که باید لاگ بگیری 📋

```javascript
// Security Events
const SECURITY_EVENTS = {
    // Authentication
    LOGIN_SUCCESS: 'login_success',
    LOGIN_FAILED: 'login_failed',
    LOGOUT: 'logout',
    PASSWORD_RESET: 'password_reset',
    ACCOUNT_LOCKED: 'account_locked',

    // Authorization
    ACCESS_DENIED: 'access_denied',
    PRIVILEGE_ESCALATION_ATTEMPT: 'privilege_escalation_attempt',

    // Data Access
    SENSITIVE_DATA_ACCESS: 'sensitive_data_access',
    BULK_DATA_EXPORT: 'bulk_data_export',
    DATA_MODIFICATION: 'data_modification',
    DATA_DELETION: 'data_deletion',

    // Suspicious Activity
    MULTIPLE_FAILED_LOGINS: 'multiple_failed_logins',
    UNUSUAL_TIME_ACCESS: 'unusual_time_access',
    UNUSUAL_LOCATION_ACCESS: 'unusual_location_access',
    SQL_INJECTION_ATTEMPT: 'sql_injection_attempt',
    XSS_ATTEMPT: 'xss_attempt',

    // System
    CONFIG_CHANGE: 'config_change',
    ADMIN_ACTION: 'admin_action',
    BACKUP_CREATED: 'backup_created',
    SERVICE_START: 'service_start',
    SERVICE_STOP: 'service_stop'
};

// مثال: دسترسی به اطلاعات حساس
app.get('/api/users/:id/credit-card', authenticateUser, (req, res) => {
    logger.warn('Sensitive data access', {
        event: SECURITY_EVENTS.SENSITIVE_DATA_ACCESS,
        resource: 'credit_card',
        userId: req.user.id,
        targetUserId: req.params.id,
        ip: req.ip,
        timestamp: new Date().toISOString()
    });

    // ...
});
```

### ۳. Alert و Monitoring 🚨

```javascript
// تشخیص Brute Force
const loginAttempts = new Map();

app.post('/login', async (req, res) => {
    const ip = req.ip;
    const attempts = loginAttempts.get(ip) || [];

    // اضافه کردن تلاش جدید
    attempts.push(Date.now());

    // فقط ۵ دقیقه اخیر رو نگه دار
    const recentAttempts = attempts.filter(time =>
        Date.now() - time < 5 * 60 * 1000
    );

    loginAttempts.set(ip, recentAttempts);

    // اگه بیشتر از ۱۰ تلاش تو ۵ دقیقه!
    if (recentAttempts.length > 10) {
        // 🚨 Alert!
        logger.error('Brute force detected!', {
            event: 'brute_force_attack',
            ip: ip,
            attempts: recentAttempts.length,
            timestamp: new Date().toISOString()
        });

        // اطلاع‌رسانی به ادمین
        await sendAlertToAdmin({
            type: 'BRUTE_FORCE',
            message: `Brute force attack from IP: ${ip}`,
            severity: 'HIGH'
        });

        // SMS به تیم امنیت
        await sendSMS(SECURITY_TEAM_PHONE, `⚠️ Brute force attack detected from ${ip}`);

        // Block کردن IP
        await blockIP(ip);

        return res.status(429).send('خیلی زیاد تلاش کردی! Block شدی! 🚫');
    }

    // ادامه لاگین معمولی...
});
```

### ۴. تشخیص الگوهای مشکوک 🕵️

```javascript
// تشخیص فعالیت غیرعادی
async function detectAnomalies(userId, action) {
    const user = await db.getUser(userId);

    // چک کردن زمان
    const hour = new Date().getHours();
    if (hour >= 2 && hour <= 6) {
        logger.warn('Unusual time access', {
            event: 'unusual_time_access',
            userId: userId,
            action: action,
            hour: hour,
            timestamp: new Date().toISOString()
        });
    }

    // چک کردن لوکیشن
    const currentIP = getCurrentIP();
    const lastIP = user.lastLoginIP;
    const distance = await getDistanceBetweenIPs(currentIP, lastIP);

    if (distance > 1000) { // بیشتر از ۱۰۰۰ کیلومتر!
        logger.warn('Unusual location access', {
            event: 'unusual_location_access',
            userId: userId,
            currentIP: currentIP,
            lastIP: lastIP,
            distance: distance,
            timestamp: new Date().toISOString()
        });

        // Alert به کاربر
        await sendEmail(user.email, `
            یه لاگین جدید از لوکیشن جدید!
            IP: ${currentIP}
            اگه شما نبودید، سریع پسوردتون رو عوض کنید!
        `);
    }
}
```

### ۵. Log Rotation و Storage 💾

```javascript
// Winston با Log Rotation
const winston = require('winston');
require('winston-daily-rotate-file');

const logger = winston.createLogger({
    transports: [
        new winston.transports.DailyRotateFile({
            filename: 'logs/application-%DATE%.log',
            datePattern: 'YYYY-MM-DD',
            maxSize: '20m',      // حداکثر سایز هر فایل
            maxFiles: '30d',     // نگه داشتن ۳۰ روز
            compress: true       // فشرده‌سازی لاگ‌های قدیمی
        })
    ]
});

// یا ارسال به سرویس مرکزی
const loggingWinston = require('@google-cloud/logging-winston');

logger.add(new loggingWinston.LoggingWinston({
    projectId: 'your-project-id',
    keyFilename: '/path/to/key.json',
}));

// یا Elasticsearch
const { ElasticsearchTransport } = require('winston-elasticsearch');

logger.add(new ElasticsearchTransport({
    level: 'info',
    clientOpts: { node: 'http://localhost:9200' }
}));
```

### ۶. Dashboard و Visualization 📊

```javascript
// ارسال متریک‌ها به Prometheus
const promClient = require('prom-client');

const loginCounter = new promClient.Counter({
    name: 'login_attempts_total',
    help: 'Total login attempts',
    labelNames: ['status']  // success, failed
});

app.post('/login', async (req, res) => {
    const user = await checkPassword(req.body.username, req.body.password);

    if (user) {
        loginCounter.inc({ status: 'success' });
        // ...
    } else {
        loginCounter.inc({ status: 'failed' });
        // ...
    }
});

// Metrics endpoint برای Prometheus
app.get('/metrics', async (req, res) => {
    res.set('Content-Type', promClient.register.contentType);
    res.end(await promClient.register.metrics());
});

// حالا می‌تونی تو Grafana dashboard بسازی! 📈
```

## 🎯 چک‌لیست کامل

### چیزایی که باید لاگ بگیری:
- ✅ همه تلاش‌های لاگین (موفق و ناموفق)
- ✅ تغییرات مهم (حذف، ویرایش)
- ✅ دسترسی به اطلاعات حساس
- ✅ تغییرات تنظیمات
- ✅ خطاها و Exception ها
- ✅ فعالیت‌های ادمین
- ✅ تلاش‌های Access Denied

### اطلاعاتی که باید توی لاگ باشه:
- ✅ چه اتفاقی افتاد (Event Type)
- ✅ کی افتاد (Timestamp)
- ✅ کی انجامش داد (User ID)
- ✅ از کجا (IP, Location)
- ✅ روی چی (Resource)
- ✅ نتیجه چی شد (Success/Failure)

### چیزایی که نباید لاگ بگیری:
- 🚫 پسوردها
- 🚫 اطلاعات کارت بانکی
- 🚫 Session Tokens
- 🚫 API Keys
- 🚫 اطلاعات خیلی شخصی (بدون دلیل)

### Monitoring:
- ✅ Real-time alerting
- ✅ تشخیص Brute Force
- ✅ تشخیص SQL Injection
- ✅ تشخیص الگوهای غیرعادی
- ✅ Dashboard برای نمایش

### Storage:
- ✅ Log Rotation
- ✅ Backup منظم
- ✅ نگهداری حداقل ۹۰ روز
- ✅ محافظت از لاگ‌ها (خود لاگ‌ها هم باید امن باشن!)

## 🎓 تمرین: لاگ خوب یا بد؟

```javascript
// مثال ۱
console.log('User login failed');
```

<details>
<summary>جواب</summary>

❌ بد!

**مشکلات:**
- کاربر کدوم؟
- زمان دقیق؟
- IP چی بود؟
- چند بار تلاش کرده؟

**نسخه خوب:**
```javascript
logger.warn('Login failed', {
    event: 'login_failed',
    username: username,
    ip: req.ip,
    userAgent: req.get('user-agent'),
    timestamp: new Date().toISOString(),
    attemptNumber: getUserAttempts(username)
});
```

</details>

---

```javascript
// مثال ۲
logger.info('Login attempt', {
    username: username,
    password: password,  // 🚨
    ip: req.ip
});
```

<details>
<summary>جواب</summary>

❌ خیلی بد!

**مشکل:** پسورد رو لاگ می‌کنه! 😱

اگه لاگ فایل لو بره، همه پسوردا رو دارن!

**نسخه خوب:**
```javascript
logger.info('Login attempt', {
    username: username,
    // پسورد نمی‌ذاریم! 🚫
    ip: req.ip,
    timestamp: new Date().toISOString()
});
```

</details>

## 🎬 داستان واقعی: Equifax (2017)

Equifax هک شد و اطلاعات ۱۴۷ میلیون نفر لو رفت.

**مشکل:** هکرا **۷۶ روز** توی سیستم بودن و داده می‌دزدیدن!

**چرا تشخیص ندادن؟**
- Monitoring ضعیف بود
- Alert ها کار نمی‌کردن
- لاگ‌ها بررسی نمیشدن

**نتیجه:**
- ۱۴۷ میلیون نفر اطلاعاتشون لو رفت
- شرکت $۷۰۰ میلیون جریمه شد
- CEO استعفا داد

**اگه Monitoring داشتن:**
- روز اول متوجه میشدن
- هکر رو Block می‌کردن
- فقط چند رکورد لو می‌رفت

## 📚 ابزارهای مفید

**Logging:**
- Winston (Node.js)
- Bunyan (Node.js)
- Python logging
- Log4j (Java) - ولی آپدیت باشه!

**Monitoring:**
- Prometheus + Grafana
- Datadog
- New Relic
- Splunk

**SIEM (Security Information and Event Management):**
- ELK Stack (Elasticsearch, Logstash, Kibana)
- Graylog
- Wazuh

**Alerting:**
- PagerDuty
- OpsGenie
- Custom Webhooks

---

**یادت باشه: اگه ندونی چی داره میشه، نمی‌تونی محافظتش کنی! چشم و گوشت رو باز نگه دار! 👀👂**

**"You can't protect what you can't see!" (نمی‌تونی از چیزی که نمی‌بینیش محافظت کنی!)**
