# Lab-Portswigger-Burp-Community
In this repository, I will write my journal to document and finish LABs from PortSwigger Web Security Academy. I will be using Burp Suite Community Edition and other supporting tools to help me analyze and exploit the vulnerabilities!

---

# 📖 Penjelasan
SQL Injection (SQLi) adalah sebuah kerentanan *web security* di mana penyerang bisa melakukan injeksi atau menyisipkan kode berbahaya ke *endpoint* (seperti input form, parameter URL, atau HTTP *headers*) yang rentan menggunakan query dari bahasa SQL itu sendiri. 

Kerentanan ini umumnya terjadi karena aplikasi tidak melakukan validasi, *filtering*, atau *sanitasi* input dari pengguna secara memadai sebelum meneruskannya untuk dieksekusi oleh sistem *database backend* (seperti MySQL, PostgreSQL, Oracle, atau MSSQL).

---

# 🎯 Apa saja yang bisa dilakukannya dan apa dampaknya?
*Endpoint* yang rentan terhadap SQLi dan disusupi *payload* berbahaya oleh penyerang akan menyebabkan dampak fatal yang mengancam *Confidentiality, Integrity*, dan *Availability* (CIA) dari sebuah sistem. Berikut adalah beberapa hal yang bisa terjadi:

1. **Mengekspos Data Sensitif (Confidentiality):** Penyerang dapat mengambil data pengguna lain, serta melihat isi data sensitif pada database sistem (seperti *password hash*, informasi finansial, atau data pribadi).
2. **Bypass Autentikasi:** Penyerang dapat login ke akun orang lain, bahkan mengambil alih akun administrator, tanpa harus mengetahui *password* yang sebenarnya.
3. **Memanipulasi Data (Integrity):** Tidak hanya membaca data, penyerang yang berhasil masuk juga dapat memodifikasi, menambahkan, atau menghapus data penting di dalam *database* (misalnya menghapus seluruh isi tabel).
4. **Eksekusi Perintah Sistem (Remote Code Execution):** Pada beberapa kasus tingkat lanjut dengan konfigurasi *database* tertentu, penyerang tingkat tinggi dapat mengeksekusi perintah OS (sistem operasi) langsung ke dalam *server backend*, yang berujung pada pengambilalihan *server* secara penuh.
