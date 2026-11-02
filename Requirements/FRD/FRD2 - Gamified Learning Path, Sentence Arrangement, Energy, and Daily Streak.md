# Functional Requirements Document (FRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | FRD2 |
| **Source PRD** | [PRD2 — Gamified Learning Path, Sentence Arrangement, Energy Engine, and Daily Streak, Version 1.0, Approved](../PRD/PRD2%20-%20Gamified%20Learning%20Path,%20Sentence%20Arrangement,%20Energy,%20and%20Daily%20Streak.md) |
| **Feature ID** | PATH-L-01, PATH-L-02, PATH-L-03, PATH-L-04, PATH-L-05, PATH-L-06 |
| **Application** | Dhou (تطبيق ضوء) — Mobile Web (PWA) & iOS Native Wrapper (CapacitorJS) |
| **Module** | Gamified Learning Path & Progression Core |
| **Sprint** | Sprint 2 |
| **Status** | Approved — disetujui sebagai kontrak implementasi |
| **Approved On** | 2026-11-02 |
| **Implementation Status** | Specification only — implementasi belum diverifikasi |
| **Version** | 1.0 |
| **Last Updated** | 2026-11-02 |
| **Owner** | Product / Lead Engineering Dhou |
| **Target Boundary Version** | `services/learning-api` v1.0.0, `apps/dhou-mobile` v1.0.0 |
| **Dependencies** | PRD2, FRD1 (Authentication), Supabase PostgreSQL 15+, CapacitorJS Core, Howler.js / Web Audio API |

---

## 1. Purpose, Sources, and Authority

Dokumen ini menerjemahkan seluruh kebutuhan bisnis dan pengalaman pengguna dari [PRD2 — Gamified Learning Path, Sentence Arrangement, Energy Engine, and Daily Streak](../PRD/PRD2%20-%20Gamified%20Learning%20Path,%20Sentence%20Arrangement,%20Energy,%20and%20Daily%20Streak.md) menjadi spesifikasi teknis definitif, kontrak antarmuka API, skema persistensi basis data relasional PostgreSQL, algoritma state machine, validasi Zod, dan matriks penanganan kasus ekstrem (*edge cases*) bagi tim Frontend dan Backend.

PRD2 memegang otoritas atas proses bisnis, aturan gamifikasi, dan intensi pedagogis. FRD2 ini memegang otoritas atas arsitektur teknis, kontrak API, skema DDL, dan kriteria penerimaan implementasi kode (*code implementation verification*).

| Sumber | Baseline yang Digunakan | Peruntukan Teknis |
| :--- | :--- | :--- |
| [PRD2](../PRD/PRD2%20-%20Gamified%20Learning%20Path,%20Sentence%20Arrangement,%20Energy,%20and%20Daily%20Streak.md) | Versi 1.0, Approved | US-PATH-01 s/d US-PATH-06, Business Rules RL-EX-01..05, RL-EQ-01..04, RL-EN-01..04, RL-ST-01..04, RL-PR-01..03 |
| [design-system.md](../../design-system.md) | Versi 1.0, Approved | Token warna (`#FF8904`, `#51A2FF`, `#22C55E`, `#EF4444`), tipografi (Fredoka & Amiri), tombol 3D Dhou |
| [vibe-coding-guidelines.md](../../vibe-coding-guidelines.md) | Versi 1.0, Approved | Batasan ukuran komponen, penanganan ortografi Arab RTL, clean architecture, debouncing |
| [RESEARCH_REFERENCE_MAPPING.md](../../RESEARCH_REFERENCE_MAPPING.md) | Versi 1.0, Master Reference | Aktivitas Tipe 4: *Sentence arrangement* (rekonstruksi sintaksis), siklus 4 pilar pedagogis |

---

## 2. Scope and Source Reconciliation

### 2.1 Rekonsiliasi Ruang Lingkup Teknis
1. **Peta Belajar & Navigasi Node (FR-PATH-01)**: Hierarki `learning_units` $\rightarrow$ `learning_nodes` dengan rendering zig-zag adaptif mobile, pemusatan scroll viewport otomatis (*auto-scroll*), dan penegakan status node (`LOCKED`, `CURRENT`, `COMPLETED`).
2. **Kuis Susun Kata Arab (FR-PATH-02)**: Setiap lesson memuat tepat **10 butir soal** (*single exercise variant* di MVP). Setiap soal menyediakan baki balok kata acak yang berisi kata-kata kalimat benar ditambah **tepat 2 kata pengecoh (*distractors*)**, dukungan RTL penuh, dan audio player.
3. **Mekanisme Antrean Error Re-queue (FR-PATH-03)**: Soal yang dijawab salah tidak menggugurkan sesi, melainkan dipindahkan ke ekor antrean (`active_queue.push(failedQuestion)`) dan diacak ulang susunannya saat dipanggil kembali di akhir sesi. Sesi hanya selesai jika antrean kosong.
4. **Sistem Kuota Energi Cerdas (FR-PATH-04)**: Arsitektur database menyimpan struktur komersial (Free Plan 5 energi, refill 1/4 jam, langganan Premium tak terbatas), namun di fase MVP diproteksi oleh *feature flag* `ENV_MVP_UNLIMITED_ENERGY = true` sehingga jawaban salah **tidak memotong kuota energi**.
5. **Mesin Api Streak Harian (FR-PATH-05)**: Kalkulasi berbasis zona waktu lokal pengguna (`user_timezone`). Minimal menyelesaikan **1 sesi lesson lengkap (10 butir soal tuntas)** untuk menyalakan api streak (🔥). Maksimal kenaikan +1 per hari kalender.
6. **Layar Rangkuman & Pembukaan Node Baru (FR-PATH-06)**: Eksekusi transaksi database atomik (*Atomic DB Transaction*) pasca-soal terakhir: penambahan XP (+10 materi baru, +5 pengulangan), kalkulasi akurasi percobaan pertama, pembaruan streak, dan pembukaan kunci node berikutnya ($N+1$).

### 2.2 Traceability Matrix (PRD2 $\rightarrow$ FRD2)

| PRD2 Source Trace | FRD ID | Operation ID | Frontend Integration Target | Test Scenario ID |
| :--- | :--- | :--- | :--- | :--- |
| US-PATH-01; AC-PATH-01-A..D; RL-PR-01 | **FR-PATH-01** | `path_get_overview` | `PathTreeCanvas`, `NodeButton`, `UnitBanner` | T-PATH-01, 02, 03 |
| US-PATH-02; AC-PATH-02-A..E; RL-EX-01..05 | **FR-PATH-02** | `lesson_start`, `lesson_submit_answer` | `SentenceBuilderZone`, `WordBankTray`, `WordTile` | T-PATH-04, 05, 06 |
| US-PATH-03; AC-PATH-03-A..D; RL-EQ-01..04 | **FR-PATH-03** | `lesson_requeue_error` | `QuizQueueManager`, `FeedbackBottomSheet` | T-PATH-07, 08 |
| US-PATH-04; AC-PATH-04-A..D; RL-EN-01..04 | **FR-PATH-04** | `energy_check_policy`, `energy_consume` | `EnergyTopBarPill`, `EnergyInfoModal` | T-PATH-09, 10 |
| US-PATH-05; AC-PATH-05-A..E; RL-ST-01..04 | **FR-PATH-05** | `streak_evaluate_daily` | `StreakTopBarPill`, `FlameCelebrationModal` | T-PATH-11, 12, 13 |
| US-PATH-06; AC-PATH-06-A..C; RL-PR-02..03 | **FR-PATH-06** | `lesson_finish`, `node_unlock_next` | `LessonSummaryModal`, `XPStatBox`, `NextUnlockFX` | T-PATH-14, 15 |

