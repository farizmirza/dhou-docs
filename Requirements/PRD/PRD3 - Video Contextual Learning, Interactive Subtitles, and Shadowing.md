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

Sebelum pembelajar diuji dengan latihan sintaksis di modul **Rute** (*Gamified Learning Path* - PRD2), mereka membutuhkan **input bahasa kontekstual otentik (*Contextual Language Input*)** pada tahap awal:
$$\text{Exposure} \longrightarrow \text{Noticing} \longrightarrow \text{Recognition} \dots$$

Tanpa modul *Video Contextual Learning*, pembelajar menghadapi hambatan kritis:
1. **Minimnya Eksposur Audiovisual Otentik**: Pembelajar hanya terpapar teks statis di buku teks, sehingga kesulitan memahami kecepatan bicara (*speech rate*), irama, intonasi emosional, dan konteks sosial budaya penutur asli.
2. **Ketiadaan Jembatan Subtitle Ortografis**: Menonton video berbahasa Arab tanpa subtitle terpandu sangat membingungkan, sedangkan subtitle bahasa Indonesia biasa membuat pembelajar tidak memperhatikan tulisan (*noticing*) dan tanda baca (*harakat*).
3. **Kekakuan Artikulasi Bicara (Hambatan Produksi Lisan)**: Pembelajar sering merasa canggung atau takut salah saat berbicara. Ketiadaan media latihan privat mandiri untuk menirukan pelafalan penutur asli menghambat pembentukan memori otot lidah (*muscle memory*).
4. **Terputusnya Alur Input Menuju Retensi**: Kosakata menarik atau ungkapan idiomatik yang ditemukan saat menonton sering kali langsung terlupakan karena tidak ada integrasi langsung ke sistem pengulangan berkala (*Spaced Repetition System / SRS*).

---

## 2. Business Value

- **Peningkatan Retensi Belajar & Pemahaman Kontekstual**: Pendekatan input video pendek (30–90 detik) bergaya *Cake* terbukti meningkatkan keterlibatan harian (*daily engagement*) hingga $>50\%$ dibandingkan metode membaca teks konvensional.
- **Konsistensi Visual Gamifikasi Global**: Tiga pilar motivasi belajar—**Streak Harian (🔥)**, **Energi (⚡)**, dan **XP (⭐)**—selalu terpampang di header awal screen Video, menyatukan kontinuitas psikologis belajar di seluruh modul.
- **Kemandirian Pembelajar via Interactive Dual-Subtitle**: Pengguna memiliki kendali penuh atas subtitle Arab berharakat, terjemahan bahasa Indonesia, dan kecepatan audio (0.75x, 0.9x, 1.0x) sehingga dapat belajar sesuai tempo kognitif masing-masing.
- **Produksi Lisan Aktif Tanpa Tekanan dengan Progres Nyata**: Fitur *Sentence-level Shadowing* dilengkapi **indikator pengerjaan visual (`[✓ Selesai]`)**, memberikan rasa pencapaian (*sense of accomplishment*) saat pengguna menuntaskan latihan imitasi suara.
- **Ekosistem Belajar Terpadu (Closed-Loop Pedagogy)**: Dilengkapi tombol **Simpan Video** ke daftar favorit dan **Simpan Idiom** ke koleksi idiom tersimpan (kuota 20), menciptakan kesinambungan dari menonton (*input*) $\rightarrow$ menghafal (*retention*).
- **Keunggulan Kompetitif Kesadaran Dialek (*Dialect-Aware Platform*)**: Setiap video ditandai secara eksplisit dengan lencana dialek (*Fusha*, *Egyptian*, *Levantine*), memastikan pembelajar memahami konteks ragam bahasa yang sedang dipelajari.

---

## 3. Purpose

Menyediakan modul **Video Contextual Learning** di aplikasi Dhou yang memfasilitasi katalog video pendek terkurasi berbasis dialek dengan bilah status statistik global (Streak 🔥, Energi ⚡, dan XP ⭐), pemutar video kustom mobile-first dengan kendali subtitle dwibahasa interaktif (Arab berharakat & Indonesia), navigasi transkrip per kalimat (*clickable transcript*), fitur penyimpanan video (*Save Video*) dan penyimpanan idiom (*Save Idioms*), mode latihan peniruan pengucapan mandiri (*Sentence Shadowing* dengan komparasi audio dan **indikator status pengerjaan selesai**), serta integrasi penandaan kosakata ke modul Review (*Save to Review*).

