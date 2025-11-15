# 🎭🌐 آسیب‌پذیری شماره ۱۰: جعل درخواست سمت سرور (Server-Side Request Forgery - SSRF)

## 🤔 این چیه به زبان ساده؟

تصور کن تو یه شرکت بزرگ کار می‌کنی. یه نفر خارجی نمی‌تونه وارد ساختمون بشه.

ولی یه پیک موتوری میاد و می‌گه: "من باید یه نامه از طبقه ۱۰ بیارم براتون!" 📨

تو که نمی‌دونی چی توی نامه‌س، بهش میگی: "باشه، برو بیار!"

اون میره بالا، **به جای آوردن نامه، در گاوصندوق رو باز می‌کنه و پول رو برمی‌داره!** 💰😱

بعد برمی‌گرده پایین و میگه: "نامه‌ای نبود!"

تو به عنوان یه **نماینده داخلی** استفاده شدی برای کاری که خارجی‌ها نمی‌تونن انجام بدن!

همین اتفاق در SSRF می‌افته! هکر از **سرور تو** استفاده می‌کنه برای درخواست‌های مخرب!

## 🎮 مثال بامزه: دانلودر تصویر 🖼️

یه سایت داری که کاربرا می‌تونن عکس از URL دانلود کنن:

```javascript
// کاربر URL عکس رو می‌ده، سرور دانلودش می‌کنه
app.get('/download-image', async (req, res) => {
    const imageUrl = req.query.url;

    // ❌ بدون چک کردن، درخواست می‌فرستیم!
    const response = await fetch(imageUrl);
    const image = await response.buffer();

    res.send(image);
});

// استفاده معمولی:
// /download-image?url=https://example.com/cat.jpg
// ✅ خوبه!
```

ولی هکر می‌تونه این کارا رو بکنه:

```javascript
// 😈 حمله ۱: خوندن فایل‌های داخلی
/download-image?url=file:///etc/passwd
// سرور فایل سیستمی رو می‌خونه و برمی‌گردونه! 💀

// 😈 حمله ۲: دسترسی به سرویس‌های داخلی
/download-image?url=http://localhost:8080/admin
// سرور به پنل ادمین داخلی دسترسی پیدا می‌کنه! 🚨

// 😈 حمله ۳: دسترسی به Metadata (AWS)
/download-image?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/
// کلیدهای AWS رو می‌دزده! 🔑💸

// 😈 حمله ۴: Port Scanning
/download-image?url=http://192.168.1.1:22
/download-image?url=http://192.168.1.1:80
/download-image?url=http://192.168.1.1:3306
// شبکه داخلی رو اسکن می‌کنه! 🔍
```

## 🎪 انواع حملات SSRF

### ۱. دسترسی به سرویس‌های داخلی 🏠

```javascript
// سرور تو:
// - Redis روی localhost:6379 (بدون پسورد!)
// - MongoDB روی localhost:27017 (بدون احراز هویت!)
// - MySQL روی localhost:3306

// هکر:
/api/fetch?url=http://localhost:6379
// دسترسی به Redis! می‌تونه دستور بده!

/api/fetch?url=http://localhost:27017
// دسترسی به MongoDB! می‌تونه دیتا بخونه!
```

### ۲. خوندن Metadata کلود ☁️

```javascript
// AWS EC2 Instance
/api/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/role-name

// نتیجه:
{
    "AccessKeyId": "ASIA...",
    "SecretAccessKey": "wJalrXUtnFEMI...",
    "Token": "IQoJb3JpZ2..."
}

// حالا هکر کلید AWS داره! 🔑💀
// می‌تونه:
// - S3 bucket ها رو ببینه
// - EC2 راه بندازه
// - همه چیز رو حذف کنه!
```

### ۳. Port Scanning و Network Discovery 🔍

```javascript
// هکر یه اسکریپت می‌نویسه:
const ips = generateIPs('192.168.1.0/24');
const ports = [22, 80, 443, 3306, 6379, 27017];

for (const ip of ips) {
    for (const port of ports) {
        const url = `http://${ip}:${port}`;
        const response = await fetch(`/api/fetch?url=${url}`);

        if (response.status !== 'error') {
            console.log(`Found: ${ip}:${port}`); // 🎯
        }
    }
}

