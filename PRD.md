# Product Requirements Document (PRD) — Dhou (تطبيق ضوء)

**Project Name:** Dhou (ضوء)  
**Document Version:** 1.0.0  
**Author:** Fariz Mirza Abdillah  
**Target Platform:** Mobile Web (PWA) & Native iOS Wrapper (CapacitorJS $\rightarrow$ App Store)  
**Reference Paper:** *Dhou: A Concept of Developing an Arabic Language Learning Application for fusha and 'ammiyyah in the Age of Artificial Intelligence*

---

## 1. Executive Summary & Problem Statement

### 1.1 Latar Belakang & Masalah
Bahasa Arab memiliki fenomena **diglosia** (*diglossia*):
- **Fusha (Modern Standard Arabic):** Digunakan dalam konteks formal, akademis, literatur, dan berita.
- **'Ammiyyah (Spoken Arabic Dialects):** Digunakan dalam komunikasi lisan sehari-hari di berbagai wilayah (misal: Mesir, Syam/Levantine, Teluk).

Banyak pembelajar non-native yang sudah mempelajari tata bahasa *fusha* formal di kelas/pesantren/kampus tetap mengalami **kesenjangan komunikasi nyata** saat berada di Timur Tengah karena minimnya eksposur terhadap ragam bahasa lisan sehari-hari (*'ammiyyah*), kecepatan bicara penutur asli (*speech rate*), dan ungkapan idiomatik kontekstual.

### 1.2 Solusi Produk: Dhou
Dhou adalah aplikasi edutech mobile berbasis AI yang menjembatani kesenjangan tersebut melalui siklus pedagogis terpadu:
$$\text{Video Kontekstual (Input)} \longrightarrow \text{Gamified Path (Latihan)} \longrightarrow \text{Conversational AI (Produksi)} \longrightarrow \text{Review (Retensi)}$$

Aplikasi ini menempatkan AI bukan sebagai pembuat kurikulum tunggal, melainkan sebagai **mitra latihan komunikasi aktif yang diawasi (*supervised practice environment*)**, dengan fondasi materi terverifikasi dan **kesadaran variasi dialek (*Arabic-variety awareness*)** di seluruh modul.

---

## 2. Target Pengguna (User Persona)

1. **Mahasiswa / Santri Lanjutan:**
   - Memiliki dasar nahwu/sharaf/fusha yang baik.
   - Hendak melanjutkan studi ke Timur Tengah (Al-Azhar Mesir, Yordania, dsb.).
   - Butuh latihan mendengar dan berbicara dialek lokal tanpa canggung.
2. **Traveler / Profesional:**
   - Membutuhkan ekspresi fungsional cepat untuk transaksi, ramah tamah, dan navigasi di negara-negara berbahasa Arab.
3. **Pembelajar Mandiri (Self-Learners):**
   - Menginginkan pengalaman belajar yang adiktif seperti Duolingo dan kaya konteks video seperti Cake.

---

## 3. Cakupan MVP (Minimum Viable Product Scope)

Untuk menjaga kualitas linguistik dan fokus pengembangan awal:
- **Variasi Dialek yang Didukung:**
  1. **Fusha (Modern Standard Arabic / الفصحى)**
  2. **Egyptian Arabic ('Ammiyyah Mesir / العامية المصرية)**
  3. **Levantine Arabic ('Ammiyyah Syam / العامية الشامية)**
- **Bahasa Pengantar UI:** Bahasa Indonesia.

---

## 4. Arsitektur Produk & Spesifikasi Fitur Utama

Dhou dibagi menjadi **4 Pilar Utama**:

```text
┌─────────────────────────────────────────────────────────────────┐
│                     TopBar (Streak, Energy, Dialect)            │
├───────────────┬─────────────────┬───────────────┬───────────────┤
│    [1. Learn] │   [2. Video]    │ [3. Dhou AI]  │  [4. Review]  │
│  Gamified Path│ Contextual Input│ Conversational│ SRS Retention │
└───────────────┴─────────────────┴───────────────┴───────────────┘
```

### 4.1 Modul 1: Video Learning (Contextual Input ala Cake)
*Memberikan eksposur audiovisual otentik terhadap ritme, intonasi, dan konteks percakapan.*

- **Fitur Kunci:**
  1. **Pustaka Video Terkurasi:** Klip pendek (30–90 detik) dari cuplikan percakapan, film, atau media berlisensi edukatif.
  2. **Label Varietas Dialek:** Setiap video ditandai secara eksplisit (Fusha / Egyptian / Levantine).
  3. **Interactive Subtitle System:**
     - Teks Arab berharakat (`dir="rtl"`).
     - Terjemahan bahasa Indonesia opsional (toggle on/off).
     - Pemutaran lambat (0.75x dan 1.0x).
  4. **Sentence-Level Replay & Shadowing:**
     - Tombol putar ulang per kalimat.
     - Mode Shadowing: Pengguna merekam suara mereka (*Record-and-Compare*) untuk melatih ritme dan intonasi.
  5. **Quick Save to Review:** Tombol simpan kosakata atau kalimat penting ke modul Review.

### 4.2 Modul 2: Gamified Learning Path (Structured Practice ala Duolingo)
*Mentransformasi input kontekstual menjadi latihan pemahaman terstruktur bertahap.*

- **Fitur Kunci:**
  1. **Path Visual Tree:** Jalur unit belajar bertahap dengan status: *Completed*, *Active (Mulai!)*, dan *Locked*.
  2. **Tipe Latihan Interaktif:**
     - *Word-Meaning Matching* (Mencocokkan kata Arab dengan arti).
     - *Sentence Scramble / Arrangement* (Menyusun kata acak menjadi kalimat yang benar).
     - *Fill in the Blank* (Melengkapi kata yang hilang dalam konteks).
     - *Listen and Choose* (Mendengar audio pengucapan dan memilih opsi).
  3. **Mekanisme Gamifikasi:**
     - **Streak Counter (🔥):** Melacak konsistensi belajar harian.
     - **Energy / Lives Indicator (🔋):** Berkurang saat jawaban salah untuk mendorong ketelitian.
     - **XP & Level Progress:** Penghargaan setelah menyelesaikan satu sesi kuis.

### 4.3 Modul 3: Conversational AI (Communicative Production)
*Memberikan ruang komunikasi fleksibel tanpa rasa malu atau takut salah berbicara.*

- **Fitur Kunci:**
  1. **Dialect Consistency Guardrails:**
     - Pengguna memilih variasi target sebelum memulai obrolan.
     - AI diwajibkan mematuhi dialek yang dipilih (tidak mencampuradukkan Fusha dengan dialek regional tanpa tanda).
  2. **Avatar & Audio Interaction:**
     - Antarmuka suara interaktif (*Push-to-Talk* atau tombol rekam).
     - Transkripsi suara pengguna menggunakan STT.
     - Respons suara AI menggunakan TTS bahasa Arab berharakat.
  3. **Transkrip & Koreksi Instan:**
     - Gelembung chat menampilkan teks Arab + transliterasi/terjemahan.
     - Fitur *Feedback Hint*: Memberikan catatan perbaikan tata bahasa atau saran idiom yang lebih natural.

### 4.4 Modul 4: Review System (Spaced Repetition System / Retention)
*Memastikan ingatan jangka panjang terhadap materi yang pernah dipelajari atau salah dijawab.*

- **Fitur Kunci:**
  1. **Koleksi Kosakata Otomatis:**
     - Kosakata yang di-bookmark dari video.
     - Pertanyaan yang salah dijawab di mode Gamified Path.
     - Frasa yang dikoreksi selama sesi Dhou AI.
  2. **Kartu Pengulangan Berjarak (SRS Flashcard):**
     - Opsi evaluasi mandiri: *"Lupa"* (muncul lebih sering) vs *"Ingat"* (interval pengulangan diperpanjang).

---

## 5. Non-Functional Requirements (NFR)

### 5.1 Desain & Pengalaman Pengguna (UI/UX)
- **Desain Responsif Mobile-First:** Optimal pada resolusi layar mobile (360px – 430px).
- **RTL & Typography:**
  - Font Arab (Amiri/Noto Sans Arabic) memiliki line-height cukup longgar (minimal `1.6`–`1.8`) agar baris harakat atas dan bawah tidak terpotong.
  - Perataan teks dan alur membaca Arab kanan-ke-kiri (*Right-to-Left*).
- **Safe Area Insets:** Mendukung area notch, dynamic island, dan home bar di iOS (`env(safe-area-inset)`).

### 5.2 Standar PWA & App Store Packaging
- **PWA Capabilities:**
  - Web App Manifest valid dengan mode `display: standalone`.
  - Service worker untuk caching aset statis dan transisi offline yang elegan.
  - Skor Lighthouse PWA $\ge 90$.
- **Native Wrapper (CapacitorJS):**
  - Izin mikrofon native (`NSMicrophoneUsageDescription`) dengan penjelasan transparan untuk evaluasi pengucapan.
  - Haptic feedback pada interaksi jawaban benar/salah.
  - Kompatibel dengan syarat Apple App Store Review Guidelines Section 4.2 (Minimum Functionality).

### 5.3 Performa & Latensi
- Latensi pemutaran audio/TTS $< 1.5$ detik.
- Waktu respons AI streaming chat $< 2.0$ detik untuk pesan pertama.

---

## 6. Success Metrics & Evaluasi

Mengacu pada rencana pengujian di paper (*moderated usability testing*):
1. **Task Completion Rate:** Minimal 85% pengguna uji berhasil menyelesaikan satu alur lengkap (menonton video $\rightarrow$ mengerjakan 1 sesi latihan $\rightarrow$ 1 sesi percakapan singkat Dhou AI).
2. **Dialect Awareness Comprehension:** Pengguna dapat mengenali apakah materi yang sedang dipelajari berstatus Fusha, Mesir, atau Syam.
3. **Retention & Streak:** Tingkat kepatuhan belajar harian pengguna didukung gamifikasi yang tidak berlebihan.

---

## 7. Roadmap Rilis Fitur

| Versi | Milestone | Deliverables |
| :--- | :--- | :--- |
| **v0.1** | Shell & Tokens (Selesai) | Inisialisasi PWA, Figma tokens, TopBar, BottomNav, 4 tab shell. |
| **v0.2** | Interactive Quiz Engine | Tipe latihan Duolingo-style lengkap dengan kalkulasi skor & nyawa. |
| **v0.3** | Video & Shadowing Engine | Pemutar video segmen pendek, audio player, MediaRecorder compare. |
| **v0.4** | AI Chat & Variety Prompts | Integrasi LLM API dengan system prompt dialek ketat & transkrip. |
| **v0.5** | Supabase & SRS Review | Integrasi Auth, database progress user, dan algoritma review. |
| **v1.0** | Capacitor iOS Packaging | Build project Xcode, pengujian TestFlight, rilis ke App Store. |