---

## 4. Objectives

1. Mengimplementasikan antarmuka pustaka video (**Video Feed / Catalog**) dengan bilah header atas yang menyajikan indikator konsisten **Streak (🔥), Energi (⚡), dan XP (⭐)** di sisi kanan, serta tombol akses video tersimpan (🔖).
2. Mempertahankan pilihan variasi bahasa/dialek pada menu Video menggunakan **filter pill** (`[Semua]`, `[Fusha]`, `[Mesir]`, `[Syam]`) di bawah header (berbeda dari menu Rute dan Dhou AI yang menggunakan dropdown di samping judul).
3. Menyediakan tombol cepat **Simpan Video (🔖 Simpan)** pada kartu feed dan detail pemutar video untuk mengarsipkan video ke daftar tontonan favorit.
4. Menyediakan tombol **Simpan Idiom (🔖 Simpan Idiom)** pada setiap kalimat transkrip dan drawer kamus mini, terintegrasi ke kuota koleksi *Saved Idioms* (maksimal 20 ungkapan).
5. Membangun pemutar video kustom (**Interactive Video Player**) dengan kontrol kecepatan bertahap (0.75x, 0.9x, 1.0x) dan sakelar toggle subtitle dwibahasa independen.
6. Menyediakan **Transkrip Interaktif Berstempel Waktu (*Interactive Timestamped Transcript*)** dengan fitur sinkronisasi kalimat aktif, lompat waktu (*click-to-seek*), dan putar ulang per kalimat (*Sentence Replay*).
7. Menghadirkan modul latihan lisan **Sentence Shadowing (Record-and-Compare)** yang dilengkapi **indikator visual status pengerjaan (`[✓ Selesai]`)** pada setiap kalimat yang telah direkam dan dicoba oleh pembelajar.
8. Menghadirkan **Kamus Mini Interaktif (*Interactive Word Tap*)** untuk melihat arti kata secara instan dan menyimpannya ke antrean SRS Review.

---

## 5. Scope

### In Scope
- **Pustaka & Filter Video dengan Header Statistik (VID-01)**: Feed kartu video dengan header global (Streak 🔥, Energi ⚡, XP ⭐), tombol bookmark koleksi video tersimpan, dan filter pill dialek horizontal.
- **Pemutar Video & Subtitle Dwibahasa (VID-02)**: Video player mobile-friendly 16:9, toggle subtitle Arab RTL berharakat, toggle terjemahan bahasa Indonesia, selektor kecepatan (0.75x, 0.9x, 1.0x), serta tombol simpan video di header.
- **Transkrip Interaktif & Navigasi Kalimat (VID-03)**: Daftar kalimat sinkron dengan waktu video, klik kalimat untuk melompat, tombol repeat per kalimat, dan tombol simpan idiom.
- **Latihan Shadowing dengan Indikator Selesai (VID-04)**: Perekam suara pengguna lokal per kalimat terpilih, kontrol putar suara asli vs suara rekaman, indikator izin mikrofon, dan **lencana visual status selesai (`[✓ Selesai]`)** setelah latihan direkam.
- **Kamus Mini Kata Arab & Ekstraksi Idiom (VID-05)**: Deteksi klik per kata pada subtitle/transkrip, modal kartu kata instan, tombol *Simpan ke Review*, dan tombol *Simpan Idiom*.
- **Penyimpanan Video & Arsip Tersimpan (VID-06)**: Penandaan video favorit (*Saved Videos*) dan akses cepat ke daftar video yang disimpan.

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

### US-VID-01: Kurasi Feed, Header Statistik & Filter Variasi Dialek

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-01 |
| **Feature ID** | VID-01 |
| **Title** | Dialect-Based Video Feed with Global Stats Header |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saat saya membuka menu Video, saya ingin melihat header statistik belajar saya (Streak 🔥, Energi ⚡, XP ⭐), baris filter dialek berbentuk pill, dan kartu video dengan tombol simpan cepat agar saya dapat memilih materi yang relevan dan memantau status belajar saya. |

