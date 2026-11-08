# Dhou: AI Design Instruction & Figma Workflow Guidelines

**Dokumen Acuan:** Standar Operasional Prosedur (SOP) bagi AI untuk memproses, merancang, menyusun komponen, dan menghasilkan antarmuka (UI/UX) di **Figma** (menggunakan Figma MCP, Figma REST API, atau Plugin).  
**Lokasi Berkas:** `DESIGN_INSTRUCTIONS.md` (Sejajar dengan `DEVELOPMENT_PLAN.md`)  
**Target Sistem:** Dhou (تطبيق ضوء) — Mobile Web (PWA) & iOS Native Wrapper (CapacitorJS)

---

## 1. Filosofi Utama & Aturan Kerja AI Designer

Saat AI diminta merancang, mengubah, atau menghasilkan desain di Figma, AI **tidak boleh bekerja secara acak atau mengandalkan asumsi semata**. Seluruh keputusan visual dan struktural harus berakar pada dokumen arsitektur produk yang sudah disetujui.

```text
PRD / FRD (Spesifikasi Bisnis & Teknis)
        │
        ▼
design-system.md (Design Tokens & Brand Identity)
        │
        ▼
Figma Canvas Execution (Frames, Components, Edge Cases, Prototyping)
```

---

## 2. 7 Pilar Wajib Pemrosesan Desain oleh AI

### Pilar 1: Penyelarasan Penuh dengan Dokumen Pendukung (PRD & FRD Traceability)
Sebelum membuat atau memodifikasi frame di Figma, AI wajib:
1. **Membaca Dokumen Terkait:**
   - Cek folder `Requirements/PRD/` untuk memahami *User Journey*, intensi pedagogis, hierarki konten, dan kriteria penerimaan (*Acceptance Criteria*).
   - Cek folder `Requirements/FRD/` untuk memastikan field data, status model (*state enum*), dan batas kuota/angka (misal: tepat 10 butir soal, 2 kata pengecoh, 3 level kecepatan) terakomodasi secara presisi.
2. **Tidak Melakukan Halusinasi Fitur:**
   - Dilarang menambahkan tombol, menu, atau fitur baru yang tidak tercantum pada PRD/FRD tanpa izin pengguna.
   - Semua teks microcopy, judul banner, dan pesan peringatan harus mencerminkan acuan dokumen pendukung.

---

### Pilar 2: Desain Menyeluruh hingga Seluruh Kasus Ekstrem (*Edge Cases Design*)
AI tidak boleh hanya merancang kondisi ideal (*happy path*). Untuk setiap fitur atau layar, AI **wajib merancang 4 variasi status (State Variations)**:

1. **Default / Success State (Happy Path):**
   - Tampilan visual saat data lengkap, media terputar lancar, dan pengguna menjawab benar.
2. **Empty State:**
   - Tampilan saat data kosong (misal: riwayat review baru 0 kata, filter video tidak menghasilkan hasil).
   - *Wajib:* Menyertakan ilustrasi ramah maskot Lentera Dhou dan tombol ajakan bertindak (*Call to Action / CTA*).
3. **Loading & Skeleton State:**
   - Visualisasi saat data/media sedang di-fetch dari server (kartu skeleton abu-abu `#E5E7EB` dengan border radius yang identik).
   - Spinner loading pada pemutar video saat terjadi *buffering*.
4. **Error & Failure States:**
   - *Network Timeout / Stalled:* Banner status koneksi terputus dengan tombol *"Coba Lagi"*.
   - *Permission Denied:* Bottom sheet informatif ketika izin mikrofon ditolak pada fitur Shadowing.
   - *Arabic Text Overflow:* Menjamin teks Arab berharakat panjang tidak terpotong (*no text clipping*), line height minimal `1.6–1.8`, dan harakat terbaca jelas.

---

### Pilar 3: Komponen Modular (General / Reusable vs. Spesifik)
Untuk menjaga kebersihan file Figma dan kemudahan implementasi kode:

1. **Prinsip Atomic & Reusability:**
   - **Komponen General (Sistem-wide):** Tombol 3D Dhou, Pill Badge Dialek (*Fusha*, *Mesir*, *Syam*), Input Field, Bottom Navigation, Modal Shell, dan Header Status TopBar (Api Streak 🔥, Baterai Energi ⚡).
   - **Komponen Spesifik (Fitur-specific):** `SentenceBuilderTray`, `AudioWaveformVisualizer`, `ShadowingRecordSheet`, `VideoSubtitleOverlay`.
2. **Aturan Promosi Komponen:**
   - Jika suatu elemen spesifik digunakan lebih dari 1 kali di fitur berbeda, AI wajib mengekstraknya menjadi *Master Component* ber-varian di Figma.
