# Lumiina QA & SDET Engineering Automation Suite

**Platform Under Test (PUT)**: [Lumiina Production](https://lumiina-art.vercel.app)  
**Author**: Sandi (B.Sc. Computer Science / Cybersecurity Specialist)  
**Standards**: IEEE 829 Test Documentation, ISTQB Certified Tester Foundation, OWASP API Security Top 10  
**Test Case Matrix**: [Google Sheets Live Matrix](https://docs.google.com/spreadsheets/d/1-3cJ7OpcyEH6wcFdAwVZBZO1ayJWbJp_vnRWuhVoDbE/edit?gid=0#gid=0)

---

## Executive Summary

Repositori ini mendokumentasikan rangkaian pengujian komprehensif, arsitektur otomasi, dan jaminan mutu perangkat lunak (*Software Quality Assurance & SDET*) untuk **Lumiina**, platform galeri seni dan ilustrasi anime berbasis arsitektur Go Clean Architecture, PostgreSQL, Redis, dan React SPA.

Pengujian dirancang secara deterministik dari level integrasi fungsional (Black-Box & White-Box Analysis), validasi batas data (Equivalence Partitioning & Boundary Value Analysis), pengujian keamanan (Anti-Enumeration, Rate Limiting, Input Sanitization), hingga otomasi E2E dan performa.

---

## Metrik Eksekusi Pengujian (Module 3: Test Matrix)

| Metrik | Nilai | Keterangan |
|---|---|---|
| **Total Test Cases** | 13 | 8 Kasus Uji Auth, 5 Kasus Uji Artwork Studio |
| **Passed** | 12 | 92.3% |
| **Failed** | 1 | 7.7% (Defect TC_AUTH_001: Backend raw validator leakage) |
| **Blocked / Skipped** | 0 | 0.0% |
| **Pass Rate** | **92.3%** | Memenuhi ambang batas rilis (Release Quality Gate >= 90%) |
| **Dokumentasi Detail** | [Test Case Matrix (IEEE 829)](./01-fundamentals-and-test-cases/03-test-case-matrix-lumiina.md) | Tabel kasus uji lengkap dengan preconditions, steps, dan actual results |

---

## Ruang Lingkup Pengujian (Test Scope)

### In-Scope
1. **Authentication & Session Lifecycle**:
   - Registrasi pengguna (Happy Path, Boundary Value Analysis, Password Complexity Enforcement).
   - Login & Session State (JWT storage, User Profile Resolution, Defensive UI state).
   - Password Recovery & Security (Anti-Account Enumeration, 15-minute token TTL).
   - Rate Limiting & Brute-Force Defense (Redis token bucket per IP namespace).
2. **Artwork Publishing Studio & Feed**:
   - Unggah karya seni (MIME sniffing, client-side canvas optimization, metadata validation).
   - Validasi file negatif (Pencegahan file non-gambar dan deteksi manipulasi ekstensi/spoofing).
   - Interaksi sosial (State persistence Like/Unlike, Bookmarks, dan Comments).
   - Pembatasan hak akses tamu (Guest boundary enforcement).

### Out-of-Scope
- Sistem pembayaran / monetisasi seniman (Fitur belum tersedia di rilis saat ini).
- Pengujian kompatibilitas peramban warisan (Internet Explorer / Opera Mini).

---

## Struktur Direktori

```
lumiina-qa-automation/
├── .gitignore                          # Konfigurasi pengabaian berkas dependensi dan output pengujian
├── README.md                           # Dokumentasi utama dan ringkasan eksekutif
├── KURIKULUM_QA.md                     # Roadmap silabus QA & SDET 9 modul
├── Bug Evidence/                       # Artefak bukti kegagalan (screenshots, network logs)
│   └── 001.png                         # Bukti kegagalan TC_AUTH_001 (Alphanumeric raw error)
├── 01-fundamentals-and-test-cases/     # Modul 1-3: Catatan fundamental, desain uji, matriks IEEE 829
│   ├── 01-qa-fundamentals-notes.md     # Ringkasan STLC, level pengujian, dan 7 prinsip ISTQB
│   ├── 02-test-design-techniques-notes.md # Ringkasan teknik EP, BVA, Decision Table, State Transition
│   └── 03-test-case-matrix-lumiina.md  # Matriks kasus uji 13 baris lengkap
├── 02-bug-reports/                     # Modul 4: Laporan cacat formal (Defect reports)
├── 03-api-automation-postman/          # Modul 5: Postman collections, environments, dan Newman CLI
├── 04-e2e-automation-playwright/       # Modul 6: Skrip otomasi browser Playwright (Page Object Model)
├── 05-performance-k6/                  # Modul 7: Skrip uji beban k6 (p95 latency, throughput, stress test)
└── 06-cicd-pipeline/                   # Modul 8: Konfigurasi GitHub Actions CI pipeline
```

---

## Tumpukan Teknologi Pengujian (Testing Stack)

- **Manual Testing & Documentation**: Google Sheets (IEEE 829 Standard), Markdown Defect Logs.
- **API Automation**: Postman, Newman CLI, JavaScript (Chai assertions).
- **Web UI Automation**: Playwright, TypeScript / JavaScript, Page Object Model (POM).
- **Performance & Load Testing**: Grafana k6 (Load, Stress, Spike testing).
- **Version Control & Defect Tracking**: Git, GitHub Issues, Conventional Commits.
