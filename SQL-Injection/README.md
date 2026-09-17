## 🧰 Security Stack

<p align="left">
  <img src="https://img.shields.io/badge/SQL-000000?style=for-the-badge&logo=databricks&logoColor=white" alt="SQL"/>
  <img src="https://img.shields.io/badge/SQL%20Injection-Basics-b91c1c?style=for-the-badge&logo=hackthebox&logoColor=white" alt="SQL Injection"/>
  <img src="https://img.shields.io/badge/Burp%20Suite-ff6633?style=for-the-badge&logo=burpsuite&logoColor=white" alt="Burp Suite"/>
  <img src="https://img.shields.io/badge/OWASP%20ZAP-00549e?style=for-the-badge&logo=owasp&logoColor=white" alt="OWASP ZAP"/>
  <img src="https://img.shields.io/badge/sqlmap-111111?style=for-the-badge&logo=linux&logoColor=white" alt="sqlmap"/>
  <img src="https://img.shields.io/badge/HTTP-005571?style=for-the-badge&logo=httpie&logoColor=white" alt="HTTP"/>
  <img src="https://img.shields.io/badge/Web%20Security-6f42c1?style=for-the-badge&logo=security&logoColor=white" alt="Web Security"/>
  <img src="https://img.shields.io/badge/OWASP-000000?style=for-the-badge&logo=owasp&logoColor=white" alt="OWASP"/>
  <img src="https://img.shields.io/badge/DVWA-8B0000?style=for-the-badge&logo=php&logoColor=white" alt="DVWA"/>
  <img src="https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black" alt="Linux"/>
  <img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub"/>
</p>

# 🛡️ SQL Injection (SQLi)

> 💉 **What's SQLi?** It's when attackers sneak malicious SQL code into your database queries through user inputs. Basically, they trick your app into running commands it shouldn't.

---

## 📚 Table of Contents