// حالا می‌دونه کدوم سرورا روشنن و چه پورتایی دارن!
```

### ۴. Blind SSRF 🙈

```javascript
// گاهی سرور نتیجه رو برنمی‌گردونه
// ولی درخواست رو می‌فرسته!

app.post('/webhook', async (req, res) => {
    const webhookUrl = req.body.url;

    // درخواست می‌فرستیم ولی نتیجه رو به کاربر نمیدیم
    await fetch(webhookUrl);

    res.send('Webhook queued!'); // همین!
});

// هکر می‌تونه:
// 1. سرور خودش رو بذاره: evil.com
// 2. URL بده: /webhook با url=http://evil.com
// 3. IP سرور تو رو می‌بینه
// 4. می‌فهمه سرور تو داره کار می‌کنه

// یا اینکه:
/webhook?url=http://localhost:6379/FLUSHALL
// Redis رو خالی می‌کنه! 💀
```

## 🛡️ چطور جلوشو بگیریم؟

### ۱. Whitelist استفاده کن! ✅

```javascript
// ❌ بد - همه URL ها قابل قبول
app.get('/fetch', async (req, res) => {
    const url = req.query.url;
    const response = await fetch(url);
    // ...
});

// ✅ خوب - فقط domain های مجاز
const ALLOWED_DOMAINS = [
    'example.com',
    'cdn.example.com',
    'images.example.com'
];

app.get('/fetch', async (req, res) => {
    const url = new URL(req.query.url);

    // چک کردن domain
    const domain = url.hostname;
    if (!ALLOWED_DOMAINS.includes(domain)) {
        return res.status(403).send('Domain غیرمجاز! 🚫');
    }

    // چک کردن Protocol
    if (url.protocol !== 'https:') {
        return res.status(403).send('فقط HTTPS! 🔒');
    }

    const response = await fetch(url.toString());
    // ...
});
```

### ۲. IP های خصوصی رو Block کن! 🚫

```javascript
const ipaddr = require('ipaddr.js');

function isPrivateIP(ip) {
    try {
        const addr = ipaddr.parse(ip);

        // IPv4 Private Ranges
        if (addr.kind() === 'ipv4') {
            return (
                addr.match(ipaddr.parseCIDR('10.0.0.0/8')) ||      // 10.x.x.x
                addr.match(ipaddr.parseCIDR('172.16.0.0/12')) ||   // 172.16-31.x.x
                addr.match(ipaddr.parseCIDR('192.168.0.0/16')) ||  // 192.168.x.x
                addr.match(ipaddr.parseCIDR('127.0.0.0/8')) ||     // localhost
                addr.match(ipaddr.parseCIDR('169.254.0.0/16'))     // Link-local
            );
        }

        // IPv6 Private
        return addr.range() !== 'unicast';
    } catch {
        return true; // اگه پارس نشد، رد کن!
    }
}

app.get('/fetch', async (req, res) => {
    const url = new URL(req.query.url);

    // Resolve کردن domain به IP
    const dns = require('dns').promises;
    const ips = await dns.resolve4(url.hostname);

    // چک کردن همه IP ها
    for (const ip of ips) {
        if (isPrivateIP(ip)) {
            return res.status(403).send('IP خصوصی ممنوع! 🚫');
        }
    }

    // الان امنه!
    const response = await fetch(url.toString());
    // ...
});
```

### ۳. از Protocol های خطرناک جلوگیری کن 🔒

```javascript
const SAFE_PROTOCOLS = ['http:', 'https:'];
const DANGEROUS_PROTOCOLS = [
    'file:',       // خوندن فایل محلی
    'ftp:',        // FTP
    'gopher:',     // پروتکل قدیمی
    'dict:',       // Dictionary
    'sftp:',       // SFTP
    'tftp:',       // TFTP
    'ldap:'        // LDAP
];

function validateURL(urlString) {
    let url;
    try {
        url = new URL(urlString);
    } catch {
        throw new Error('URL نامعتبر!');
    }

    // چک کردن Protocol
    if (!SAFE_PROTOCOLS.includes(url.protocol)) {
        throw new Error(`Protocol ${url.protocol} مجاز نیست! فقط HTTP/HTTPS!`);
    }

    // چک کردن @ (username در URL)
    if (url.username || url.password) {
        throw new Error('Username/Password توی URL ممنوع!');
    }

    return url;
}