---

## 3. Actors and Authorization

| Aktor | Hak Akses Baca (*Read*) | Hak Akses Mutasi (*Write*) | Batasan Keamanan (*Scope & Security*) |
| :--- | :--- | :--- | :--- |
| **Authenticated Student** | Peta kurikulum, daftar node, antrean soal kuis miliknya, status streak & energi profil | Inisialisasi sesi lesson, submit jawaban, menyelesaikan sesi, update timezone profil | Header `Authorization: Bearer <JWT>` wajib ada. RLS Supabase memastikan user hanya dapat memutasi `lesson_sessions` miliknya sendiri. |
| **Dhou System / Worker** | Seluruh data agregat pengguna | Refill kuota energi otomatis (post-MVP), pembersihan sesi *abandoned* $>24$ jam | Operasi *service_role* aman di backend. |

---

## 4. Functional Requirements

### 4.1 FR-PATH-01 — Navigasi Visual Path & Node Belajar

| ID | Kebutuhan Fungsional | Sumber PRD2 |
| :--- | :--- | :--- |
| **FR-PATH-01.1** | Endpoint `GET /api/v1/path/overview` wajib mengembalikan hierarki unit, daftar node per unit dengan status relasional pengguna (`LOCKED`, `CURRENT`, atau `COMPLETED`), serta status agregat streak dan energi. | AC-PATH-01-A; AC-PATH-01-B |
| **FR-PATH-01.2** | Algoritma penentuan status node: Node ke-1 berstatus `CURRENT` (jika user baru) atau `COMPLETED` (jika sudah tuntas). Node $N+1$ berstatus `CURRENT` jika dan hanya jika Node $N$ berstatus `COMPLETED`. Seluruh node setelah node `CURRENT` wajib berstatus `LOCKED`. | AC-PATH-01-B; RL-PR-01 |
| **FR-PATH-01.3** | Frontend merender node pada kanvas SVG zig-zag 5-jalur (*horizontal offsets*: 0%, -20%, 0%, +20%, 0%) yang proporsional pada lebar kontainer mobile 360px–430px. | UI 8.1; AC-PATH-01-A |
| **FR-PATH-01.4** | Mengklik node `LOCKED` memicu getaran haptic error (via `@capacitor/haptics`), animasi visual goyang (*CSS shake 300ms*), dan toast informasi: *"Selesaikan pelajaran sebelumnya terlebih dahulu!"*. Mutasi API ke sesi kuis dilarang keras di client dan server (`403 Forbidden`). | AC-PATH-01-C; BR-PATH-01 |
| **FR-PATH-01.5** | Saat viewport halaman Path dimuat, komponen `PathView` wajib memanggil `scrollIntoView({ behavior: 'smooth', block: 'center' })` ke elemen DOM node yang memiliki status `CURRENT`. | AC-PATH-01-D |

---

### 4.2 FR-PATH-02 — Latihan Interaktif Susun Kata Arab (Sentence Arrangement)

| ID | Kebutuhan Fungsional | Sumber PRD2 |
| :--- | :--- | :--- |
| **FR-PATH-02.1** | Endpoint `POST /api/v1/lessons/:nodeId/start` wajib memuat tepat **10 butir soal** berjenis `SENTENCE_ARRANGEMENT` dari tabel `exercises`. Jika jumlah soal aktif pada node $<10$, API wajib melempar error `500 Internal Server Error (ERR_INCOMPLETE_NODE_CONTENT)`. | RL-EX-01; AC-PATH-02-A |
| **FR-PATH-02.2** | Pada setiap soal, sistem wajib menggabungkan array `correct_tokens` dan array `distractor_tokens` (berisi **tepat 2 kata pengecoh**), lalu mengacak susunan posisinya menggunakan algoritma Fisher-Yates Shuffle sebelum disajikan di *Word Bank Tray*. | RL-EX-03; AC-PATH-02-B |
| **FR-PATH-02.3** | Area Jawaban (*Answer Drop Zone*) dan Balok Kata wajib menggunakan atribut `dir="rtl"` dengan font Arab berharakat lengkap (Amiri/Noto Sans Arabic) dan *line-height* $\ge 1.7$ untuk mencegah keterpotongan tanda harakat. | RL-EX-05; AC-PATH-02-C |
| **FR-PATH-02.4** | Interaksi ketuk dua arah (*Bidirectional Tap Mechanism*):<br>1. Mengetuk balok di *Word Bank Tray* memindahkannya ke posisi paling akhir urutan RTL di *Answer Drop Zone*. Balok asal di tray berubah menjadi *Ghost Slot* transparan.<br>2. Mengetuk balok di *Answer Drop Zone* mengembalikannya ke posisi slot asalnya di *Word Bank Tray* dengan animasi transisi $<150\text{ ms}$. | AC-PATH-02-D |
| **FR-PATH-02.5** | Tombol "Periksa" wajib berstatus `disabled` selama jumlah balok di *Answer Drop Zone* adalah $0$. | AC-PATH-02-E |
| **FR-PATH-02.6** | Komponen audio wajib memfasilitasi pemutaran file pengucapan penutur asli via URL `audio_url` menggunakan HTML5 Audio / Howler.js dengan latensi mulai putar $<300\text{ ms}$. | UI 8.2; AC-PATH-02 |

---

### 4.3 FR-PATH-03 — Mesin Antrean & Pengulangan Soal Salah (Error Re-queuing)