- [What is SQL Injection?](#-what-is-sql-injection)
- [How Does It Work?](#-how-does-it-work)
- [Types of SQLi](#-types-of-sqli)
- [Real-World Examples](#-real-world-examples)
- [Detection & Tools](#-detection--tools)
- [Mitigation & Prevention](#-mitigation--prevention)
- [Resources & Learning](#-resources--learning)

---

## 🔍 What is SQL Injection?

SQL Injection is one of the **oldest and most dangerous** web vulnerabilities out there. It happens when your application doesn't properly sanitize user input before using it in SQL queries.

**Imagine this:** You have a login form, and instead of checking credentials normally, the attacker types something like `' OR '1'='1` and suddenly they're logged in as admin. Yeah, it's that simple sometimes. 😅

### Why Should You Care?

- 🚨 Can lead to **data breaches** (user info, passwords, payment data)
- 💰 Financial losses and legal issues
- 🔓 Full database access (read, modify, delete)
- 🎯 Often the first step in bigger attacks

---

## ⚙️ How Does It Work?

Your app probably has code like this:

```python
# ❌ BAD - Vulnerable to SQLi
query = "SELECT * FROM users WHERE username = '" + user_input + "'"
```

If `user_input` is `admin' --`, the query becomes:

```sql
SELECT * FROM users WHERE username = 'admin' --'
```

The `--` comments out the rest, and boom — they're in as admin.

---

## 🎭 Types of SQLi

| Type | Description | Difficulty |
|------|-------------|------------|
| **In-band SQLi** | Attacker uses the same channel to attack and gather results | Easy |
| **Error-based** | Triggers database errors to reveal info | Easy-Medium |
| **Union-based** | Uses UNION operator to combine results | Medium |
| **Blind SQLi** | No error messages, attacker infers from behavior | Hard |
| **Boolean-based** | True/false responses reveal data bit by bit | Hard |
| **Time-based** | Uses delays (SLEEP, WAITFOR) to infer data | Hard |
| **Out-of-band SQLi** | Results sent via different channel (DNS, HTTP) | Medium-Hard |
| **Second-order SQLi** | Payload stored first, executed later | Hard |

### Quick Breakdown:

#### 🔊 In-band SQLi
- **Error-based**: Database errors leak info (table names, columns, etc.)
- **Union-based**: Uses `UNION SELECT` to grab data from other tables

#### 🙈 Blind SQLi
- **Boolean-based**: "Is the first letter 'a'? Is it 'b'?" — yes/no answers
- **Time-based**: "If true, wait 5 seconds" — timing reveals the answer

#### 📡 Out-of-band SQLi
- Used when in-band isn't possible
- Data exfiltrated via DNS queries or HTTP requests

---

## 💀 Real-World Examples

### Example 1: Login Bypass
```sql
-- Input: admin' --
SELECT * FROM users WHERE username = 'admin' --' AND password = 'anything'
```

### Example 2: Data Extraction (Union-based)
```sql
-- Input: ' UNION SELECT username, password FROM users --
SELECT id, name FROM products WHERE id = '' UNION SELECT username, password FROM users --'
```

### Example 3: Drop Table (Because why not 😈)
```sql
-- Input: '; DROP TABLE users; --
SELECT * FROM users WHERE id = ''; DROP TABLE users; --'
```

---

## 🕵️ Detection & Tools

### Manual Testing
- Try `'` or `"` in input fields → watch for errors
- Use `' OR '1'='1` in login forms
- Add `AND 1=1` or `AND 1=2` to URLs → check behavior changes

### Automated Tools
| Tool | Best For |
|------|----------|
| **sqlmap** | Automatic detection & exploitation |
| **Burp Suite** | Manual testing + scanner |
| **OWASP ZAP** | Free alternative to Burp |
| **Havij** | Windows-based SQLi tool |

**Pro tip:** Always test in a safe environment first. Don't be that person breaking production. 🙃

---

## 🛡️ Mitigation & Prevention

### ✅ DO THIS:

#### 1. Use Prepared Statements (Parameterized Queries)
```python
# ✅ GOOD - Using parameterized query
cursor.execute("SELECT * FROM users WHERE username = ?", (user_input,))
```

```php
// ✅ GOOD - PHP PDO
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = :username");
$stmt->execute(['username' => $user_input]);
```

#### 2. Use ORM (Object-Relational Mapping)
- **Python**: SQLAlchemy, Django ORM
- **PHP**: Eloquent, Doctrine
- **Node.js**: Sequelize, TypeORM

ORMs handle sanitization for you (mostly). Still validate inputs though!

#### 3. Input Validation
- Whitelist allowed characters
- Validate data types (integers, emails, etc.)
- Reject unexpected input early

#### 4. Least Privilege Principle
- Database user should only have **necessary permissions**
- No `DROP TABLE` or `DELETE` unless absolutely needed
- Separate read-only and write accounts

#### 5. Escape Special Characters
If you MUST use dynamic queries (please don't):
```php
$username = mysqli_real_escape_string($conn, $user_input);
```

#### 6. Use WAF (Web Application Firewall)
- Cloudflare, AWS WAF, ModSecurity
- Blocks common SQLi patterns
- **Not a silver bullet** — use with other measures!

### ❌ DON'T DO THIS:

```python
# ❌ NEVER concatenate user input into queries!
query = f"SELECT * FROM users WHERE id = {user_id}"
query = "SELECT * FROM users WHERE name = '" + name + "'"
```

```javascript
// ❌ Node.js example - BAD
db.query(`SELECT * FROM users WHERE email = '${email}'`)
```

---

## 🧪 Testing Checklist

- [ ] All user inputs validated and sanitized
- [ ] Using prepared statements everywhere
- [ ] Database user has minimal privileges
- [ ] Error messages don't leak SQL info
- [ ] WAF configured and tested
- [ ] Regular security audits scheduled
- [ ] Team trained on secure coding practices

---

## 📚 Resources & Learning

### Must-Read
- [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
- [PortSwigger SQLi Labs](https://portswigger.net/web-security/sql-injection)
- [SQL Injection Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)

### Practice Labs
- **DVWA** (Damn Vulnerable Web App)
- **SQLi Labs** (GitHub)
- **HackTheBox** & **TryHackMe** SQLi challenges
- **PortSwigger Web Security Academy**

### Tools Documentation
- [sqlmap GitHub](https://github.com/sqlmapproject/sqlmap)
- [Burp Suite Docs](https://portswigger.net/burp/documentation)

---

## ⚠️ Disclaimer

> **This repository is for EDUCATIONAL PURPOSES ONLY.** 📚

The information provided here is meant to help developers, security researchers, and students understand SQL Injection vulnerabilities and how to prevent them. 

### 📌 Important Notes:

- **DO NOT** use the techniques described here to attack systems you don't own or have explicit permission to test
- **DO NOT** use this knowledge for illegal activities or malicious purposes
- **ALWAYS** test in controlled environments (local labs, VMs, or authorized bug bounty programs)
- **RESPECT** laws and regulations in your country regarding cybersecurity and computer crime

### 🛡️ Responsible Use:

| ✅ Do | ❌ Don't |
|-------|----------|
| Learn to protect your own apps | Attack websites without permission |
| Practice on legal labs (DVWA, HackTheBox) | Use sqlmap on production sites |
| Report vulnerabilities responsibly | Exploit bugs for personal gain |
| Share knowledge ethically | Teach others how to hack illegally |

### ⚖️ Legal Notice:

Unauthorized access to computer systems is **illegal** in most jurisdictions and can result in:
- Criminal charges
- Heavy fines
- Imprisonment
- Civil lawsuits

**You are solely responsible for your actions.** The authors and contributors of this repository are not liable for any misuse of the information provided.

### 🎯 Intended Audience:

- Web developers learning secure coding
- Security researchers and bug bounty hunters
- Students studying cybersecurity
- Anyone interested in understanding web vulnerabilities

**If you're not sure if something is legal, DON'T DO IT.** When in doubt, get written permission first. 📝

Stay ethical, stay legal, stay safe. 🙏

---

## 🎯 Quick Reference

| Scenario | Solution |
|----------|----------|
| Login form | Prepared statements + input validation |
| Search feature | Parameterized queries + whitelist characters |
| Admin panel | RBAC + audit logs + WAF |
| API endpoints | ORM + rate limiting + input sanitization |

---

## 📝 Final Thoughts

SQLi isn't going anywhere. It's been around since the 90s and **still works** because devs keep making the same mistakes. 

**The fix is simple:** 
1. Never trust user input
2. Always use prepared statements
3. Keep your database permissions tight
4. Test regularly

Stay safe, code smart, and don't let your database become someone else's playground. 🔒

---

<div align="center">

**Made with ❤️ for the security community**

Found this helpful? ⭐ Star this repo!

</div>