app.get('/fetch', async (req, res) => {
    try {
        const url = validateURL(req.query.url);
        // ادامه...
    } catch (error) {
        res.status(400).send(error.message);
    }
});
```

### ۴. DNS Rebinding رو جلوگیری کن 🔄

```javascript
// مشکل: هکر یه domain داره که:
// اول: evil.com -> 1.2.3.4 (IP عمومی) ✅
// بعد: evil.com -> 127.0.0.1 (localhost) 😱

// راه حل: چک کردن IP هم موقع Resolve و هم موقع Connect

const dns = require('dns').promises;

async function safeFetch(urlString) {
    const url = new URL(urlString);

    // مرحله ۱: Resolve و چک IP
    const ips = await dns.resolve4(url.hostname);

    for (const ip of ips) {
        if (isPrivateIP(ip)) {
            throw new Error('IP خصوصی! 🚫');
        }
    }

    // مرحله ۲: Force کردن IP (نه domain!)
    // تا DNS Rebinding کار نکنه
    const safeUrl = url.toString().replace(url.hostname, ips[0]);

    // مرحله ۳: درخواست با IP
    const response = await fetch(safeUrl, {
        headers: {
            'Host': url.hostname  // ولی Host header رو نگه می‌داریم
        }
    });

    return response;
}
```

### ۵. Network Segmentation استفاده کن 🏗️

```bash
# سرورهای وب رو از سرویس‌های داخلی جدا کن

# Web Server:
# - فقط به اینترنت دسترسی داره
# - به Redis/MySQL/MongoDB دسترسی نداره!

# Application Server:
# - به Database دسترسی داره
# - از اینترنت مجزاست

# Database Server:
# - فقط از Application Server قابل دسترسیه
# - از اینترنت کاملا جداست

# حالا حتی اگه SSRF هم بشه، نمی‌تونه به دیتابیس برسه! 💪
```

### ۶. Response رو فیلتر کن 🧹

```javascript
// حتی اگه درخواست رو فرستادی، response رو چک کن!

app.get('/fetch', async (req, res) => {
    // ... validation ...

    const response = await fetch(url);

    // فقط Content-Type های مجاز
    const contentType = response.headers.get('content-type');
    const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];

    if (!allowedTypes.some(type => contentType.includes(type))) {
        return res.status(400).send('فقط عکس مجازه! 🖼️');
    }

    // محدود کردن سایز (جلوگیری از DoS)
    const MAX_SIZE = 5 * 1024 * 1024; // 5 MB
    if (response.headers.get('content-length') > MAX_SIZE) {
        return res.status(400).send('خیلی بزرگه! حداکثر ۵ مگابایت! 📏');
    }

    const image = await response.buffer();
    res.send(image);
});
```

## 🎯 چک‌لیست امنیتی

**Validation:**
- ✅ Whitelist domain های مجاز
- ✅ فقط HTTP/HTTPS (نه file://, gopher://, etc.)
- ✅ IP های خصوصی رو رد کن
- ✅ localhost رو رد کن (127.0.0.1, ::1)
- ✅ از @ توی URL جلوگیری کن

**DNS:**
- ✅ DNS Rebinding رو جلوگیری کن
- ✅ IP رو هم موقع Resolve و هم موقع Connect چک کن
- ✅ از DNS Cache استفاده کن

**Network:**
- ✅ Network Segmentation
- ✅ Firewall rules
- ✅ سرویس‌های داخلی فقط از localhost قابل دسترسی

**Response:**
- ✅ Content-Type رو چک کن
- ✅ سایز رو محدود کن
- ✅ Timeout تنظیم کن

**Cloud:**
- ✅ AWS Metadata رو محدود کن
- ✅ از IMDSv2 استفاده کن (AWS)
- ✅ Network Policies تنظیم کن

## 🎓 تمرین: آسیب‌پذیر یا نه؟

```javascript
// مثال ۱
app.get('/proxy', async (req, res) => {
    const url = req.query.url;

    if (!url.startsWith('http://') && !url.startsWith('https://')) {
        return res.status(400).send('فقط HTTP/HTTPS!');
    }

    const response = await fetch(url);
    res.send(await response.text());
});
```

<details>
<summary>جواب</summary>

❌ آسیب‌پذیره!

**مشکلات:**
1. IP های خصوصی رو چک نمی‌کنه
2. localhost رو چک نمی‌کنه
3. Domain رو whitelist نمی‌کنه
4. DNS Rebinding ممکنه

**حملات ممکن:**
```javascript
/proxy?url=http://localhost:6379
/proxy?url=http://192.168.1.1/admin
/proxy?url=http://169.254.169.254/latest/meta-data/
```

**نسخه امن:**
```javascript
const ALLOWED_DOMAINS = ['api.example.com'];

