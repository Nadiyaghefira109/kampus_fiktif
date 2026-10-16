# SIAKAD · Institut Teknologi & Keamanan Digital Lampung (ITKD Lampung)

Aplikasi Portal Akademik Kampus Fiktif untuk Latihan & Pembuktian Kerentanan Web (OWASP Top 10) pada Mata Kuliah PMI 1522 Keamanan Sistem Informasi.

> **Catatan Keaslian & Data Sintetis:**
> Seluruh identitas institusi, akun pengguna, matakuliah, dan transkrip nilai dalam repositori ini dibuat secara sintetis, otentik, dan unik untuk keperluan tugas evaluasi keamanan informasi.

---

## 1. Identitas Institusi & Hak Akses (Role)
* **Institusi:** Institut Teknologi & Keamanan Digital Lampung (ITKD Lampung)
* **Program Studi:** D-III Manajemen Informatika

### Data Akun Sintetis:
| Peran (Role) | ID / NIM / NIDN | Nama Pengguna | Kata Sandi Default |
|---|---|---|---|
| **Mahasiswa 1** | `24781018` | Nadiya Ghefira Ramadhani (Sintetis) | `nadiya@2026` |
| **Mahasiswa 2** | `24781033` | Zahrana Qinthara (Sintetis) | `password123` |
| **Mahasiswa 3** | `24781045` | Fikri Haikal Alamsyah (Sintetis) | `password123` |
| **Dosen Pengampu 1** | `0215088901` | Ir. Hendra Kusuma, S.T., M.T., CEH (Sintetis) | `dosen@itkd` |
| **Dosen Pengampu 2** | `0204118302` | Dr. Ratna Dewi Sartika, S.Kom., M.T.I. (Sintetis) | `dosen@itkd` |
| **Super Administrator** | `superadmin_itkd` | Biro Administrasi Sistem & Keamanan ITKD | `admin@itkd` |

---

## 2. Fitur-Fitur Sistem:
1. **Mahasiswa:**
   * Autentikasi sesi login.
   * Melihat Kartu Hasil Studi (KHS) dan nilai semester aktif.
   * Mengunggah berkas laporan praktikum mandiri.
   * Berpartisipasi dalam Forum Diskusi Akademik.
2. **Dosen Pengampu:**
   * Melihat daftar mahasiswa bimbingan.
   * Memperbarui dan menginput nilai angka/huruf mahasiswa.
   * Berinteraksi di forum tanya jawab.
3. **Administrator:**
   * Manajemen pendaftaran akun baru (mahasiswa, dosen, staf).
   * Penghapusan akun pengguna non-aktif.

---

## 3. Dokumentasi 2 Kerentanan Yang Dibuat Sengaja

### A. SQL Injection (Bypass Otentikasi Login)
* **Kategori OWASP:** A03:2021 – Injection
* **Lokasi:** Formulir Login Utama (`view-login`).
* **Penyebab Kerentanan:** String masukan dari pengguna digabungkan langsung ke dalam kueri backend tanpa validasi tipe data maupun Prepared Statements:
  ```sql
  SELECT * FROM akun_pengguna WHERE id_user = '${username}' AND kata_sandi = '${password}'
  ```
* **Cara Reproduksi Eksploitasi:**
  1. Masukkan pada kolom username: `' OR '1'='1`
  2. Isi kata sandi dengan karakter bebas apa saja (contoh: `bypass`).
  3. Klik tombol **Masuk Sistem** (atau klik tombol cepat **⚡ Eksploit SQL Injection**).
  4. Karena klausa `'1'='1'` selalu menghasilkan nilai *TRUE*, sistem mengabaikan verifikasi kata sandi dan langsung meloloskan sesi sebagai akun berhak tertinggi (*Super Administrator*).

### B. Stored Cross-Site Scripting (Stored XSS)
* **Kategori OWASP:** A03:2021 – Injection (Cross-Site Scripting)
* **Lokasi:** Forum Diskusi Akademik Kampus (`tab-forum`).
* **Penyebab Kerentanan:** Komentar yang ditulis pengguna disimpan ke dalam basis data dan ditampilkan kembali menggunakan properti manipulasi DOM langsung `element.innerHTML` tanpa melakukan penyaringan (*sanitization*) atau encoding entitas HTML (`htmlspecialchars`).
* **Cara Reproduksi Eksploitasi:**
  1. Masuk ke tab **Forum Diskusi Kampus**.
  2. Tuliskan pesan berikut pada kotak tanggapan:
     ```html
     <b style="color:#dc2626;">[UJI PENETRASI]</b> <img src="invalid" onerror="alert('Celah Keamanan Stored XSS Terbuka!')">
     ```
     *(atau klik tombol **⚡ Uji Coba Payload Stored XSS**)*.
  3. Klik **Kirim Komentar**.
  4. Skrip JavaScript tersebut akan tersimpan secara persisten dan otomatis dieksekusi di browser siapa pun yang membuka halaman forum diskusi.

---

### C. Kerentanan Tambahan: Validasi Berkas Lemah (Unrestricted File Upload)
* **Lokasi:** Formulir Unggah Tugas (`tab-tugas`).
* **Penyebab Kerentanan:** Tidak adanya whitelist filter ekstensi berkas, memungkinkan berkas berekstensi berbahaya terunggah ke sistem.
