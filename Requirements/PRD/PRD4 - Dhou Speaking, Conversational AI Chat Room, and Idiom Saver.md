# Product Requirement Document (PRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | PRD4 |
| **Feature ID** | SPK-01, SPK-02, SPK-03, SPK-04, SPK-05, SPK-06 |
| **Feature Name** | Dhou Speaking: Conversational AI Chat Room, Dialect Inheritance, Speech Practice, and Saved Idioms/Conversations |
| **Application** | Dhou (تطبيق ضوء) — Mobile Web (PWA) & iOS Native Wrapper (CapacitorJS) |
| **Module** | Conversational AI & Communicative Production Core (Pilar 3) |
| **Sprint** | Sprint 4 |
| **Epic** | Communicative Production, Voice Interaction & Idiom Retention |
| **Priority** | Critical (P0) |
| **Status** | Approved |
| **Implementation Status** | Planned |
| **Version** | 1.0 |
| **Last Updated** | 2026-11-05 |
| **Product Owner** | Fariz Mirza Abdillah |
| **Dependencies** | PRD1 (Authentication), PRD2 (Learning Path - Dialect State), Design System (Figma Dhou), Research Reference Mapping (Pilar 3) |

---

## 1. Business Problem

Dalam proses pembelajaran bahasa Arab, banyak pembelajar menghadapi fenomena **kegagapan komunikasi nyata (*communication breakdown*)**. Meskipun mereka telah mempelajari tata bahasa formal (*fusha*) di ruang kelas dan memahami arti kalimat di modul pasif, mereka tetap:
1. **Mengalami Kecemasan Berbicara (*Speaking Anxiety*)**: Takut salah tata bahasa, takut ditertawakan, atau malu saat berbicara langsung dengan penutur asli.
2. **Ketiadaan Mitra Bicara Dialek Sehari-hari**: Sulit menemukan lawan bicara yang secara konsisten menggunakan dialek lisan sehari-hari (*'Ammiyyah Mesir* atau *Syam*).
3. **Frustrasi Hambatan Awal (High Friction Entry)**: Sebagian besar aplikasi percakapan AI membebani pengguna dengan banyak pengaturan rumit di awal (pilih topik, pilih kepribadian AI, pilih level), sehingga pengguna enggan memulai.
4. **Hilangnya Idiom dan Ungkapan Percakapan Penting**: Percakapan lisan kaya akan idiom dan ungkapan situasional (misal: *كل سنة وانت طيب*, *يعطيك العافية*). Tanpa fitur simpan idiom yang instan langsung dari gelembung chat, kosakata berharga tersebut langsung terlupakan.

---

## 2. Business Value

- **Zero-Friction Immersion (Langsung Masuk & Bicara)**: Pengguna mengetuk tab navigasi bawah dan langsung berada di dalam *Chat Room* tanpa layar konfigurasi berbelit. Dialek otomatis mengikuti materi terakhir yang dipelajari di menu *Path*, sehingga pengalaman belajar terasa mulus dan terintegrasi.
- **Produksi Lisan Aktif Mandiri Tanpa Rasa Takut**: Tombol mikrofon instan memicu AI mengajak berbicara dalam bahasa Arab komunikatif dengan audio Text-to-Speech (TTS) yang alami, membiasakan telinga dan lidah pengguna.
- **Peningkatan Retensi melalui Saved Idioms (Maksimal 20 Ungkapan)**: Pembelajar dapat menandai (*bookmark*) ungkapan atau idiom khas dari setiap gelembung chat AI ke dalam koleksi idiom tersimpan dengan kuota maksimal 20 ungkapan untuk diulang kapan saja.
- **Kontrol Penyimpanan Percakapan Cerdas (Maksimal 3 Sesi)**: Pengguna dapat menyimpan maksimal 3 percakapan utuh favorit untuk diputar ulang, menjaga kebersihan ruang penyimpanan server sekaligus menjadi insentif retensi yang terukur.
- **Skalabilitas & Transparansi Dua Arah**: Setiap gelembung chat menyajikan teks Arab berharakat, audio pengucapan, terjemahan bahasa Indonesia, serta aksi cepat salin teks (📋) dan simpan idiom (🔖), memberikan kenyamanan belajar yang optimal bagi pembelajar pemula hingga menengah.

---

## 3. Purpose

Menyediakan modul **Dhou Speaking (Conversational AI Chat Room)** yang memungkinkan pengguna masuk langsung ke ruang obrolan suara interaktif dengan dialek yang otomatis tersinkronisasi dari progres menu *Path*. Pengguna cukup mengetuk tombol mikrofon/putar untuk langsung diajak berbicara oleh AI, melihat transkrip Arab berharakat dua arah disertai terjemahan Indonesia, menyalin teks Arab ke clipboard secara instan, memutar ulang audio rekaman per gelembung chat, menyimpan idiom/frasa penting di setiap pesan AI hingga kuota maksimal 20 ungkapan (*quota up to 20 saved idioms*), serta mengarsipkan hingga maksimal 3 percakapan utuh (*saved conversations*) dengan akses navigasi cepat dari ruang obrolan.

---

## 4. Objectives

1. **Auto-Inherited Dialect & Direct Room Entry**:
   - Menghilangkan *setup modal* di awal: mengetuk menu "Dhou AI" pada bottom navigation langsung membuka layar *Chat Room*.
   - Dialek ruang obrolan otomatis mewarisi dialek terakhir yang sedang aktif dipelajari di menu *Gamified Path* (misal: *'Ammiyyah Mesir* atau *Fusha*). Jika pengguna baru belum memiliki riwayat, sistem default ke *Fusha*.
2. **Instant Voice Initiation (Push/Tap-to-Speak)**:
   - Satu ketukan pada tombol mikrofon/play utama di panel bawah langsung mengaktifkan sesi: AI langsung membuka percakapan dengan sapaan suara ramah sesuai dialek, lalu mendengarkan respons pengguna.
3. **Bilingual Interactive Chat Bubbles & Quick Actions**:
   - Gelembung pesan AI dan Pengguna menampilkan teks Arab berharakat (`dir="rtl"`, font *Amiri/Noto Sans Arabic*).
   - Terdapat tombol toggle/teks terjemahan bahasa Indonesia di setiap gelembung.
   - Di bawah setiap gelembung pesan terdapat aksi cepat:
     - Ikon Speaker (🔊) untuk memutar ulang audio tuturan secara independen.
     - Tombol Salin (📋 Salin): Tersedia di setiap gelembung pesan AI dan gelembung pesan pengguna untuk menyalin teks Arab ke clipboard disertai feedback visual (*"Tersalin!"*) dan notifikasi toast.
     - Tombol Simpan (🔖 Simpan / ✓ Tersimpan): Selalu muncul di setiap pesan AI (termasuk salam pembuka *Fusha*), bukan hanya yang memiliki penanda idiom khusus. Jika pesan disimpan, status tombol berubah menjadi *"Tersimpan ✓"* berwarna kuning/amber khas Dhou.
4. **Manajemen Arsip Kuota Cerdas**:
   - **Saved Idioms (Maksimal 20 Ungkapan)**: Pengguna dapat menyimpan hingga **maksimal 20 ungkapan/idiom**. Notifikasi kuota `⭐ {jumlah}/20` ditampilkan di header modal kamus ungkapan, banner pemberitahuan, serta panel koleksi desktop kanan. Jika kuota sudah mencapai 20, tombol simpan menolak penambahan baru dan memunculkan toast: *"⚠️ Kuota idiom penuh (maksimal 20 idiom tersimpan)"*.
   - **Saved Conversations**: Pengguna dapat menyimpan maksimal **3 sesi percakapan utuh** ke arsip cloud. Jika kuota penuh (3/3), sistem menampilkan modal pilihan untuk menghapus percakapan lama sebelum menyimpan yang baru.
5. **Struktur Header & Akses Halaman Koleksi Terpadu**:
   - **Header / TopBar (Mobile & Desktop)**:
     - **Rata Kiri (*Left-Aligned*)**: Tombol pemilih/ganti dialek (*Dialect Switcher*, misal `[Mesir 🇪🇬 ▾]`) untuk pergantian cepat tanpa kembali ke modul Path.
     - **Tengah (*Centered*)**: Identitas AI (Avatar Lentera Dhou & status *"Online"*).
     - **Rata Kanan (*Right-Aligned*)**: Ikon Bookmark (🔖) sebagai akses cepat menuju **Halaman Koleksi Tersimpan (*Saved Collection Page*)**.
   - **Halaman Koleksi Tersimpan Terpadu (Satu Halaman dengan 2 Tab)**:
     - Halaman/drawer koleksi tidak lagi terpisah, melainkan digabung menjadi satu halaman tunggal dengan sistem tab:
       - **Tab 1: Idiom Tersimpan (⭐ {jumlah}/20)**: Menampilkan kartu idiom tersimpan dengan pemutar audio 🔊, tombol salin 📋, dan filter dialek.
       - **Tab 2: Percakapan Tersimpan (📁 {jumlah}/3)**: Menampilkan hingga 3 sesi dialog tersimpan untuk dibuka dan diputar ulang dalam mode *Read-Only*.

---

## 5. Scope

### In Scope
- **Direct Chat Room Landing & Dialect Switcher (SPK-01)**: Masuk langsung ke ruang obrolan saat tab navigasi bawah ditekan, dengan status dialek otomatis terikat ke progres *Path*, dan tombol ganti dialek di sudut kiri atas header (rata kiri) pada mobile & desktop.
- **Instant Audio Conversation Engine (SPK-02)**: Tombol rekam mikrofon / pemutar suara yang langsung memicu interaksi lisan dua arah dengan AI Dhou.
- **Bilingual Transcript, Audio Replay & Quick Copy (SPK-03)**: Gelembung chat menampilkan transkrip Arab berharakat, terjemahan Indonesia, tombol salin teks Arab ke clipboard (📋 Salin) dengan status visual *"Tersalin!"*, dan ikon speaker (🔊) untuk mendengarkan ulang audio kapan saja.
- **Penyimpanan Idiom Kuota Maksimal 20 (SPK-04)**: Tombol simpan idiom (🔖) yang selalu tersedia di setiap pesan AI dengan status tombol *"Tersimpan ✓"* berwarna kuning/amber khas Dhou, dibatasi maksimal 20 ungkapan beserta peringatan toast saat penuh.
- **Penyimpanan Percakapan Kuota 3 Sesi (SPK-05)**: Fitur simpan sesi obrolan aktif dengan batas maksimum 3 sesi percakapan per akun, disertai validasi kuota penuh.
- **Navigasi Layar Koleksi Terpadu Ber-Tab (SPK-06)**: Halaman/drawer koleksi tunggal yang menggabungkan tab *Idiom Tersimpan* (kuota ⭐ 0–20/20) dan tab *Percakapan Tersimpan* (kuota 📁 0–3/3), diakses melalui tombol bookmark (🔖) di rata kanan header.

### Out of Scope (Post-MVP)
- Fitur video call atau avatar 3D yang bergerak mengikuti ucapan (cukup avatar lampion Dhou ramah dengan indikator animasi suara di header).
- Fitur koreksi fonetik mendalam tingkat milidetik (misal: visualisasi gelombang spektrogram fonem).
- Pengunggahan rekaman audio kustom dari luar aplikasi oleh pengguna.

---

## 6. User Stories & Acceptance Criteria

### US-SPK-01: Masuk Langsung ke Chat Room & Pewarisan Dialek

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-01 |
| **Feature ID** | SPK-01 |
| **Title** | Direct Chat Room Landing with Auto-Inherited Dialect & Global Stats Header |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saat saya mengetuk ikon menu Dhou AI di bottom navigation, saya ingin langsung masuk ke ruang obrolan dengan dialek yang sama persis seperti materi terakhir yang saya pelajari di menu Rute, serta melihat status streak, energi, dan XP saya di header agar saya tidak membuang waktu mengatur ulang bahasa. |

#### User Journey
1. Pengguna sedang mempelajari unit dialek Mesir di menu *Rute*.
2. Pengguna mengetuk tab **"Dhou AI"** pada bilah navigasi bawah (*Bottom Navigation Bar*).
3. Aplikasi seketika membuka layar **Chat Room**.
4. Di bagian header atas, terlihat judul menu dan dropdown pemilih dialek di **rata kiri** (`Dhou AI [Mesir 🇪🇬 ▾]`), serta indikator stats pembelajaran (`[🔥 3] [⚡ ∞] [⭐ 150]`) dan ikon bookmark koleksi (`🔖`) di **rata kanan**. Format pilihan dialek menggunakan Dropdown UI yang seragam baik di desktop maupun mobile.
5. Pengguna siap memulai obrolan tanpa harus melalui jendela pop-up persiapan.

#### Acceptance Criteria
- **AC-SPK-01-A (Zero Setup Flow):** Mengetuk tab Dhou AI tidak menampilkan dialog atau form persiapan; layar Chat Room langsung dirender dalam waktu $<300\text{ ms}$.
- **AC-SPK-01-B (Dialect Inheritance):** Sistem membaca `user_preferences.last_active_dialect`. Jika sesi terakhir di menu *Rute* adalah `egyptian`, AI di ruang obrolan otomatis berbicara dalam dialek Mesir.
- **AC-SPK-01-C (Fallback Default):** Jika pengguna baru pertama kali menggunakan aplikasi dan belum pernah membuka menu Rute, sistem otomatis menetapkan dialek `fusha` (Standar).
- **AC-SPK-01-D (Left-Aligned Title & Dialect Dropdown - Mobile & Desktop):** Judul menu dan dropdown dialek wajib bersanding di sudut kiri atas header (`Dhou AI [Mesir 🇪🇬 ▾]`) menggunakan komponen Dropdown UI baik pada tata letak Mobile maupun Desktop.
- **AC-SPK-01-E (Global Stats Header & Bookmark):** Sudut kanan atas header wajib selalu menampilkan 3 pill stats pembelajaran aktif (`[🔥 {streak}] [⚡ {energy}] [⭐ {xp}]`) bersanding dengan ikon bookmark koleksi (`[🔖]`).

---

### US-SPK-02: Latihan Bicara & Inisiasi Suara AI Instan

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-02 |
| **Feature ID** | SPK-02 |
| **Title** | Instant Voice Initiation & Conversational Speech Practice |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saya ingin menekan tombol mikrofon/putar besar di bawah chat room agar AI langsung menyapa dan mengajak saya berbicara bahasa Arab secara lisan sehingga saya terbiasa mendengar dan merespons. |

#### User Journey
1. Di bagian bawah layar chat room, pengguna melihat tombol utama mikrofon melingkar (🎙️) dengan label *"Mulai Bicara"*.
2. Pengguna mengetuk tombol mikrofon.
3. AI Dhou seketika merespons dengan memutar suara sapaan pembuka (TTS) dalam dialek aktif (misal: *"أهلاً وسهلاً يا صديقي! عامل إيه النهاردة؟"*).
4. Tombol mikrofon berubah status menjadi mendengarkan (*Listening... / Wave Animation*).
5. Pengguna mengucapkan jawaban dalam bahasa Arab.
6. Suara pengguna ditranskripsi menjadi teks, dikirim ke AI, dan AI membalas kembali dengan suara serta teks secara bergantian.

#### Acceptance Criteria
- **AC-SPK-02-A (One-Tap Start):** Mengetuk tombol mikrofon/play utama langsung mengaktifkan sesi audio dua arah tanpa perlu menekan tombol 'Play' terpisah.
- **AC-SPK-02-B (Speech-to-Text Accuracy):** Audio masukan pengguna ditranskripsikan ke dalam teks aksara Arab berharakat dengan penyesuaian dialek terpilih.
- **AC-SPK-02-C (Natural TTS Audio):** AI merespons dengan audio suara penutur Arab yang jernih dengan ritme bicara yang ramah bagi pembelajar.
- **AC-SPK-02-D (Alternative Keyboard Input):** Di samping tombol mikrofon terdapat tombol keyboard kecil (⌨️) untuk beralih ke input teks jika pengguna sedang di tempat umum/bising.

#### Edge Cases & Error Handling
- **EC-SPK-02-1 (Izin Mikrofon Ditolak):** Jika pengguna menolak izin mikrofon perangkat, sistem menampilkan pop-up instruksi izin dan otomatis mengalihkan input ke mode keyboard teks agar percakapan tetap bisa berlanjut.
- **EC-SPK-02-2 (Input Hening / Tidak Terdengar):** Jika pengguna menekan tombol bicara namun tidak ada suara selama 5 detik, sistem memunculkan toast ramah: *"Suara tidak terdengar. Silakan coba ucapkan lagi!"* tanpa menghentikan sesi.

---

### US-SPK-03: Gelembung Chat Dwibahasa, Putar Ulang Audio & Salin Teks

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-03 |
| **Feature ID** | SPK-03 |
| **Title** | Bilingual Chat Bubbles with Audio Replay & Quick Copy |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saya ingin setiap kalimat percakapan tertranskripsi jelas dalam bahasa Arab berharakat lengkap dengan opsi terjemahan Indonesia, tombol speaker, serta tombol salin teks agar saya bisa membaca, mendengarkan, dan menyalin tuturan tersebut dengan mudah. |

#### User Journey
1. Sambil mendengarkan AI berbicara, gelembung chat AI muncul di sisi kiri layar (atau gelembung pesan pengguna di sisi kanan).
2. Teks Arab tampil dengan ukuran font besar dan berharakat lengkap (`dir="rtl"`).
3. Di bawah teks Arab, terdapat teks terjemahan bahasa Indonesia berwarna abu-abu ramah.
4. Di baris bawah gelembung pesan, terdapat bilah aksi cepat:
   - Ikon Speaker (🔊) untuk memutar ulang audio rekaman tuturan.
   - Tombol Salin (📋 Salin) untuk menyalin teks aksara Arab.
   - Tombol Simpan (🔖 Simpan) pada gelembung AI.
5. Pengguna mengetuk ikon Speaker (🔊): audio kalimat tersebut diputar ulang secara jernih tanpa mengganggu posisi obrolan.
6. Pengguna mengetuk tombol Salin (📋 Salin):
   - Teks Arab berharakat tersalin seketika ke clipboard perangkat.
   - Label tombol berubah sesaat menjadi *"Tersalin!"* dengan ikon centang selama 2 detik.
   - Muncul feedback notifikasi toast: *"Teks Arab berhasil disalin ke clipboard"*.

#### Acceptance Criteria
- **AC-SPK-03-A (Arabic Typography Standards):** Teks Arab menggunakan font *Amiri* atau *Noto Sans Arabic* berukuran minimal `18px–20px` dengan line-height `1.8` agar harakat jelas terbaca.
- **AC-SPK-03-B (Independent Audio Replay):** Mengetuk ikon speaker (🔊) pada gelembung manapun memutar ulang audio rekaman asli dari pesan tersebut. Jika audio pesan lain sedang berputar, audio sebelumnya otomatis dijeda (*stop & switch*).
- **AC-SPK-03-C (Translation Visibility):** Terjemahan bahasa Indonesia ditampilkan secara default di bawah teks Arab, atau dapat disembunyikan lewat toggle global di header bagi pembelajar tingkat lanjut yang ingin melatih kemampuan menyimak murni.
- **AC-SPK-03-D (Clipboard Copy & Visual Feedback):** Setiap gelembung pesan AI maupun Pengguna dilengkapi tombol salin (📋 Salin). Mengklik tombol menyalin teks Arab ke clipboard, menampilkan feedback teks *"Tersalin!"* selama 2 detik, dan memunculkan toast konfirmasi.

---

### US-SPK-04: Simpan Ungkapan / Idiom (Maksimal Kuota 20)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-04 |
| **Feature ID** | SPK-04 |
| **Title** | Universal AI Message Saving with Max 20 Idiom Quota |
| **Priority** | High (P1) |
| **Description** | Sebagai pembelajar, saya ingin tombol simpan selalu muncul di setiap pesan AI untuk menyimpan ungkapan penting ke daftar koleksi dengan batas kuota maksimal 20 ungkapan agar saya dapat mengulang dan menghafalnya tanpa kelebihan kapasitas. |

#### User Journey
1. Pengguna membaca pesan AI pada gelembung obrolan (baik salam pembuka *Fusha*, kalimat dialog harian, maupun idiom khusus).
2. Di bawah setiap pesan AI, tombol **🔖 Simpan** selalu terlihat dan siap ditekan.
3. Pengguna mengetuk tombol **🔖 Simpan**:
   - **Kondisi Kuota Tersedia ($< 20$):**
     - Tombol seketika berubah status menjadi **"Tersimpan ✓"** dengan warna latar kuning/amber khas Dhou (`#FF8904` / `#FFF7ED` border `#D67000`) disertai haptic feedback ringan.
     - Ungkapan tersimpan ke koleksi *Saved Idioms* pengguna.
     - Indikator kuota idiom diperbarui (misal bertambah menjadi `⭐ 12/20`).
   - **Kondisi Kuota Penuh (20/20):**
     - Sistem menolak penambahan baru.
     - Muncul peringatan toast: *"⚠️ Kuota idiom penuh (maksimal 20 idiom tersimpan)"*.
     - Tombol tetap berada pada status belum tersimpan (*"🔖 Simpan"*).
4. Pengguna dapat membuka modal kamus ungkapan untuk menghapus ungkapan lama jika ingin mengosongkan slot kuota.

#### Acceptance Criteria
- **AC-SPK-04-A (Max 20 Quota Limit):** Sistem memberlakukan batas kuota ketat **maksimal 20 idiom tersimpan** per akun. Penambahan ke-21 diblokir dengan peringatan toast: *"⚠️ Kuota idiom penuh (maksimal 20 idiom tersimpan)"*.
- **AC-SPK-04-B (Universal AI Message Availability):** Tombol simpan selalu muncul pada **setiap gelembung pesan AI** tanpa pengecualian (termasuk salam awal *Fusha*), bukan hanya pada pesan yang memiliki penanda idiom khusus.
- **AC-SPK-04-C (Amber Dhou Brand Feedback):** Saat tersimpan, tombol menampilkan status *"Tersimpan ✓"* dengan aksen visual kuning/amber khas Dhou (`#FF8904` / border `#D67000`).
- **AC-SPK-04-D (Metadata Retention):** Data idiom yang disimpan wajib menyertakan: teks Arab berharakat, terjemahan Indonesia, audio snippet URL, asal dialek, dan konteks percakapan.
- **AC-SPK-04-E (Toggle Un-save):** Mengetuk kembali tombol *"Tersimpan ✓"* membatalkan simpanan (*un-save*), mengurangi penghitung kuota, dan mengembalikan tombol ke tampilan awal *"🔖 Simpan"*.
- **AC-SPK-04-F (Multi-surface Quota Badge):** Notifikasi kuota `⭐ {jumlah}/20` wajib ditampilkan secara konsisten di header modal kamus ungkapan, banner pemberitahuan, serta panel koleksi desktop kanan.

---

### US-SPK-05: Simpan Percakapan Utuh (Maksimal 3 Percakapan)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-05 |
| **Feature ID** | SPK-05 |
| **Title** | Full Conversation Session Archiving (Max 3 Slots) |
| **Priority** | High (P1) |
| **Description** | Sebagai pembelajar, saya ingin menyimpan seluruh sesi percakapan obrolan ini ke daftar percakapan tersimpan saya dengan kuota maksimal 3 sesi agar saya dapat meninjau kembali seluruh alur dialog di lain hari. |

#### User Journey
1. Pengguna telah menyelesaikan sesi obrolan yang memuaskan dengan AI.
2. Pengguna mengetuk tombol menu / opsi di header atas chat room, lalu memilih **"Simpan Percakapan Ini (💾)"**.
3. Sistem memeriksa kuota percakapan tersimpan pengguna saat ini:
   - **Jika slot tersedia ($< 3$):** Sesi obrolan berhasil disimpan; muncul toast notifikasi *"Percakapan berhasil disimpan (1/3)"*.
   - **Jika kuota penuh (3/3):** Muncul modal *Quota Limit Reached* yang menampilkan daftar 3 percakapan lama, memberi opsi kepada pengguna untuk memilih 1 percakapan lama yang ingin dihapus (*replace*) agar percakapan baru dapat tersimpan.

#### Acceptance Criteria
- **AC-SPK-05-A (Max 3 Quota Rule):** Sistem membatasi tepat **maksimal 3 percakapan tersimpan** per akun pengguna pada level basis data dan API.
- **AC-SPK-05-B (Complete Session Payload):** Percakapan yang disimpan memuat seluruh riwayat pesan dua arah, audio rekaman masing-masing gelembung, timestamp, dan dialek yang digunakan.
- **AC-SPK-05-C (Replace Modal Interaction):** Pada kondisi kuota 3/3, pengguna tidak diblokir secara buntu, melainkan diberikan dialog pemilihan untuk menimpa/menghapus salah satu dari 3 percakapan lama.

#### Edge Cases & Error Handling
- **EC-SPK-05-1 (Percakapan Kosong):** Jika sesi hanya memuat kurang dari 2 pesan bolak-balik, tombol simpan percakapan dinonaktifkan dengan peringatan: *"Lakukan percakapan minimal 2 putaran untuk dapat menyimpan"*.

---

### US-SPK-06: Layar Koleksi Terpadu Ber-Tab (Saved Idioms & Conversations)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-06 |
| **Feature ID** | SPK-06 |
| **Title** | Unified Saved Collection Navigation with Tabbed Interface via Bookmark Icon |
| **Priority** | Medium (P2) |
| **Description** | Sebagai pembelajar, saya ingin mengetuk ikon bookmark di rata kanan header chat room untuk membuka satu halaman koleksi terpadu yang memuat tab Idiom Tersimpan dan tab Percakapan Tersimpan agar seluruh materi arsip saya terpusat dan mudah diakses. |

#### User Journey
1. Di bilah navigasi header atas Chat Room:
   - **Rata Kiri (*Left-Aligned*)**: Tombol pemilih/pengganti dialek (`[Mesir 🇪🇬 ▾]`)
   - **Tengah (*Centered*)**: Avatar Lentera Dhou & status *"Online"*
   - **Rata Kanan (*Right-Aligned*)**: Tombol ikon bookmark (`🔖`)
2. Pengguna mengetuk tombol ikon bookmark (`🔖`) di rata kanan header.
3. Aplikasi membuka **Halaman Koleksi Tersimpan (*Saved Collection Page / Drawer*)** dengan 2 tab terpadu:
   - **Tab 1: ⭐ Idiom Tersimpan ({jumlah}/20)**:
     - Menampilkan daftar kartu idiom tersimpan.
     - Setiap kartu memiliki tombol pemutar audio (🔊), tombol salin teks Arab (📋 Salin), lencana dialek asal, dan tombol hapus (*un-save*).
   - **Tab 2: 📁 Percakapan Tersimpan ({jumlah}/3)**:
     - Menampilkan hingga 3 kartu sesi dialog yang pernah disimpan beserta tanggal, dialek, dan jumlah pesan.
     - Pengguna dapat mengetuk kartu sesi untuk membuka dan menyimak kembali riwayat obrolan dalam mode *Read-Only*.
4. Pengguna dapat beralih antar-tab dengan sekali sentuh pada label tab atau melalui gestur *swipe*.

#### Acceptance Criteria
- **AC-SPK-06-A (Right-Aligned Bookmark Entry):** Pintu masuk ke layar koleksi adalah satu tombol berikon bookmark (`🔖`) yang ditempatkan secara konsisten di sudut kanan atas header (**rata kanan**), baik pada tata letak Mobile maupun Desktop.
- **AC-SPK-06-B (Unified Single Page with Tabs):** Halaman arsip idiom dan percakapan dijadikan **satu halaman/drawer terpadu**, dibedakan dengan sistem 2 tab (*Tabbed Navigation*).
- **AC-SPK-06-C (Tab Quota Indicators):** Label masing-masing tab menyertakan badge kuota aktual secara dinamis (Tab Idiom: `⭐ {jumlah}/20`, Tab Percakapan: `📁 {jumlah}/3`).
- **AC-SPK-06-D (Read-Only & Audio Playback in Tabs):** Tab Idiom menyediakan aksi putar audio dan salin teks Arab; Tab Percakapan memungkinkan pemutaran kembali seluruh alur dialog dalam mode *Read-Only* tanpa memicu STT/TTS baru.

---

## 7. User Interface & Wireframe Specs

#### 7.1 Layout Chat Room Utama (Mobile Viewport: 360px–430px)

```text
┌────────────────────────────────────────────────────────┐
│ Dhou AI [Mesir 🇪🇬 ▾]         [🔥 3] [⚡ ∞] [⭐ 150] [🔖]│
├────────────────────────────────────────────────────────┤
│                    Hari Ini, 10:15                     │
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 🤖 Dhou AI                                         │ │
│ │ أَهْلاً وَسَهْلاً يَا صَدِيقِي! كَيْفَ حَالُكَ اليَوْم؟    │ │
│ │ Halo sahabatku! Bagaimana kabarmu hari ini?       │ │
│ │ ────────────────────────────────────────────────── │ │
│ │ [🔊 Putar]   [📋 Salin]   [🔖 Simpan]              │ │
│ └────────────────────────────────────────────────────┘ │
│                                                        │
│                           ┌──────────────────────────┐ │
│                           │ 👤 Kamu                  │ │
│                           │ أَنَا بِخَيْرٍ الحَمْدُ لِلَّه!   │ │
│                           │ Saya baik, alhamdulillah!│ │
│                           │ ──────────────────────── │ │
│                           │ [🔊 Putar]    [📋 Salin] │ │
│                           └──────────────────────────┘ │
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 🤖 Dhou AI                                         │ │
│ │ عَامِل إِيه فِي دِرَاسَتَك؟ كُلُّه تَمَام؟            │ │
│ │ Bagaimana belajarmu? Semua lancar?                 │ │
│ │ ────────────────────────────────────────────────── │ │
│ │ [🔊 Putar]   [📋 Salin]   [Tersimpan ✓]            │ │
│ └────────────────────────────────────────────────────┘ │
├────────────────────────────────────────────────────────┤
│ PANEL INPUT BICARA                                     │
│ ┌────────────────────────────────────────────────────┐ │
│ │              [ 🎙️ TEKAN UNTUK BICARA ]             │ │
│ │             (Sedang mendengarkan...)               │ │
│ └────────────────────────────────────────────────────┘ │
│ [⌨️ Beralih ke Teks]                [💾 Simpan Sesi (1/3)]│
└────────────────────────────────────────────────────────┘
```

### 7.1.b Layout Halaman Koleksi Terpadu (Mobile Viewport)

```text
┌────────────────────────────────────────────────────────┐
│ [← Kembali]          Koleksi Tersimpan                 │
├────────────────────────────────────────────────────────┤
│   [ ⭐ Idiom (12/20) ]     │    [ 📁 Percakapan (1/3) ] │
│ ══════════════════════════ │ ────────────────────────── │
├────────────────────────────────────────────────────────┤
│ ┌────────────────────────────────────────────────────┐ │
│ │ عَامِل إِيه؟                                  [🇪🇬] │ │
│ │ Bagaimana kabarmu? (Dialek Mesir)                  │ │
│ │ [🔊 Putar]        [📋 Salin]          [🗑️ Hapus]   │ │
│ └────────────────────────────────────────────────────┘ │
│ ┌────────────────────────────────────────────────────┐ │
│ │ كُلُّه تَمَام                                 [🇪🇬] │ │
│ │ Semua lancar / baik-baik saja                      │ │
│ │ [🔊 Putar]        [📋 Salin]          [🗑️ Hapus]   │ │
│ └────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────┘
```

### 7.2 Design Tokens & Visual Hierarchy

| Komponen UI | Nilai / Token Design System | Deskripsi Visual & Interaksi |
| :--- | :--- | :--- |
| **TopBar Title + Dialect Dropdown (Rata Kiri)** | Judul: Fredoka Bold `18px`, Dropdown pill: `#EFF6FF` (Bg), `#2563EB` (Teks), Border `#BFDBFE` | Menampilkan "Dhou AI" bersanding dengan Dropdown dialek di sudut kiri atas (Mobile & Desktop) |
| **TopBar Global Stats & Bookmark (Rata Kanan)** | Stats pills: `🔥` (Streak), `⚡` (Energi), `⭐` (XP) + Bookmark Icon `🔖` (`#4A5565`, Border `#E5E7EB`) | Menampilkan 3 pill status belajar seragam aplikasi bersanding dengan tombol bookmark koleksi |
| **Unified Collection Tabs** | Active: Border Bottom `3px solid #FF8904`, Teks `#FF8904` | Tab navigasi terpadu untuk beralih antara Idiom Tersimpan dan Percakapan Tersimpan |
| **AI Bubble Card** | Bg: `#FFFFFF`, Border: `1.5px solid #F3F4F6`, Shadow halus | Aligned Left, padding `14px`, Radius `16px 16px 16px 4px` |
| **User Bubble Card** | Bg: `#FFF7ED`, Border: `1.5px solid #FFD6A8` | Aligned Right, padding `14px`, Radius `16px 16px 4px 16px` |
| **Main Mic Button** | Bg: `#FF8904`, Border bottom: `4px solid #D67000`, Radius `9999px` | Lingkaran besar $\ge 64\text{px}$, ikon mikrofon putih, efek tekan 3D |
| **Audio Replay Button (🔊)**| Bg: `#F3F4F6`, Teks: `#4A5565`, Icon Soundwave | Tombol kecil rounded `8px`, memutar audio rekaman |
| **Copy Button (📋 Salin)** | Bg: `#F9FAFB`, Border: `1px solid #E5E7EB`, Teks: `#4A5565` | Hadir di pesan AI & User; klik menyalin teks Arab $\rightarrow$ feedback visual *"Tersalin!"* (Emerald `#059669`) selama 2 detik + toast |
| **Save Idiom Button (🔖)** | Default: `#F3F4F6` (Abu, border `#E5E7EB`). Aktif: `#FFF7ED` (Amber Tint, border `1.5px solid #FF8904`, teks `#D67000`) | Selalu muncul di seluruh pesan AI; transisi ke *"Tersimpan ✓"* saat aktif |
| **Conversation Quota (1/3)**| Bg: `#FEF3C7`, Teks: `#B45309`, Font: Fredoka Bold | Menampilkan kuota simpan percakapan utuh di Tab Percakapan |
| **Idiom Quota Badge (⭐ /20)**| Bg: `#FEF3C7`, Teks: `#B45309`, Border: `#FDE68A`, Font: Fredoka Bold | Menampilkan kuota `⭐ {jumlah}/20` di Tab Idiom, banner, dan desktop side panel |

---

## 8. Business Rules Summary Matrix

| Rule ID | Domain | Aturan Bisnis | Konsekuensi Sistem |
| :--- | :--- | :--- | :--- |
| **RL-SPK-01** | Pewarisan Dialek | Chat Room wajib menggunakan dialek dari unit terakhir yang dibuka di menu *Path*. | Mencegah kebingungan pengguna karena dialek yang tidak sinkron antar-modul. |
| **RL-SPK-02** | Kuota Percakapan | Maksimal **3 sesi percakapan tersimpan** per akun. | Mutasi simpan percakapan ke-4 ditolak dengan pesan error kuota penuh dan modal opsi hapus salah satu. |
| **RL-SPK-03** | Kuota Idiom | Maksimal **20 idiom/ungkapan tersimpan** per akun. | Mutasi simpan ke-21 ditolak dengan toast error: *"⚠️ Kuota idiom penuh (maksimal 20 idiom tersimpan)"*. Notifikasi kuota `⭐ {jumlah}/20` tampil di header modal, banner, dan panel desktop. |
| **RL-SPK-04** | Dukungan Audio | Setiap pesan dua arah (AI & User) wajib memiliki file audio rekaman yang dapat diputar ulang. | Pengguna dapat melatih kembali pengucapannya dan membandingkan dengan audio AI kapan saja. |
| **RL-SPK-05** | Deduplikasi Idiom | Menyimpan idiom yang identik dari obrolan yang sama tidak menduplikasi baris data di basis data. | Operasi idempoten (*upsert*). |
| **RL-SPK-06** | Aksi Cepat Bubble | Setiap pesan memiliki tombol salin teks Arab (📋 Salin), dan setiap pesan AI wajib memiliki tombol simpan (🔖 Simpan). | Teks Arab dapat disalin instan ke clipboard; seluruh pesan AI (termasuk salam Fusha) dapat disimpan dengan feedback amber Dhou *"Tersimpan ✓"*. |

---

## 9. Business Metrics & KPIs

| Metrik | Definisi | Target MVP | Cara Pengukuran |
| :--- | :--- | :--- | :--- |
| **Speaking Session Rate** | Persentase pengguna aktif harian yang melakukan minimal 1 sesi bicara di Dhou AI. | $\ge 40\%$ | Event `speaking_session_started / DAU` |
| **Average Turns per Session**| Rata-rata putaran percakapan lisan bolak-balik per sesi obrolan. | $4 - 8$ Putaran | Agregat jumlah `chat_messages` per session |
| **Idiom Bookmark Rate** | Rata-rata jumlah idiom yang disimpan pengguna per minggu. | $\ge 5$ Idiom | Query tabel `saved_idioms` per pengguna aktif |
| **Conversation Save Rate** | Persentase pengguna yang memanfaatkan slot penyimpanan percakapan ($\ge 1$ dari 3 slot). | $\ge 50\%$ | Query tabel `saved_conversations` |

---

## 10. Appendix & FRD4 Mapping

Dokumen PRD4 ini menjadi acuan definitif untuk spesifikasi teknis fungsional pada berkas:  
[`Requirements/FRD/FRD4 - Dhou Speaking, Conversational AI Chat Room, and Idiom Saver.md`](file:///Users/farizmirza/Documents/Project/dhou-docs/Requirements/FRD/FRD4%20-%20Dhou%20Speaking,%20Conversational%20AI%20Chat%20Room,%20and%20Idiom%20Saver.md).
