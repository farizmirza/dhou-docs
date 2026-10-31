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

6. **[AUTH_PRD.md](AUTH_PRD.md)**
   - *Product Requirement Document* spesifik untuk modul Autentikasi Dhou (Login, Register, Lupa Kata Sandi, Google SSO, Logout, User Stories US01–US06, dan Business Rules).

7. **[AUTH_FRD.md](AUTH_FRD.md)**
   - *Functional Requirements Document* detail modul Autentikasi: Traceability matrix, spesifikasi fungsional FR-AUTH-01–06, kontrak API definitif, skema DDL PostgreSQL, skema validasi Zod, dan matriks kasus ekstrem fungsional (EC-01–EC-G06).