| ID | Kebutuhan Fungsional | Sumber PRD2 |
| :--- | :--- | :--- |
| **FR-PATH-03.1** | Endpoint `POST /api/v1/lessons/session/:sessionId/submit` memvalidasi susunan kata: array string `user_tokens` dibandingkan secara identik (*exact array match*) dengan salah satu variasi array kata sah pada kolom `accepted_permutations`. | BR-PATH-02; AC-PATH-03 |
| **FR-PATH-03.2** | Jika jawaban dinyatakan **BENAR**: Soal dihapus dari antrean aktif (`active_queue.shift()`). Respons mengembalikan feedback status `CORRECT`, audio feedback sukses, dan sisa jumlah soal dalam antrean. | AC-PATH-03 |
| **FR-PATH-03.3** | Jika jawaban dinyatakan **SALAH**: Soal saat ini dipindahkan ke ekor antrean (`active_queue.push(active_queue.shift())`). Nilai `mistake_count` sesi bertambah +1. Respons mengembalikan status `INCORRECT`, kunci jawaban benar, teks transliterasi latin, dan audio koreksi. | RL-EQ-01; AC-PATH-03-A |
| **FR-PATH-03.4** | Saat butir soal yang di-requeue tiba kembali gilirannya di layar, frontend wajib mengacak ulang (*re-shuffle*) tata letak balok katanya di baki untuk mencegah penghafalan posisi visual semata. | RL-EQ-02; AC-PATH-03-B |
| **FR-PATH-03.5** | Sesi kuis HANYA DAPAT diselesaikan jika panjang array `active_queue` mencapai $0$. Endpoint penyelesaian sesi menolak request jika masih ada soal yang tersisa di antrean (`400 Bad Request: ERR_UNFINISHED_EXERCISES`). | RL-EQ-03; AC-PATH-03-C |
| **FR-PATH-03.6** | Nilai akurasi sesi (*first-attempt accuracy*) dihitung dengan rumus: $\text{Accuracy} = \operatorname{ROUND}\left(\frac{10}{10 + \text{mistake\_count}} \times 100\right)\%$. | RL-EQ-04; AC-PATH-03-D |

---

### 4.4 FR-PATH-04 — Sistem Kuota Energi Cerdas & Kebijakan Promo MVP

| ID | Kebutuhan Fungsional | Sumber PRD2 |
| :--- | :--- | :--- |
| **FR-PATH-04.1** | Tabel `user_energy` wajib memelihara kolom: `user_id`, `current_energy` (INT, default 5), `max_energy` (INT, default 5), `is_unlimited` (BOOLEAN), dan `last_refill_at` (TIMESTAMPTZ). | AC-PATH-04-A |
| **FR-PATH-04.2** | Sistem mengimplementasikan Feature Flag `ENV_MVP_UNLIMITED_ENERGY = true`. Selama flag bernilai `true`: Seluruh pengguna (termasuk Free Plan) diperlakukan sebagai pengguna energi tak terbatas. | RL-EN-01; AC-PATH-04-B |
| **FR-PATH-04.3** | Selama mode MVP aktif, kesalahan submit jawaban pada kuis **tidak boleh memotong nilai `current_energy`**. Endpoint `lesson_submit_answer` tidak melakukan dekremen kuota energi. | RL-EN-02; AC-PATH-04-B |
| **FR-PATH-04.4** | Komponen UI Top Bar wajib merender badge energi dengan simbol tak hingga `⚡ ∞` dengan warna hijau cerah (`#22C55E`). Mengetuk badge membuka modal informasi promo MVP. | AC-PATH-04-D; RL-EN-03 |
| **FR-PATH-04.5** | Kode backend disiapkan dengan *guard clause* yang siap beralih ke aturan berbayar pasca-MVP tanpa refaktor database: jika `is_unlimited == false` dan flag MVP non-aktif, sistem memotong 1 energi per kesalahan dan menolak `lesson_start` jika `current_energy <= 0` (`403 Forbidden: ERR_INSUFFICIENT_ENERGY`). | RL-EN-04; AC-PATH-04-C |

---

### 4.5 FR-PATH-05 — Mesin Api Streak Harian (Daily Streak Engine)

| ID | Kebutuhan Fungsional | Sumber PRD2 |
| :--- | :--- | :--- |
| **FR-PATH-05.1** | Tabel `user_streaks` wajib memelihara: `user_id`, `current_streak` (INT), `longest_streak` (INT), `last_activity_date` (DATE format YYYY-MM-DD), dan `timezone` (VARCHAR). | AC-PATH-05; RL-ST-04 |
| **FR-PATH-05.2** | Penentuan tanggal kalender lokal wajib dikonversi di sisi server menggunakan fungsi PostgreSQL: `CURRENT_DATE AT TIME ZONE user_streaks.timezone`. Waktu lokal pengguna menentukan batas hari (00:00:00 – 23:59:59). | AC-PATH-05-B; RL-ST-04 |
| **FR-PATH-05.3** | Syarat mutlak kenaikan streak adalah penyelesaian **minimal 1 sesi lesson penuh (10 butir soal tuntas)** pada pemanggilan `POST /api/v1/lessons/session/:sessionId/finish`. | RL-ST-01; AC-PATH-05-A |
| **FR-PATH-05.4** | Algoritma evaluasi streak harian:<br>1. Ambil `today_local = CURRENT_DATE AT TIME ZONE timezone`.<br>2. Ambil `yesterday_local = today_local - INTERVAL '1 day'`.<br>3. Jika `last_activity_date == today_local`: Streak tidak bertambah (`is_streak_incremented = false`), api tetap aktif.<br>4. Jika `last_activity_date == yesterday_local`: `current_streak += 1`, `last_activity_date = today_local`, `is_streak_incremented = true`.<br>5. Jika `last_activity_date < yesterday_local` (atau null): `current_streak = 1`, `last_activity_date = today_local`, `is_streak_incremented = true` (streak baru).<br>6. Jika `current_streak > longest_streak`: `longest_streak = current_streak`. | AC-PATH-05-C; RL-ST-02 |
| **FR-PATH-05.5** | Respons penyelesaian sesi mengembalikan status visual streak: `is_streak_ignited: true` jika ini adalah lesson pertama hari ini, memicu animasi perayaan api berkobar (*flame burst Lottie*) dan efek suara pada frontend. | AC-PATH-05-E |

---

### 4.6 FR-PATH-06 — Layar Rangkuman Sesi & Pembukaan Node Berikutnya

| ID | Kebutuhan Fungsional | Sumber PRD2 |
| :--- | :--- | :--- |
| **FR-PATH-06.1** | Endpoint `POST /api/v1/lessons/session/:sessionId/finish` wajib dijalankan dalam satu blok **Database Transaction atomik**: Menandai sesi `COMPLETED`, mengupdate `user_node_progress`, menambahkan XP, mengevaluasi streak, dan membuka node berikutnya. | AC-PATH-06-A |
| **FR-PATH-06.2** | Aturan pemberian XP: Jika node baru pertama kali diselesaikan (`attempts_count == 1`), sistem memberikan **+10 XP**. Jika node berstatus pengulangan materi (`COMPLETED`), sistem memberikan **+5 XP**. | RL-PR-02; AC-PATH-06-A |
| **FR-PATH-06.3** | Pembukaan Node Berikutnya: Sistem mencari `learning_nodes` pada unit yang sama dengan `order_index = current_node.order_index + 1`. Jika ditemukan, sistem melakukan upsert pada tabel `user_node_progress` dengan status `UNLOCKED`. | AC-PATH-06-B; RL-PR-01 |
| **FR-PATH-06.4** | Penanganan Pembatalan (*Session Abandonment*): Jika pengguna keluar di tengah jalan atau sesi tidak selesai dalam 24 jam, status sesi menjadi `ABANDONED`. Progres parsial tidak disimpan, streak tidak terpicu, dan XP tidak diberikan. | RL-PR-03; AC-PATH-06-C |

