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
- **Peningkatan Retensi melalui Saved Idioms (Tak Terbatas)**: Pembelajar dapat menandai (*bookmark*) ungkapan atau idiom khas dari gelembung chat ke dalam koleksi idiom tersimpan tanpa batas untuk diulang kapan saja.
- **Kontrol Penyimpanan Percakapan Cerdas (Maksimal 3 Sesi)**: Pengguna dapat menyimpan maksimal 3 percakapan utuh favorit untuk diputar ulang, menjaga kebersihan ruang penyimpanan server sekaligus menjadi insentif retensi yang terukur.
- **Skalabilitas & Transparansi Dua Arah**: Setiap gelembung chat menyajikan teks Arab berharakat, audio pengucapan, dan terjemahan bahasa Indonesia, memberikan rasa aman bagi pembelajar pemula hingga menengah.

---

## 3. Purpose

Menyediakan modul **Dhou Speaking (Conversational AI Chat Room)** yang memungkinkan pengguna masuk langsung ke ruang obrolan suara interaktif dengan dialek yang otomatis tersinkronisasi dari progres menu *Path*. Pengguna cukup mengetuk tombol mikrofon/putar untuk langsung diajak berbicara oleh AI, melihat transkrip Arab berharakat dua arah disertai terjemahan Indonesia, memutar ulang audio rekaman per gelembung chat, menyimpan idiom/frasa penting tanpa batas (*unlimited saved idioms*), serta mengarsipkan hingga maksimal 3 percakapan utuh (*saved conversations*) dengan akses navigasi cepat dari ruang obrolan.

---

## 4. Objectives

