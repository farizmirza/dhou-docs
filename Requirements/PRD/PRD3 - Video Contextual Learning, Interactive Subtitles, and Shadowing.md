# Product Requirement Document (PRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | PRD3 |
| **Feature ID** | VID-01, VID-02, VID-03, VID-04, VID-05 |
| **Feature Name** | Video Contextual Learning, Interactive Bilingual Subtitles, Word Extraction, and Sentence Shadowing |
| **Application** | Dhou (تطبيق ضوء) — Mobile Web (PWA) & iOS Native Wrapper (CapacitorJS) |
| **Module** | Video Learning & Contextual Input Core (Pilar 1) |
| **Sprint** | Sprint 3 |
| **Epic** | Audiovisual Immersion, Diglossic Exposure & Speech Production |
| **Priority** | Critical (P0) |
| **Status** | Approved |
| **Implementation Status** | Planned |
| **Version** | 1.0 |
| **Last Updated** | 2026-11-04 |
| **Product Owner** | Fariz Mirza Abdillah |
| **Dependencies** | PRD1 (User Authentication), Design System (Figma Dhou), Research Reference Mapping (Pilar 1: Video Learning) |

---

## 1. Business Problem

Dhou dirancang untuk memecahkan fenomena **diglosia bahasa Arab** (*Arabic Diglossia*)—jurang pemisah antara penguasaan bahasa formal (*Fusha*) dengan kemampuan komunikasi lisan sehari-hari (*'Ammiyyah*). 

Sebelum pembelajar diuji dengan latihan sintaksis di modul *Gamified Learning Path* (PRD2), mereka membutuhkan **input bahasa kontekstual otentik (*Contextual Language Input*)** pada tahap awal:
$$\text{Exposure} \longrightarrow \text{Noticing} \longrightarrow \text{Recognition} \dots$$

Tanpa modul *Video Contextual Learning*, pembelajar menghadapi hambatan kritis:
1. **Minimnya Eksposur Audiovisual Otentik**: Pembelajar hanya terpapar teks statis di buku teks, sehingga kesulitan memahami kecepatan bicara (*speech rate*), irama, intonasi emosional, dan konteks sosial budaya penutur asli.
2. **Ketiadaan Jembatan Subtitle Ortografis**: Menonton video berbahasa Arab tanpa subtitle terpandu sangat membingungkan, sedangkan subtitle bahasa Indonesia biasa membuat pembelajar tidak memperhatikan tulisan (*noticing*) dan tanda baca (*harakat*).
3. **Kekakuan Artikulasi Bicara (Hambatan Produksi Lisan)**: Pembelajar sering merasa canggung atau takut salah saat berbicara. Ketiadaan media latihan privat mandiri untuk menirukan pelafalan penutur asli menghambat pembentukan memori otot lidah (*muscle memory*).
4. **Terputusnya Alur Input Menuju Retensi**: Kosakata menarik atau ungkapan idiomatik yang ditemukan saat menonton sering kali langsung terlupakan karena tidak ada integrasi langsung ke sistem pengulangan berkala (*Spaced Repetition System / SRS*).

---

## 2. Business Value

- **Peningkatan Retensi Belajar & Pemahaman Kontekstual**: Pendekatan input video pendek (30–90 detik) bergaya *Cake* terbukti meningkatkan keterlibatan harian (*daily engagement*) hingga $>50\%$ dibandingkan metode membaca teks konvensional.
- **Kemandirian Pembelajar via Interactive Dual-Subtitle**: Pengguna memiliki kendali penuh atas subtitle Arab berharakat, terjemahan bahasa Indonesia, dan kecepatan audio (0.75x, 0.9x, 1.0x) sehingga dapat belajar sesuai tempo kognitif masing-masing.
- **Produksi Lisan Aktif Tanpa Tekanan (*Low-Anxiety Shadowing*)**: Fitur *Sentence-level Shadowing (Record-and-Compare)* memungkinkan pembelajar merekam suaranya dan membandingkannya langsung dengan penutur asli secara mandiri tanpa rasa malu dihakimi.
- **Ekosistem Belajar Terpadu (Closed-Loop Pedagogy)**: Setiap kata sulit yang diketuk pada video dapat disimpan langsung ke antrean modul *Review* dengan 1 klik, menciptakan kesinambungan dari menonton (*input*) $\rightarrow$ menghafal (*retention*).
- **Keunggulan Kompetitif Kesadaran Dialek (*Dialect-Aware Platform*)**: Setiap video ditandai secara eksplisit dengan lencana dialek (*Fusha*, *Egyptian*, *Levantine*), memastikan pembelajar memahami konteks ragam bahasa yang sedang dipelajari.

---

## 3. Purpose

Menyediakan modul **Video Contextual Learning** di aplikasi Dhou yang memfasilitasi katalog video pendek terkurasi berbasis dialek, pemutar video kustom mobile-first dengan kendali subtitle dwibahasa interaktif (Arab berharakat & Indonesia), navigasi transkrip per kalimat (*clickable transcript*), kamus mini instan per kata (*Word Tap Definition*), mode latihan peniruan pengucapan mandiri (*Sentence Shadowing* dengan komparasi audio), serta integrasi penandaan kosakata ke modul Review (*Save to Review*).

---

## 4. Objectives

1. Mengimplementasikan antarmuka pustaka video (**Video Feed / Catalog**) dengan filter variasi dialek (*Fusha*, *Egyptian*, *Levantine*), penanda level kemahiran (Pemula / Menengah), dan durasi video.
2. Membangun pemutar video kustom (**Interactive Video Player**) yang responsif untuk Mobile PWA dan iOS Capacitor dengan:
   - Dukungan rasio layar 16:9 yang nyaman untuk membaca subtitle dan transkrip di bawah video.
   - Kontrol kecepatan putar audio/video bertahap (0.75x, 0.9x, 1.0x).
   - Toggle sakelar fleksibel: Subtitle Arab (Aktif/Nonaktif) dan Terjemahan Indonesia (Aktif/Nonaktif).
3. Menyediakan **Transkrip Interaktif Berstempel Waktu (*Interactive Timestamped Transcript*)**:
   - Menyorot kalimat Arab yang sedang aktif diputar secara *real-time*.
   - Mengizinkan pengguna mengetuk kalimat manapun untuk langsung melompat (*seek*) ke detik pemutaran terkait.
   - Tombol putar ulang satu kalimat (*Sentence Replay*) untuk mendengarkan berulang kali tanpa scrub timeline manual.
4. Menghadirkan **Kamus Mini Interaktif (*Interactive Word Tap*)**:
   - Mengetuk kata Arab pada transkrip memunculkan *bottom sheet* definisi singkat, transliterasi Latin, dan tombol simpan (*Bookmark*).
5. Membangun modul latihan lisan **Sentence Shadowing (Record-and-Compare)**:
   - Pengguna dapat merekam suara imitasi mereka menggunakan mikrofon perangkat (Web Audio / Capacitor).
   - Pengguna dapat memutar berdampingan suara penutur asli (*Native Audio*) dan rekaman suara sendiri (*User Audio*) untuk refleksi pelafalan.
6. Menyediakan tombol cepat **Save to Review (🔖)** untuk mengarsipkan kata atau kalimat ke modul *Review (SRS)*.

---

## 5. Scope

### In Scope
- **Pustaka & Filter Video (VID-01)**: Daftar kartu video dengan metadata lengkap (thumbnail, judul, dialek badge, tingkat kesulitan, durasi). Filter kategori dialek di bagian atas layar.
- **Pemutar Video & Subtitle Dwibahasa (VID-02)**: Video player mobile-friendly, toggle subtitle Arab RTL berharakat, toggle terjemahan bahasa Indonesia, selektor kecepatan (0.75x, 0.9x, 1.0x).
- **Transkrip Interaktif & Navigasi Kalimat (VID-03)**: Daftar kalimat di bawah video yang sinkron dengan waktu video berjalan, klik kalimat untuk melompat, tombol repeat/loop per kalimat.
- **Kamus Mini Kata Arab (VID-04)**: Deteksi klik per kata pada subtitle/transkrip, modal kartu kata instan (arti kata, transliterasi), dan tombol *Simpan ke Review*.
- **Latihan Shadowing & Komparasi Suara (VID-05)**: Perekam suara pengguna lokal per kalimat terpilih, kontrol putar suara asli vs suara rekaman, indikator izin mikrofon, dan pembatasan rekaman cerdas.

### Out of Scope (Tahap Selanjutnya / Post-MVP)
- Fitur scoring otomatis berbasis AI Speech-to-Text / phoneme accuracy (menggunakan evaluasi mandiri *Record-and-Compare* terlebih dahulu untuk MVP guna menjamin latensi rendah dan keandalan dialek).
- Pengunggahan video oleh pengguna umum (*User Generated Content*).
- Video vertikal swipe bergaya TikTok reel penuh layar (mengutamakan format kartu terstruktur ala Cake agar transkrip dan tombol rekam tidak tertutup elemen visual).
- Fitur live streaming atau download video offline terenkripsi (video membutuhkan koneksi internet, hanya aset UI dan audio rekaman lokal yang di-cache).

---

## 6. User Personas

1. **Rian (Santri / Mahasiswa Bahasa Arab Menengah)**:
   - *Kebutuhan:* Memahami percakapan santai orang Mesir yang sering didengar di film, namun kesulitan menangkap kata-kata cepat tanpa bantuan teks Arab.
   - *Perilaku:* Membutuhkan subtitle Arab berharakat dengan kecepatan 0.9x, serta memutar ulang kalimat berulang kali sampai telinganya terbiasa.
2. **Nadia (Pegawai Mandiri / Traveler)**:
   - *Kebutuhan:* Ingin bisa mengucapkan salam dan tawar-menawar dalam dialek Syam (*Levantine*) tanpa terdengar kaku seperti membaca kitab teks.
   - *Perilaku:* Menggunakan fitur *Shadowing* untuk menirukan intonasi penutur asli sebelum berangkat, serta menandai kata-kata penting ke daftar review.

---

## 7. User Stories & Acceptance Criteria

### US-VID-01: Kurasi Feed & Filter Variasi Dialek (Video Feed & Filtering)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-01 |
| **Feature ID** | VID-01 |
| **Title** | Dialect-Based Video Feed & Exploration |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saya ingin menelusuri katalog video pendek yang dikelompokkan berdasarkan dialek (*Fusha*, *Egyptian*, *Levantine*) agar saya dapat memilih materi yang relevan dengan target percakapan saya. |

#### User Journey
1. Pengguna membuka tab menu **"Video"** dari navigasi bawah (*Bottom Navigation Bar*).
2. Layar menyajikan baris filter dialek di bagian atas: `[Semua]`, `[Fusha]`, `[Mesir]`, `[Syam]`.
3. Pengguna melihat daftar kartu video dalam format feed vertikal yang mulus.
4. Setiap kartu video menampilkan thumbnail, badge dialek berwarna khas, judul video dwibahasa, durasi (misal: `0:45`), dan indikator level (`Pemula` / `Menengah`).
5. Pengguna mengetuk salah satu kartu video untuk masuk ke halaman detail pemutar video.

#### Acceptance Criteria
- **AC-VID-01-A (Dialect Pill Filtering):** Memilih filter dialek tertentu wajib menyaring daftar kartu video secara instan tanpa me-reload seluruh halaman.
- **AC-VID-01-B (Card Visual Identity):** Kartu video wajib menampilkan badge dialek dengan warna token yang konsisten:
  - *Fusha:* Hijau Emerald (`#10B981` / `#059669`).
  - *Egyptian ('Ammiyyah Mesir):* Biru Langit Dhou (`#51A2FF` / `#2563EB`).
  - *Levantine ('Ammiyyah Syam):* Oranye Hangat Dhou (`#FF8904` / `#D97706`).
- **AC-VID-01-C (Empty State Handling):** Jika tidak ada video pada filter terpilih, tampilkan ilustrasi lentera Dhou ramah dengan pesan: *"Belum ada video untuk dialek ini. Coba pilih dialek lainnya!"*.

#### Edge Cases & Error Handling
- **EC-VID-01-1 (Koneksi Lemah Saat Muat Katalog):** Jika metadata video gagal dimuat karena gangguan jaringan, sistem menampilkan kartu skeleton abu-abu selama 5 detik, lalu menyajikan tombol *"Coba Lagi"* tanpa merusak layout tab.

---

### US-VID-02: Pemutar Video Interaktif & Kontrol Subtitle Dwibahasa

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-02 |
| **Feature ID** | VID-02 |
| **Title** | Video Player with Dual-Subtitle & Playback Speed Controls |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saya ingin menonton video dengan subtitle teks Arab berharakat dan terjemahan Indonesia yang bisa saya nyalakan/matikan serta kecepatan putar yang bisa disesuaikan agar saya dapat melatih pendengaran secara fleksibel. |

#### User Journey
1. Pengguna berada di layar pemutar video. Video dimuat dengan rasio 16:9 di bagian atas layar.
2. Di bawah layar video terdapat panel kontrol praktis:
   - Tombol Toggle **Sub Arab** (Aktif / Nonaktif).
   - Tombol Toggle **Arti ID** (Aktif / Nonaktif).
   - Tombol Kecepatan: `[0.75x]` `[0.9x]` `[1.0x]`.
3. Saat video diputar, overlay subtitle Arab berharakat (`dir="rtl"`) dan terjemahan Indonesia muncul tepat di atas timeline pemutar dengan kontras tinggi.
4. Pengguna dapat menjeda (*pause*) atau melanjutkan (*play*) video kapan saja dengan mengetuk layar video.

#### Acceptance Criteria
- **AC-VID-02-A (Bilingual Subtitle Overlay):** Subtitle Arab wajib menggunakan font Arab bervolume tinggi (*Amiri / Noto Sans Arabic*) dengan ukuran minimal `20px`, perataan tengah, berharakat lengkap, dan memiliki latar belakang pelindung semi-transparan (`bg-black/70`) agar mudah terbaca di atas latar video apapun.
- **AC-VID-02-B (Independent Toggles):** Toggle Subtitle Arab dan Terjemahan Indonesia bekerja secara independen:
  - Kedua subtitle aktif (mode belajar pemula).
  - Hanya subtitle Arab aktif (mode latihan membaca teks lisan).
  - Hanya terjemahan aktif atau keduanya nonaktif (mode melatih telinga murni / *blind listening*).
- **AC-VID-02-C (Playback Speed Synchronization):** Mengubah kecepatan (0.75x, 0.9x, 1.0x) langsung mengubah `playbackRate` elemen video HTML5 tanpa mengubah nada suara secara distorsif (*pitch preservation* bawaan Web Audio).

#### Edge Cases & Error Handling
- **EC-VID-02-1 (Video Buffering):** Jika streaming video tersendat (*buffering*), indikator loading spinner berputar lembut di tengah video. Transkrip di bagian bawah tetap dapat dibaca dan tidak membeku.
- **EC-VID-02-2 (Interupsi Aplikasi / Layar Terkunci):** Jika pengguna beralih aplikasi atau menerima panggilan telepon di perangkat, pemutaran video wajib otomatis dijeda (*auto-paused*) untuk menjaga sinkronisasi posisi detik.

---

### US-VID-03: Transkrip Interaktif, Navigasi Kalimat & Replay

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-03 |
| **Feature ID** | VID-03 |
| **Title** | Interactive Timestamped Transcript & Sentence Navigation |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saya ingin melihat transkrip percakapan lengkap yang tersinkronisasi di bawah video dan dapat diklik per kalimat agar saya bisa melompat atau mengulang kalimat tertentu berulang kali. |

#### User Journey
1. Di area bawah panel pemutar video, pengguna melihat daftar baris transkrip percakapan (*Transcript List*).
2. Baris kalimat yang sedang diucapkan penutur di video otomatis menyala (*active highlight* dengan aksen oranye lembut) dan sistem menggulirkan posisi transkrip secara halus (*auto-scroll*).
3. Setiap baris kalimat menyediakan:
   - Teks Arab berharakat (`dir="rtl"`).
   - Terjemahan bahasa Indonesia di baris bawahnya.
   - Tombol **Replay Kalimat (🔁)**.
   - Tombol **Mode Shadowing (🎙️)**.
4. Pengguna mengetuk tombol Replay Kalimat: video langsung melompat ke awal kalimat tersebut dan memutarnya hingga batas akhir kalimat tersebut.

#### Acceptance Criteria
- **AC-VID-03-A (Real-Time Sentence Sync):** Sistem memperbarui status kalimat aktif berdasarkan `currentTime` video dengan toleransi latensi $<100\text{ ms}$.
- **AC-VID-03-B (Click-to-Seek):** Mengetuk baris kalimat manapun wajib memindahkan posisi *playback time* video ke `start_time` kalimat tersebut dan memulai pemutaran.
- **AC-VID-03-C (Sentence Replay Mode):** Mengetuk ikon Replay memutar ulang rentang `[start_time, end_time]` kalimat tersebut, lalu video dijeda otomatis pada `end_time` (tidak lanjut bablas ke kalimat berikutnya) sehingga pengguna bisa fokus mencerna.

#### Edge Cases & Error Handling
- **EC-VID-03-1 (Pengguna Melakukan Manual Scroll):** Jika pengguna sedang men-scroll manual daftar transkrip untuk membaca, sistem menangguhkan fitur *auto-scroll* selama 4 detik agar pandangan pengguna tidak terdistraksi paksa.

---

### US-VID-04: Latihan Shadowing Berbasis Rekam & Bandingkan (Record-and-Compare)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-04 |
| **Feature ID** | VID-04 |
| **Title** | Sentence Shadowing & Dual-Audio Playback Comparison |
| **Priority** | High (P1) |
| **Description** | Sebagai pembelajar, saya ingin merekam pengucapan saya untuk satu kalimat tertentu dan membandingkan suara saya dengan suara penutur asli agar saya dapat memperbaiki ritme dan intonasi dialek saya secara mandiri. |

#### User Journey
1. Pengguna mengetuk ikon mikrofon **"Shadowing"** pada salah satu baris kalimat di transkrip.
2. Muncul *Bottom Sheet Latihan Shadowing*:
   - Menampilkan teks Arab kalimat target dalam font besar berharakat.
   - Tombol 🔊 **"Dengar Suara Asli"**: Memutar potongan audio penutur asli dari kalimat tersebut.
   - Tombol besar 🎙️ **"Tahan / Ketuk untuk Rekam"**: Pengguna mulai mengucapkan kalimat.
3. Selama merekam, bar visual indikator suara berdenyut dan timer berjalan.
4. Pengguna menyelesaikan rekaman. Sistem langsung menyajikan dua tombol pemutar berdampingan:
   - Tombol A: 🔊 **"Suara Penutur Asli"**
   - Tombol B: 👤 **"Suara Saya"**
5. Pengguna mendengarkan keduanya secara bergantian, menyadari letak perbedaan intonasinya, dan dapat menekan tombol **"Rekam Ulang"** jika belum puas.

#### Acceptance Criteria
- **AC-VID-04-A (Local Recording Pipeline):** Rekaman audio menggunakan Web MediaRecorder API / Capacitor Audio Plugin dengan format `audio/webm` atau `audio/m4a` dan disimpan sementara di memori perangkat (*local blob URL*) tanpa membebani bandwidth jaringan server.
- **AC-VID-04-B (Capacitor & Web Permission):** Sistem meminta izin mikrofon secara sopan (*graceful permission request*). Jika diizinkan, perekaman langsung aktif.
- **AC-VID-04-C (Smart Max Duration):** Durasi perekaman dibatasi otomatis maksimal $1.5\times$ durasi kalimat asli (atau maksimal 15 detik) untuk mencegah rekaman yang lupa dimatikan.
- **AC-VID-04-D (Side-by-Side Playback):** Memutar salah satu audio (misal Suara Asli) otomatis menghentikan pemutaran audio pasangannya (Suara Saya) agar kedua suara tidak bertumpuk di telinga pengguna.

#### Edge Cases & Error Handling
- **EC-VID-04-1 (Izin Mikrofon Ditolak):** Jika pengguna menolak izin mikrofon, tombol rekam dinonaktifkan (*disabled*) dan muncul kotak dialog ramah: *"Dhou membutuhkan akses mikrofon untuk fitur Shadowing. Anda dapat mengaktifkannya di Pengaturan Browser/iPhone"*.
- **EC-VID-04-2 (Rekaman Senyap / Hening):** Jika pengguna merekam tanpa suara (rata-rata amplitude audio mendekati 0), sistem menampilkan tooltip: *"Suara tidak terdengar jelas. Pastikan mikrofon tidak tertutup lalu coba lagi"*.

---

### US-VID-05: Kamus Mini Instan & Bookmark ke Review (Save to Review)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-05 |
| **Feature ID** | VID-05 |
| **Title** | Word Tap Instant Definition & SRS Review Bookmark |
| **Priority** | High (P1) |
| **Description** | Sebagai pembelajar, saya ingin mengetuk kata manapun pada teks Arab untuk melihat arti katanya dan menyimpannya ke daftar Review dengan satu ketukan agar saya dapat menghafalnya nanti di modul SRS. |

#### User Journey
1. Pengguna mengetuk satu kata Arab spesifik pada teks subtitle atau baris transkrip (misal kata: *عامل*).
2. Video otomatis dijeda (*auto-pause*).
3. Muncul *Word Popup / Drawer Mini*:
   - Menampilkan kata Arab terpilih beserta harakatnya.
   - Transliterasi Latin (misal: *'aamil*).
   - Label part-of-speech (misal: *Kata Kerja / Participle*).
   - Terjemahan bahasa Indonesia (misal: *melakukan / bagaimana kabarmu*).
   - Label asal dialek (misal: *'Ammiyyah Mesir*).
   - Tombol aksi 🔖 **"Simpan ke Review"**.
4. Pengguna mengetuk tombol simpan: ikon berubah menjadi centang hijau **"Tersimpan ✓"** dengan getaran haptic ringan.
5. Kata tersebut otomatis masuk ke antrean database review pengguna.

#### Acceptance Criteria
- **AC-VID-05-A (Word Boundary Detection):** Setiap token kata pada transkrip dapat diketuk secara individual tanpa mengganggu keterbacaan teks Arab RTL.
- **AC-VID-05-B (Instant Bookmark Mutation):** Menekan tombol simpan mengirimkan mutasi penyimpanan ke backend. Jika offline, operasi di-cache secara optimistik di *local state*.
- **AC-VID-05-C (Dialect Metadata Preservation):** Kosakata yang disimpan wajib menyertakan atribut dialek asalnya (*variety_code*) agar saat diulang di modul Review, konteks dialek tidak hilang.

#### Edge Cases & Error Handling
- **EC-VID-05-1 (Kata yang Sudah Pernah Disimpan):** Jika kata yang diketuk sudah ada di daftar review pengguna, tombol langsung berstatus aktif *"Tersimpan ✓"*. Mengetuknya kembali akan memunculkan konfirmasi singkat untuk menghapus dari bookmark (*un-bookmark*).
- **EC-VID-05-2 (Kata Berimbuhan / Attached Clitics):** Pada MVP, jika sebuah kata memiliki awalan huruf sambung (seperti *وَ* atau *بِـ*), sistem menampilkan arti frasa utuh yang tertera pada leksikon materi untuk menjaga kesederhanaan pemahaman kontekstual.

---

## 8. User Interface & Wireframe Specs

### 8.1 Layout Halaman Pemutar Video (Mobile Viewport: 360px–430px)

```text
┌────────────────────────────────────────────────────────┐
│ [← Kembali]       Klip Percakapan Kafe      [Bookmark] │
├────────────────────────────────────────────────────────┤
│ ┌────────────────────────────────────────────────────┐ │
│ │                                                    │ │
│ │             VIDEO PLAYER SCREEN (16:9)             │ │
│ │                                                    │ │
│ │  [Sub Arab: إزيك يا أحمد؟ عامل إيه النهاردة؟]       │ │
│ │  [Arti ID: Bagaimana kabarmu Ahmed? Apa kabar?]    │ │
│ └────────────────────────────────────────────────────┘ │
│ ┌────────────────────────────────────────────────────┐ │
│ │ [▶ Play]  [0:15 / 0:45]   [Sub Arab ✓] [Arti ID ✓]  │ │
│ │ Kecepatan: (0.75x)  (0.9x)  ([1.0x])               │ │
│ └────────────────────────────────────────────────────┘ │
├────────────────────────────────────────────────────────┤
│ TRANSKRIP INTERAKTIF (Klik kata untuk arti)           │
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 0:00 - 0:05 | [Mesir]                [🔁]  [🎙️ Rekam]│ │
│ │ صباح الخير! إزيك يا صاحبي؟                          │ │
│ │ Selamat pagi! Bagaimana kabarmu kawan?             │ │
│ └────────────────────────────────────────────────────┘ │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 0:06 - 0:12 | [Mesir] (AKTIF)        [🔁]  [🎙️ Rekam]│ │
│ │ الحمد لله، كله تمام. إنت عامل إيه؟                  │ │
│ │ Alhamdulillah, semua baik. Kamu bagaimana kabarnya?│ │
│ └────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────┘
```

### 8.2 Design Tokens & Visual Hierarchy

| Komponen UI | Token Warna & Tipografi | Interaksi & Status |
| :--- | :--- | :--- |
| **Dialect Badge (Fusha)** | Bg: `#ECFDF5`, Teks: `#059669`, Border: `#A7F3D0` | Statis, Pill Rounded `9999px` |
| **Dialect Badge (Mesir)** | Bg: `#EFF6FF`, Teks: `#2563EB`, Border: `#BFDBFE` | Statis, Pill Rounded `9999px` |
| **Dialect Badge (Syam)** | Bg: `#FFFBEB`, Teks: `#D97706`, Border: `#FDE68A` | Statis, Pill Rounded `9999px` |
| **Active Sentence Row** | Bg: `#FFF7ED`, Border: `2px solid #FF8904`, Radius: `16px` | Transisi border `200ms ease`, Auto-scroll center |
| **Arabic Subtitle Font** | Font: *Amiri* / *Noto Sans Arabic*, Size: `20px–22px`, Weight: `700`, RTL | Line height `1.8` (ruang lega untuk harakat) |
| **3D Record Button** | Bg: `#EF4444`, Border bottom: `4px solid #B91C1C`, Teks putih | Efek tekan (*pressed scale 0.96*), Haptic feedback |
| **Bookmark Pill Button** | Bg: `#FF8904`, Border bottom: `3px solid #D67000`, Teks putih | Berubah hijau saat aktif (`#22C55E`) |

---

## 9. Business Rules Summary Matrix

| Rule ID | Domain | Aturan Bisnis | Konsekuensi Sistem |
| :--- | :--- | :--- | :--- |
| **RL-VD-01** | Label Varietas | Setiap video wajib memiliki tepat 1 label dialek primer (*Fusha*, *Egyptian*, atau *Levantine*). | Video tanpa label dialek ditolak oleh validasi kurasi konten. |
| **RL-VD-02** | Kecepatan Putar | Kecepatan yang didukung hanya 3 opsi: 0.75x, 0.9x, dan 1.0x. | Mengunci opsi agar kualitas artikulasi fonetik bahasa Arab tetap jelas tanpa distorsi pitch. |
| **RL-VD-03** | Rekaman Shadowing | Audio rekaman pengguna diproses secara *client-side local memory*. | Tidak ada audio rekaman yang diunggah ke server kecuali pengguna secara eksplisit meminta sinkronisasi profil masa depan. |
| **RL-VD-04** | Durasi Maksimal Rekam | Batas rekam per kalimat adalah $\min(15\text{ detik}, 1.5 \times \text{durasi kalimat})$. | Perekaman otomatis berhenti jika melebihi batas waktu. |
| **RL-VD-05** | Deduplikasi Review | Menambahkan kata yang sudah ada di daftar review tidak menghasilkan duplikasi record di database. | Sistem melakukan *upsert* atau memperbarui timestamp terakhir dilihat. |
| **RL-VD-06** | Penyelesaian Video | Video ditandai *"Tuntas Ditonton"* jika pengguna telah memutar minimal 80% dari total durasi video. | Memperbarui bar progres video pengguna di database. |

---

## 10. Business Metrics & KPIs

| Metrik | Definisi | Target MVP | Cara Pengukuran |
| :--- | :--- | :--- | :--- |
| **Video Completion Rate** | Rasio video yang ditonton hingga $\ge 80\%$ dibanding total video yang dibuka. | $\ge 60\%$ | Event analytics `video_completed / video_opened` |
| **Shadowing Engagement** | Persentase sesi video di mana pengguna melakukan minimal 1 kali latihan rekaman shadowing. | $\ge 35\%$ | Event analytics `shadowing_record_completed` |
| **Word Bookmark Rate** | Rata-rata jumlah kosakata yang disimpan pengguna ke modul Review per 3 video yang ditonton. | $\ge 2.5$ Kata | Record di tabel `saved_review_words` |
| **Playback Speed Adoption**| Persentase pengguna yang memanfaatkan fitur slow-motion (0.75x / 0.9x). | $\ge 40\%$ | Event `speed_change` pada video player |

---

## 11. Appendix & FRD3 Mapping

Dokumen PRD3 ini menjadi acuan definitif untuk spesifikasi teknis fungsional pada berkas:  
[`Requirements/FRD/FRD3 - Video Contextual Learning, Interactive Subtitles, and Shadowing.md`](file:///Users/farizmirza/Documents/Project/dhou-docs/Requirements/FRD/FRD3%20-%20Video%20Contextual%20Learning,%20Interactive%20Subtitles,%20and%20Shadowing.md).