---

## 5. Concrete REST API Design & Contracts

### 5.1 Standar Header & Konvensi Error
Seluruh endpoint terproteksi mewajibkan header:
```http
Authorization: Bearer <Supabase_JWT_Token>
Content-Type: application/json
X-Device-Timezone: Asia/Jakarta
```

Format respons error seragam (*RFC 7807 problem details*):
```json
{
  "success": false,
  "error": {
    "code": "ERR_INSUFFICIENT_ENERGY",
    "message": "Kuota energi Anda telah habis. Tunggu waktu isi ulang atau tingkatkan ke paket Premium.",
    "details": null
  }
}
```

---

### 5.2 Endpoint 1: Ambil Ikhtisar Jalur Belajar (Path Overview)

- **HTTP Method & Path**: `GET /api/v1/path/overview`
- **Operasi**: `path_get_overview`
- **Deskripsi**: Mengambil struktur unit, daftar node dengan status progres pengguna, status streak hari ini, dan kuota energi.
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "user_summary": {
      "total_xp": 140,
      "energy": {
        "current": 5,
        "max": 5,
        "is_unlimited": true,
        "promo_label": "Promo MVP: Energi Tanpa Batas"
      },
      "streak": {
        "current_streak": 4,
        "longest_streak": 7,
        "is_active_today": true,
        "last_activity_date": "2026-11-02"
      }
    },
    "current_unit": {
      "id": "u-001-fusha-basic",
      "unit_number": 1,
      "title": "Perkenalan & Sapaan Sehari-hari",
      "description": "Kuasai kosakata dasar menyapa dan memperkenalkan diri dalam ragam Fusha.",
      "dialect": "fusha",
      "total_nodes": 5,
      "completed_nodes": 2
    },
    "nodes": [
      {
        "id": "node-101",
        "title": "Sapaan Pagi & Sore",
        "order_index": 1,
        "status": "COMPLETED",
        "xp_reward": 10
      },
      {
        "id": "node-102",
        "title": "Menanyakan Kabar",
        "order_index": 2,
        "status": "COMPLETED",
        "xp_reward": 10
      },
      {
        "id": "node-103",
        "title": "Memperkenalkan Diri",
        "order_index": 3,
        "status": "CURRENT",
        "xp_reward": 10
      },
      {
        "id": "node-104",
        "title": "Asal Kota & Kebangsaan",
        "order_index": 4,
        "status": "LOCKED",
        "xp_reward": 10
      },
      {
        "id": "node-105",
        "title": "Uji Pemahaman Unit 1",
        "order_index": 5,
        "status": "LOCKED",
        "xp_reward": 20
      }
    ]
  }
}
```

---

### 5.3 Endpoint 2: Memulai Sesi Belajar (Start Lesson Session)

- **HTTP Method & Path**: `POST /api/v1/lessons/:nodeId/start`
- **Operasi**: `lesson_start`
- **Deskripsi**: Menginisialisasi sesi kuis 10 butir soal susun kata, memverifikasi ketersediaan node, dan mengembalikan soal pertama dengan balok acak + 2 pengecoh.
- **Request Body**: Kosong `{}`.
- **Respons Sukses (201 Created)**:
```json
{
  "success": true,
  "data": {
    "session_id": "sess-882f-4a91-912b",
    "node_id": "node-103",
    "total_questions": 10,
    "current_queue_index": 1,
    "remaining_in_queue": 10,
    "exercise": {
      "id": "ex-103-01",
      "prompt_translation": "Halo, nama saya Ahmad",
      "dialect_label": "Fusha (الفصحى)",
      "audio_url": "https://assets.dhou.app/audio/ex-103-01.mp3",
      "scrambled_word_tiles": [
        { "id": "t1", "text": "اسْمِي" },
        { "id": "t2", "text": "أَنْتَ" },
        { "id": "t3", "text": "مَرْحَبًا" },
        { "id": "t4", "text": "أَحْمَد" },
        { "id": "t5", "text": "كِتَابٌ" }
      ]
    }
  }
}
```
*Catatan Teknis: `scrambled_word_tiles` menggabungkan kata benar `["مَرْحَبًا", "اسْمِي", "أَحْمَد"]` dengan tepat 2 pengecoh `["أَنْتَ", "كِتَابٌ"]` dalam susunan teracak.*

---

### 5.4 Endpoint 3: Submit Jawaban Butir Soal (Submit Exercise Answer)

- **HTTP Method & Path**: `POST /api/v1/lessons/session/:sessionId/submit`
- **Operasi**: `lesson_submit_answer`
- **Request Body**:
```json
{
  "exercise_id": "ex-103-01",
  "selected_tile_ids": ["t3", "t1", "t4"],
  "selected_tokens": ["مَرْحَبًا", "اسْمِي", "أَحْمَد"]
}
```
- **Respons Sukses — Jawaban Benar (200 OK)**:
```json
{
  "success": true,
  "data": {
    "is_correct": true,
    "evaluation": "EXCELLENT",
    "feedback_message": "Luar biasa! Susunan kalimatmu sempurna.",
    "audio_feedback": "chime_success.mp3",
    "correct_sentence_arabic": "مَرْحَبًا، اسْمِي أَحْمَد",
    "transliteration": "Marhaban, ismii Ahmad",
    "remaining_in_queue": 9,
    "next_exercise": {
      "id": "ex-103-02",
      "prompt_translation": "Selamat pagi, bagaimana kabarmu?",
      "dialect_label": "Fusha (الفصحى)",
      "audio_url": "https://assets.dhou.app/audio/ex-103-02.mp3",
      "scrambled_word_tiles": [
        { "id": "t1", "text": "كَيْفَ" },
        { "id": "t2", "text": "صَبَاحُ" },
        { "id": "t3", "text": "مَسَاءُ" },
        { "id": "t4", "text": "الْخَيْرِ" },
        { "id": "t5", "text": "حَالُكَ" },
        { "id": "t6", "text": "أَيْنَ" }
      ]
    }
  }
}
```
- **Respons Sukses — Jawaban Salah (200 OK)**:
```json
{
  "success": true,
  "data": {
    "is_correct": false,
    "evaluation": "INCORRECT",
    "feedback_message": "Jawaban yang benar adalah:",
    "correct_sentence_arabic": "مَرْحَبًا، اسْمِي أَحْمَد",
    "transliteration": "Marhaban, ismii Ahmad",
    "audio_url": "https://assets.dhou.app/audio/ex-103-01.mp3",
    "requeued_to_tail": true,
    "remaining_in_queue": 10,
    "energy_deducted": 0,
    "energy_policy_note": "Promo MVP: Energi tidak berkurang",
    "next_exercise": {
      "id": "ex-103-02",
      "prompt_translation": "Selamat pagi, bagaimana kabarmu?",
      "dialect_label": "Fusha (الفصحى)",
      "audio_url": "https://assets.dhou.app/audio/ex-103-02.mp3",
      "scrambled_word_tiles": [ ... ]
    }
  }
}
```

---

### 5.5 Endpoint 4: Menyelesaikan Sesi Belajar (Finish Lesson Session)

- **HTTP Method & Path**: `POST /api/v1/lessons/session/:sessionId/finish`
- **Operasi**: `lesson_finish`
- **Request Body**: Kosong `{}`.
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "session_id": "sess-882f-4a91-912b",
    "node_id": "node-103",
    "is_first_completion": true,
    "xp_earned": 10,
    "total_user_xp": 150,
    "accuracy_rate": 91,
    "mistake_count": 1,
    "streak_update": {
      "previous_streak": 3,
      "current_streak": 4,
      "longest_streak": 7,
      "is_streak_ignited_today": true,
      "streak_flame_level": "BURNING",
      "message": "Api Streak Menyala! 4 hari berturut-turut! 🔥"
    },
    "progression": {
      "completed_node_id": "node-103",
      "newly_unlocked_node": {
        "id": "node-104",
        "title": "Asal Kota & Kebangsaan",
        "order_index": 4,
        "status": "CURRENT"
      }
    }
  }
}
```

