# Product Requirement Document (PRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | PRD6 |
| **Feature ID** | ADM-CORE, ADM-CMS-01, ADM-CMS-02, ADM-AI-03, ADM-VOCAB-04, ADM-OPS-05, ADM-ANL-06, ADM-SEC-07 |
| **Feature Name** | Dhou Administrative Portal & Content Management System (Desktop Only) |
| **Application** | Dhou Admin Portal (تطبيق ضوء — لوحة التحكم) |
| **Target Platform** | **Desktop Web Only** (Resolusi Minimal $\ge 1024\text{px}$, Rekomendasi Utama $1440\times 900\text{px}$ & $1920\times 1080\text{px}$) |
| **Module** | Back-Office Operations, Linguistic CMS, Prompt Engineering Studio, User Support & Analytics |
| **Sprint** | Sprint 6–8 |
| **Epic** | Back-Office Infrastructure & Content Operations |
| **Priority** | High (P1) |
| **Status** | Draft Ready for Review |
| **Implementation Status** | Planned |
| **Version** | 1.0 |
| **Last Updated** | 2026-11-23 |
| **Product Owner** | Fariz Mirza Abdillah |
| **Dependencies** | Supabase DB & Auth (RLS, Service Role), Cloudflare R2 / S3 (Video & Audio Storage), LLM APIs (Gemini 1.5/2.0 Flash/Pro, OpenAI), ElevenLabs/Azure TTS Arabic, PostHog/Google Analytics |

---

## 1. Executive Summary & Business Problem

