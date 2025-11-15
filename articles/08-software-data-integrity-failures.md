# 📦🔓 آسیب‌پذیری شماره ۸: شکست یکپارچگی نرم‌افزار و داده (Software and Data Integrity Failures)

## 🤔 این چیه به زبان ساده؟

تصور کن می‌ری یه داروخونه و می‌خوای آسپرین بخری. داروساز یه جعبه بهت میده.

حالا دو سناریو:

**سناریو ۱:** جعبه بازه، سر‌درش نیست، معلوم نیست کسی توش دست کرده یا نه! 📦❌

**سناریو ۲:** جعبه سربسته است، پلمپ داره، تاریخ انقضا داره، کد رهگیری داره! 📦✅

همین مسئله توی دنیای نرم‌افزار هم هست! **یکپارچگی** یعنی اطمینان از این که:
- کد یا داده دستکاری نشده
- از منبع معتبر اومده
- توی مسیر تغییر نکرده

## 🎮 مثال‌های بامزه

### مثال ۱: آپدیت بدون امضا! 🎭

```javascript
// برنامه موبایل چک می‌کنه آپدیت جدید هست؟
app.get('/check-update', (req, res) => {
    res.json({
        version: '2.0.0',
        downloadUrl: 'https://myserver.com/app-v2.apk'
    });
});

// برنامه دانلود می‌کنه و نصب می‌کنه!
// ولی چک نمی‌کنه که آیا این فایل واقعا از شرکت ماست؟! 😱

// هکر می‌تونه:
// ۱. DNS رو Hijack کنه
// ۲. یه APK بدافزار بذاره جای فایل اصلی
// ۳. کاربر نصبش می‌کنه و... هک شد! 💀
```

**راه حل:**
```javascript
// آپدیت با checksum و امضا دیجیتال
app.get('/check-update', (req, res) => {
    res.json({
        version: '2.0.0',
        downloadUrl: 'https://myserver.com/app-v2.apk',
        sha256: '9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08',
        signature: 'MEUCIQDx...' // امضا شده با کلید خصوصی شرکت
    });
});

// برنامه:
// ۱. فایل رو دانلود می‌کنه
// ۲. SHA256 رو حساب می‌کنه و چک می‌کنه
// ۳. امضا رو با کلید عمومی شرکت Verify می‌کنه
// ۴. اگه همه چی OK بود، نصب می‌کنه ✅
```

### مثال ۲: Deserialization خطرناک! 🧨

```javascript
// دریافت داده از کاربر
app.post('/save-preferences', (req, res) => {
    // کاربر یه JSON فرستاده
    const data = req.body.data;

    // Deserialize می‌کنیم
    const preferences = deserialize(data);

    // ذخیره می‌کنیم
    db.save(preferences);
});

// مشکل: کاربر می‌تونه هر چی بخواد بفرسته!
```

**حمله:**
```javascript
// هکر یه Object سمی می‌فرسته:
{
    "theme": "dark",
    "__proto__": {
        "isAdmin": true
    }
}

// Prototype Pollution! 😱
// حالا همه Object های جدید isAdmin: true دارن!
```

### مثال ۳: CI/CD بدون امنیت 🏗️

```yaml
# GitHub Actions
name: Deploy

on: [push]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      # ❌ خطرناک!
      - name: Install dependencies
        run: curl https://some-random-site.com/install.sh | bash

      # ❌ خطرناک!
      - name: Deploy
        run: npm install && npm run deploy
```

**مشکل:**
- اون اسکریپت رو از یه سایت رندوم دانلود می‌کنیم!
- چک نمی‌کنیم که دستکاری نشده باشه!
- اگه اون سایت هک بشه، ما هم هک میشیم! 💀

## 🎪 انواع حملات

### ۱. Supply Chain Attack ⛓️

```bash
# یه کتابخونه محبوب: "cool-library"
npm install cool-library

# کتابخونه داخلش از "helper-utils" استفاده می‌کنه
# helper-utils از "tiny-lib" استفاده می‌کنه
# tiny-lib از "micro-package" استفاده می‌کنه

# حالا سازنده micro-package حسابش رو فروخته
# به یه هکر! 😱

# هکر نسخه جدید میده که بدافزار داره:
# نسخه 1.0.0: سالمه ✅
# نسخه 1.0.1: بدافزار داره! 💀

# وقتی npm update می‌کنی:
# همه پروژه‌هایی که از cool-library استفاده می‌کنن
# الوده میشن! 🦠
```

**مثال واقعی:** `event-stream` (2018)

### ۲. Insecure Deserialization 🎁💣

```python
# Python - pickle
import pickle

# دریافت داده از کاربر
data = request.get_data()

# Deserialize
obj = pickle.loads(data)  # 😱 خطرناک!

# هکر می‌تونه کد اجرا کنه!
```

**Payload هکر:**
```python
import pickle
import os

class Exploit:
    def __reduce__(self):
        return (os.system, ('rm -rf /',))

malicious_data = pickle.dumps(Exploit())
# این رو می‌فرسته به سرور...
# وقتی pickle.loads اجرا میشه، rm -rf / اجرا میشه! 💀
```

