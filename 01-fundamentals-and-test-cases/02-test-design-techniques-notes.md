# 📘 Modul 2: Black Box Test Design Techniques
> **Tanggal Catatan**: 2026-09-09  
> **Target Aplikasi**: [Lumiina Live](https://lumiina-art.vercel.app)  
> **Acuan Standar**: ISTQB Foundation Level (Black Box Testing Techniques)

---

## 1. Pengertian Black Box Testing

- **Definisi**: Metode pengujian di mana tester **hanya mengevaluasi sistem dari perilaku luar (Input $\rightarrow$ Output)** tanpa mengetahui struktur kode internal, database query, atau algoritma di baliknya.
- **Analogi Vending Machine**: Pembeli memasukkan uang koin Rp 10.000 dan menekan tombol kopi, lalu mengamati apakah kaleng kopi keluar. Pembeli tidak perlu membongkar baut atau mengerti sirkuit dinamo di dalam mesin.
- **Tiga Spektrum Testing**:
  - **Black Box**: Nol pengetahuan kode. Sudut pandang end-user (fungsionalitas & bisnis).
  - **Grey Box**: Pengetahuan parsial. Sudut pandang QA/Security (tahu endpoint API, skema DB, network requests).
  - **White Box**: Pengetahuan penuh. Sudut pandang developer (source code, unit test, memory leak, code coverage).

---

## 2. Equivalence Partitioning (EP)

- **Konsep**: Membagi domain input ke dalam partisi/kelas yang diasumsikan diperlakukan sama oleh sistem.
- **Tipe Partisi**:
  1. *Valid Equivalence Class*: Kumpulan data yang seharusnya diterima.
  2. *Invalid Equivalence Class*: Kumpulan data yang seharusnya ditolak dengan pesan error.
- **Kaidah**: Cukup pilih **1 data uji perwakilan** dari setiap partisi untuk menghemat waktu uji tanpa menurunkan cakupan kualitas.

---

## 3. Boundary Value Analysis (BVA)

- **Teori Tepi**: $>70\%$ kesalahan logika programmer terjadi pada batas kondisi perulangan atau operator perbandingan (`<` vs `<=`, `>` vs `>=`).
- **Formula 2-Value Boundary**:
  - $Min - 1$: Tepat 1 nilai di bawah batas minimum (**Harus Ditolak / Invalid**).
  - $Min$: Batas minimum pas (**Harus Diterima / Valid**).
  - $Max$: Batas maksimum pas (**Harus Diterima / Valid**).
  - $Max + 1$: Tepat 1 nilai di atas batas maksimum (**Harus Ditolak / Invalid**).

### Studi Kasus: Panjang Username Lumiina (3 - 30 Karakter)
| Nilai Uji | Tipe Batas | Nilai Input | Expected Result | Status Uji |
|---|---|---|---|:---:|
| 2 karakter | $Min - 1$ | `"ep"` | Ditolak (Error: "Username minimal 3 karakter") | ✅ Validasi BVA |
| 3 karakter | $Min$ | `"iva"` | Diterima (Valid) | ✅ Validasi BVA |
| 30 karakter | $Max$ | 30 karakter alfanumerik | Diterima (Valid) | ✅ Validasi BVA |
| 31 karakter | $Max + 1$ | 31 karakter alfanumerik | Ditolak (Error: "Username maksimal 30 karakter") | ✅ Validasi BVA |

---

## 4. Decision Table Testing (Tabel Keputusan)

- **Tujuan**: Menangani kombinasi kondisi IF-ELSE berlapis secara matematis agar tidak ada kasus logika bisnis yang terlewat.
- **Rumus Aturan**: $\text{Jumlah Rules} = 2^n$ (di mana $n$ adalah jumlah kondisi True/False).
- **Studi Kasus Lumiina Auth Matrix**:
  - 3 Kondisi: User Terdaftar ($C_1$), Password Benar ($C_2$), Email Terverifikasi ($C_3$).
  - Menghasilkan 8 kombinasi aturan untuk memastikan respon sistem konsisten (termasuk pertahanan *Anti-Account Enumeration*).

---

## 5. State Transition Testing (Transisi Status)

- **Tujuan**: Memastikan perpindahan status entitas (misal: status akun, status pesanan, status karya) hanya bisa terjadi melalui aksi (*events*) yang sah.
- **Siklus Status Akun Lumiina**:
  $$\text{Unverified} \xrightarrow{\text{Klik Link Email}} \text{Active} \xrightarrow{\text{Reset Password}} \text{SessionRevoked}$$
- **Fokus Pengujian QA**:
  - Memastikan *Happy Path* transisi berjalan mulus.
  - Memastikan *Negative Path*: Akun *Unverified* atau *Guest* diblokir dari aksi interaksi seperti Like, Bookmark, Follow, dan Upload.

---

## 6. Error Guessing & Adversarial Testing

- **Teknik berbasis pengalaman & naluri penyerang (Security/HTB Mindset)**:
  - Injeksi XSS (`<script>...`) pada field nama dan komentar.
  - Karakter tak kasat mata (*Zero-width space* `\u200B`).
  - Emoji 4-byte UTF-8 (`🎨✨`).
  - Pemalsuan Magic Bytes / MIME sniffing pada form upload berkas.
