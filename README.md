# Portal Akademik Kampus Fiktif (Tugas Praktikum Keamanan Web)

Aplikasi portal kampus fiktif yang dibangun untuk latihan pengujian keamanan sistem informasi. Aplikasi ini sengaja dirancang memiliki celah kerentanan (vulnerabilities) untuk demonstrasi keamanan berbasis OWASP Top 10.

## Fitur & Hak Akses (Role):
1. **Mahasiswa:**
   - Masuk / Login ke portal.
   - Melihat Kartu Hasil Studi (KHS) dan nilai semester.
   - Mengunggah berkas laporan/tugas kuliah.
   - Berdiskusi di Forum Akademik.
2. **Dosen:**
   - Masuk / Login ke portal.
   - Menginput dan memperbarui nilai mahasiswa.
   - Berpartisipasi dalam forum diskusi.
3. **Administrator:**
   - Mengelola akun kampus (menambah akun baru & menghapus akun).

## Akun Demo Sintetis:
- **Mahasiswa:** `24781018` / `password123` (Nadiya Ghefira)
- **Mahasiswa 2:** `24781019` / `password123` (Bima Prasetyo)
- **Dosen:** `19850101` / `dosen123` (Dr. Ir. Ahmad Jayadi, M.Kom)
- **Admin:** `admin` / `admin123`

---

## 2 Kerentanan Yang Sengaja Dibuat:

### 1. SQL Injection (Bypass Otentikasi Login)
- **Lokasi:** Form Login.
- **Penyebab:** String input username digabungkan secara mentah tanpa prepared statements:
  `SELECT * FROM users WHERE username = '${username}' AND password = '${password}'`
- **Cara Eksploitasi:**
  Masukkan username: `' OR '1'='1` dengan password apa saja, atau klik tombol **⚡ Eksploit SQL Injection**. Sistem akan bypass autentikasi dan langsung masuk sebagai Administrator.

### 2. Stored Cross-Site Scripting (Stored XSS)
- **Lokasi:** Tab Forum Diskusi.
- **Penyebab:** Pesan komentar yang dikirim pengguna disimpan dan langsung dirender menggunakan `element.innerHTML` tanpa encoding karakter HTML.
- **Cara Eksploitasi:**
  Kirim pesan forum berupa: `<img src=x onerror="alert('Celah Stored XSS Terbuka!')">` atau klik tombol **⚡ Uji Coba Payload Stored XSS**. Script akan tersimpan secara permanen dan otomatis dieksekusi di browser siapa pun yang membuka forum.

### 3. (Bonus) Validasi Berkas Lemah
- **Lokasi:** Tab Unggah Tugas Mahasiswa.
- **Penyebab:** Tidak ada whitelist filter ekstensi berkas.