---

### 5.6 Endpoint 5: Sinkronisasi Zona Waktu Pengguna (Update User Timezone)

- **HTTP Method & Path**: `PUT /api/v1/user/timezone`
- **Operasi**: `user_update_timezone`
- **Request Body**:
```json
{
  "timezone": "Asia/Jakarta"
}
```
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "timezone": "Asia/Jakarta",
    "updated_at": "2026-11-02T12:28:00Z"
  }
}
```

---

### 5.7 Tabel Ringkasan Kode Error HTTP

| Kode HTTP | Error Code | Skenario Pemicu | Solusi / Tindakan Frontend |
| :--- | :--- | :--- | :--- |
| `400 Bad Request` | `ERR_UNFINISHED_EXERCISES` | Memanggil `/finish` padahal antrean kuis masih menyisakan soal salah yang belum diselesaikan. | Frontend menahan pemicuan finish; tetap sajikan soal berikutnya. |
| `400 Bad Request` | `ERR_EMPTY_SUBMISSION` | Mengirim payload jawaban dengan array `selected_tokens` kosong. | Frontend menonaktifkan tombol submit selama area jawaban kosong. |
| `401 Unauthorized` | `ERR_AUTH_EXPIRED` | Sesi token JWT pengguna kedaluwarsa atau tidak valid. | Alihkan pengguna ke layar Masuk (PRD1 / FRD1). |
| `403 Forbidden` | `ERR_NODE_LOCKED` | Mencoba memulai sesi pada node yang masih berstatus `LOCKED`. | Tampilkan dialog bahwa node sebelumnya wajib dituntaskan. |
| `403 Forbidden` | `ERR_INSUFFICIENT_ENERGY` | (Mode pasca-MVP) Kuota energi = 0 dan bukan pengguna unlimited. | Tampilkan Modal Kehabisan Energi dengan hitung mundur refill. |
| `404 Not Found` | `ERR_SESSION_NOT_FOUND` | `sessionId` tidak terdaftar atau sudah berstatus `ABANDONED` / `COMPLETED`. | Arahkan pengguna kembali ke layar Path. |
| `409 Conflict` | `ERR_SESSION_ALREADY_FINISHED`| Mengirim submit atau finish pada sesi yang telah selesai diproses. | Cegah *double submit*; buka layar summary hasil yang tersimpan. |

---

## 6. Persistence & Data Modeling (PostgreSQL DDL)

Berikut adalah skema DDL lengkap yang siap dieksekusi di Supabase PostgreSQL dengan dukungan integritas relasional, *Row Level Security (RLS)*, dan indeks performa:

```sql
-- 1. EXTENSIONS
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- 2. ENUM TYPES
CREATE TYPE dialect_type AS ENUM ('fusha', 'egyptian', 'levantine');
CREATE TYPE node_status_type AS ENUM ('LOCKED', 'CURRENT', 'COMPLETED');
CREATE TYPE session_status_type AS ENUM ('IN_PROGRESS', 'COMPLETED', 'ABANDONED');