### 1.1 Latar Belakang & Problem Statement
Dhou adalah aplikasi edutech pembelajaran bahasa Arab (*Fusha* dan *'Ammiyyah*) berbasis gamifikasi dan AI. Karakteristik inti Dhou menuntut akurasi linguistik yang sangat tinggi:
1. **Kompleksitas Diglosia & Variasi Dialek:** Kurikulum Dhou tidak hanya mengajarkan bahasa formal (*Fusha*), melainkan dialek lisan (*'Ammiyyah Mesir* dan *'Ammiyyah Syam*). Setiap butir materi, video, dan prompt AI harus dijaga ketat agar tidak terjadi pencampuran dialek (*dialect leakage*) tanpa anotasi yang jelas.
2. **Kebutuhan Format Teks Arab Berharakat (Tasykil):** Teks Arab membutuhkan tanda baca harakat lengkap dan penanganan bidirectional (RTL) yang tepat. Salah harakat akan mengubah makna gramatikal (*I'rab*) dan pelafalan.
3. **Sinkronisasi Multimodal Presisi Tinggi:** Modul video kontekstual membutuhkan penataan subtitel interaktif kata-per-kata (*word-level timing*) dan segmen audio *shadowing* dengan akurasi milidetik.
4. **Volume Soal & Tipe Latihan Interaktif:** Terdapat 20 tipe aktivitas kuis (susun kalimat, tebak audio, mencocokkan kata, dll.) yang memerlukan penyusunan bank soal dinamis, kata pengecoh (*distractor*), dan penandaan tingkat kesulitan.

Saat ini, pengelolaan materi masih mengandalkan file migrasi SQL mentah (*seed files*) atau berkas spreadsheet manual. Hal ini menimbulkan masalah operasional serius:
- **Tingginya Human Error:** Risiko salah input harakat, salah tag dialek, atau kesalahan penulisan skema JSON soal.
- **Bottleneck Tim Engineering:** Setiap penambahan konten pelajaran, revisi subtitel video, atau penyesuaian instruksi prompt AI membutuhkan campur tangan developer untuk *push code* atau manipulasi database langsung.
- **Ketiadaan Visibilitas Operasional:** Tim kurikulum dan *customer support* tidak memiliki alat untuk memantau konsumsi token AI, latensi audio TTS, atau mengatasi keluhan pengguna terkait bug streak harian dan energi.

### 1.2 Solusi Produk: Dhou Admin Portal (Desktop Only)
Dhou Admin Portal adalah aplikasi web internal berbasis **khusus desktop (*desktop-only*)** yang dirancang sebagai pusat kendali operasional Dhou. Portal ini memfasilitasi tim kurikulum, ahli bahasa Arab (*linguist*), manajer produk, dan tim operasional untuk menyusun kurikulum, mengedit subtitel video, mengonfigurasi persona & prompt AI, mengelola kamus dialek, serta mengawasi metrik performa aplikasi.

---

## 2. Business Value & Strategic Impact

- **Akselerasi Produksi Konten (Content Velocity):** Memangkas waktu penyusunan modul dari 3 hari kerja menjadi $< 2$ jam per unit belajar dengan visual builder dan live simulator preview.
- **Jaminan Kualitas Linguistik (Linguistic Quality Assurance):** Validator harakat otomatis, editor RTL yang ergonomis, dan *Dialect Consistency Guardrail* mencegah lolosnya materi yang cacat ke aplikasi produksi pengguna.
- **Efisiensi Operasional & Biaya AI (Cost & Observability):** Pemantauan kuota token LLM, biaya API per sesi percakapan, dan latensi TTS secara *real-time* untuk mencegah lonjakan biaya tak terkendali.
- **Resolusi Dukungan Pengguna Cepat (Support Agility):** Tim support dapat menyelesaikan tiket perselisihan streak harian atau bug pengisian energi pengguna dalam $< 1$ menit melalui antarmuka pencarian dan inspeksi terpadu.

---

## 3. Justifikasi Desain "Hanya Desktop" (Desktop-Only Mandate)

Admin Dhou diputuskan secara tegas beroperasi **HANYA PADA PERANGKAT DESKTOP / LAPTOP** dengan pertimbangan ergonomis dan produktivitas tingkat tinggi:

1. **Kepadatan Informasi Ekstrem (High-Density UI):** Pengelolaan kurikulum bahasa Arab membutuhkan penataan struktur bertingkat (*Hierarchy Tree*: Dialek $\rightarrow$ Unit $\rightarrow$ Level $\rightarrow$ Lesson $\rightarrow$ Exercise). Hal ini memerlukan ruang layar horizontal luas untuk tabel data interaktif dengan multi-filter dan sortir dinamis.
2. **Editor Bersisian (Split-View / Live Mobile Preview):** Tim kurikulum wajib melihat bagaimana soal atau subtitel video tampil di layar ponsel pengguna secara *real-time* (panel editor di kiri dan simulator layar ponsel 390px di kanan).
3. **Editor Subtitel Berbasis Waveform:** Sinkronisasi audio/video memerlukan scrubbing audio *timeline* horizontal presisi tinggi menggunakan kursor mouse dan shortcut keyboard, yang mustahil dilakukan secara presisi di layar ponsel.
4. **Tipografi Arab Multibahasa (Bidi Editing):** Mengetik teks Arab berharakat (`dir="rtl"`, font Amiri $\ge 18\text{px}$) bersamaan dengan teks terjemahan bahasa Indonesia (`dir="ltr"`) membutuhkan keyboard fisik dan bidang input yang lapang.
5. **Kebijakan Akses Keamanan:** Akses administratif hanya diizinkan melalui browser workstation resmi dengan proteksi session timeout dan jaringan aman kantor/VPN, meminimalisir risiko operasional dari perangkat seluler yang rentan hilang atau terintip.

> [!IMPORTANT]
> **Desktop-Only Gatekeeper:** Jika portal admin dibuka pada layar dengan lebar $< 1024\text{px}$ atau terdeteksi menggunakan *user-agent* mobile/tablet, sistem akan secara otomatis memblokir interaksi dan menampilkan layar edukatif:  
> *"Dhou Admin Portal dirancang khusus untuk layar desktop ($\ge 1024\text{px}$) guna mendukung pengeditan kurikulum, manajemen subtitel video, dan konfigurasi AI dengan presisi tinggi. Silakan buka melalui komputer atau laptop Anda."*

---

## 4. Target Pengguna (User Persona)

| Persona & Peran | Tanggung Jawab Utama | Kebutuhan Kunci di Desktop Portal |
| :--- | :--- | :--- |
| **Ustadz / Arabic Linguist (Ahmad, 38 th)** | Memverifikasi ketepatan harakat Fusha, keotentikan dialek Mesir/Syam, dan kewajaran terjemahan. | Editor teks Arab dengan font Amiri yang jelas, pintasan keyboard harakat (*tasykil shortcuts*), kamus akar kata (*jadzr*), dan pemutar audio instan. |
| **Curriculum & Instructional Designer (Fathia, 29 th)** | Menyusun urutan rute belajar (*learning path*), membuat 20 variasi kuis, menentukan kata pengecoh, dan menyeimbangkan tingkat kesulitan. | Visual node editor pohon rute, drag-and-drop urutan soal, simulator pratinjau mobile *side-by-side*, validasi kelengkapan soal. |
| **Video Content Specialist (Rian, 27 th)** | Mengunggah klip video percakapan otentik (30–90 detik), mengatur timestamp subtitel kata-per-kata, dan menandai potongan frasa *shadowing*. | Scrubbing audio waveform visual, penandaan segmen *in-point / out-point* dengan shortcut spasi/tombol arah, visualisasi sinkronisasi subtitle. |
| **AI Prompt Engineer / Product Lead (Fariz, 26 th)** | Merancang persona bot percakapan (Dhou AI), memperketat instruksi dialek, mengatur parameter LLM (temperature, top-p), memantau biaya token. | Playground uji coba interaktif percakapan AI, pengujian skenario multi-dialek, panel analitik token burn dan latensi STT/TTS. |
| **Customer Support & Community Ops (Dina, 25 th)** | Menangani keluhan pengguna (streak terputus, energi habis akibat bug), moderasi laporan kalimat janggal dari pengguna. | Pencarian cepat ID/email user, inspeksi log aktivitas belajar harian, tombol koreksi manual streak/energi, antarmuka penanganan laporan *flagged questions*. |
| **Super Administrator (Tech Lead / DevOps)** | Mengatur akun staf admin, hak akses (RBAC), audit logging, konfigurasi *environment* (Staging vs Production). | Matriks izin peran (*role permissions*), log audit audit trail yang tidak dapat diubah (*immutable*), toggle pemeliharaan sistem. |

---

## 5. Objectives & Key Results (OKRs)

1. **Efisiensi Kurikulum:** Memangkas durasi pembuatan 1 Unit Belajar (berisi 5 Level dan 50 butir latihan terverifikasi) dari 24 jam kerja menjadi $\le 3$ jam kerja.
2. **Nol Cacat Linguistik (Zero Linguistic Defect):** Mengurangi angka laporan kesalahan harakat (*harakat typo*) dan kekeliruan dialek pada aplikasi mobile hingga $< 0.2\%$ dari seluruh butir soal terbitan.
3. **Kecepatan Resolusi Dukungan:** Memungkinkan tim customer support menyelesaikan $95\%$ tiket komplain streak/energi dalam waktu $\le 60$ detik sejak pencarian akun.
4. **Visibilitas Biaya AI:** Menyajikan estimasi biaya API LLM dan TTS per hari dengan deviasi $< 3\%$ terhadap tagihan riil penyedia API.

---

## 6. Scope of Work

### In Scope
1. **Desktop Viewport Gatekeeper & Safety System (ADM-CORE):**
   - Deteksi ukuran layar ($\ge 1024\text{px}$) dan perangkat desktop.
   - Layar pemblokiran responsif elegan untuk perangkat mobile/tablet.
   - Layout desktop adaptif optimal pada resolusi $1280\times 800$, $1440\times 900$, dan $1920\times 1080$.
2. **Modul 1: Curriculum & Gamified Path CMS (ADM-CMS-01):**
   - Manajemen struktur hirarki: Dialek (*Fusha*, *Egyptian*, *Levantine*) $\rightarrow$ Unit $\rightarrow$ Level $\rightarrow$ Lesson.
   - Editor latihan interaktif mendukung 20 variasi tipe soal Dhou (Susun Kata, Tebak Suara, Terjemahan, Isian Kosong, dsb.).
   - RTL Arabic Text Editor dengan font Amiri/Noto Sans Arabic, *tasykil toolbar*, dan validator harakat.
   - Dual-pane simulator preview (pratinjau interaktif real-time ukuran layar iPhone 390px).
3. **Modul 2: Video Learning & Subtitle Studio (ADM-CMS-02):**
   - Manajemen berkas video edukasi (upload ke cloud storage, metadata dialek, judul, durasi 30–90 detik).
   - Audio waveform timeline scrubber dengan kontrol playback kecepatan variabel (0.5x, 0.75x, 1.0x).
   - Editor subtitel interaktif kata-per-kata berharakat dengan penanda waktu (*timestamp start-end* milidetik).
   - Penanda segmen latihan berbicara (*Shadowing Clip Segmenter*).
4. **Modul 3: Conversational AI & Prompt Studio (ADM-AI-03):**
   - Manajemen persona lawan bicara Dhou AI (nama karakter, latar belakang profesi, avatar, tingkat keramahan).
   - Konfigurasi system prompt dengan *Strict Dialect Guardrails* (Fusha, Mesir, Syam).
   - Pengaturan hyperparameter model (Pilihan LLM: Gemini Flash/Pro, GPT-4o, Temperature, Top-P, Max Tokens).
   - Sandbox Playground: Pengujian obrolan langsung di dalam admin dengan panel inspeksi *prompt token*, *latency*, dan *raw JSON response*.
5. **Modul 4: Master Kamus Dialek & Kosakata SRS (ADM-VOCAB-04):**
   - Pangkalan data master kata & frasa (Akar kata/Jadzr, Lemma, Varian Harakat, Terjemahan Indonesia, Kelas Kata).
   - Pemetaan ekuivalensi dialek (*Cross-Dialect Mapping*: misal kata Fusha ماذا vs Mesir إيه vs Syam شو).
   - Pengelolaan file audio pelafalan native (upload, rekam langsung, atau auto-generate TTS).
6. **Modul 5: User Support & Learning Progress Operations (ADM-OPS-05):**
   - Pencarian pengguna (berdasarkan ID Supabase, Nama, Email terdaftar).
   - Dasbor inspeksi profil belajar: Riwayat streak harian (🔥), saldo energi (🔋), level XP, dan status pohon belajar.
   - Aksi manual yang diaudit: Perbaikan streak (*Streak Freeze/Restore Override*), penambahan energi, reset password manual, dan suspend/ban akun.
   - Moderasi laporan konten bermasalah (*Flagged Content Queue*) yang dikirimkan oleh pengguna aplikasi mobile.
7. **Modul 6: Analytics & System Observability (ADM-ANL-06):**
   - Metrik aktivitas: DAU, WAU, MAU, retensi hari ke-1, 7, 30.
   - Corong penyelesaian pelajaran (*Lesson Completion Funnel*) per dialek.
   - Dasbor biaya & performa AI: Konsumsi token harian, biaya estimasi USD/IDR, dan metrik latensi p95 STT/TTS.
8. **Modul 7: RBAC, Keamanan & Audit Trail (ADM-SEC-07):**
   - Role-Based Access Control: Super Admin, Content Editor, Arabic Linguist, Customer Support, Read-Only Analyst.
   - Wajib Multi-Factor Authentication (MFA / TOTP Authenticator App).
   - *Immutable Audit Trail Log*: Mencatat setiap aksi `CREATE`, `UPDATE`, `DELETE`, dan `OVERRIDE` lengkap dengan User ID, Timestamp, IP Address, dan perubahan *diff data* sebelum vs sesudah.

### Out of Scope
- Antarmuka versi mobile web atau aplikasi seluler native untuk admin (ditegaskan secara fungsional ditiadakan).
- *In-browser video transcoding/rendering engine* (proses konversi kompresi video dilakukan secara otomatis oleh worker Cloudflare Stream / Supabase Storage Webhook).
- Gateway pencairan dana manual / direct bank transfer admin (pembayaran langganan dikelola via Apple App Store In-App Purchase & Stripe Dashboard).

---

## 7. Information Architecture & Desktop Navigation Layout

Antarmuka desktop Dhou Admin menggunakan layout standar enterprise modern: **Left Sidebar + Top Navigation Bar + Main Dynamic Canvas (Split/Full) + Right Auxiliary Drawer/Simulator**.

```text
┌────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│ TOP HEADER BAR: [Logo Dhou Admin] [Env: Production ▾] [Dialect Filter: All ▾]  🔍 Global Search  [Profile] │
├──────────────┬────────────────────────────────────────────────────────────┬────────────────────────────┤
│ LEFT SIDEBAR │ MAIN CONTENT WORKSPACE                                     │ RIGHT AUXILIARY PANEL      │
│              │                                                            │ (Contextual Simulator)     │
│ 📊 Dashboard │  Breadcrumbs: Kurikulum > Mesir > Unit 1 > Soal #4          │ ┌────────────────────────┐ │
│ 📚 Kurikulum │ ────────────────────────────────────────────────────────── │ │  📱 Mobile Preview     │ │
│ 🎬 Video CMS │  [Form Input Soal (Dual Directionality)]                   │ │     (Width: 390px)     │ │
│ 🤖 Dhou AI   │  - Tipe: [Susun Kata (Sentence Scramble) ▾]                │ │ ┌────────────────────┐ │ │
│ 📖 Kamus     │  - Teks Arab (RTL): [ كَيْفَ حَالُكَ اليَوْمَ ؟ ]          │ │ │ [TopBar Dhou]      │ │ │
│ 👥 Pengguna  │  - Terjemahan:      [ Bagaimana kabarmu hari ini? ]        │ │ │                    │ │ │
│ 🚩 Moderasi  │  - Kata Pengecoh:   [ أَنْتَ ] [ هُوَ ]                    │ │ │ كَيْفَ  حَالُكَ    │ │ │
│ 📈 Analitik  │  - Audio Pronounce: [ 🔊 audio_unit1_04.mp3 (Play) ]       │ │ │                    │ │ │
│ ⚙️ Pengaturan│ ────────────────────────────────────────────────────────── │ │ └────────────────────┘ │ │
│              │  [Tombol Aksi: Simpan Draf]  [Terbitkan ke Staging/Prod]   │ └────────────────────────┘ │
└──────────────┴────────────────────────────────────────────────────────────┴────────────────────────────┘
```

### 7.1 Spesifikasi Ergonomi Desktop
1. **Sidebar Navigasi (Left):**
   - Lebar tetap `240px` (dapat diciutkan / *collapsible* menjadi `64px` mini-icon rail untuk memaksimalkan area kerja).
   - Akses keyboard cepat: `Ctrl/Cmd + 1` hingga `Ctrl/Cmd + 8` untuk berpindah menu utama.
2. **Top Header Bar:**
   - Tinggi tetap `56px`.
   - Indikator Lingkungan Kerja Terang: Badge oranye menyala jika berada di **[PRODUCTION]**, badge biru jika di **[STAGING]**.
   - Dialect Global Switcher: Opsi cepat memfilter seluruh tampilan kurikulum ke satu dialek tertentu (*Fusha* / *Mesir* / *Syam*).
   - Global Omnibox Search (`Cmd/Ctrl + K`): Pencarian instan untuk melompat ke ID pengguna, judul video, nama unit, atau kosakata tertentu.
3. **Main Content Workspace:**
   - Mengadopsi prinsip *Split-Pane Responsive Layout*:
     - Mode 1: **Full Table Mode** (lebar 100%) untuk melihat data master, tabel pengguna, dan laporan analitik.
     - Mode 2: **Editor + Mobile Simulator** (65% form editor di kiri, 35% interactive simulator di kanan) untuk pembuatan kuis dan subtitel video.
4. **Mobile Simulator Interaktif (Right Pane):**
   - Bingkai visual berskala presisi seukuran viewport iPhone 14/15/16 Pro ($390\times 844\text{px}$).
   - Merender komponen UI mobile Dhou yang sebenarnya menggunakan Web Components/iFrame sandbox.
   - Setiap perubahan karakter harakat atau kata pengecoh langsung diperbarui secara *live* ($< 100\text{ms}$ debounce) tanpa perlu refresh halaman.

---

## 8. Detailed Functional Requirements (Spesifikasi Fitur)

### Modul 0: Desktop Viewport Gatekeeper (ADM-CORE)

| ID Fitur | Spesifikasi Kebutuhan |
| :--- | :--- |
| **FR-CORE-01** | **Viewport Width Guard:** Sistem wajib memeriksa resolusi layar browser klien secara berkelanjutan. Jika lebar jendela browser $< 1024\text{px}$, seluruh antarmuka admin ditutup dan digantikan oleh *Desktop Enforcement Screen*. |
| **FR-CORE-02** | **Device Type Detection:** Jika aplikasi diakses dari perangkat mobile/tablet melalui deteksi *pointer: coarse* dan *user-agent*, tampilkan peringatan pemblokiran ramah dengan panduan membuka di laptop/PC. |
| **FR-CORE-03** | **Workspace Resizing Safety:** Jika pengguna mengecilkan jendela browser dari $\ge 1024\text{px}$ ke $< 1024\text{px}$ saat sedang mengedit formulir tanpa disimpan, sistem wajib menahan status draf (*in-memory state autosave*) sehingga data tidak hilang saat jendela dibesarkan kembali. |

---

### Modul 1: Curriculum & Gamified Path CMS (ADM-CMS-01)

| ID Fitur | Spesifikasi Kebutuhan |
| :--- | :--- |
| **FR-CMS-01** | **Hierarchical Path Tree Manager:** Menampilkan visual tree struktur kurikulum: Dialek $\rightarrow$ Unit Belajar $\rightarrow$ Level (1–5) $\rightarrow$ Lesson $\rightarrow$ Exercise Items. Admin dapat menambah, mengurutkan ulang (*drag-and-drop*), menggandakan (*duplicate*), atau mengarsipkan unit. |
| **FR-CMS-02** | **20-Exercise Interactive Question Builder:** Mendukung konfigurasi lengkap untuk 20 tipe aktivitas Dhou (merujuk pada paper riset Dhou):<br>1. *Sentence Scramble* (Susun Kata)<br>2. *Word-Meaning Match* (Pasang Kata-Arti)<br>3. *Audio Listen & Choose*<br>4. *Fill in the Blank*<br>5. *Shadowing Pronunciation Record*, dll.<br>Formulir secara dinamis menyesuaikan input berdasarkan tipe soal yang dipilih. |
| **FR-CMS-03** | **Bidi Arabic RTL Editor with Tasykil Tools:** Area input teks Arab wajib menggunakan font `Amiri` berukuran minimal `18px` dengan line-height `1.8` dan atribut `dir="rtl"`. Menyediakan *floating virtual diacritics keyboard* (Fathah, Kasrah, Dhommah, Sukun, Syaddah, Tanwin) untuk mempermudah pengetikan di keyboard Latin biasa. |
| **FR-CMS-04** | **Harakat Completeness Validator:** Saat admin mengklik "Simpan", sistem memvalidasi teks Arab. Jika ditemukan huruf tanpa harakat pada materi tingkat pemula (Level 1–3), sistem menampilkan peringatan konfirmasi: *"Perhatian: Terdapat 3 huruf tanpa harakat. Apakah ini disengaja?"* |
| **FR-CMS-05** | **Distractor Word Generator:** Untuk soal susun kata (*Sentence Scramble*), sistem menyediakan tombol *"Saran Pengecoh Otomatis"* berbasis kamus kosakata sepadan agar editor tidak perlu memikirkan kata pengecoh secara manual. |
| **FR-CMS-06** | **Live Mobile Simulator Sync:** Setiap ketukan tombol di editor soal langsung terproyeksikan ke simulator mobile di panel kanan dengan haptik/animasi yang identik dengan aplikasi pengguna asli. |

---

### Modul 2: Video Learning & Interactive Subtitle Studio (ADM-CMS-02)

| ID Fitur | Spesifikasi Kebutuhan |
| :--- | :--- |
| **FR-VID-01** | **Video Asset Ingestion:** Antarmuka upload video (format MP4/WebM, resolusi maksimal 1080p, durasi 30–90 detik, ukuran file $\le 100\text{MB}$). Dilengkapi tagging metadata: Dialek (*Fusha* / Mesir / Syam), Topik/Tema, dan Tingkat Kesulitan (CEFR A1, A2, B1). |
| **FR-VID-02** | **Waveform Timeline & Precision Scrubbing:** Menampilkan visualisasi bentuk gelombang audio (*audio waveform*) dari video. Admin dapat melakukan navigasi waktu presisi menggunakan shortcut keyboard: `Spasi` (Play/Pause), `Tombol Panah Kiri/Kanan` (Maju/Mundur 100ms), `Shift + Panah` (Maju/Mundur 1 detik). |
| **FR-VID-03** | **Interactive Subtitle Segmenter (Word & Sentence Level):** Admin dapat membuat balok segmen subtitel langsung di timeline. Setiap balok memuat:<br>- *Start Time* & *End Time* (akurasi hingga 10 milidetik)<br>- Teks Arab berharakat (`dir="rtl"`)<br>- Terjemahan resmi Bahasa Indonesia<br>- Transliterasi latin opsional. |
| **FR-VID-04** | **Shadowing Marker Tool:** Menandai segmen kalimat spesifik di dalam video sebagai "Poin Latihan Shadowing", di mana pengguna mobile akan diinstruksikan merekam suara mereka menirukan video. |
| **FR-VID-05** | **Auto-Sync / AI Transcription Draft:** Tombol *"Transkrip Otomatis via AI"* yang memanfaatkan model Whisper / Gemini untuk menghasilkan draf awal subtitel ber-timestamp, yang kemudian diverifikasi dan disempurnakan oleh editor manusia (*Human-in-the-Loop*). |

---

### Modul 3: Conversational AI & Prompt Studio (ADM-AI-03)

| ID Fitur | Spesifikasi Kebutuhan |
| :--- | :--- |
| **FR-AI-01** | **Persona & Scenario Catalog:** Admin dapat membuat dan mengubah profil mitra percakapan AI (misal: "Ahmad sang Barista Kairo", "Maryam Mahasiswi Damaskus", "Petugas Imigrasi Bandara Riyadh"). Metadata meliputi: Nama, Foto Avatar, Lokasi Skenario, Dialek Target, dan Batasan Kosakata. |
| **FR-AI-02** | **System Prompt Builder with Dialect Guardrails:** Editor teks dengan penanda variabel dinamis (seperti `{user_name}`, `{target_dialect}`, `{current_topic}`) untuk menyusun instruksi sistem AI. Dilengkapi *guardrail rules* standar untuk mencegah AI membalas dengan dialek selain yang dipilih skenario. |
| **FR-AI-03** | **Model & Hyperparameter Tuning:** Pemilihan mesin LLM per skenario (Gemini 1.5 Flash, Gemini 2.0 Flash, GPT-4o-mini), pengaturan *Temperature* (0.0–1.0), *Top-P*, dan *Max Tokens*. Pengaturan suara TTS (Penyedia: ElevenLabs atau Azure Speech Arabic, Voice ID, Speech Rate 0.8x–1.1x). |
| **FR-AI-04** | **Interactive AI Sandbox Playground:** Panel uji coba percakapan interaktif desktop di mana admin dapat mengobrol langsung (via teks maupun mikrofon laptop) dengan persona yang sedang dikonfigurasi. Menampilkan metrik *real-time*: jumlah token input/output, latensi respon pesan pertama ($T_0$), dan evaluasi kepatuhan dialek. |

---

### Modul 4: Kamus Terpadu & Master Dialect Bank (ADM-VOCAB-04)

| ID Fitur | Spesifikasi Kebutuhan |
| :--- | :--- |
| **FR-VOC-01** | **Master Lexicon Repository:** Basis data kosakata terpusat dengan filter akar kata (*Jadzr* 3 atau 4 huruf), lemma, harakat lengkap, arti bahasa Indonesia, dan contoh kalimat. |
| **FR-VOC-02** | **Cross-Dialect Equivalence Matrix:** Menautkan kosakata antara ragam bahasa: entri Fusha dapat dihubungkan ke padanan kata dalam ragam Mesir (*'Ammiyyah Mishriyyah*) dan Syam (*'Ammiyyah Syamiyyah*), lengkap dengan catatan pergeseran fonologis (misal: huruf *Qaf* dilafalkan sebagai Hamzah di Kairo). |
| **FR-VOC-03** | **Audio Pronunciation Manager:** Pengelolaan aset audio native per kata. Admin dapat mengunggah file `.mp3`/`.wav` manusia asli atau memicu pembuatan audio berkualitas tinggi melalui integrasi Text-to-Speech resmi Dhou. |

---

### Modul 5: User Support & Learning Progress Operations (ADM-OPS-05)

| ID Fitur | Spesifikasi Kebutuhan |
| :--- | :--- |
| **FR-OPS-01** | **Global User Search & Detail Inspector:** Pencarian pengguna berdasarkan Supabase User UUID, Email, atau Nama Pengguna. Menampilkan ringkasan status akun, tanggal registrasi, perangkat terakhir, dan riwayat login. |
| **FR-OPS-02** | **Gamification State Inspection & Override:** Menampilkan status gamifikasi riil pengguna: jumlah hari Streak (🔥), riwayat freeze, saldo Energi saat ini (🔋), dan riwayat XP. Menyediakan tombol aksi operasional dengan dialog konfirmasi & alasan wajib:<br>- *Restore / Freeze Streak Manual* (misal: kompensasi downtime server)<br>- *Refill Energy Manual* (+5 Energi atau Unlimited Energy voucher)<br>- *Reset User Progress* pada unit tertentu. |
| **FR-OPS-03** | **Account Moderation & Security Actions:** Tombol bekukan akun (*Suspend Account*) dengan masa berlaku tertentu atau blokir permanen (*Ban User*) jika terdeteksi melakukan pelanggaran komunitas atau abuse API AI. |
| **FR-OPS-04** | **User Content Report Queue (Flagged Questions):** Daftar laporan masalah yang dikirimkan oleh pengguna aplikasi mobile (misal: "Audio tidak bunyi", "Terjemahan keliru", "Koreksi AI salah"). Admin dapat memvalidasi laporan, mengoreksi soal secara langsung dari layar tiket, dan menandai status tiket sebagai *Resolved*. |

---

### Modul 6: Analytics & System Observability (ADM-ANL-06)

| ID Fitur | Spesifikasi Kebutuhan |
| :--- | :--- |
| **FR-ANL-01** | **Core Engagement Dashboard:** Menampilkan grafik interaktif: Daily Active Users (DAU), Monthly Active Users (MAU), rasio retensi D1, D7, D30, dan proporsi pengguna aktif per dialek (*Fusha* vs Mesir vs Syam). |
| **FR-ANL-02** | **Learning Drop-off & Difficulty Funnel:** Visualisasi corong retensi per unit dan level. Mengidentifikasi latihan/soal mana yang memiliki persentase kegagalan (*error rate*) tertinggi ($> 40\%$) untuk dievaluasi oleh tim kurikulum. |
| **FR-ANL-03** | **AI Cost & Latency Monitor:** Grafik *real-time* pemakaian token LLM, perkiraan tagihan bulanan (USD/IDR), distribusi durasi sesi obrolan Dhou AI, serta grafik p50, p90, dan p99 latensi audio TTS dan STT. |

---

### Modul 7: Role-Based Access Control (RBAC) & Audit Logs (ADM-SEC-07)

| ID Fitur | Spesifikasi Kebutuhan |
| :--- | :--- |
| **FR-SEC-01** | **Role-Based Access Matrix:** Mendukung 5 peran akses baku:<br>1. **Super Admin:** Akses penuh seluruh modul, konfigurasi sistem, dan manajemen user admin.<br>2. **Linguist & Content Author:** Akses tulis pada Kurikulum, Video, Kamus, dan Prompt AI.<br>3. **Curriculum Reviewer:** Akses tinjau dan *Approve/Reject* sebelum materi dipublikasikan ke Production.<br>4. **Customer Support:** Akses baca/tulis terbatas pada Modul Pengguna (Streak/Energy override) dan Tiket Moderasi.<br>5. **Auditor / Analyst:** Akses *read-only* pada analitik dan riwayat log. |
| **FR-SEC-02** | **Mandatory Multi-Factor Authentication (MFA):** Seluruh pengguna admin wajib mengaktifkan 2FA berbasis aplikasi autentikator (TOTP Google Authenticator / 1Password) saat pertama kali login. |
| **FR-SEC-03** | **Immutable Audit Trail:** Setiap mutasi data (`INSERT`, `UPDATE`, `DELETE`) dan aksi administratif khusus (seperti memodifikasi streak pengguna atau menerbitkan unit belajar baru) dicatat secara permanen di tabel audit log dengan rekaman: Admin UUID, Waktu UTC, IP Address, Aksi, Entitas Target, dan *JSON Diff Snapshot*. |

---

## 9. User Stories & Acceptance Criteria

### US-ADM-01: Pemblokiran Akses Non-Desktop (Desktop Gatekeeper)
> **Sebagai** Product Manager Dhou,  
> **Saya ingin** memastikan portal admin tidak dapat dioperasikan pada layar berukuran kecil atau perangkat mobile,  
> **Agar** integritas data kurikulum dan tata letak data padat terlindungi dari salah klik akibat layar sempit.

```gherkin
Scenario: Pengguna membuka admin portal pada layar mobile atau tablet (< 1024px)
  Given Pengguna mengakses URL admin portal "admin.dhou.app"
  When Lebar viewport browser terdeteksi kurang dari 1024px (misal: 390px iPhone)
  Then Sistem tidak merender antarmuka dashboard atau form pengeditan
  And Sistem menampilkan layar edukatif "Akses Khusus Desktop"
  And Layar menyajikan pesan bahwa Dhou Admin membutuhkan layar minimal 1024px untuk presisi kurikulum

Scenario: Pengguna memperbesar ukuran jendela browser laptop ke >= 1024px
  Given Pengguna sedang berada di layar pemblokiran "Akses Khusus Desktop"
  When Pengguna memperbesar jendela browser laptop hingga lebar mencapai >= 1024px
  Then Sistem secara otomatis menyembunyikan layar pemblokiran
  And Sistem memuat antarmuka kerja admin secara normal tanpa perlu refresh manual
```

---

### US-ADM-02: Pembuatan Soal Susun Kata Arab dengan Live Mobile Simulator
> **Sebagai** Ahli Bahasa Arab / Content Author Dhou,  
> **Saya ingin** menyusun soal latihan susun kata (*Sentence Scramble*) dengan editor RTL dan melihat tampilannya langsung di simulator ponsel pada layar yang sama,  
> **Agar** saya dapat memastikan harakat, posisi kata, dan kelayakan visual soal sebelum diterbitkan.

```gherkin
Scenario: Author membuat soal susun kata baru pada Unit Dialek Mesir
  Given Author membuka editor soal di Modul Kurikulum pada layar desktop resolusi 1440x900
  When Author memilih tipe soal "Sentence Scramble"
  And Author mengetik teks Arab berharakat "أَنَا عَايِز أَشْرَب شَاي" di kolom input teks utama
  And Author menambahkan kata pengecoh "قَهْوَة" dan "هُوَ"
  Then Teks Arab langsung dirender dengan font Amiri yang rapi tanpa harakat terpotong
  And Panel Simulator Mobile di sisi kanan layar seketika memperbarui tampilannya (< 100ms)
  And Simulator menampilkan potongan kartu kata acak persis seperti yang akan dilihat pengguna
```

---

### US-ADM-03: Sinkronisasi Subtitel Video Berbasis Waveform
> **Sebagai** Video Content Specialist Dhou,  
> **Saya ingin** menandai durasi kemunculan subtitel kata Arab menggunakan visual audio waveform di desktop,  
> **Agar** waktu kemunculan kata pada pemutar video mobile selaras sempurna dengan suara penutur asli.

```gherkin
Scenario: Editor menyelaraskan waktu mulai dan selesai kalimat percakapan
  Given Editor membuka video klip percakapan pasar Kairo (durasi 45 detik)
  When Editor menekan tombol "Spasi" untuk memutar video
  And Editor menekan shortcut "[" untuk menandai In-Point pada detik 00:04.200
  And Editor menekan shortcut "]" untuk menandai Out-Point pada detik 00:06.850
  And Editor mengaitkan teks Arab "بِكَم هَذَا الكِتَابُ؟" pada segmen tersebut
  Then Segmen balok subtitel berwarna biru muncul di atas garis waktu audio waveform
  And Pemutar video simulator di panel kanan menguji pemutaran loop pada segmen tersebut
```

---

### US-ADM-04: Pengujian Prompt Dialek pada AI Sandbox Playground
> **Sebagai** AI Prompt Engineer Dhou,  
> **Saya ingin** menguji instruksi dialek Levantine pada persona "Pelayan Restoran Beirut" secara langsung di portal desktop,  
> **Agar** saya dapat memverifikasi AI tidak tergelincir menggunakan dialek Mesir atau Fusha sebelum prompt diaktifkan ke publik.

```gherkin
Scenario: Engineer menguji kepatuhan dialek persona Levantine
  Given Engineer berada di tab "AI Playground" dengan model Gemini 2.0 Flash terpilih
  When Engineer mengirim pesan teks tes "بدي اطلب أكل لو سمحت"
  Then AI membalas dalam waktu kurang dari 2 detik dengan dialek Syam yang valid (misal: "تفضل يا هلا، شو بتحب تطلب اليوم؟")
  And Panel inspeksi teknis di samping chat menampilkan: Token Prompt: 450, Token Output: 28, Biaya: $0.00014
  And Indikator Dialect Guardrail menampilkan badge hijau "Levantine Verified: 100%"
```

---

### US-ADM-05: Penanganan Tiket Kompensasi Streak Pengguna
> **Sebagai** Customer Support Admin Dhou,  
> **Saya ingin** mencari profil pengguna yang melaporkan kehilangan streak akibat gangguan koneksi dan memulihkannya,  
> **Agar** pengguna tetap termotivasi belajar dan tidak kecewa (*churn*).

```gherkin
Scenario: Support memulihkan streak harian pengguna
  Given Admin menerima nomor tiket bantuan dari pengguna "farhan@mahasiswa.id"
  When Admin mengetik "farhan@mahasiswa.id" pada Global Omnibox Search (`Cmd+K`)
  And Admin membuka kartu Gamifikasi Pengguna dan melihat streak terakhir bernilai 0 (terputus kemarin)
  And Admin mengklik tombol "Pulihkan Streak (Manual Override)"
  And Admin memilih nilai streak "14 Hari" dan mengisi alasan "Kompensasi gangguan server tgl 22 Nov"
  Then Status streak pengguna di database Supabase terbarui menjadi 14 hari seketika
  And Sistem mencatat riwayat pemulihan tersebut ke dalam Immutable Audit Log dengan ID admin yang bertugas
```

---

## 10. Non-Functional Requirements (NFR) Khusus Desktop

### 10.1 Ergonomi Tampilan & Standar Resolusi
- **Baseline Viewport:** Resolusi minimal mutlak $1024 \times 768\text{px}$.
- **Target Desain Utama:** $1440 \times 900\text{px}$ (layar standar MacBook Pro 14"/16" dan laptop kantor) serta $1920 \times 1080\text{px}$ (monitor desktop Full HD).
- **Kerapatan Informasi:** Spasi elemen (*padding/margin*) menggunakan sistem grid rapat (*compact mode*) `8px`–`16px` untuk meminimalkan scrolling vertikal yang tidak perlu pada tabel data.
- **Pintasan Keyboard Terpadu:**
  - `Cmd/Ctrl + S`: Menyimpan draf aktif.
  - `Cmd/Ctrl + Enter`: Menerbitkan atau menyetujui perubahan.
  - `Cmd/Ctrl + K`: Membuka pencarian global (Omnibox).
  - `Esc`: Menutup modal drawer atau pratinjau simulator.

### 10.2 Tipografi Arab & Penanganan Bidirectional (Bidi)
- **Rendering Font Arab:** Seluruh elemen teks Arab wajib menggunakan font **Amiri** atau **Noto Sans Arabic** dengan ukuran minimal `18px` pada kolom input dan `16px` pada tabel data.
- **Line-Height Safety:** Menggunakan `line-height: 1.8` hingga `2.0` secara ketat pada teks Arab agar harakat atas (Fathah, Dhommah, Syaddah) dan harakat bawah (Kasrah) tidak terpotong oleh batas atas/bawah kolom input.
- **Dual Directionality Isolation:** Input teks Arab wajib memiliki atribut `dir="rtl"` yang terisolasi dari label antarmuka form yang berbahasa Indonesia (`dir="ltr"`), memastikan tanda baca seperti tanda tanya (`؟`) atau titik berada di posisi gramatikal yang benar.

### 10.3 Performa Data Grid & Kecepatan Respons
- **Virtual Scrolling:** Tabel master kurikulum dan tabel pengguna wajib menggunakan teknik *Virtual DOM Scrolling* (misal: TanStack Table / Virtualized Grid) sehingga tetap lancar (60 FPS) saat merender $\ge 10.000$ baris data.
- **Debounced Live Simulator:** Sinkronisasi pengetikan dari form editor ke panel simulator mobile di sisi kanan layar memiliki batas *debounce* maksimal `100ms`, memberikan sensasi pembaruan seketika (*instant WYSIWYG*).
- **Sub-Second Filtering:** Operasi filter tabel berdasarkan dialek, status draf/terbit, atau tipe soal harus menghasilkan data baru dalam waktu $< 300\text{ms}$.

### 10.4 Keamanan Data & Standar Akses
- **Inactivity Session Lockout:** Sesi login admin otomatis terkunci (*auto-logout* atau modal re-autentikasi) setelah 30 menit tidak ada aktivitas keyboard atau mouse.
- **Role Isolation (Least Privilege):** Staf customer support tidak memiliki izin (*write permission*) untuk mengubah prompt AI atau menghapus unit kurikulum.
- **Enkripsi Token & Kunci Rahasia:** API Key pihak ketiga (OpenAI, Gemini, Cloudflare, Supabase Service Role) disimpan di vault terenkripsi dan tidak pernah diekspos secara mentah ke browser klien admin.

---

## 11. Edge Cases & Error Handling Matrix

| Kode | Kondisi Ekstrem / Kasus Batas | Respon & Mekanisme Sistem |
| :--- | :--- | :--- |
| **EC-ADM-01** | Pengguna membuka admin pada tablet iPad/Android dalam mode portrait (lebar $< 1024\text{px}$). | Sistem mengaktifkan *Desktop Gatekeeper* dan menampilkan layar blokir ramah meminta perangkat dialihkan ke desktop/laptop. |
| **EC-ADM-02** | Pengguna memperkecil jendela browser saat sedang mengisi soal kurikulum panjang. | Sistem menyimpan draf formulir ke *Local Session Storage* secara instan. Layar blokir muncul sementara; saat jendela diperbesar kembali, form kembali terisi utuh. |
| **EC-ADM-03** | Editor memasukkan teks Arab tanpa harakat sama sekali pada unit level pemula. | Sistem menampilkan modal dialog peringatan dengan rincian huruf gundul sebelum memperbolehkan aksi simpan permanen. |
| **EC-ADM-04** | Koneksi internet terputus saat proses upload file video klip 80MB sedang berjalan di menit ke-2. | Uploader menghentikan proses sementara (*chunked upload*), menampilkan notifikasi oranye *"Koneksi terputus, mencoba kembali otomatis..."*, dan melanjutkan dari blok terakhir tanpa mengulang dari 0%. |
| **EC-ADM-05** | Dua admin mengedit butir soal atau unit belajar yang sama pada saat bersamaan (*Concurrent Edit*). | Sistem menerapkan *Optimistic Locking* berbasis versi kolom `updated_at`. Admin kedua yang menekan simpan akan menerima notifikasi *"Konten telah diperbarui oleh Admin B. Lihat perbedaan (Diff) sebelum menimpa."* |
| **EC-ADM-06** | Kuota API token LLM harian hampir habis ($> 85\%$ batas anggaran). | Banner peringatan merah muncul di Top Bar admin, dan sistem mengirimkan notifikasi peringatan ke kanal Slack/Email teknis internal. |
| **EC-ADM-07** | Admin Customer Support keliru memasukkan nilai penambahan streak (misal salah ketik 1000 hari). | Sistem menerapkan validasi batas wajar (*Sanity Guardrail*): penambahan manual streak maksimal $+30$ hari per aksi. Angka $> 30$ memerlukan persetujuan otorisasi Super Admin. |
| **EC-ADM-08** | Berkas subtitel VTT/SRT yang diimpor memiliki format penomoran waktu rusak. | Parser subtitel menampilkan pesan error spesifik nomor baris yang rusak dan menyorot baris tersebut pada editor teks monospaced. |

---

## 12. Success Metrics & Key Performance Indicators (KPIs)

```text
┌───────────────────────────────────────┬───────────────────────────┬────────────────────────────┐
│ Metrik Operasional                    │ Baseline (Tanpa Portal)   │ Target Pasca Peluncuran    │
├───────────────────────────────────────┼───────────────────────────┼────────────────────────────┤
│ Waktu Produksi 1 Unit Kurikulum       │ ~24 Jam (Via SQL/Devs)    │ ≤ 3 Jam (Mandiri oleh tim) │
│ Durasi Sinkronisasi Subtitel Video    │ ~45 Menit / video         │ ≤ 8 Menit / video          │
│ Waktu Resolusi Tiket Gamifikasi User  │ ~24 Jam (Eskalasi Dev)    │ ≤ 60 Detik (Diselesaikan CS)│
│ Angka Error Harakat pada Aplikasi     │ ~3.5% dari butir soal     │ < 0.2% dari butir soal     │
│ Angka Kejadian Salah Dialek di AI     │ ~5.0% respons chat        │ < 0.5% (Terkontrol prompt) │
└───────────────────────────────────────┴───────────────────────────┴────────────────────────────┘
```

---

## 13. Release Roadmap & Milestones

| Fase | Target Waktu | Fokus Utama & Deliverables |
| :--- | :--- | :--- |
| **Fase 1: MVP Core & Curriculum CMS** | Sprint 6 | - Desktop Gatekeeper & Shell Layout (Sidebar, TopBar, Split-View).<br>- Hierarchical Curriculum Tree & Question Builder untuk 5 tipe soal utama.<br>- Dual-pane Live Mobile Simulator.<br>- Autentikasi Admin, RBAC dasar, dan integrasi database Supabase. |
| **Fase 2: Video Studio & User Operations** | Sprint 7 | - Video Ingestion & Waveform Audio Timeline Scrubber.<br>- Interactive Word-by-Word Subtitle Editor & Shadowing Segment Marker.<br>- User Search, Gamification Inspector, dan Manual Streak/Energy Override.<br>- Immutable Audit Logging System. |
| **Fase 3: AI Prompt Studio & Advanced Analytics** | Sprint 8 | - AI Persona Manager & Strict Dialect Guardrail System Prompt Editor.<br>- Interactive Sandbox AI Playground dengan metrik token & latensi.<br>- Kamus Master Dialek & Cross-Dialect Equivalence Matrix.<br>- Dasbor Analitik Pengguna, Corong Retensi Kurikulum, dan Monitoring Biaya API. |

---

## 14. Approval & Sign-Off

| Stakeholder Role | Nama | Tanggal | Status |
| :--- | :--- | :--- | :--- |
| **Product Owner** | Fariz Mirza Abdillah | 2026-11-23 | Pending Review |
| **Lead Arabic Linguist** | — | — | Planned |
| **Frontend Engineering Lead** | — | — | Planned |
| **Backend & Cloud Lead** | — | — | Planned |