3. **Pemanfaatan Auto Layout & Constraints:**
   - Setiap komponen dan frame **wajib menggunakan Auto Layout Figma** (vertikal/horizontal, gap terukur, padding simetris) agar responsif dan mudah diterjemahkan ke Flexbox/Tailwind CSS.

---

### Pilar 4: Protokol Wajib Validasi Responsiveness (Desktop vs. Mobile)
Sebelum menghasilkan frame desain pertama, AI **wajib menanyakan atau mengonfirmasi kebutuhan resolusi kepada pengguna**:

> *"Apakah desain untuk modul [Nama Fitur] ini difokuskan untuk Mobile-first (PWA / iOS Capacitor 360px–430px) saja, atau memerlukan varian responsif untuk Tablet / Desktop?"*

* **Viewport Standar Dhou:**
  - **Mobile Default:** Lebar `402px` (setara iPhone 15/16 Pro Max & Android modern), fleksibel hingga `360px`.
  - **Tablet (jika diminta):** Lebar `768px` (iPad Mini / Portrait).
  - **Desktop (jika diminta):** Lebar `1280px` atau `1440px`.

---

### Pilar 5: Spesifikasi Interaksi & Prototype Figma
AI harus memastikan frame dapat dihubungkan menjadi prototype interaktif fungsional:

1. **Trigger & Transisi yang Digunakan:**
   - **On Tap / Click:** Navigasi antar-layar, pemilihan opsi kuis, toggle subtitle.
   - **While Pressing (Tombol 3D Dhou):** Mengubah `bottom border` dari `4px` ke `1px` dan pergeseran posisi visual `translate-y: 2px` untuk mensimulasikan tombol fisik ditekan.
   - **Open Overlay / Bottom Sheet:** Untuk modal kamus mini (*Word Tap*), rekaman shadowing, dan konfirmasi keluar.
2. **Tipe Animasi:**
   - Gunakan **Smart Animate** dengan kurva `Ease-in-and-out` durasi `200ms – 300ms` untuk pergerakan transkrip atau perpindahan node path.
   - Gunakan **Instant** untuk sakelar toggle atau pergantian status tombol submit.

---

### Pilar 6: Harmonisasi Visual dengan Style Target (`design-system.md`)
AI dilarang keras menciptakan warna atau gaya tipografi acak di luar token desain resmi Dhou:

| Token Visual | Nilai Baku | Penggunaan Wajib di Figma |
| :--- | :--- | :--- |
| **Primary Brand** | `#FF8904` | Warna tombol aksi utama, header unit, api streak aktif. |
| **3D Button Shadow**| `#D67000` / `#B91C1C` | Stroke tebal `4px` di sisi bawah tombol untuk efek kedalaman. |
| **Fusha Dialect** | `#10B981` (Emerald) | Pill badge dialek standar formal, aksen hijau sukses. |
| **Egyptian Dialect**| `#51A2FF` (Sky Blue) | Pill badge dialek Mesir, bubble percakapan AI. |
| **Levantine Dialect**| `#FF8904` (Warm Amber) | Pill badge dialek Syam. |
| **Error / Alert** | `#EF4444` (Red) | Indikator salah, tombol rekam, batas waktu habis. |
| **Typography Display**| `Fredoka` (Semibold) | Teks skor XP, angka streak, judul modal, tombol 3D. |
| **Typography Body** | `Poppins` / `Roboto` | Label instruksi, terjemahan Indonesia, deskripsi. |
| **Typography Arab** | `Amiri` / `Noto Sans Arabic` | Seluruh teks bahasa Arab, `dir="rtl"`, line-height `1.6–1.8`. |
| **Corner Radius** | `12px` (Cards/Buttons), `9999px` (Pills/Badges) | Menjaga konsistensi kelengkungan sudut di seluruh layar. |

---

### Pilar 7: Standarisasi Penamaan Frame (*Machine-Readable Naming Convention*)
Penamaan Frame, Section, dan Komponen di Figma harus **sinkron 1:1 dengan ID pada PRD dan FRD**. Tujuannya agar AI Developer (saat *vibe coding* atau eksekusi kode) dapat langsung memetakan artboard Figma ke struktur direktori kode Next.js/React.

#### Format Baku:
```text
[Feature-ID] / [Screen atau Component Name] / [State]
```

#### Contoh Penerapan Riil di Figma:

* **PRD1 — User Authentication, Sign In, and Password Recovery (AUTH):**
  - `[US-AUTH-01] Sign In / Default`
  - `[US-AUTH-01] Sign In / Validation Error`
  - `[US-AUTH-02] Forgot Password / Email Sent Modal`
  - `[US-AUTH-03] Register / Default`
  - `[US-AUTH-03] Register / Password Strength Indicator`