-- 3. LEARNING UNITS TABLE
CREATE TABLE IF NOT EXISTS public.learning_units (
    id VARCHAR(64) PRIMARY KEY,
    unit_number INT NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    dialect dialect_type NOT NULL DEFAULT 'fusha',
    order_index INT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- 4. LEARNING NODES TABLE
CREATE TABLE IF NOT EXISTS public.learning_nodes (
    id VARCHAR(64) PRIMARY KEY,
    unit_id VARCHAR(64) NOT NULL REFERENCES public.learning_units(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    order_index INT NOT NULL,
    xp_reward INT NOT NULL DEFAULT 10,
    total_questions INT NOT NULL DEFAULT 10,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    CONSTRAINT uq_unit_node_order UNIQUE (unit_id, order_index)
);

-- 5. EXERCISES TABLE (Susun Kata dengan 2 Pengecoh)
CREATE TABLE IF NOT EXISTS public.exercises (
    id VARCHAR(64) PRIMARY KEY,
    node_id VARCHAR(64) NOT NULL REFERENCES public.learning_nodes(id) ON DELETE CASCADE,
    order_index INT NOT NULL,
    prompt_translation TEXT NOT NULL,
    correct_tokens TEXT[] NOT NULL,
    distractor_tokens TEXT[] NOT NULL,
    accepted_permutations JSONB NOT NULL DEFAULT '[]'::jsonb,
    audio_url TEXT,
    transliteration TEXT NOT NULL,
    dialect_label VARCHAR(64) NOT NULL DEFAULT 'Fusha',
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    CONSTRAINT chk_distractor_count CHECK (cardinality(distractor_tokens) = 2)
);

-- 6. USER PROGRESS PER NODE TABLE
CREATE TABLE IF NOT EXISTS public.user_node_progress (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    node_id VARCHAR(64) NOT NULL REFERENCES public.learning_nodes(id) ON DELETE CASCADE,
    status node_status_type NOT NULL DEFAULT 'LOCKED',
    accuracy_rate INT DEFAULT 0,
    attempts_count INT NOT NULL DEFAULT 0,
    last_completed_at TIMESTAMPTZ,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    CONSTRAINT uq_user_node UNIQUE (user_id, node_id)
);

-- 7. USER DAILY STREAKS TABLE (Timezone-Aware)
CREATE TABLE IF NOT EXISTS public.user_streaks (
    user_id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    current_streak INT NOT NULL DEFAULT 0,
    longest_streak INT NOT NULL DEFAULT 0,
    last_activity_date DATE,
    timezone VARCHAR(64) NOT NULL DEFAULT 'Asia/Jakarta',
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- 8. USER ENERGY TABLE (Scalable Model + MVP Unlimited)
CREATE TABLE IF NOT EXISTS public.user_energy (
    user_id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    current_energy INT NOT NULL DEFAULT 5,
    max_energy INT NOT NULL DEFAULT 5,
    is_unlimited BOOLEAN NOT NULL DEFAULT TRUE, -- Default true untuk promo MVP
    last_refill_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- 9. LESSON SESSIONS TABLE (Active Queue & Mastery Loop)
CREATE TABLE IF NOT EXISTS public.lesson_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    node_id VARCHAR(64) NOT NULL REFERENCES public.learning_nodes(id) ON DELETE CASCADE,
    active_queue TEXT[] NOT NULL,
    mistake_count INT NOT NULL DEFAULT 0,
    status session_status_type NOT NULL DEFAULT 'IN_PROGRESS',
    started_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    completed_at TIMESTAMPTZ
);

-- 10. INDEXES FOR PERFORMANCE
CREATE INDEX IF NOT EXISTS idx_learning_nodes_unit ON public.learning_nodes(unit_id, order_index);
CREATE INDEX IF NOT EXISTS idx_exercises_node ON public.exercises(node_id, order_index);
CREATE INDEX IF NOT EXISTS idx_user_node_progress_lookup ON public.user_node_progress(user_id, status);
CREATE INDEX IF NOT EXISTS idx_lesson_sessions_user_status ON public.lesson_sessions(user_id, status);

-- 11. ROW LEVEL SECURITY (RLS) POLICIES
ALTER TABLE public.learning_units ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.learning_nodes ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.exercises ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.user_node_progress ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.user_streaks ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.user_energy ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.lesson_sessions ENABLE ROW LEVEL SECURITY;

-- Public Content Read Policies
CREATE POLICY "Public read for units" ON public.learning_units FOR SELECT USING (true);
CREATE POLICY "Public read for nodes" ON public.learning_nodes FOR SELECT USING (true);
CREATE POLICY "Public read for exercises" ON public.exercises FOR SELECT USING (true);

-- User-Specific Data Isolation Policies
CREATE POLICY "User progress access" ON public.user_node_progress
    FOR ALL USING (auth.uid() = user_id);

CREATE POLICY "User streak access" ON public.user_streaks
    FOR ALL USING (auth.uid() = user_id);

CREATE POLICY "User energy access" ON public.user_energy
    FOR ALL USING (auth.uid() = user_id);

CREATE POLICY "User sessions access" ON public.lesson_sessions
    FOR ALL USING (auth.uid() = user_id);
```

---

## 7. Algorithmic State Machines & Logic Engines

### 7.1 Algoritma Tap Dua Arah Balok Kata Arab (RTL Word Slot Algorithm)

```typescript
// State antarmuka kuis Frontend
interface WordTile {
  id: string;
  text: string;
  isPlaced: boolean; // Jika true, tile di Word Bank berstatus Ghost Slot
}

interface SentenceState {
  trayTiles: WordTile[];      // Balok yang tersedia di baki bawah
  placedTiles: WordTile[];    // Balok yang sedang aktif di Drop Zone atas (RTL)
}

export function handleTileTapFromTray(state: SentenceState, tileId: string): SentenceState {
  const tileIndex = state.trayTiles.findIndex((t) => t.id === tileId);
  if (tileIndex === -1 || state.trayTiles[tileIndex].isPlaced) return state;

  const targetTile = state.trayTiles[tileIndex];
  return {
    trayTiles: state.trayTiles.map((t) => (t.id === tileId ? { ...t, isPlaced: true } : t)),
    placedTiles: [...state.placedTiles, targetTile] // Menambahkan ke ekor array RTL
  };
}

export function handleTileTapFromDropZone(state: SentenceState, tileId: string): SentenceState {
  return {
    trayTiles: state.trayTiles.map((t) => (t.id === tileId ? { ...t, isPlaced: false } : t)),
    placedTiles: state.placedTiles.filter((t) => t.id !== tileId)
  };
}
```

---

### 7.2 Logika Evaluasi Streak Harian Berbasis Timezone (Server-Side)

```sql
CREATE OR REPLACE FUNCTION evaluate_daily_streak(
    p_user_id UUID,
    p_client_timezone VARCHAR
) RETURNS JSONB AS $$
DECLARE
    v_user_tz VARCHAR;
    v_today DATE;
    v_yesterday DATE;
    v_last_activity DATE;
    v_cur_streak INT;
    v_longest_streak INT;
    v_ignited BOOLEAN := FALSE;
BEGIN
    -- 1. Ambil atau inisialisasi zona waktu
    v_user_tz := COALESCE(p_client_timezone, 'Asia/Jakarta');
    
    -- 2. Tentukan tanggal hari ini dan kemarin berdasarkan timezone lokal
    v_today := (NOW() AT TIME ZONE v_user_tz)::DATE;
    v_yesterday := v_today - 1;

    -- 3. Ambil data streak pengguna saat ini
    SELECT current_streak, longest_streak, last_activity_date
    INTO v_cur_streak, v_longest_streak, v_last_activity
    FROM public.user_streaks
    WHERE user_id = p_user_id;

    IF NOT FOUND THEN
        -- Pengguna baru
        INSERT INTO public.user_streaks (user_id, current_streak, longest_streak, last_activity_date, timezone)
        VALUES (p_user_id, 1, 1, v_today, v_user_tz);
        v_cur_streak := 1;
        v_longest_streak := 1;
        v_ignited := TRUE;
    ELSE
        IF v_last_activity = v_today THEN
            -- Sudah pernah menyelesaikan lesson hari ini, streak tidak bertambah
            v_ignited := FALSE;
        ELSIF v_last_activity = v_yesterday THEN
            -- Selesai kemarin, streak bertambah +1
            v_cur_streak := v_cur_streak + 1;
            IF v_cur_streak > v_longest_streak THEN
                v_longest_streak := v_cur_streak;
            END IF;
            v_ignited := TRUE;
        ELSE
            -- Terlewat lebih dari 1 hari, streak reset ke 1
            v_cur_streak := 1;
            v_ignited := TRUE;
        END IF;

        -- Update record streak
        UPDATE public.user_streaks
        SET current_streak = v_cur_streak,
            longest_streak = v_longest_streak,
            last_activity_date = v_today,
            timezone = v_user_tz,
            updated_at = NOW()
        WHERE user_id = p_user_id;
    END IF;

    RETURN jsonb_build_object(
        'current_streak', v_cur_streak,
        'longest_streak', v_longest_streak,
        'is_streak_ignited_today', v_ignited,
        'last_activity_date', v_today
    );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## 8. Telemetry, Audit & Event Tracking

Untuk mengukur metrik pada PRD2 Section 10, sistem memicu pengiriman event analitik standar:

| Nama Event | Payload Parameters | Momen Pemicu |
| :--- | :--- | :--- |
| `path_overview_viewed` | `user_id`, `current_unit_id`, `active_node_id`, `current_streak` | Pengguna membuka tab Belajar. |
| `lesson_session_started`| `session_id`, `node_id`, `total_exercises: 10`, `user_energy_mode` | Pengguna menekan tombol "Mulai" pada node aktif. |
| `exercise_answer_checked`| `session_id`, `exercise_id`, `is_correct`, `attempt_number`, `mistake_count` | Pengguna menekan tombol "Periksa". |
| `exercise_requeued` | `session_id`, `exercise_id`, `new_queue_length` | Jawaban salah dipindahkan ke ekor antrean. |
| `streak_ignited_daily` | `user_id`, `new_streak_value`, `timezone`, `local_date` | Lesson pertama hari ini tuntas, api membara. |
| `lesson_session_finished`| `session_id`, `node_id`, `accuracy_rate`, `xp_awarded`, `duration_seconds` | Pengguna mencapai layar Lesson Summary. |

---

## 9. Acceptance & Verification Test Plan (T-PATH-01 s/d T-PATH-15)

| Test ID | Skenario Pengujian | Input & Aksi | Kriteria Keberhasilan (*Pass Criteria*) |
| :--- | :--- | :--- | :--- |
| **T-PATH-01** | Visualisasi Pohon Jalur Belajar | Panggil `GET /api/v1/path/overview` | Merender urutan node zig-zag dengan status `COMPLETED`, `CURRENT`, dan `LOCKED` yang valid. |
| **T-PATH-02** | Proteksi Node Terkunci | Pengguna mengetuk node berstatus `LOCKED` | Animasi getar CSS aktif, toast peringatan muncul, tidak ada sesi kuis yang dimulai. |
| **T-PATH-03** | Auto-scroll Viewport ke Node Aktif | Buka halaman Path pada layar berukuran 390px | Layar otomatis memusatkan posisi scroll ke node `CURRENT`. |
| **T-PATH-04** | Inisialisasi 10 Butir Soal | Panggil `POST /api/v1/lessons/:nodeId/start` | Sesi terbentuk dengan array antrean berisi tepat 10 butir soal susun kata. |
| **T-PATH-05** | Tepat 2 Kata Pengecoh | Periksa baki balok kata pada soal kuis | Menampilkan seluruh kata kalimat benar ditambah **tepat 2 kata pengecoh**. |
| **T-PATH-06** | Tap Dua Arah Balok Kata RTL | Ketuk balok di tray $\rightarrow$ ketuk balok di drop zone | Balok meluncur ke Drop Zone RTL; saat diketuk kembali ke posisi semula tanpa glitch. |
| **T-PATH-07** | Error Re-queuing ke Ekor Antrean | Pengguna salah menjawab soal $Q_1$ | Soal $Q_1$ dipindahkan ke posisi paling belakang antrean ($Q_{11}$); antrean menjadi 10 butir tersisa. |
| **T-PATH-08** | Acak Ulang Balok Saat Re-queue | Giliran soal $Q_1$ muncul kembali di akhir | Posisi balok kata di baki teracak dengan susunan berbeda dari tampilan pertama. |
| **T-PATH-09** | Kebijakan Energi MVP Unlimited | Pengguna menjawab salah 5 kali berturut-turut | Kuota energi tidak berkurang; pengguna tidak diblokir dari melanjutkan belajar. |
| **T-PATH-10** | Tampilan Badge Energi Top Bar | Amati header navigasi atas | Merender ikon baterai/petir dengan simbol tak hingga `⚡ ∞` berwarna hijau. |
| **T-PATH-11** | Aktivasi Api Streak Lesson Pertama | Pengguna menuntaskan 10 soal pada hari ini ($T$) | Streak bertambah +1, ikon api menyala terang membara, animasi flame burst muncul. |
| **T-PATH-12** | Pembatasan Kenaikan Streak Harian | Pengguna menyelesaikan lesson ke-2 di hari yang sama | XP bertambah, namun counter streak tetap sama (+0 penambahan ganda). |
| **T-PATH-13** | Timezone-Aware Daily Boundary | Selesaikan lesson pada pukul 23:55 (hari $T$) dan 00:05 (hari $T+1$) waktu lokal | Sistem mencatat 2 hari aktif berturut-turut; streak bertambah 2 hari. |
| **T-PATH-14** | Pembukaan Node $N+1$ Otomatis | Tekan tombol "Lanjut" di modal summary | Node $N$ berubah `COMPLETED`, node $N+1$ berubah `CURRENT` dan dapat langsung dimainkan. |
| **T-PATH-15** | Pembatalan Sesi Tengah Jalan | Pengguna menutup browser di soal ke-5 | Sesi ditandai `ABANDONED`; streak tidak bertambah dan XP tidak diberikan. |

---

## 10. Technical Appendix

### 10.1 Skema Validasi Zod (Request Payloads)

```typescript
import { z } from 'zod';

export const StartLessonSchema = z.object({
  nodeId: z.string().min(1, 'Node ID wajib diisi')
});

export const SubmitAnswerSchema = z.object({
  exercise_id: z.string().min(1, 'Exercise ID wajib diisi'),
  selected_tile_ids: z.array(z.string()).min(1, 'Pilih minimal satu kata'),
  selected_tokens: z.array(z.string().trim()).min(1, 'Token kata Arab tidak boleh kosong')
});

export const FinishLessonSchema = z.object({
  session_id: z.string().uuid('Session ID harus berupa UUID valid')
});

export const UpdateUserTimezoneSchema = z.object({
  timezone: z.string().refine((tz) => {
    try {
      Intl.DateTimeFormat(undefined, { timeZone: tz });
      return true;
    } catch {
      return false;
    }
  }, 'Zona waktu harus merupakan IANA Timezone yang valid (contoh: Asia/Jakarta)')
});
```

---

### 10.2 Matriks Penanganan 25+ Kasus Ekstrem Fungsional & Teknis (Edge Cases)

| ID | Kategori Kasus | Skenario Spesifik | Mitigasi & Penanganan Teknis Sistem |
| :---: | :--- | :--- | :--- |
| **EC-01** | Timezone / Waktu | Sesi kuis dimulai jam 23:58 dan selesai jam 00:02 di hari berikutnya. | Streak dihitung berdasarkan timestamp **penyelesaian sesi (*completed_at*)**. Streak dihitung untuk tanggal baru ($T+1$). |
| **EC-02** | Timezone / Waktu | Pengguna mengubah jam/tanggal di pengaturan ponsel secara manual untuk mencurangi streak. | Sistem menolak timestamp lokal klien; seluruh perhitungan tanggal kalender dihitung murni di server PostgreSQL via UTC dikonversi ke IANA timezone profil. |
| **EC-03** | Timezone / Waktu | Pengguna bepergian antar-negara melintasi garis batas tanggal (contoh: Jakarta ke Tokyo atau sebaliknya). | Header `X-Device-Timezone` mendeteksi perubahan IANA timezone dan memicu pembaruan `user_streaks.timezone` tanpa merusak riwayat tanggal `last_activity_date`. |
| **EC-04** | Jaringan / Offline | Koneksi internet terputus tepat saat pengguna menekan tombol "Periksa". | Frontend menampilkan banner koneksi retry; request di-buffer di memory dan tombol berubah menjadi "Coba Lagi" tanpa mereset susunan kata di Drop Zone. |
| **EC-05** | Jaringan / Offline | Koneksi internet terputus saat menekan "Lanjut" di layar penyelesaian sesi. | Service worker PWA menyimpan payload finish di IndexedDB lokal dan otomatis menyinkronkannya saat online kembali via *background sync*. |
| **EC-06** | UI / Interaksi | Pengguna melakukan *rapid double tap* pada satu balok kata di Word Bank. | Frontend menerapkan flag `isDebouncing = true` selama $150\text{ ms}$; hanya ketukan pertama yang diproses untuk mencegah penggandaan balok. |
| **EC-07** | UI / Interaksi | Pengguna memasukkan semua kata yang tersedia (termasuk 2 kata pengecoh) ke dalam area jawaban. | Tombol "Periksa" tetap aktif, namun evaluasi backend memvalidasi panjang array kata. Karena melebihi jumlah kata benar, sistem mengevaluasinya sebagai `INCORRECT`. |
| **EC-08** | UI / Interaksi | Pengguna tidak memilih satu pun kata dan menekan tombol "Periksa". | Tombol "Periksa" dalam kondisi disabled di UI; jika di-bypass via API, endpoint menolak dengan `400 Bad Request (ERR_EMPTY_SUBMISSION)`. |
| **EC-09** | Ortografi Arab | Teks Arab memiliki karakter harakat majemuk (contoh: fathah + tasydid). | Karakter dinormalisasi ke standar Unicode NFC sebelum dibandingkan di backend agar tidak terjadi kegagalan validasi akibat urutan byte harakat. |
| **EC-10** | Ortografi Arab | Layar ponsel sangat kecil (lebar 360px), kalimat target Arab sangat panjang ($>7$ kata). | `SentenceBuilderZone` menggunakan flex-wrap RTL dengan ukuran font adaptif (`text-lg` mengecil ke `text-base` jika baris $>2$) agar balok tidak meluap ke luar layar (*no horizontal overflow*). |
| **EC-11** | Antrean Kuis | Pengguna menjawab salah soal yang sama sebanyak 3 kali berturut-turut. | Soal tetap dipindahkan ke urutan paling belakang setiap kali salah; antrean kuis terus berputar dinamis hingga pengguna berhasil menjawabnya dengan benar. |
| **EC-12** | Antrean Kuis | Pengguna menjawab salah pada soal ke-10 (soal terakhir). | Karena antrean hanya menyisakan soal tersebut, soal langsung dimuat kembali sebagai soal berikutnya setelah lembar koreksi ditutup, dengan balok kata yang diacak ulang. |
| **EC-13** | Sesi & Lifecycle | Pengguna menutup tab browser atau melakukan force-close aplikasi di soal ke-7. | Sesi berstatus `IN_PROGRESS` dibiarkan terbuka hingga 24 jam; jika tidak dilanjutkan, worker backend menandainya sebagai `ABANDONED`. Streak tidak bertambah. |
| **EC-14** | Sesi & Lifecycle | Pengguna me-refresh halaman browser (F5) saat sedang berada di tengah kuis. | Frontend menyimpan `sessionId` aktif di `sessionStorage` lokal dan memulihkan state antrean kuis terkini dari server tanpa mengulang dari soal 1. |
| **EC-15** | Otorisasi & Akses | Pengguna mencoba memanggil endpoint `/start` untuk node berstatus `LOCKED` via Postman/curl. | RLS dan query backend memverifikasi status node sebelumnya. Jika belum selesai, server melempar `403 Forbidden (ERR_NODE_LOCKED)`. |
| **EC-16** | Otorisasi & Akses | Pengguna mencoba memanipulasi `sessionId` milik akun lain pada endpoint submit. | RLS Supabase memastikan `auth.uid() == lesson_sessions.user_id`. Request ditolak seketika dengan `403 Forbidden`. |
| **EC-17** | Energi / Kebijakan | Flag `ENV_MVP_UNLIMITED_ENERGY` bernilai `false` (uji coba transisi pasca-MVP), energi user = 0. | Endpoint `lesson_start` menolak request dengan `403 Forbidden (ERR_INSUFFICIENT_ENERGY)`; UI merender Modal Kehabisan Energi. |
| **EC-18** | Energi / Kebijakan | Pengguna Free Plan menjawab salah di mode MVP. | Backend memastikan nilai `current_energy` tidak didekremen dan field `energy_deducted: 0` dikembalikan pada payload respons. |
| **EC-19** | Progresi & XP | Pengguna mengulang node yang sudah pernah `COMPLETED`. | Backend mendeteksi riwayat; memberikan +5 XP (bukan +10 XP) dan tidak memicu pembukaan node baru yang tidak valid. |
| **EC-20** | Progresi & XP | Node yang diselesaikan adalah node terakhir pada suatu unit (Checkpoint Node). | Backend otomatis membuat record progres awal untuk Node 1 pada Unit berikutnya (`unit_number + 1`) dengan status `UNLOCKED`. |
| **EC-21** | Audio / Media | File audio gagal dimuat akibat CDN timeout atau URL audio null. | Komponen pemutar audio menampilkan ikon bisu redup; kuis tetap dapat dimainkan tanpa audio (*graceful fallback*), tombol audio tidak memblokir tombol "Periksa". |
| **EC-22** | Streak / Konkurensi | Pengguna membuka 2 tab browser bersamaan dan menyelesaikan sesi lesson secara paralel pada waktu yang sama. | Prosedur `evaluate_daily_streak` menggunakan transaksi atomik dengan kunci baris (*Row-Level Lock `FOR UPDATE`*) pada tabel `user_streaks` untuk mencegah race condition. |
| **EC-23** | Haptic / Native | Fitur Haptics dipanggil pada lingkungan browser desktop yang tidak mendukung vibrasi. | Wrapper Capacitor mendeteksi ketersediaan API getar; jika tidak didukung, pemanggilan haptic diabaikan (*silent fail*) tanpa melempar runtime exception. |
| **EC-24** | Safe Area Mobile | Perangkat iPhone dengan notch/dynamic island dan home indicator. | Layout kuis dan baki balok kata menghormati CSS `env(safe-area-inset-top)` dan `env(safe-area-inset-bottom)` agar balok kata tidak tertutup bilah navigasi perangkat. |
| **EC-25** | Akurasi Sesi | Pengguna menjawab 10 soal dengan benar sempurna tanpa pernah salah satu kali pun. | `mistake_count = 0`, formula menghasilkan $\text{Accuracy} = 100\%$, UI menampilkan label *"Sempurna! 100%"* dengan lencana bintang emas. |