app.get('/proxy', async (req, res) => {
    let url;
    try {
        url = new URL(req.query.url);
    } catch {
        return res.status(400).send('URL نامعتبر!');
    }

    // Protocol
    if (url.protocol !== 'https:') {
        return res.status(400).send('فقط HTTPS!');
    }

    // Domain
    if (!ALLOWED_DOMAINS.includes(url.hostname)) {
        return res.status(403).send('Domain غیرمجاز!');
    }

    // IP
    const dns = require('dns').promises;
    const ips = await dns.resolve4(url.hostname);
    for (const ip of ips) {
        if (isPrivateIP(ip)) {
            return res.status(403).send('IP خصوصی ممنوع!');
        }
    }

    const response = await fetch(url.toString(), { timeout: 5000 });
    res.send(await response.text());
});
```

</details>

## 🎬 داستان واقعی: Capital One (2019) 🏦

یکی از بزرگترین نقض‌های امنیتی تاریخ!

**چی شد:**
1. Capital One از AWS استفاده می‌کرد
2. یه Firewall اشتباه تنظیم شده بود
3. هکر از SSRF استفاده کرد و به AWS Metadata دسترسی پیدا کرد:
   ```
   http://169.254.169.254/latest/meta-data/iam/security-credentials/
   ```
4. کلیدهای AWS رو دزدید
5. به S3 bucket دسترسی پیدا کرد
6. اطلاعات **۱۰۶ میلیون** مشتری رو دزدید! 😱💔

**اگه جلوگیری می‌کردن:**
- ✅ Metadata endpoint رو Block می‌کردن
- ✅ از IMDSv2 استفاده می‌کردن (نیاز به Token داره)
- ✅ IP های خصوصی رو رد می‌کردن

## 📚 ابزارهای مفید

**Testing:**
- [SSRFmap](https://github.com/swisskyrepo/SSRFmap)
- Burp Suite
- [SSRF Sheriff](https://github.com/teknogeek/ssrf-sheriff)

**Prevention:**
- [SafeCurl](https://github.com/wkcaj/safecurl) - کتابخونه امن برای cURL
- AWS IMDSv2 - Metadata امن

**Validation Libraries:**
- `validator.js` - URL validation
- `ipaddr.js` - IP validation

## 🔧 پیکربندی AWS برای جلوگیری از SSRF

```bash
# محدود کردن دسترسی به Metadata
# در Security Group:
# بسته شدن دسترسی به 169.254.169.254

# استفاده از IMDSv2 (نیاز به Token)
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567890abcdef0 \
    --http-tokens required \
    --http-endpoint enabled

# حالا برای دسترسی به Metadata باید Token بگیری:
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" \
    -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`

# و با Token درخواست بفرستی:
curl -H "X-aws-ec2-metadata-token: $TOKEN" \
    http://169.254.169.254/latest/meta-data/

# SSRF معمولی نمی‌تونه Token بگیره! ✅
```

---

**یادت باشه: هیچ‌وقت به کاربر اجازه نده URL دلخواه بده! اگه لازمه، خیلی سخت‌گیرانه چک کن! 🔍🚫**

**"سرور تو یه نماینده است، مراقب باش ازش برای کارای بد استفاده نشه! 🤖✋"**

---

## 🎉 تبریک! همه OWASP Top 10 رو یاد گرفتی!

حالا می‌دونی:
1. 🚪 Broken Access Control
2. 🔐 Cryptographic Failures
3. 💉 Injection
4. 🏗️ Insecure Design
5. ⚙️ Security Misconfiguration
6. 📦 Vulnerable Components
7. 🎭 Authentication Failures
8. 📦 Integrity Failures
9. 📹 Logging Failures
10. 🌐 SSRF

**قدم بعدی:** برو تمرین کن! سایتت رو امن کن! و همیشه یادت باشه: **امنیت یه فرآیند است، نه یه محصول!** 🛡️✨
