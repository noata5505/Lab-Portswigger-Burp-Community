# 📝 Lab: SQL injection UNION attack, retrieving data from other tables

- **Vulnerability:** SQL Injection
- **Difficulty:** Apprentice
- **Objective:** Retrieve credentials from another table and log in as the administrator.

---

## 🛠️ Tools Used
- Browser (FoxyProxy)
- Burp Suite

---
*(Note: Keeping this one short and sweet since we've already covered the basics! 😊)*

## 🐾 Steps to Reproduce
1. Open the lab's main page, turn on the *FoxyProxy* extension, and click on any category (e.g., Pets).
2. Open **Burp Suite -> Proxy -> HTTP History**, find the request, and press `Ctrl+R` to send it to **Repeater**.
3. In Repeater, look at the request: `GET /filter?category=Pets HTTP/2`. The `category` parameter is our vulnerable injection point.
4. Since the lab description already gives away the table name (`users`) and column names (`username`, `password`), we can directly inject our `UNION` payload:
   `'+UNION+SELECT+username,password+FROM+users--`
5. Send the request and scroll down through the response body to locate the `administrator` username along with its password.
6. Go to the **My account** login page, log in using the administrator credentials, and boom! The **Lab Solved** banner will appear.
<img width="1917" height="910" alt="Screenshot 2026-09-17 200540" src="https://github.com/user-attachments/assets/931dbb44-744b-4a30-a415-0cb9a77e47e5" />

---

## 🎯 Impact
By leveraging a UNION-based SQL injection, an attacker can query arbitrary tables in the database to exfiltrate sensitive data. In this scenario, dumping user credentials directly leads to an **Account Takeover (ATO)** of the administrator account, granting full control over the application.

---

## 💣 Payload
```sql
'+UNION+SELECT+username,password+FROM+users--
```
## 🔗 Reference:
For more powerful payloads, check out the [PortSwigger SQLi Cheat Sheet.](https://portswigger.net/web-security/sql-injection/cheat-sheet)
