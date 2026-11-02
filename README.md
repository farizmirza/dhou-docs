# Dhou Documentation (dhou-docs)

Dokumentasi lengkap perancangan, arsitektur, dan pengembangan aplikasi **Dhou (تطبيق ضوء)** — Aplikasi Pembelajaran Bahasa Arab (*Fusha* & *'Ammiyyah*).

Repository Utama Aplikasi: [github.com/farizmirza/dhou-app](https://github.com/farizmirza/dhou-app)

---

## 📚 Daftar Berkas Dokumentasi

1. **[RESEARCH_REFERENCE_MAPPING.md](RESEARCH_REFERENCE_MAPPING.md)**
   - Acuan resmi pemetaan aplikasi berdasarkan paper riset:
     *"Dhou: A Concept of Developing an Arabic Language Learning Application for fusha and 'ammiyyah in the Age of Artificial Intelligence"* oleh Fariz Mirza Abdillah.
   - Memuat siklus belajar 4 pilar (*Contextual Input $\rightarrow$ Structured Practice $\rightarrow$ Communicative Production $\rightarrow$ Reinforcement*), 17 fungsi video, 20 tipe aktivitas kuis, dan *variety consistency guardrail*.

2. **[PRD.md](PRD.md)**
   - *Product Requirements Document* resmi Dhou.
   - Spesifikasi fitur, arsitektur informasi, non-functional requirements (RTL, audio latency, iOS safe area), dan metrik keberhasilan.

3. **[DEVELOPMENT_PLAN.md](DEVELOPMENT_PLAN.md)**
   - Rencana aksi pengembangan (*action plan*) bertahap berbasis *Design-Driven Development (DDD)*.
   - Roadmap implementasi MVP, integrasi Supabase, dan pembungkusan PWA ke iOS App Store via CapacitorJS.

4. **[design-system.md](design-system.md)**
   - Spesifikasi sistem desain yang diekstrak langsung dari file Figma Dhou.
   - Token warna (Warm Orange `#FF8904`, Dialect Blue `#51A2FF`), tipografi (Fredoka & Amiri), dan komponen layout mobile shell.

5. **[vibe-coding-guidelines.md](vibe-coding-guidelines.md)**
   - Panduan kedisiplinan kode agar tetap modular, mudah dirawat (*maintainable*), batasan ukuran komponen, penanganan teks Arab RTL, dan aturan version control.

6. **[Requirements/PRD/PRD1 - User Authentication, Sign In, and Password Recovery.md](Requirements/PRD/PRD1%20-%20User%20Authentication,%20Sign%20In,%20and%20Password%20Recovery.md)**
   - *Product Requirement Document* (PRD1) spesifik untuk modul Autentikasi Dhou: Masuk Email/Sandi, Google SSO, Registrasi Akun, Lupa Kata Sandi Mandiri, Logout, User Stories (US01–US06), dan Business Rules.

7. **[Requirements/FRD/FRD1 - User Authentication, Sign In, and Password Recovery.md](Requirements/FRD/FRD1%20-%20User%20Authentication,%20Sign%20In,%20and%20Password%20Recovery.md)**
   - *Functional Requirements Document* (FRD1) detail implementasi teknis: Requirement traceability matrix, spesifikasi fungsional FR-AUTH-01–06, kontrak API definitif, skema DDL PostgreSQL, skema validasi Zod, dan matriks 25 kasus ekstrem fungsional (EC-01–EC-G06).

8. **[Requirements/PRD/PRD2 - Gamified Learning Path, Sentence Arrangement, Energy, and Daily Streak.md](Requirements/PRD/PRD2%20-%20Gamified%20Learning%20Path,%20Sentence%20Arrangement,%20Energy,%20and%20Daily%20Streak.md)**
   - *Product Requirement Document* (PRD2) modul gamifikasi Dhou: Navigasi peta visual (*Learning Path Tree*), latihan susun kata Arab (10 butir soal, 2 kata pengecoh, *error re-queue*), mesin api streak harian (🔥), dan arsitektur kuota energi dengan promo *Unlimited Energy* untuk MVP.

9. **[Requirements/FRD/FRD2 - Gamified Learning Path, Sentence Arrangement, Energy, and Daily Streak.md](Requirements/FRD/FRD2%20-%20Gamified%20Learning%20Path,%20Sentence%20Arrangement,%20Energy,%20and%20Daily%20Streak.md)**
   - *Functional Requirements Document* (FRD2) detail implementasi teknis: Requirement traceability matrix, spesifikasi fungsional FR-PATH-01–06, kontrak API definitif (overview, start, submit, finish, timezone), skema DDL PostgreSQL dengan RLS, Zod schemas, dan matriks 25 kasus ekstrem (EC-01–EC-25).

