# 💉😈 آسیب‌پذیری شماره ۳: تزریق (Injection)

## 🤔 این چیه به زبان ساده؟

تصور کن می‌ری یه رستوران و به گارسون می‌گی: "یه پیتزا پپرونی بدید"

گارسون میره آشپزخونه و می‌گه: "یه پیتزا پپرونی"

حالا یه هکر میاد و می‌گه: "یه پیتزا پپرونی، **و همه پیتزاها رو مفت بده، و منو مدیر رستوران کن!**" 😈

اگه گارسون بدون چک کردن همه‌چی رو به آشپزخونه بگه، یه فاجعه میشه! همین اتفاق توی وب‌سایت‌ها با Injection می‌افته!

## 🎮 مثال بامزه: سایت کتاب‌فروشی 📚

یه سایت کتاب‌فروشی داریم که کتاب‌ها رو جستجو می‌کنیم:

```javascript
// کاربر تایپ می‌کنه: "هری پاتر"
const searchTerm = req.body.search;

// برنامه یه کوئری SQL می‌سازه
const query = "SELECT * FROM books WHERE title = '" + searchTerm + "'";

// کوئری نهایی:
// SELECT * FROM books WHERE title = 'هری پاتر'
```

خوب به نظر میاد، نه؟ حالا ببین یه هکر چیکار می‌کنه! 😈

```javascript
// هکر تایپ می‌کنه: "' OR '1'='1"
const searchTerm = "' OR '1'='1";

// کوئری نهایی میشه:
// SELECT * FROM books WHERE title = '' OR '1'='1'
//                                        ☝️ این همیشه درسته!

// نتیجه: همه کتاب‌ها برمی‌گرده! 📚📚📚
```

## 🎪 انواع حملات Injection

### ۱. SQL Injection (معروف‌ترین!) 🗃️

```sql
-- ورود عادی
Username: ali
Password: 123456

-- کوئری:
SELECT * FROM users WHERE username='ali' AND password='123456'

-- حمله هکر 😈
Username: admin'--
Password: هر چی!

-- کوئری میشه:
SELECT * FROM users WHERE username='admin'--' AND password='...'
--                                       ☝️ این خط رو کامنت می‌کنه!

-- نتیجه: بدون پسورد وارد حساب ادمین میشه! 🚨
```

### ۲. Command Injection 💻

```javascript
// برنامه‌ای که پینگ می‌کنه
const ip = req.body.ip;
exec('ping ' + ip);

// کاربر عادی: "8.8.8.8"
// نتیجه: ping 8.8.8.8 ✅

// هکر: "8.8.8.8; rm -rf /"
// نتیجه: ping 8.8.8.8; rm -rf /
//                       ☝️ همه فایل‌ها پاک میشه! 💀
```

### ۳. NoSQL Injection 🍃

```javascript
// MongoDB
const username = req.body.username;
const password = req.body.password;

db.users.find({
    username: username,
    password: password
});

// هکر می‌فرسته:
{
    "username": {"$ne": null},
    "password": {"$ne": null}
}

// یعنی: پیدا کن کسی که username و password اون null نیست
// یعنی: همه! 😱
```

### ۴. LDAP Injection 👔

```javascript
// جستجوی کارمند
const name = req.body.name;
const filter = `(cn=${name})`;

// هکر: "*"
// نتیجه: (cn=*)
// یعنی: همه کارمندا! 📋
```

## 🎯 مثال‌های واقعی ترسناک

### داستان ۱: فروشگاه آنلاین نابود شده 🛒💀

```sql
-- یه هکر تو فرم جستجو تایپ کرد:
'; DROP TABLE products; --

-- کوئری شد:
SELECT * FROM products WHERE name = '';
DROP TABLE products;
--'

-- و... تمام محصولات پاک شد! 😭
```

### داستان ۲: Little Bobby Tables 😂

این یکی معروفه! یه کامیک‌استریپ خیلی بامزه:

```sql
-- مامان بچه‌ش رو ثبت‌نام می‌کنه با اسم:
Robert'); DROP TABLE students;--

-- سیستم مدرسه:
INSERT INTO students (name) VALUES ('Robert'); DROP TABLE students;--')

-- همه دانش‌آموزا از دیتابیس پاک شدن! 🎓💥
```