#### User Journey
1. Pengguna membuka tab menu **"Video"** dari navigasi bawah (*Bottom Navigation Bar*).
2. Di bilah navigasi atas (header), tampil judul **"Video"** di sisi kiri, serta tiga pill statistik motivasi: **Api Streak (🔥)**, **Energi (⚡ ∞)**, **XP (⭐)**, dan tombol akses video tersimpan (**🔖**) di sisi kanan.
3. Tepat di bawah header, terdapat deretan tombol filter dialek berbentuk pill horizontal: `[Semua]`, `[Fusha]`, `[Mesir]`, `[Syam]`.
4. Pengguna melihat daftar kartu video dalam format feed vertikal yang mulus.
5. Setiap kartu video menampilkan thumbnail dengan tombol simpan video cepat (🔖), badge dialek berwarna khas, judul video dwibahasa, durasi, dan indikator level (`Pemula` / `Menengah`).
6. Pengguna mengetuk salah satu kartu video untuk masuk ke halaman detail pemutar video.

#### Acceptance Criteria
- **AC-VID-01-A (Dialect Pill Filtering):** Memilih filter dialek tertentu wajib menyaring daftar kartu video secara instan tanpa me-reload seluruh halaman.
- **AC-VID-01-B (Card Visual Identity):** Kartu video wajib menampilkan badge dialek dengan warna token yang konsisten:
  - *Fusha:* Hijau Emerald (`#10B981` / `#059669`).
  - *Egyptian ('Ammiyyah Mesir):* Biru Langit Dhou (`#51A2FF` / `#2563EB`).
  - *Levantine ('Ammiyyah Syam):* Oranye Hangat Dhou (`#FF8904` / `#D97706`).
- **AC-VID-01-C (Empty State Handling):** Jika tidak ada video pada filter terpilih, tampilkan ilustrasi lentera Dhou ramah dengan pesan: *"Belum ada video untuk dialek ini. Coba pilih dialek lainnya!"*.
- **AC-VID-01-D (Global Stats Header Integration):** Header atas menu Video wajib menyajikan 3 pill statistik persisten (Streak 🔥, Energi ⚡, XP ⭐) identik dengan menu Rute dan Dhou AI.
- **AC-VID-01-E (Quick Video Bookmark):** Pada setiap kartu feed terdapat ikon bookmark (🔖) untuk menyimpan video ke koleksi tersimpan (*Saved Videos*).

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
2. Di bar header pemutar video, terdapat tombol kembali (←), judul video, dan tombol **Simpan Video (🔖)**.
3. Di bawah layar video terdapat panel kontrol praktis:
   - Tombol Toggle **Sub Arab** (Aktif / Nonaktif).
   - Tombol Toggle **Arti ID** (Aktif / Nonaktif).
   - Tombol Kecepatan: `[0.75x]` `[0.9x]` `[1.0x]`.
4. Saat video diputar, overlay subtitle Arab berharakat (`dir="rtl"`) dan terjemahan Indonesia muncul tepat di atas timeline pemutar dengan kontras tinggi.
5. Pengguna dapat menjeda (*pause*) atau melanjutkan (*play*) video kapan saja dengan mengetuk layar video.

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

### US-VID-03: Transkrip Interaktif, Navigasi Kalimat, Replay & Simpan Idiom

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-03 |
| **Feature ID** | VID-03 |
| **Title** | Interactive Timestamped Transcript, Sentence Actions & Idiom Saving |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saya ingin melihat transkrip percakapan tersinkronisasi di bawah video, dapat melompat per kalimat, menyimpan ungkapan menarik sebagai idiom, dan melihat status latihan shadowing agar belajar lebih terstruktur. |

#### User Journey
1. Di area bawah panel pemutar video, pengguna melihat daftar baris transkrip percakapan (*Transcript List*).
2. Baris kalimat yang sedang diucapkan penutur di video otomatis menyala (*active highlight* dengan aksen oranye lembut) dan sistem menggulirkan posisi transkrip secara halus (*auto-scroll*).
3. Setiap baris kalimat menyediakan:
   - Teks Arab berharakat (`dir="rtl"`).
   - Terjemahan bahasa Indonesia di baris bawahnya.
   - Bilah aksi cepat:
     - Tombol **Replay Kalimat (🔁)**.
     - Tombol **Salin Teks Arab (📋)**.
     - Tombol **Simpan Idiom (🔖 Simpan)**: Menyimpan kalimat/ungkapan ke koleksi *Saved Idioms* (maksimal 20).
     - Tombol / Indikator **Shadowing**:
       - Jika belum dikerjakan: `[🎙️ Rekam]`
       - Jika sudah pernah dikerjakan: `[✓ Selesai]` (badge hijau).
4. Pengguna mengetuk tombol Replay Kalimat: video langsung melompat ke awal kalimat tersebut dan memutarnya hingga batas akhir kalimat tersebut.

#### Acceptance Criteria
- **AC-VID-03-A (Real-Time Sentence Sync):** Sistem memperbarui status kalimat aktif berdasarkan `currentTime` video dengan toleransi latensi $<100\text{ ms}$.
- **AC-VID-03-B (Click-to-Seek):** Mengetuk baris kalimat manapun wajib memindahkan posisi *playback time* video ke `start_time` kalimat tersebut dan memulai pemutaran.
- **AC-VID-03-C (Sentence Replay Mode):** Mengetuk ikon Replay memutar ulang rentang `[start_time, end_time]` kalimat tersebut, lalu video dijeda otomatis pada `end_time`.
- **AC-VID-03-D (Idiom Saving from Transcript):** Mengetuk tombol simpan idiom (🔖) pada kalimat menambahkan ungkapan tersebut ke daftar *Saved Idioms* pengguna (menghormati kuota 20 idiom PRD4). Tombol berubah menjadi status *"Tersimpan ✓"* berwarna amber Dhou.

---

### US-VID-04: Latihan Shadowing dengan Indikator Status Pengerjaan (Record-and-Compare)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-04 |
| **Feature ID** | VID-04 |
| **Title** | Sentence Shadowing with Visual Completion State Indicator |
| **Priority** | High (P1) |
| **Description** | Sebagai pembelajar, saya ingin merekam pengucapan saya untuk satu kalimat tertentu, membandingkannya dengan penutur asli, dan melihat penanda visual jelas bahwa kalimat tersebut sudah saya latih agar saya tahu kalimat mana saja yang sudah selesai dipraktikkan. |

#### User Journey
1. Pengguna mengetuk tombol **"🎙️ Rekam"** pada salah satu baris kalimat di transkrip.
2. Muncul *Bottom Sheet Latihan Shadowing*:
   - Menampilkan teks Arab kalimat target dalam font besar berharakat.
   - Tombol 🔊 **"Dengar Suara Asli"**: Memutar potongan audio penutur asli dari kalimat tersebut.
   - Tombol besar 🎙️ **"Tahan / Ketuk untuk Rekam"**: Pengguna mulai mengucapkan kalimat.
3. Selama merekam, bar visual indikator suara berdenyut dan timer berjalan.
4. Pengguna menyelesaikan rekaman. Sistem menyajikan dua tombol pemutar berdampingan:
   - Tombol A: 🔊 **"Suara Penutur Asli"**
   - Tombol B: 👤 **"Suara Saya"**
5. Pengguna mendengarkan keduanya untuk mengevaluasi intonasinya.
6. Saat pengguna menutup lembar latihan atau menekan *"Simpan Latihan"*, sistem menandai kalimat tersebut sebagai **selesai dipraktikkan**.
7. Pada daftar transkrip, tombol kalimat yang tadinya bertuliskan `[🎙️ Rekam]` kini berubah status menjadi **`[✓ Selesai]`** dengan lencana hijau ramah. Pengguna tetap dapat mengetuknya kembali kapan saja jika ingin merekam ulang.
8. Di header transkrip, indikator progres bertambah (misal: *"Shadowing: 3/5 Selesai"*).

#### Acceptance Criteria
- **AC-VID-04-A (Local Recording Pipeline):** Rekaman audio menggunakan Web MediaRecorder API / Capacitor Audio Plugin dengan format `audio/webm` atau `audio/m4a` dan disimpan sementara di memori perangkat (*local blob URL*).
- **AC-VID-04-B (Capacitor & Web Permission):** Sistem meminta izin mikrofon secara sopan (*graceful permission request*). Jika diizinkan, perekaman langsung aktif.
- **AC-VID-04-C (Smart Max Duration):** Durasi perekaman dibatasi otomatis maksimal $1.5\times$ durasi kalimat asli (atau maksimal 15 detik).
- **AC-VID-04-D (Side-by-Side Playback):** Memutar salah satu audio otomatis menghentikan audio pasangannya.
- **AC-VID-04-E (Visual Completion Indicator):** Setelah pengguna menyelesaikan perekaman minimal 1 kali putaran, sistem menetapkan status kalimat sebagai selesai (`is_shadowed: true`), menampilkan lencana hijau **`[✓ Selesai]`** pada baris transkrip terkait, dan memperbarui penghitung progres shadowing video.

#### Edge Cases & Error Handling
- **EC-VID-04-1 (Izin Mikrofon Ditolak):** Jika pengguna menolak izin mikrofon, tombol rekam dinonaktifkan (*disabled*) dan muncul kotak dialog ramah: *"Dhou membutuhkan akses mikrofon untuk fitur Shadowing. Anda dapat mengaktifkannya di Pengaturan Browser/iPhone"*.
- **EC-VID-04-2 (Rekaman Senyap / Hening):** Jika pengguna merekam tanpa suara, sistem menampilkan tooltip: *"Suara tidak terdengar jelas. Pastikan mikrofon tidak tertutup lalu coba lagi"* dan tidak menandai latihan sebagai selesai.

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

---

### US-VID-06: Penyimpanan Video Favorit (Save Video / Bookmark Video)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-VID-06 |
| **Feature ID** | VID-06 |
| **Title** | Save Video / Bookmark Video to Favorites Collection |
| **Priority** | High (P1) |
| **Description** | Sebagai pembelajar, saya ingin menyimpan video yang saya sukai ke daftar video tersimpan saya agar saya dapat menontonnya kembali kapan saja tanpa harus mencarinya lagi di feed katalog. |

#### User Journey
1. Saat berada di feed katalog video atau di dalam pemutar video, pengguna melihat ikon **Bookmark Video (🔖)**.
2. Pengguna mengetuk ikon bookmark tersebut.
3. Ikon seketika berubah status menjadi aktif berwarna amber Dhou (`#FF8904`) dengan getaran haptic ringan, dan muncul notifikasi toast: *"Video berhasil disimpan ke koleksi!"*.
4. Video tersebut tercatat pada daftar *Saved Videos* akun pengguna.
5. Di bagian header katalog video, pengguna dapat mengetuk tombol koleksi **Bookmark (🔖)** untuk membuka daftar seluruh video tersimpan miliknya.
6. Mengetuk kembali tombol simpan pada video yang sudah tersimpan akan membatalkan simpanan (*un-save*) setelah konfirmasi singkat.

#### Acceptance Criteria
- **AC-VID-06-A (Multi-surface Video Bookmark):** Tombol simpan video tersedia pada kartu feed katalog video dan pada header pemutar video detail.
- **AC-VID-06-B (Saved Videos Drawer / Page):** Ikon bookmark di header feed video membuka layar daftar video tersimpan dengan filter dialek yang sama.
- **AC-VID-06-C (State Persistence):** Status video tersimpan (`is_saved: true`) tersimpan ke basis data pengguna dan bertahan antar-perangkat.

---

## 8. User Interface & Wireframe Specs

### 8.1 Layout Feed Video Katalog (Awal Screen Menu Video)

```text
┌────────────────────────────────────────────────────────┐
│ Video              [🔥 3]   [⚡ ∞]   [⭐ 150]       [🔖] │
├────────────────────────────────────────────────────────┤
│ [Semua]      [Fusha]      [Mesir]      [Syam]          │
├────────────────────────────────────────────────────────┤
│ ┌────────────────────────────────────────────────────┐ │
│ │ ┌────────────────────────────────────────────────┐ │ │
│ │ │                                           [🔖] │ │ │
│ │ │        THUMBNAIL VIDEO (16:9)                  │ │ │
│ │ │        Durasi: 0:45                  [Mesir]   │ │ │
│ │ └────────────────────────────────────────────────┘ │ │
│ │ Pesan Kopi di Kafe Kairo                           │ │
│ │ طلب قهوة في قهوة بلدي                   • Pemula   │ │
│ └────────────────────────────────────────────────────┘ │
│ ┌────────────────────────────────────────────────────┐ │
│ │ ┌────────────────────────────────────────────────┐ │ │
│ │ │                                           [🔖] │ │ │
│ │ │        THUMBNAIL VIDEO (16:9)                  │ │ │
│ │ │        Durasi: 1:15                  [Syam]    │ │ │
│ │ └────────────────────────────────────────────────┘ │ │
│ │ Belanja di Pasar Damaskus                          │ │
│ │ التسوق في سوق دمشق                      • Menengah │ │
│ └────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────┘
```

### 8.2 Layout Halaman Pemutar Video (Mobile Viewport: 360px–430px)

```text
┌────────────────────────────────────────────────────────┐
│ [← Kembali]       Klip Percakapan Kafe     [🔖 Tersimpan]│
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
│ TRANSKRIP INTERAKTIF               Shadowing: 1/2 Selesai│
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 0:00 - 0:05 | [Mesir]       [🔁] [📋] [🔖] [✓ Selesai]│ │
│ │ صباح الخير! إزيك يا صاحبي؟                          │ │
│ │ Selamat pagi! Bagaimana kabarmu kawan?             │ │
│ └────────────────────────────────────────────────────┘ │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 0:06 - 0:12 | [Mesir] (AKTIF)[🔁] [📋] [🔖] [🎙️ Rekam]│ │
│ │ الحمد لله، كله تمام. إنت عامل إيه؟                  │ │
│ │ Alhamdulillah, semua baik. Kamu bagaimana kabarnya?│ │
│ └────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────┘
```

### 8.3 Design Tokens & Visual Hierarchy

| Komponen UI | Token Warna & Tipografi | Interaksi & Status |
| :--- | :--- | :--- |
| **Streak Pill Badge** | Bg: `#FFF7ED`, Border: `#FFD6A8`, Teks: `#EA580C`, Font: Fredoka Bold | Tampil di header awal screen Video (`🔥 3`) |
| **Energy Pill Badge** | Bg: `#ECFDF5`, Border: `#A7F3D0`, Teks: `#059669`, Font: Fredoka Bold | Tampil di header awal screen Video (`⚡ ∞`) |
| **XP Pill Badge** | Bg: `#FEF3C7`, Border: `#FDE68A`, Teks: `#B45309`, Font: Fredoka Bold | Tampil di header awal screen Video (`⭐ 150`) |
| **Dialect Pill Filter** | Default: `#F3F4F6`, Aktif: `#FF8904` (Teks putih), Radius `9999px` | Filter horizontal di bawah header katalog video |
| **Shadowing Done Badge**| Bg: `#DCFCE7`, Border: `#86EFAC`, Teks: `#16A34A`, Font: Fredoka Medium | Menggantikan tombol rekam saat kalimat selesai dilatih (`✓ Selesai`) |
| **Video Bookmark Button**| Default: `#F3F4F6` (Abu). Aktif: `#FFF7ED` (Border `#FF8904`, Teks/Icon `#D67000`) | Tombol simpan video di thumbnail dan header pemutar |
| **Save Idiom Button (🔖)**| Default: `#F3F4F6`. Aktif: `#FFF7ED` (Teks `#D67000`, Amber Dhou) | Tombol simpan idiom per kalimat transkrip |
| **Active Sentence Row** | Bg: `#FFF7ED`, Border: `2px solid #FF8904`, Radius: `16px` | Transisi border `200ms ease`, Auto-scroll center |
| **Arabic Subtitle Font**| Font: *Amiri* / *Noto Sans Arabic*, Size: `20px–22px`, Weight: `700`, RTL | Line height `1.8` (ruang lega untuk harakat) |

---

## 9. Business Rules Summary Matrix

| Rule ID | Domain | Aturan Bisnis | Konsekuensi Sistem |
| :--- | :--- | :--- | :--- |
| **RL-VD-01** | Label Varietas | Setiap video wajib memiliki tepat 1 label dialek primer (*Fusha*, *Egyptian*, atau *Levantine*). | Video tanpa label dialek ditolak oleh validasi kurasi konten. |
| **RL-VD-02** | Kecepatan Putar | Kecepatan yang didukung hanya 3 opsi: 0.75x, 0.9x, dan 1.0x. | Mengunci opsi agar kualitas artikulasi fonetik bahasa Arab tetap jelas tanpa distorsi pitch. |
| **RL-VD-03** | Rekaman Shadowing | Audio rekaman pengguna diproses secara *client-side local memory*. | Tidak ada audio rekaman yang diunggah ke server kecuali pengguna secara eksplisit meminta sinkronisasi profil masa depan. |
| **RL-VD-04** | Durasi Maksimal Rekam | Batas rekam per kalimat adalah $\min(15\text{ detik}, 1.5 \times \text{durasi kalimat})$. | Perekaman otomatis berhenti jika melebihi batas waktu. |
| **RL-VD-05** | Deduplikasi Review & Idiom | Menambahkan kata atau idiom yang sudah ada tidak menghasilkan duplikasi record di database. | Sistem melakukan *upsert* atau memperbarui timestamp terakhir dilihat. |
| **RL-VD-06** | Penyelesaian Video | Video ditandai *"Tuntas Ditonton"* jika pengguna telah memutar minimal 80% dari total durasi video. | Memperbarui bar progres video pengguna di database. |
| **RL-VD-07** | Indikator Shadowing | Setiap kalimat yang telah direkam minimal 1 kali mempertahankan status `is_shadowed: true` dalam sesi video aktif. | Transkrip menampilkan badge hijau `[✓ Selesai]` untuk memandu navigasi latihan pengguna. |
| **RL-VD-08** | Global Header Gamifikasi | Header awal screen Video wajib menampilkan tiga indikator konsisten: Streak (🔥), Energi (⚡), dan XP (⭐). | Kontinuitas data progres harian pengguna tetap sinkron di seluruh menu belajar. |

---

## 10. Business Metrics & KPIs

| Metrik | Definisi | Target MVP | Cara Pengukuran |
| :--- | :--- | :--- | :--- |
| **Video Completion Rate** | Rasio video yang ditonton hingga $\ge 80\%$ dibanding total video yang dibuka. | $\ge 60\%$ | Event analytics `video_completed / video_opened` |
| **Shadowing Completion Rate**| Persentase kalimat video dengan status `[✓ Selesai]` per video yang ditonton. | $\ge 40\%$ | Agregat `shadowed_sentences / total_sentences` |
| **Saved Video Rate** | Persentase pengguna yang menyimpan minimal 1 video ke daftar favorit. | $\ge 30\%$ | Query tabel `saved_videos` |
| **Idiom Bookmark Rate** | Rata-rata jumlah idiom yang disimpan pengguna dari video per minggu. | $\ge 3$ Idiom | Query tabel `saved_idioms` dengan `source = 'video'` |
| **Playback Speed Adoption**| Persentase pengguna yang memanfaatkan fitur slow-motion (0.75x / 0.9x). | $\ge 40\%$ | Event `speed_change` pada video player |

---

## 11. Appendix & FRD3 Mapping

Dokumen PRD3 ini menjadi acuan definitif untuk spesifikasi teknis fungsional pada berkas:  
[`Requirements/FRD/FRD3 - Video Contextual Learning, Interactive Subtitles, and Shadowing.md`](file:///Users/farizmirza/Documents/Project/dhou-docs/Requirements/FRD/FRD3%20-%20Video%20Contextual%20Learning,%20Interactive%20Subtitles,%20and%20Shadowing.md).