* **PRD2 — Gamified Learning Path, Sentence Arrangement, Energy, and Daily Streak (PATH):**
  - `[US-PATH-01] Path Canvas / Units Overview`
  - `[US-PATH-02] Sentence Builder / Question 1 (Initial)`
  - `[US-PATH-02] Sentence Builder / Token Selected (Active)`
  - `[US-PATH-03] Sentence Builder / Answer Feedback - Correct (Sheet)`
  - `[US-PATH-03] Sentence Builder / Answer Feedback - Wrong (Sheet)`
  - `[US-PATH-04] Energy Bar / Low Energy Warning`
  - `[US-PATH-05] Daily Streak / Streak Active`
  - `[US-PATH-05] Daily Streak / Streak Broken (Empty State)`
  - `[US-PATH-06] Lesson Summary / Celebration (Streak Ignited)`

* **PRD3 — Video Contextual Learning, Interactive Subtitles, and Shadowing (VID):**
  - `[US-VID-01] Video Feed / All Dialects`
  - `[US-VID-01] Video Feed / Filter Mesir (Empty State)`
  - `[US-VID-02] Video Player / Playing (Dual Subtitles)`
  - `[US-VID-02] Video Player / Buffering (Spinner)`
  - `[US-VID-03] Interactive Transcript / Sentence 2 Active`
  - `[US-VID-04] Shadowing / Sheet - Ready to Record`
  - `[US-VID-04] Shadowing / Sheet - Recording (Waveform Active)`
  - `[US-VID-04] Shadowing / Sheet - Compare (Dual Player)`
  - `[US-VID-05] Word Definition / Drawer - Bookmark Saved`

* **PRD4 — Dhou Speaking, Conversational AI Chat Room, and Idiom Saver (SPK):**
  - `[US-SPK-01] Chat Room / Default (Dialect Inherited)`
  - `[US-SPK-01] Chat Room / Empty State (No Messages Yet)`
  - `[US-SPK-02] Chat Room / Recording (Mic Active - Waveform)`
  - `[US-SPK-02] Chat Room / AI Responding (Loading Bubble)`
  - `[US-SPK-03] Chat Bubble / AI - Bilingual (AR + ID Visible)`
  - `[US-SPK-03] Chat Bubble / AI - Translation Hidden`
  - `[US-SPK-04] Idiom Saver / Bookmark Saved (Toast Confirmation)`
  - `[US-SPK-04] Saved Idioms / List - Default`
  - `[US-SPK-04] Saved Idioms / Empty State`
  - `[US-SPK-05] Save Conversation / Quota Full Modal (3/3)`
  - `[US-SPK-05] Saved Conversations / List - 3 Sessions`
  - `[US-SPK-06] Chat Room Header / Quick Access (Saved Icons)`

---

## 3. Standar Ekspresi Maskot Lentera Dhou (Visual Identity)

Untuk menghadirkan elemen gamifikasi yang hangat dan bersahabat ala Duolingo:

| Status Layar / Pengalaman | Ekspresi Visual Maskot Lentera Dhou | Deskripsi Penggunaan |
| :--- | :--- | :--- |
| **Sukses / Pencapaian** | Tersenyum ceria, mata berbinar, api keemasan berkobar | Layar *Lesson Summary*, saat streak bertambah (+1 🔥). |
| **Empty State** | Memegang lentera kecil/kaca pembesar dengan ekspresi ingin tahu | Daftar kosakata kosong, pencarian video tidak ditemukan. |
| **Streak Terancam** | Wajah cemas berkeringat, api lilin berkedip mengecil | Pengingat belajar malam hari jika belum ada sesi selesai. |
| **Penyemangat (Salah Jawaban)**| Tersenyum lembut dan mengacungkan jempol | Bottom sheet saat jawaban kuis salah (tidak boleh membuat frustrasi). |

---

## 4. Checklist Pra-Eksekusi AI Designer

Sebelum AI melaporkan bahwa pekerjaan desain di Figma telah tuntas, lakukan evaluasi mandiri (*self-check*):

- [ ] Apakah seluruh teks dan alur sudah sesuai dengan **PRD & FRD** modul terkait?
- [ ] Apakah frame mencakup varian **Edge Cases** (Empty, Error, Loading, Text Overflow)?
- [ ] Apakah kebutuhan **Desktop vs. Mobile** sudah dikonfirmasi ke pengguna?
- [ ] Apakah tombol, warna, dan font mematuhi token di **`design-system.md`**?
- [ ] Apakah seluruh frame dinamai dengan format baku `[Feature-ID] / [Screen] / [State]`?
- [ ] Apakah elemen interaktif sudah siap dihubungkan pada **Figma Prototype**?