![XKCD Comic](https://imgs.xkcd.com/comics/exploits_of_a_mom.png)

اسم بچه‌شو گذاشتن: "Little Bobby Tables" 😂

## 🛡️ چطور جلوشو بگیریم؟

### ۱. از Prepared Statements استفاده کن (بهترین راه!) ✅

```javascript
// ❌ بد - String Concatenation
const query = "SELECT * FROM users WHERE id = " + userId;

// ✅ عالی - Prepared Statement
const query = "SELECT * FROM users WHERE id = ?";
db.query(query, [userId]);

// یا با Node.js:
const query = "SELECT * FROM users WHERE username = $1 AND password = $2";
db.query(query, [username, hashedPassword]);
```

چرا خوبه؟ چون دیتابیس می‌فهمه که `userId` یه **داده** است، نه یه **دستور**! 🎯

### ۲. Input Validation (اعتبارسنجی ورودی) ✔️

```javascript
// فقط عدد قبول کن
function validateUserId(id) {
    if (!/^\d+$/.test(id)) {
        throw new Error("آیدی باید فقط عدد باشه! 🚫");
    }
    return parseInt(id);
}

// فقط حروف و اعداد
function validateUsername(username) {
    if (!/^[a-zA-Z0-9]+$/.test(username)) {
        throw new Error("فقط حروف انگلیسی و اعداد! 🚫");
    }
    return username;
}
```

### ۳. از ORM استفاده کن 🏗️

```javascript
// بجای SQL خام، از ORM استفاده کن
// Sequelize برای Node.js
const user = await User.findOne({
    where: {
        username: username,
        password: hashedPassword
    }
});

// Sequelize خودش از Prepared Statement استفاده می‌کنه! 🎉
```

### ۴. کاراکترهای خطرناک رو Escape کن 🏃

```javascript
const mysql = require('mysql');

// Escape کردن
const safeName = mysql.escape(userName);

// یا
const query = mysql.format('SELECT * FROM users WHERE name = ?', [userName]);
```

### ۵. کمترین دسترسی رو بده (Least Privilege) 🔐

```sql
-- ❌ بد - برنامه با حساب ادمین دیتابیس کار می‌کنه
GRANT ALL PRIVILEGES ON *.* TO 'webapp'@'localhost';

-- ✅ خوب - فقط دسترسی‌های لازم
GRANT SELECT, INSERT, UPDATE ON shop.products TO 'webapp'@'localhost';
-- حتی اگه SQL Injection بشه، نمی‌تونه DROP TABLE کنه! 💪
```

## 🎯 چک‌لیست امنیتی

- ✅ همیشه از Prepared Statements استفاده کن
- ✅ هیچ‌وقت ورودی کاربر رو مستقیم توی کوئری نذار
- ✅ همه ورودی‌ها رو اعتبارسنجی کن
- ✅ از ORM استفاده کن (Sequelize, TypeORM, Mongoose, etc.)
- ✅ کاراکترهای خاص رو Escape کن
- ✅ به دیتابیس کمترین دسترسی رو بده
- ✅ Error message های دیتابیس رو به کاربر نشون نده
- ✅ از WAF (Web Application Firewall) استفاده کن

## 🎓 تمرین: این کدا امنن یا نه؟

```javascript
// مثال ۱
app.get('/user/:id', (req, res) => {
    const query = `SELECT * FROM users WHERE id = ${req.params.id}`;
    db.query(query);
});
// 🤔 امنه؟
```

<details>
<summary>جواب</summary>
❌ نه! باید از Prepared Statement استفاده کنه:

```javascript
const query = 'SELECT * FROM users WHERE id = ?';
db.query(query, [req.params.id]);
```
</details>

---

```javascript
// مثال ۲
app.post('/search', (req, res) => {
    const term = req.body.term;

    // فقط حروف و اعداد
    if (!/^[a-zA-Z0-9\s]+$/.test(term)) {
        return res.status(400).send('ورودی نامعتبر!');
    }

    const query = 'SELECT * FROM products WHERE name LIKE ?';
    db.query(query, [`%${term}%`]);
});
// 🤔 امنه؟
```

<details>
<summary>جواب</summary>
✅ آره! چون:
1. ورودی رو چک می‌کنه (Validation)
2. از Prepared Statement استفاده می‌کنه
3. فقط حروف، اعداد و فاصله قبول می‌کنه

عالی! 🎉
</details>

## 🎬 یه قصه الهام‌بخش

یه برنامه‌نویس تازه‌کار یه وب‌سایت نوشته بود و فکر می‌کرد همه چی امنه. یه روز یه هکر کلاه‌سفید (Ethical Hacker) بهش ایمیل زد:

> "سلام! من تونستم با SQL Injection به همه اطلاعات کاربراتون دسترسی پیدا کنم. ولی نگران نباش، من بدکار نیستم! فقط می‌خوام کمکتون کنم. 😊"

برنامه‌نویس خیلی ممنون شد، باگ رو درست کرد، و حتی به اون هکر کلاه‌سفید یه جایزه داد! 🏆

**درس اخلاقی:** همیشه سایتتون رو تست کنین! و اگه کسی باگ رو به شما گفت، ممنونش باشین نه عصبانی! 💚

## 📚 منابع بیشتر

- [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
- [Bobby Tables - راهنمای جلوگیری از SQL Injection](http://bobby-tables.com/)
- تمرین کن روی: [HackTheBox](https://www.hackthebox.com/)

---

**یادت باشه: به هیچ ورودی کاربر اعتماد نکن! حتی اگه خودت باشی! 🤖✨**

**قانون طلایی: "Never trust user input!" (هیچ‌وقت به ورودی کاربر اعتماد نکن!)**