### ۳. Unsigned/Unverified Updates 📲

```javascript
// الکترون اپ (Electron App)
const { autoUpdater } = require('electron-updater');

// چک کردن آپدیت
autoUpdater.checkForUpdates();

// اگه آپدیت بود، دانلود و نصب کن
autoUpdater.on('update-downloaded', () => {
    autoUpdater.quitAndInstall();
});

// ❌ مشکل: امضا رو چک نمی‌کنه!
// Man-in-the-Middle می‌تونه یه آپدیت جعلی بده! 😱
```

## 🛡️ چطور درستش کنیم؟

### ۱. Integrity Checks (چک کردن یکپارچگی) ✅

```html
<!-- ❌ بد - بدون integrity -->
<script src="https://cdn.example.com/library.js"></script>

<!-- ✅ خوب - با Subresource Integrity (SRI) -->
<script
    src="https://cdn.example.com/library.js"
    integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/ux..."
    crossorigin="anonymous">
</script>

<!-- اگه فایل تغییر کرده باشه، مرورگر لودش نمی‌کنه! ✅ -->
```

**تولید SRI hash:**
```bash
openssl dgst -sha384 -binary library.js | openssl base64 -A
```

### ۲. Code Signing (امضای کد) ✍️

```javascript
const crypto = require('crypto');
const fs = require('fs');

// امضا کردن فایل
function signFile(filePath, privateKey) {
    const fileData = fs.readFileSync(filePath);

    const sign = crypto.createSign('SHA256');
    sign.update(fileData);
    sign.end();

    const signature = sign.sign(privateKey, 'hex');
    return signature;
}

// تایید امضا
function verifyFile(filePath, signature, publicKey) {
    const fileData = fs.readFileSync(filePath);

    const verify = crypto.createVerify('SHA256');
    verify.update(fileData);
    verify.end();

    return verify.verify(publicKey, signature, 'hex');
}

// استفاده:
const signature = signFile('app-v2.apk', privateKey);

// کاربر چک می‌کنه:
if (verifyFile('app-v2.apk', signature, publicKey)) {
    console.log('فایل معتبره! ✅');
    installUpdate();
} else {
    console.log('فایل دستکاری شده! 🚨');
}
```

### ۳. Secure Deserialization 🔒

```javascript
// ❌ بد - deserialize خام
const obj = JSON.parse(userInput);

// ✅ خوب - با اعتبارسنجی
const Ajv = require('ajv');
const ajv = new Ajv();

// Schema تعریف کن
const schema = {
    type: 'object',
    properties: {
        theme: { type: 'string', enum: ['light', 'dark'] },
        fontSize: { type: 'number', minimum: 10, maximum: 30 }
    },
    required: ['theme'],
    additionalProperties: false // فقط این فیلدها!
};

const validate = ajv.compile(schema);

// اعتبارسنجی
const data = JSON.parse(userInput);
if (validate(data)) {
    // امنه! استفاده کن
    savePreferences(data);
} else {
    // نامعتبره! رد کن
    console.log('داده نامعتبر!', validate.errors);
}
```

```python
# Python - بجای pickle از JSON استفاده کن
import json

# ❌ خطرناک
import pickle
obj = pickle.loads(data)

# ✅ امن
obj = json.loads(data)

# یا اگه حتما باید pickle استفاده کنی:
import hmac
import hashlib

# امضا کردن
def sign_pickle(obj, secret_key):
    pickled = pickle.dumps(obj)
    signature = hmac.new(secret_key, pickled, hashlib.sha256).hexdigest()
    return pickled, signature

# تایید و unpickle
def verify_unpickle(pickled, signature, secret_key):
    expected_sig = hmac.new(secret_key, pickled, hashlib.sha256).hexdigest()
    if not hmac.compare_digest(signature, expected_sig):
        raise ValueError('امضا نامعتبر!')
    return pickle.loads(pickled)
```

### ۴. Dependency Pinning 📌

```json
// ❌ بد - نسخه شناور
{
    "dependencies": {
        "express": "^4.17.1",  // هر 4.x.x
        "lodash": "~4.17.20"   // هر 4.17.x
    }
}

// ✅ خوب - نسخه دقیق
{
    "dependencies": {
        "express": "4.17.1",   // دقیقا این
        "lodash": "4.17.21"    // دقیقا این
    }
}

// ✅ عالی - با lock file
# همیشه package-lock.json یا yarn.lock رو commit کن!
```

### ۵. Secure CI/CD Pipeline 🔐

```yaml
# ✅ امن
name: Deploy

on: [push]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      # از GitHub Action های رسمی استفاده کن
      - uses: actions/checkout@v3

      # نسخه رو قفل کن (pin version)
      - uses: actions/setup-node@v3
        with:
          node-version: '18.12.0'  # نسخه دقیق

      # Dependency ها رو از lock file نصب کن
      - run: npm ci  # نه npm install!

      # فقط از منابع معتبر دانلود کن
      - name: Download tool
        run: |
          curl -O https://releases.example.com/tool.tar.gz
          echo "expected-sha256-hash  tool.tar.gz" | sha256sum -c
          tar -xzf tool.tar.gz

      # Environment variables از Secrets
      - name: Deploy
        env:
          API_KEY: ${{ secrets.API_KEY }}
        run: npm run deploy
```

