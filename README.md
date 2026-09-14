# Lab-Portswigger-Burp-Community
In this repository, I will write my journal to document and finish LABs from PortSwigger Web Security Academy. I will be using Burp Suite Community Edition and other supporting tools to help me analyze and exploit the vulnerabilities!

---

# 📖 Explanation
SQL Injection (SQLi) is a web security vulnerability where an attacker can inject or insert malicious code into vulnerable endpoints (such as input forms, URL parameters, or HTTP headers) using queries from the SQL language itself. 

This vulnerability typically occurs because the application fails to properly validate, filter, or sanitize user input before passing it to be executed by the backend database system (such as MySQL, PostgreSQL, Oracle, or MSSQL).

---

# 🎯 What can it do and what is the impact?
Vulnerable endpoints that are compromised with malicious payloads can lead to fatal consequences threatening the Confidentiality, Integrity, and Availability (CIA triad) of a system. Here are some of the potential impacts:

1. **Exposing Sensitive Data (Confidentiality):** Attackers can retrieve other users' data and view sensitive information stored in the system's database (such as password hashes, financial information, or personal data).
2. **Authentication Bypass:** Attackers can log into other users' accounts, or even take over an administrator account, without needing to know the actual password.
3. **Data Manipulation (Integrity):** Beyond just reading data, an attacker who successfully breaches the system can also modify, add, or delete critical data within the database (for example, dropping entire tables).
4. **System Command Execution (Remote Code Execution):** In advanced cases involving specific database configurations, sophisticated attackers can execute OS (Operating System) commands directly on the backend server, leading to a complete server takeover.