1. **Auto-Inherited Dialect & Direct Room Entry**:
   - Menghilangkan *setup modal* di awal: mengetuk menu "Dhou AI" pada bottom navigation langsung membuka layar *Chat Room*.
   - Dialek ruang obrolan otomatis mewarisi dialek terakhir yang sedang aktif dipelajari di menu *Gamified Path* (misal: *'Ammiyyah Mesir* atau *Fusha*). Jika pengguna baru belum memiliki riwayat, sistem default ke *Fusha*.
2. **Instant Voice Initiation (Push/Tap-to-Speak)**:
   - Satu ketukan pada tombol mikrofon/play utama di panel bawah langsung mengaktifkan sesi: AI langsung membuka percakapan dengan sapaan suara ramah sesuai dialek, lalu mendengarkan respons pengguna.
3. **Bilingual Interactive Chat Bubbles**:
   - Gelembung pesan AI dan Pengguna menampilkan teks Arab berharakat (`dir="rtl"`, font *Amiri/Noto Sans Arabic*).
   - Terdapat tombol toggle/teks terjemahan bahasa Indonesia di setiap gelembung.
   - Di bawah setiap gelembung terdapat:
     - Ikon Speaker (🔊) untuk memutar ulang audio tuturan.
     - Tombol Simpan Idiom (🔖) untuk menyimpan frasa/idiom penting ke arsip.
4. **Manajemen Arsip Kuota Cerdas**:
   - **Saved Idioms**: Pengguna dapat menyimpan idiom/frasa tanpa batas (*unlimited*).
   - **Saved Conversations**: Pengguna dapat menyimpan maksimal **3 sesi percakapan utuh** ke arsip cloud. Jika kuota penuh (3/3), sistem menampilkan modal pilihan untuk menghapus percakapan lama sebelum menyimpan yang baru.
5. **Akses Cepat ke Halaman Koleksi**:
   - Di bagian atas *Chat Room* (Header/Menu) tersedia tombol navigasi langsung menuju:
     - 📁 **Percakapan Tersimpan (*Saved Conversations*)**
     - 💡 **Idiom Tersimpan (*Saved Idioms*)**

---

## 5. Scope

### In Scope
- **Direct Chat Room Landing (SPK-01)**: Masuk langsung ke ruang obrolan saat tab navigasi bawah ditekan, dengan status dialek otomatis terikat ke progres *Path*.
- **Instant Audio Conversation Engine (SPK-02)**: Tombol rekam mikrofon / pemutar suara yang langsung memicu interaksi lisan dua arah dengan AI Dhou.
- **Bilingual Transcript & Audio Replay (SPK-03)**: Gelembung chat menampilkan transkrip Arab berharakat, terjemahan Indonesia, dan ikon speaker (🔊) untuk mendengarkan ulang audio kapan saja.
- **Penyimpanan Idiom Bebas Batas (SPK-04)**: Tombol simpan idiom (🔖) di bawah setiap bubble chat yang langsung menambahkan frasa ke daftar *Saved Idioms*.
- **Penyimpanan Percakapan Kuota 3 Sesi (SPK-05)**: Fitur simpan sesi obrolan aktif dengan batas maksimum 3 sesi percakapan per akun, disertai validasi kuota penuh.
- **Navigasi Layar Koleksi (SPK-06)**: Drawer/halaman arsip untuk melihat daftar percakapan tersimpan dan daftar idiom tersimpan.

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
| **Title** | Direct Chat Room Landing with Auto-Inherited Dialect |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saat saya mengetuk ikon menu Dhou AI di bottom navigation, saya ingin langsung masuk ke ruang obrolan dengan dialek yang sama persis seperti materi terakhir yang saya pelajari di menu Path agar saya tidak membuang waktu mengatur ulang bahasa. |

#### User Journey
1. Pengguna sedang mempelajari unit dialek Mesir di menu *Learn (Path)*.
2. Pengguna mengetuk tab **"Dhou AI"** pada bilah navigasi bawah (*Bottom Navigation Bar*).
3. Aplikasi seketika membuka layar **Chat Room**.
4. Di bagian header atas, terlihat Avatar Lentera Dhou dengan status *"Online"* dan lencana dialek: `[Mesir 🇪🇬]`.
5. Pengguna siap memulai obrolan tanpa harus melalui jendela pop-up pemilihan dialek.

#### Acceptance Criteria
- **AC-SPK-01-A (Zero Setup Flow):** Mengetuk tab Dhou AI tidak menampilkan dialog atau form persiapan; layar Chat Room langsung dirender dalam waktu $<300\text{ ms}$.
- **AC-SPK-01-B (Dialect Inheritance):** Sistem membaca `user_preferences.last_active_dialect`. Jika sesi terakhir di menu *Path* adalah `egyptian`, AI di ruang obrolan otomatis berbicara dalam dialek Mesir.
- **AC-SPK-01-C (Fallback Default):** Jika pengguna baru pertama kali menggunakan aplikasi dan belum pernah membuka menu Path, sistem otomatis menetapkan dialek `fusha` (Standar).
- **AC-SPK-01-D (Manual Switcher):** Di header tetap disediakan tombol/dropdown kecil untuk mengganti dialek secara manual jika pengguna ingin berlatih dialek lain tanpa harus kembali ke Path.

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

### US-SPK-03: Gelembung Chat Dwibahasa & Putar Ulang Audio

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-03 |
| **Feature ID** | SPK-03 |
| **Title** | Bilingual Chat Bubbles with Audio Replay |
| **Priority** | Critical (P0) |
| **Description** | Sebagai pembelajar, saya ingin setiap kalimat percakapan tertranskripsi jelas dalam bahasa Arab berharakat lengkap dengan opsi terjemahan Indonesia dan tombol speaker agar saya bisa membaca dan mendengarkan ulang tuturan tersebut. |

#### User Journey
1. Sambil mendengarkan AI berbicara, gelembung chat AI muncul di sisi kiri layar.
2. Teks Arab tampil dengan ukuran font besar dan berharakat lengkap (`dir="rtl"`).
3. Di bawah teks Arab, terdapat teks terjemahan bahasa Indonesia berwarna abu-abu ramah.
4. Di baris bawah gelembung pesan, terdapat ikon Speaker (🔊) dan tombol Simpan Idiom (🔖).
5. Pengguna mengetuk ikon Speaker (🔊): audio kalimat tersebut diputar ulang secara jernih tanpa mengganggu posisi obrolan.

#### Acceptance Criteria
- **AC-SPK-03-A (Arabic Typography Standards):** Teks Arab menggunakan font *Amiri* atau *Noto Sans Arabic* berukuran minimal `18px–20px` dengan line-height `1.8` agar harakat jelas terbaca.
- **AC-SPK-03-B (Independent Audio Replay):** Mengetuk ikon speaker (🔊) pada gelembung manapun memutar ulang audio rekaman asli dari pesan tersebut. Jika audio pesan lain sedang berputar, audio sebelumnya otomatis dijeda (*stop & switch*).
- **AC-SPK-03-C (Translation Visibility):** Terjemahan bahasa Indonesia ditampilkan secara default di bawah teks Arab, atau dapat disembunyikan lewat toggle global di header bagi pembelajar tingkat lanjut yang ingin melatih kemampuan menyimak murni.

---

### US-SPK-04: Simpan Idiom / Ungkapan Penting (Unlimited Quota)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-04 |
| **Feature ID** | SPK-04 |
| **Title** | Instant Idiom Saving with Unlimited Storage |
| **Priority** | High (P1) |
| **Description** | Sebagai pembelajar, saya ingin menekan tombol simpan di bawah gelembung pesan untuk menyimpan idiom atau ungkapan khas tersebut ke daftar koleksi idiom saya tanpa batas kuota agar saya bisa menghafalnya kelak. |

#### User Journey
1. Pengguna menemukan ungkapan menarik pada ucapan AI (misal: *"على عيني وراسي"* – *'Ala 'aini wa raasi*).
2. Pengguna mengetuk tombol **🔖 Simpan Idiom** di bawah gelembung chat tersebut.
3. Tombol langsung berubah warna menjadi hijau dengan ikon centang: **"Tersimpan ✓"** dan disertai getaran haptic ringan.
4. Ungkapan tersebut tersimpan permanen ke basis data koleksi *Saved Idioms* pengguna.

#### Acceptance Criteria
- **AC-SPK-04-A (Unlimited Quota):** Tidak ada batasan jumlah idiom yang dapat disimpan pengguna (*unlimited storage*).
- **AC-SPK-04-B (Metadata Retention):** Data idiom yang disimpan wajib menyertakan: teks Arab berharakat, terjemahan Indonesia, audio snippet URL, asal dialek, dan konteks percakapan.
- **AC-SPK-04-C (Toggle Un-save):** Mengetuk kembali tombol idiom yang berstatus tersimpan akan membatalkan simpanan (*un-save*) setelah konfirmasi singkat.

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

### US-SPK-06: Navigasi Layar Arsip (Saved Conversations & Saved Idioms)

| Attribute | Specification |
| :--- | :--- |
| **User Story ID** | US-SPK-06 |
| **Feature ID** | SPK-06 |
| **Title** | Direct Navigation to Saved Conversations and Saved Idioms |
| **Priority** | Medium (P2) |
| **Description** | Sebagai pembelajar, saya ingin dapat membuka daftar percakapan tersimpan dan daftar idiom tersimpan langsung dari header chat room agar saya dapat membaca dan memutar kembali koleksi saya dengan mudah. |

#### User Journey
1. Di bilah navigasi header atas Chat Room, terdapat dua ikon akses cepat:
   - Tombol Arsip Percakapan: 📁 **(3/3)**
   - Tombol Arsip Idiom: 💡
2. Pengguna mengetuk tombol 📁 **Percakapan**:
   - Terbuka layar/drawer *Saved Conversations* yang menampilkan hingga 3 kartu percakapan.
   - Pengguna dapat mengetuk salah satu kartu untuk membuka dan memutar ulang riwayat obrolan tersebut dalam mode *Read-Only*.
3. Pengguna mengetuk tombol 💡 **Idiom**:
   - Terbuka daftar *Saved Idioms* vertikal. Setiap kartu idiom memiliki tombol speaker 🔊 untuk memutar suara dan informasi asal dialeknya.

---

## 7. User Interface & Wireframe Specs

### 7.1 Layout Chat Room Utama (Mobile Viewport: 360px–430px)

```text
┌────────────────────────────────────────────────────────┐
│ [📁 Percakapan (1/3)]    Dhou AI (Mesir 🇪🇬)   [💡 Idiom]│
├────────────────────────────────────────────────────────┤
│                    Hari Ini, 10:15                     │
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 🤖 Dhou AI                                         │ │
│ │ أَهْلاً وَسَهْلاً يَا صَدِيقِي! كَيْفَ حَالُكَ اليَوْم؟    │ │
│ │ Halo sahabatku! Bagaimana kabarmu hari ini?       │ │
│ │ ────────────────────────────────────────────────── │ │
│ │ [🔊 Putar Suara]                [🔖 Simpan Idiom]  │ │
│ └────────────────────────────────────────────────────┘ │
│                                                        │
│                           ┌──────────────────────────┐ │
│                           │ 👤 Kamu                  │ │
│                           │ أَنَا بِخَيْرٍ الحَمْدُ لِلَّه!   │ │
│                           │ Saya baik, alhamdulillah!│ │
│                           │ ──────────────────────── │ │
│                           │ [🔊 Putar]   [🔖 Simpan] │ │
│                           └──────────────────────────┘ │
│                                                        │
│ ┌────────────────────────────────────────────────────┐ │
│ │ 🤖 Dhou AI                                         │ │
│ │ عَامِل إِيه فِي دِرَاسَتَك؟ كُلُّه تَمَام؟            │ │
│ │ Bagaimana belajarmu? Semua lancar?                 │ │
│ │ ────────────────────────────────────────────────── │ │
│ │ [🔊 Putar Suara]                [🔖 Simpan Idiom]  │ │
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

### 7.2 Design Tokens & Visual Hierarchy

| Komponen UI | Nilai / Token Design System | Deskripsi Visual & Interaksi |
| :--- | :--- | :--- |
| **TopBar Dialect Badge** | `#EFF6FF` (Bg), `#2563EB` (Teks Mesir), Border `#BFDBFE` | Pill rounded `9999px`, menampilkan bendera & dialek aktif |
| **AI Bubble Card** | Bg: `#FFFFFF`, Border: `1.5px solid #F3F4F6`, Shadow halus | Aligned Left, padding `14px`, Radius `16px 16px 16px 4px` |
| **User Bubble Card** | Bg: `#FFF7ED`, Border: `1.5px solid #FFD6A8` | Aligned Right, padding `14px`, Radius `16px 16px 4px 16px` |
| **Main Mic Button** | Bg: `#FF8904`, Border bottom: `4px solid #D67000`, Radius `9999px` | Lingkaran besar $\ge 64\text{px}$, ikon mikrofon putih, efek tekan 3D |
| **Audio Replay Button (🔊)**| Bg: `#F3F4F6`, Teks: `#4A5565`, Icon Soundwave | Tombol kecil rounded `8px`, memutar audio rekaman |
| **Save Idiom Button (🔖)** | Default: `#F3F4F6` (Abu). Aktif: `#DCFCE7` (Hijau `#16A34A`) | Teks: *"Simpan Idiom"* $\rightarrow$ *"Tersimpan ✓"* |
| **Quota Badge (1/3)** | Bg: `#FEF3C7`, Teks: `#B45309`, Font: Fredoka Bold | Menampilkan sisa kuota penyimpanan percakapan |

---

## 8. Business Rules Summary Matrix

| Rule ID | Domain | Aturan Bisnis | Konsekuensi Sistem |
| :--- | :--- | :--- | :--- |
| **RL-SPK-01** | Pewarisan Dialek | Chat Room wajib menggunakan dialek dari unit terakhir yang dibuka di menu *Path*. | Mencegah kebingungan pengguna karena dialek yang tidak sinkron antar-modul. |
| **RL-SPK-02** | Kuota Percakapan | Maksimal **3 sesi percakapan tersimpan** per akun. | Mutasi simpan percakapan ke-4 ditolak dengan pesan error kuota penuh dan modal opsi hapus salah satu. |
| **RL-SPK-03** | Kuota Idiom | Penyimpanan idiom/frasa bersifat **tidak terbatas (*unlimited*)**. | Sistem mengizinkan bookmark idiom sebanyak-banyaknya tanpa memotong kuota percakapan. |
| **RL-SPK-04** | Dukungan Audio | Setiap pesan dua arah (AI & User) wajib memiliki file audio rekaman yang dapat diputar ulang. | Pengguna dapat melatih kembali pengucapannya dan membandingkan dengan audio AI kapan saja. |
| **RL-SPK-05** | Deduplikasi Idiom | Menyimpan idiom yang identik dari obrolan yang sama tidak menduplikasi baris data di basis data. | Operasi idempoten (*upsert*). |

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