### ۶. واکسی کردن داده های ورودی 🧼

```javascript
// ❌ بد
app.post('/api/update', (req, res) => {
    const userData = req.body;
    Object.assign(currentUser, userData); // 😱 Prototype Pollution!
});

// ✅ خوب
app.post('/api/update', (req, res) => {
    const allowedFields = ['name', 'email', 'age'];

    const userData = {};
    allowedFields.forEach(field => {
        if (req.body[field] !== undefined) {
            userData[field] = req.body[field];
        }
    });

    // فقط فیلدهای مجاز
    Object.assign(currentUser, userData);
});

// یا با کتابخونه
const sanitize = require('mongo-sanitize');

app.post('/api/update', (req, res) => {
    const cleanData = sanitize(req.body);
    // حالا امنه!
});
```

## 🎯 چک‌لیست امنیتی

**Dependencies:**
- ✅ از lock file استفاده کن (package-lock.json, yarn.lock)
- ✅ نسخه‌ها رو pin کن
- ✅ فقط از منابع معتبر نصب کن (npm, PyPI, Maven Central)
- ✅ منظم `npm audit` اجرا کن
- ✅ Dependabot یا Snyk فعال کن

**Updates:**
- ✅ آپدیت‌ها رو امضا کن (Code Signing)
- ✅ Checksum/Hash ارائه بده
- ✅ از HTTPS استفاده کن
- ✅ Certificate Pinning (برای موبایل)

**Serialization:**
- ✅ از JSON بجای pickle/serialize استفاده کن
- ✅ Schema validation اعمال کن
- ✅ فیلدهای اضافی رو رد کن
- ✅ از Object.assign مستقیم استفاده نکن

**CI/CD:**
- ✅ نسخه action ها رو pin کن
- ✅ از secret management استفاده کن
- ✅ فایل‌های دانلود شده رو verify کن
- ✅ Least privilege برای CI/CD

**CDN & External Resources:**
- ✅ Subresource Integrity (SRI)
- ✅ از CDN های معتبر استفاده کن
- ✅ نسخه کتابخونه رو مشخص کن
- ✅ فایل‌ها رو خودت هم host کن (backup)

## 🎓 تمرین: کد امن یا ناامن?

```javascript
// مثال ۱
app.post('/import', (req, res) => {
    const config = JSON.parse(req.body.config);
    Object.assign(appConfig, config);
    res.send('Config updated!');
});
```

<details>
<summary>جواب</summary>

❌ ناامن!

**مشکلات:**
1. Prototype Pollution - می‌تونه `__proto__` بفرسته
2. هیچ validation نداره
3. همه فیلدهای config رو می‌پذیره

**راه حل:**
```javascript
const Ajv = require('ajv');
const ajv = new Ajv();

const configSchema = {
    type: 'object',
    properties: {
        theme: { type: 'string' },
        maxUsers: { type: 'number' }
    },
    additionalProperties: false
};

app.post('/import', (req, res) => {
    let config;
    try {
        config = JSON.parse(req.body.config);
    } catch {
        return res.status(400).send('JSON نامعتبر!');
    }

    const validate = ajv.compile(configSchema);
    if (!validate(config)) {
        return res.status(400).send('Config نامعتبر!');
    }

    // فقط فیلدهای مجاز
    appConfig.theme = config.theme;
    appConfig.maxUsers = config.maxUsers;

    res.send('Config updated! ✅');
});
```

</details>

## 🎬 داستان واقعی: SolarWinds (2020) ☀️💥

یکی از بزرگترین حملات Supply Chain تاریخ!

**چی شد:**
1. هکرا به CI/CD سیستم SolarWinds دسترسی پیدا کردن
2. بدافزار رو به آپدیت نرم‌افزار اضافه کردن
3. SolarWinds این آپدیت رو امضا کرد و منتشر کرد! (فکر می‌کرد سالمه)
4. ۱۸,۰۰۰ شرکت و سازمان دولتی آپدیت کردن
5. همه هک شدن! 😱💀

**درس:**
- CI/CD باید خیلی امن باشه!
- فرآیند build رو Monitor کن
- Multi-factor authentication برای همه
- Code review حتی برای آپدیت‌ها

## 📚 ابزارهای مفید

**Dependency Scanning:**
- npm audit
- Snyk
- Dependabot
- WhiteSource

**Code Signing:**
- GPG
- Authenticode (Windows)
- Codesign (macOS)

**Integrity:**
- [SRI Hash Generator](https://www.srihash.org/)
- `openssl dgst`

**Schema Validation:**
- Ajv (JSON Schema - JavaScript)
- Pydantic (Python)
- Joi (JavaScript)

---

**یادت باشه: به هر چیزی که از بیرون میاد شک کن! حتی اگه از یه منبع معتبر باشه، verify کن! 🔍✅**

**"Trust, but verify!" (اعتماد کن، ولی تایید کن!)**
