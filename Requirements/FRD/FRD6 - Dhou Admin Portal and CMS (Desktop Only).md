# Functional Requirements Document (FRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | FRD6 |
| **Source PRD** | [PRD6 — Dhou Administrative Portal & Content Management System (Desktop Only)](../PRD/PRD6%20-%20Dhou%20Admin%20Portal%20and%20CMS%20(Desktop%20Only).md) |
| **Feature ID** | ADM-CORE, ADM-CMS-01, ADM-CMS-02, ADM-AI-03, ADM-VOCAB-04, ADM-OPS-05, ADM-ANL-06, ADM-SEC-07 |
| **Application** | Dhou Admin Portal (تطبيق ضوء — لوحة التحكم) |
| **Target Platform** | **Desktop Web Only** (Resolusi Minimal $\ge 1024\text{px}$, Baseline Standar $1440\times 900\text{px}$ & $1920\times 1080\text{px}$) |
| **Module** | Back-Office Operations, Linguistic CMS, Prompt Engineering Studio, User Support & Analytics |
| **Sprint** | Sprint 6–8 |
| **Status** | Approved — disetujui sebagai kontrak implementasi teknis |
| **Approved On** | 2026-11-23 |
| **Implementation Status** | Specification Only |
| **Version** | 1.0 |
| **Last Updated** | 2026-11-23 |
| **Owner** | Product Owner / Lead Engineering Dhou |
| **Target Boundary Version** | `apps/dhou-admin-web` v1.0.0, `services/admin-api` v1.0.0 |
| **Dependencies** | PRD6, Supabase PostgreSQL & Auth (GoTrue RLS), Cloudflare R2 (S3 API), Google Gemini API (AI Studio Free Tier), Edge-TTS / Web Speech API |

---

## 1. Purpose, Sources, and Technical Authority

Dokumen ini menerjemahkan spesifikasi produk dari [PRD6 — Dhou Admin Portal and CMS (Desktop Only)](../PRD/PRD6%20-%20Dhou%20Admin%20Portal%20and%20CMS%20(Desktop%20Only).md) menjadi spesifikasi fungsional teknis, arsitektur antarmuka desktop, kontrak API definitif, skema DDL database PostgreSQL Supabase dengan Row-Level Security (RLS), Zod validation schemas, serta penanganan kasus ekstrem (*edge cases*).

Dokumen ini mengikat implementasi teknis untuk repositori `dhou-admin-web` dan layanan backend API.

| Sumber | Status Acuan | Penggunaan dalam FRD |
| :--- | :--- | :--- |
| [PRD6](../PRD/PRD6%20-%20Dhou%20Admin%20Portal%20and%20CMS%20(Desktop%20Only).md) | Versi 1.0, Approved | Ruang lingkup fitur, persona admin, user stories, NFR desktop |
| [design-system.md](../../design-system.md) | Versi 1.0, Approved | Token warna dialek, tipografi Amiri & Fredoka, komponen layout |
| [RESEARCH_REFERENCE_MAPPING.md](../../RESEARCH_REFERENCE_MAPPING.md) | Versi 1.0, Approved | 20 variasi tipe aktivitas kuis, siklus 4 pilar pedagogis Dhou |
| [Google AI Studio Gemini API Docs](https://ai.google.dev) | v1beta SDK | Konfigurasi Gemini 1.5 & 2.0 Flash (Free Tier: 15 RPM / 1M TPM) |
| [Cloudflare R2 Object Storage Docs](https://developers.cloudflare.com/r2) | S3 Compatible | Penyimpanan media video/audio tanpa biaya egress bandwidth |
| [Microsoft Edge-TTS / Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API) | W3C Standard | Sintesis audio TTS bahasa Arab berkualitas tinggi dengan biaya $0 |

---

## 2. Key Architectural Decisions (Hasil Arahan Strategis)

### 2.1 Keputusan 1: Single-Click Instant Publishing Flow (S6-FRD-DEC-01)
- **Problem:** Alur persetujuan bertingkat (*multi-stage bureaucratic approval*) memperlambat perputaran revisi materi bahasa Arab dan menambah kompleksitas kode.
- **Keputusan:** Menerapkan sistem **Single-Click Instant Publishing**:
  1. Author atau Linguist yang memiliki hak akses (*role* `admin_author` atau `super_admin`) dapat langsung menerbitkan perubahan unit/soal/video ke status aktif (*Production*) dengan **1 kali klik tombol "Terbitkan"**.
  2. Sistem menjalankan validasi integritas otomatis di latar belakang (*Harakat completeness check*, kelengkapan media, dan validitas skema JSON).
  3. Saat diterbitkan, sistem memicu *Instant Version Snapshot* ke tabel `content_versions` dan menampilkan *floating toast undo* selama 5 detik.
  4. Jika terjadi kekeliruan fatal pasca-terbit, admin dapat mengembalikan konten ke versi sebelumnya hanya dengan **1 kali klik tombol "Rollback"** pada panel riwayat versi.

### 2.2 Keputusan 2: Arsitektur Biaya Nol / Termurah (Zero-Cost / Ultra-Low-Cost Stack) (S6-FRD-DEC-02)
- **Problem:** Biaya operasional AI dan media streaming dapat membengkak dengan cepat jika menggunakan layanan komersial tanpa kuota gratis.
- **Keputusan:** Seluruh infrastruktur Dhou Admin dioptimasi menggunakan tier gratis (*Generous Free Tier*) atau biaya termurah di industri:
  1. **Mesin LLM & Sandbox Percakapan:** Menggunakan **Google Gemini 1.5 Flash / Gemini 2.0 Flash via Google AI Studio API**.
     - *Free Tier:* 15 RPM (Request per minute), 1,000,000 TPM (Token per minute), dan 1,500 RPD (Request per day) **100% Gratis ($0)**.
     - *Paid Fallback:* Bila melebihi kuota gratis, tarif hanya \$0.075 per 1M input tokens (jauh lebih murah dari GPT-4o yang mencapai \$2.50+).
  2. **Text-to-Speech (TTS) Bahasa Arab:**
     - *Tier Utama (100% Gratis):* **Edge-TTS / Microsoft Neural Voice** (e.g., `ar-EG-SalmaNeural` untuk Mesir, `ar-SY-AmanyNeural` untuk Syam, `ar-SA-HamedNeural` untuk Fusha) via microservice Python serverless atau **Web Speech API** bawaan browser desktop. Biaya \$0 / bulan tanpa batasan karakter.
     - *Tier Sekunder:* **Google Cloud TTS Free Tier** (gratis 4.000.000 karakter per bulan untuk suara standar).
  3. **Speech-to-Text (STT) Playground:**
     - Menggunakan **Web Speech API SpeechRecognition** native di browser Chrome/Edge desktop ($0) atau transkripsi audio native multimodal Gemini 2.0 Flash ($0 dalam free quota).
  4. **Penyimpanan Objek Media (Video & Audio):**
     - Menggunakan **Cloudflare R2 Object Storage**.
     - *Free Tier:* 10 GB penyimpanan per bulan gratis.
     - **Biaya Egress: $0.00** (Bebas biaya transfer data keluar selamanya, mengeliminasi risiko pembengkakan biaya bandwidth seperti AWS S3).
  5. **Database, Auth & Hosting Admin:**
     - Database & Auth: **Supabase Free Tier** (PostgreSQL 500MB, 50,000 MAU Auth, RLS terintegrasi).
     - Web App Hosting: **Cloudflare Pages / Vercel Hobby** (Gratis $0 dengan SSL otomatis dan Edge CDN).

---

## 3. Requirement Traceability Matrix

| Source PRD ID | FRD ID | Operation ID | Komponen UI Desktop | Kriteria Verifikasi |
| :--- | :--- | :--- | :--- | :--- |
| FR-CORE-01..03 | **FR-ADM-CORE** | `core_guard_viewport` | `DesktopGuardOverlay.tsx` | T-ADM-01, T-ADM-02 |
| FR-CMS-01..06 | **FR-ADM-CMS-01** | `cms_tree_list`, `cms_item_upsert` | `CurriculumTree.tsx`, `QuizBuilder.tsx` | T-ADM-03, T-ADM-04 |
| S6-FRD-DEC-01 | **FR-ADM-CMS-02** | `cms_single_click_publish`, `cms_rollback` | `PublishActionHeader.tsx`, `VersionDrawer.tsx`| T-ADM-05, T-ADM-06 |
| FR-VID-01..05 | **FR-ADM-VID-01** | `video_upload_r2`, `video_save_subtitles` | `WaveformScrubber.tsx`, `SubtitleBlockList.tsx`| T-ADM-07, T-ADM-08 |
| FR-AI-01..04 | **FR-ADM-AI-01** | `ai_gemini_sandbox_chat` | `PromptPlayground.tsx`, `DialectMetricCard.tsx`| T-ADM-09, T-ADM-10 |
| FR-VOC-01..03 | **FR-ADM-VOC-01** | `vocab_search`, `vocab_upsert` | `LexiconTable.tsx`, `TtsPreviewButton.tsx` | T-ADM-11, T-ADM-12 |
| FR-OPS-01..04 | **FR-ADM-OPS-01** | `ops_override_streak`, `ops_refill_energy` | `UserInspectorModal.tsx`, `ReportQueueTable.tsx`| T-ADM-13, T-ADM-14 |
| FR-SEC-01..03 | **FR-ADM-SEC-01** | `sec_mfa_verify`, `sec_audit_log` | `MfaChallengeModal.tsx`, `AuditLogDrawer.tsx` | T-ADM-15, T-ADM-16 |

---

## 4. Actors and Role-Based Access Control (RBAC)

Akses admin dikendalikan oleh PostgreSQL Row-Level Security (RLS) berbasis `admin_role` pada klaim JWT Supabase Auth:

```text
┌─────────────────────────┬────────────────────────────────────────────────────────────────────────┐
│ Role Admin              │ Hak Izin Spesifik                                                      │
├─────────────────────────┼────────────────────────────────────────────────────────────────────────┤
│ super_admin             │ Akses penuh (Read, Write, Single-Click Publish, Rollback, User Ops,   │
│                         │ Config API, Audit Logs, Manajemen Akun Admin).                         │
│ content_author          │ Read & Write Kurikulum, Video, Kamus, AI Prompts. Single-Click Publish │
│                         │ ke Staging & Production. Rollback versi konten.                        │
│ support_agent           │ Read Profil Pengguna. Write: Override Streak, Refill Energy, Handle   │
│                         │ Flagged Reports. DILARANG mengubah kurikulum atau prompt AI.           │
│ readonly_auditor        │ Read-Only seluruh modul, tabel data, riwayat audit log, dan analitik.  │
└─────────────────────────┴────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Detailed Functional Specifications

### 5.1 FR-ADM-CORE: Desktop Viewport Gatekeeper
1. **Pemeriksaan Dimensi Layar (Screen Check):**
   - Hook `useDesktopGuard` memantau event `window.resize` dengan debouncing 100ms.
   - Jika `window.innerWidth < 1024px`, aplikasi menghentikan rendering tree utama (`return null`) dan merender komponen `DesktopGuardOverlay`.
2. **Device Pointer Detection:**
   - Memeriksa CSS media query `(pointer: coarse) and (hover: none)`. Jika terdeteksi perangkat layar sentuh murni (seperti smartphone), tampilkan banner edukasi desktop.
3. **In-Memory Draft Preservation:**
   - Form yang sedang aktif diedit otomatis tersimpan ke `sessionStorage` (`dhou_admin_active_draft`) setiap kali terjadi perubahan nilai input, sehingga data tidak terhapus jika jendela browser tanpa sengaja diperkecil.

---

### 5.2 FR-ADM-CMS-01 & 02: Curriculum Tree & Single-Click Publish Engine

#### A. Interactive Question Builder (20 Tipe Kuis)
1. **Dynamic Form Switcher:** Mengubah skema input berdasarkan tipe soal:
   - `sentence_scramble`: Input kalimat utuh berharakat, pembagian token kata otomatis, input kata pengecoh (*distractors*).
   - `word_match`: Pasangan kata Arab dan terjemahan Indonesia.
   - `audio_listen`: Unggah audio / auto-generate Edge-TTS + pilihan jawaban ganda.
   - `fill_blank`: Teks kalimat dengan sintaks penanda `[target]` yang menjadi celah kosong.
2. **Arabic Bidi Editor & Tasykil Virtual Keyboard:**
   - Input Arab selalu terisolasi dengan atribut `dir="rtl"`, font `Amiri`, ukuran font `18px`, `line-height: 1.8`.
   - Floating toolbar tasykil (shortcuts: `Ctrl+1` Fathah, `Ctrl+2` Kasrah, `Ctrl+3` Dhommah, `Ctrl+4` Sukun, `Ctrl+5` Syaddah, `Ctrl+6` Tanwin).
3. **Live Mobile Simulator (390px):**
   - Panel kanan berukuran tetap `390px` merender preview interaktif menggunakan komponen mobile Dhou.
   - State preview bereaksi secara reaktif ($< 50\text{ms}$) terhadap setiap keystroke input.

#### B. Single-Click Instant Publishing Flow
1. **Trigger:** Tombol utama **"Terbitkan Sekarang (Single-Click)"** pada header form.
2. **Automated Sanity Validation (Pre-Flight Check):**
   - Validasi kelengkapan teks Arab dan terjemahan.
   - Harakat Check: Jika unit level pemula memiliki huruf tanpa harakat, tampilkan modal konfirmasi cepat: *"Ada 2 huruf gundul. Terbitkan langsung? [Batal] [Tetap Terbitkan]"*.
3. **Eksekusi 1-Klik:**
   - Sistem mengirim payload ke endpoint `POST /api/v1/admin/curriculum/publish`.
   - Backend membuka PostgreSQL Transaction:
     a. Menyimpan snapshot kondisi sebelumnya ke tabel `content_versions`.
     b. Memperbarui status unit/soal menjadi `status = 'published'` dan `is_active = true`.
     c. Mencatat log ke `admin_audit_logs`.
4. **Post-Publish Undo Toast:**
   - Frontend menampilkan floating toast hijau: *"Materi berhasil diterbitkan! [Batalkan / Undo (5s)]"*.
   - Jika tombol *Undo* diklik dalam 5 detik, sistem langsung memanggil endpoint `POST /api/v1/admin/curriculum/rollback`.

---

### 5.3 FR-ADM-VID-01: Video Learning & Waveform Subtitle Studio
1. **Cloudflare R2 Direct Upload:**
   - Admin memilih berkas video MP4/WebM ($\le 100\text{MB}$).
   - Frontend meminta Presigned Upload URL ke backend via `POST /api/v1/admin/video/presigned-url`.
   - Berkas diunggah langsung dari browser klien ke Cloudflare R2 bucket tanpa membebani server backend (*zero bandwidth pass-through*).
2. **Audio Waveform Scrubber (`wavesurfer.js`):**
   - Merender gelombang audio visual secara horizontal dengan zoom level variabel (1x s/d 10x).
   - Kontrol pemutaran keyboard: `Spasi` (Play/Pause), `[` (Set Start Time), `]` (Set End Time), `Panah Kiri/Kanan` (Geser 100ms).
3. **Interactive Subtitle Word Sync:**
   - Setiap balok kalimat berharakat dapat dipecah menjadi kata-per-kata dengan timestamp milidetik.
   - Label penanda: Pilihan *Fusha*, Mesir, atau Syam, serta checkbox *Shadowing Target Segment*.

---

### 5.4 FR-ADM-AI-01: Dhou Speaking Studio via Gemini Free Tier
1. **Persona & Guardrail Configuration:**
   - Konfigurasi persona: Nama, Deskripsi Latar Belakang, Topik Percakapan, dan Dialek Wajib (*Strict Dialect Constraint*).
   - Template System Prompt standar menyisipkan instruksi penjaga dialek:
     ```text
     You are {persona_name}, a native Arabic speaker from {location}. 
     You MUST strictly speak in {target_dialect}. Never mix with Modern Standard Arabic 
     unless explicitly explaining a formal concept. Keep sentences concise (1-2 sentences).
     ```
2. **Gemini API Integration (Free Tier):**
   - Terkoneksi ke endpoint Google AI Studio `models/gemini-2.0-flash` atau `gemini-1.5-flash`.
   - Menggunakan API Key dari environment variable terenkripsi.
3. **Interactive Test Playground (Desktop Split View):**
   - Obrolan live dua arah: Teks dan Suara (Web Speech API input).
   - Inspector Panel menampilkan:
     - Waktu Latensi Respon Pertama ($T_0$ dalam milidetik).
     - Jumlah Token Input & Output (menghitung sisa kuota 1M TPM gratis).
     - Raw JSON Response dari Gemini API.
4. **Free TTS Previewer:**
   - Menghasilkan audio respons bot secara instan menggunakan `edge-tts` (Microsoft Neural Arabic) tanpa biaya.

---

### 5.5 FR-ADM-OPS-01: User Support & Gamification Overrides
1. **Global Omnibox Search (`Cmd/Ctrl + K`):**
   - Mencari akun pengguna berdasarkan Supabase UUID, email, atau nama lengkap secara *typeahead* ($< 200\text{ms}$).
2. **Gamification State Inspector & Fast Overrides:**
   - Menampilkan visual status pengguna saat ini: Jumlah Streak (🔥), Freeze tersisa, Saldo Energi (🔋).
   - **Tombol Cepat 1-Klik:**
     - `+1 Hari Freeze Streak`: Menambahkan pelindung streak jika terjadi komplain teknis.
     - `Pulihkan Streak ke X Hari`: Memasukkan angka streak valid (maksimal $+30$ hari) + isian teks alasan wajib.
     - `Refill Energi Penuh (5/5)`: Memulihkan kuota energi user secara instan.
3. **Tiket Laporan Konten (Flagged Reports):**
   - Menampilkan antrean soal yang dilaporkan oleh murid di aplikasi mobile.
   - Admin dapat mengklik *"Buka Soal di Editor"*, memperbaiki kesalahan harakat/terjemahan, dan menyelesaikan tiket dalam satu alur kerja.

---

## 6. Definitive API Contracts & Schema

Seluruh endpoint admin diproteksi dengan header:
`Authorization: Bearer <Supabase_JWT_Token>` (Role wajib terverifikasi).

### 6.1 Single-Click Publish Endpoint

#### `POST /api/v1/admin/curriculum/publish`
- **Tujuan:** Menerbitkan unit belajar atau butir soal ke status aktif produksi dalam 1 kali eksekusi.
- **Request Headers:** `Content-Type: application/json`
- **Request Body:**
```json
{
  "entity_type": "unit",
  "entity_id": "8f3b2c1a-5d6e-4f7a-9b0c-1e2d3f4a5b6c",
  "change_summary": "Penyempurnaan harakat pada Level 2 dan penambahan 2 kata pengecoh",
  "force_publish": false
}
```
- **Response Success (`200 OK`):**
```json
{
  "success": true,
  "data": {
    "entity_id": "8f3b2c1a-5d6e-4f7a-9b0c-1e2d3f4a5b6c",
    "version_number": 4,
    "status": "published",
    "published_at": "2026-11-23T09:45:00Z",
    "published_by": "adm_9918273645",
    "undo_token": "undo_sec_token_7a8b9c0d"
  },
  "message": "Unit berhasil diterbitkan ke lingkungan aktif."
}
```
- **Response Error (`422 Unprocessable Entity - Harakat Warning`):**
```json
{
  "success": false,
  "error": {
    "code": "CURR_422_HARAKAT_MISSING",
    "message": "Ditemukan 3 huruf tanpa tanda harakat pada materi pemula.",
    "details": {
      "missing_count": 3,
      "words_affected": ["كتاب", "جديد"]
    }
  }
}
```

---

### 6.2 Instant Rollback Endpoint

#### `POST /api/v1/admin/curriculum/rollback`
- **Tujuan:** Mengembalikan status materi ke versi sebelumnya secara instan (*1-Click Undo*).
- **Request Body:**
```json
{
  "entity_id": "8f3b2c1a-5d6e-4f7a-9b0c-1e2d3f4a5b6c",
  "target_version": 3,
  "reason": "Salah input audio pada butir soal 5"
}
```
- **Response Success (`200 OK`):**
```json
{
  "success": true,
  "data": {
    "entity_id": "8f3b2c1a-5d6e-4f7a-9b0c-1e2d3f4a5b6c",
    "current_version": 5,
    "restored_from_version": 3,
    "status": "published"
  },
  "message": "Konten berhasil dikembalikan ke versi 3."
}
```

---

### 6.3 Gemini Free Tier AI Sandbox Endpoint

#### `POST /api/v1/admin/ai/sandbox-test`
- **Tujuan:** Menguji respon persona AI menggunakan Gemini API kuota gratis secara real-time.
- **Request Body:**
```json
{
  "persona_id": "persona_barista_cairo",
  "model_name": "gemini-2.0-flash",
  "temperature": 0.7,
  "message": "صباح الخير! بدي قهوة مظبوطة لو سمحت",
  "conversation_history": [
    { "role": "assistant", "content": "أهلاً وسهلاً يا باشا! منور القهوة." }
  ]
}
```
- **Response Success (`200 OK`):**
```json
{
  "success": true,
  "data": {
    "reply_text": "صباح الفل والياسمين! من عينيّا الاتنين، أحلى فنجان قهوة مظبوط يظبط الدماغ في ثواني.",
    "metrics": {
      "latency_ms": 780,
      "prompt_tokens": 342,
      "candidates_tokens": 31,
      "total_tokens": 373,
      "estimated_cost_usd": 0.00,
      "dialect_guardrail_pass": true,
      "detected_dialect": "egyptian_arabic"
    }
  }
}
```

---

### 6.4 Free Edge-TTS Audio Generation Endpoint

#### `POST /api/v1/admin/tts/synthesize`
- **Tujuan:** Menghasilkan audio pelafalan Arab native berkualitas tinggi menggunakan Edge-TTS (Biaya $0).
- **Request Body:**
```json
{
  "text": "كَيْفَ حَالُكَ اليَوْمَ ؟",
  "dialect": "egyptian",
  "voice_id": "ar-EG-SalmaNeural",
  "speed_rate": "+0%"
}
```
- **Response Success (`200 OK`):**
```json
{
  "success": true,
  "data": {
    "audio_url": "https://media.dhou.app/audio/temp_synth_9a8b7c.mp3",
    "duration_seconds": 1.85,
    "cost_usd": 0.00
  }
}
```

---

### 6.5 User Gamification Override Endpoint

#### `POST /api/v1/admin/users/:user_id/override-streak`
- **Tujuan:** Memulihkan atau menambah streak harian pengguna akibat kendala sistem.
- **Request Body:**
```json
{
  "new_streak_count": 15,
  "add_freeze_credits": 1,
  "reason": "Kompensasi gangguan server tgl 22 November"
}
```
- **Response Success (`200 OK`):**
```json
{
  "success": true,
  "data": {
    "user_id": "usr_1a2b3c4d5e",
    "previous_streak": 0,
    "current_streak": 15,
    "freeze_credits": 2,
    "updated_at": "2026-11-23T09:46:12Z"
  },
  "message": "Streak pengguna berhasil dipulihkan."
}
```

---

## 7. PostgreSQL Persistence & DDL Database Schema

Seluruh skema database dihosting pada **Supabase PostgreSQL Free Tier** dengan ekstensi `uuid-ossp` dan enkripsi RLS.

```sql
-- 1. Tabel Peran dan Profil Pengguna Admin
CREATE TABLE admin_profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    email VARCHAR(255) UNIQUE NOT NULL,
    full_name VARCHAR(100) NOT NULL,
    role VARCHAR(50) NOT NULL CHECK (role IN ('super_admin', 'content_author', 'support_agent', 'readonly_auditor')),
    is_mfa_enabled BOOLEAN NOT NULL DEFAULT false,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- 2. Tabel Unit Kurikulum & Status Publikasi
CREATE TABLE curriculum_units (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    dialect VARCHAR(20) NOT NULL CHECK (dialect IN ('fusha', 'egyptian', 'levantine')),
    unit_order INT NOT NULL,
    title VARCHAR(200) NOT NULL,
    description TEXT NULL,
    icon_name VARCHAR(50) NOT NULL DEFAULT 'book',
    status VARCHAR(20) NOT NULL DEFAULT 'draft' CHECK (status IN ('draft', 'published', 'archived')),
    current_version INT NOT NULL DEFAULT 1,
    published_at TIMESTAMP WITH TIME ZONE NULL,
    published_by UUID REFERENCES admin_profiles(id) NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
CREATE INDEX idx_curriculum_units_dialect ON curriculum_units(dialect, unit_order);

-- 3. Tabel Latihan Soal Interaktif (20 Tipe Kuis)
CREATE TABLE curriculum_exercises (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    unit_id UUID NOT NULL REFERENCES curriculum_units(id) ON DELETE CASCADE,
    level_index INT NOT NULL CHECK (level_index BETWEEN 1 AND 5),
    exercise_type VARCHAR(50) NOT NULL, -- 'sentence_scramble', 'word_match', 'audio_listen', etc.
    question_payload JSONB NOT NULL,    -- Menyimpan teks Arab berharakat, audio_url, distractor, jawaban benar
    explanation TEXT NULL,
    display_order INT NOT NULL DEFAULT 1,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
CREATE INDEX idx_curriculum_exercises_unit ON curriculum_exercises(unit_id, level_index);

-- 4. Tabel Snapshot Versi Konten (Untuk Single-Click Rollback)
CREATE TABLE content_versions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    entity_type VARCHAR(50) NOT NULL, -- 'unit', 'video', 'prompt'
    entity_id UUID NOT NULL,
    version_number INT NOT NULL,
    snapshot_payload JSONB NOT NULL,   -- Seluruh state unit & exercise dibekukan dalam JSON
    change_summary TEXT NULL,
    created_by UUID REFERENCES admin_profiles(id) NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
CREATE INDEX idx_content_versions_lookup ON content_versions(entity_type, entity_id, version_number DESC);

-- 5. Tabel Berkas Video & Subtitel (Cloudflare R2)
CREATE TABLE video_content (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    title VARCHAR(200) NOT NULL,
    dialect VARCHAR(20) NOT NULL CHECK (dialect IN ('fusha', 'egyptian', 'levantine')),
    r2_storage_key VARCHAR(500) NOT NULL, -- Key file di Cloudflare R2
    public_url VARCHAR(500) NOT NULL,
    duration_seconds DECIMAL(6, 2) NOT NULL,
    subtitles_json JSONB NOT NULL DEFAULT '[]'::jsonb, -- Array segmen [ {start, end, arabic_text, id_translation, is_shadowing} ]
    status VARCHAR(20) NOT NULL DEFAULT 'draft',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- 6. Tabel Persona & System Prompt Dhou AI (Gemini Studio)
CREATE TABLE ai_personas (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    persona_key VARCHAR(50) UNIQUE NOT NULL, -- 'barista_cairo', 'student_damascus'
    display_name VARCHAR(100) NOT NULL,
    target_dialect VARCHAR(20) NOT NULL CHECK (target_dialect IN ('fusha', 'egyptian', 'levantine')),
    avatar_url VARCHAR(500) NOT NULL,
    system_prompt TEXT NOT NULL,
    default_model VARCHAR(50) NOT NULL DEFAULT 'gemini-2.0-flash',
    temperature DECIMAL(3, 2) NOT NULL DEFAULT 0.70,
    voice_id VARCHAR(50) NOT NULL DEFAULT 'ar-EG-SalmaNeural',
    is_active BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- 7. Tabel Immutable Audit Trail
CREATE TABLE admin_audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    admin_id UUID REFERENCES admin_profiles(id) NOT NULL,
    action VARCHAR(50) NOT NULL,       -- 'PUBLISH', 'ROLLBACK', 'OVERRIDE_STREAK', 'REFILL_ENERGY', 'UPDATE_PROMPT'
    target_entity VARCHAR(50) NOT NULL,
    target_id VARCHAR(100) NOT NULL,
    diff_payload JSONB NULL,           -- Menyimpan delta perubahan { before: {}, after: {} }
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
CREATE INDEX idx_audit_logs_timestamp ON admin_audit_logs(created_at DESC);

-- 8. Kebijakan Keamanan Row Level Security (RLS)
ALTER TABLE admin_profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE curriculum_units ENABLE ROW LEVEL SECURITY;
ALTER TABLE curriculum_exercises ENABLE ROW LEVEL SECURITY;
ALTER TABLE content_versions ENABLE ROW LEVEL SECURITY;
ALTER TABLE admin_audit_logs ENABLE ROW LEVEL SECURITY;

-- Policy: Hanya user dengan role admin yang diizinkan mengakses data admin
CREATE POLICY "Admin All Access" ON curriculum_units
    FOR ALL
    TO authenticated
    USING (
        EXISTS (
            SELECT 1 FROM admin_profiles 
            WHERE admin_profiles.id = auth.uid() 
            AND admin_profiles.role IN ('super_admin', 'content_author')
        )
    );
```

---

## 8. Frontend Component & Layout Architecture

```text
apps/dhou-admin-web/src/
├── components/
│   ├── core/
│   │   ├── DesktopGuardOverlay.tsx   # Layar blokir jika lebar < 1024px
│   │   ├── TopHeaderBar.tsx          # 56px Bar: Env Badge, Omnibox, Profile
│   │   ├── LeftSidebar.tsx           # 240px -> 64px collapsible navigation
│   │   └── UndoToast.tsx             # 5s floating undo banner
│   ├── curriculum/
│   │   ├── CurriculumTree.tsx        # Node hierarchy tree visualizer
│   │   ├── QuizBuilder.tsx           # Form dinamis 20 tipe kuis
│   │   ├── ArabicBidiEditor.tsx      # RTL input dengan font Amiri & tasykil toolbar
│   │   ├── LiveMobileSimulator.tsx   # 390px live preview frame
│   │   └── PublishActionHeader.tsx   # Tombol Single-Click Publish & Version Menu
│   ├── video/
│   │   ├── WaveformScrubber.tsx      # wavesurfer.js timeline visualizer
│   │   └── SubtitleSegmenter.tsx     # Word-level sync builder
│   ├── ai/
│   │   ├── PromptPlayground.tsx      # Chat simulator dengan Gemini 2.0 Flash
│   │   └── TokenBurnWidget.tsx       # Live TPM / RPM counter (Free Tier monitor)
│   └── ops/
│       ├── UserInspectorModal.tsx    # Kartu gamifikasi & override streak
│       └── FlaggedReportDrawer.tsx   # Moderasi laporan error kuis
├── hooks/
│   ├── useDesktopGuard.ts            # Resize listener & coarse pointer detector
│   ├── useGeminiSandbox.ts           # Client hook ke API Studio endpoint
│   └── useEdgeTts.ts                 # Client hook sintesis audio gratis
└── lib/
    ├── supabaseClient.ts             # Typed Supabase client
    └── zodSchemas.ts                 # Validasi schema kurikulum & kuis
```

---

## 9. Edge Cases & Functional Matrix

| ID Kasus | Kondisi Batas | Perilaku Sistem Frontend / Backend |
| :--- | :--- | :--- |
| **EC-ADM-01** | Pengguna membuka portal di laptop layar kecil (lebar jendela 1000px). | `DesktopGuardOverlay` langsung muncul menutup layar kerja, memberikan instruksi agar jendela browser dimaksimalkan (*maximize window*). |
| **EC-ADM-02** | Author menekan tombol Single-Click Publish saat ada huruf Arab tanpa harakat. | Sistem mencegat aksi dan memunculkan popover peringatan harakat dalam 50ms. Jika author menekan *"Tetap Terbitkan"*, materi langsung tayang. |
| **EC-ADM-03** | Kuota API Gemini Free Tier mencapai batas 15 RPM saat admin menguji prompt. | Backend mengembalikan `429 Too Many Requests`. Frontend menampilkan notifikasi ramah: *"Batas uji coba per menit tercapai. Menunggu 20 detik..."* dengan progress bar countdown. |
| **EC-ADM-04** | Author mengklik tombol "Undo" pada floating toast setelah 4.8 detik terbit. | Sistem membatalkan publikasi secara instan via endpoint `/rollback`, memulihkan versi snapshot sebelumnya, dan memperbarui status menjadi `draft`. |
| **EC-ADM-05** | File video yang diunggah melebihi batas 100MB atau berdurasi $> 90$ detik. | Frontend memvalidasi metadata file sebelum presigned URL diminta. Upload dibatalkan di sisi browser dengan pesan error ukuran file. |
| **EC-ADM-06** | Jaringan internet terputus saat admin sedang mengetik naskah Arab panjang. | Naskah tersimpan aman di `sessionStorage` lokal browser. Indikator di pojok kanan bawah berubah menjadi *"Mode Offline (Draf Tersimpan di Komputer)"*. |
| **EC-ADM-07** | Admin Support mencoba memasukkan angka override streak sebesar 500 hari. | Form menolak input dengan pesan error validasi Zod: *"Maksimal penambahan streak adalah 30 hari per aksi."* |
| **EC-ADM-08** | Dua author mengedit unit kurikulum yang sama pada saat bersamaan. | Saat salah satu author mempublikasikan materi, author kedua yang menekan tombol akan menerima dialog konflik dengan opsi *"Muat Versi Terbaru"* atau *"Simpan sebagai Draf Baru"*. |

---

## 10. Verification Scenarios & Test Plan

```text
┌───────────────┬───────────────────────────────────┬────────────────────────────────────────────────────────┐
│ Test ID       │ Skenario Pengujian                │ Kriteria Keberhasilan (Pass Criteria)                  │
├───────────────┼───────────────────────────────────┼────────────────────────────────────────────────────────┤
│ T-ADM-01      │ Deteksi Lebar Layar Desktop       │ Layar blokir muncul saat viewport < 1024px, dan hilang │
│               │                                   │ seketika saat viewport direntangkan >= 1024px.         │
│ T-ADM-03      │ Live Mobile Simulator Sync        │ Ketukan teks Arab di form terproyeksikan ke panel      │
│               │                                   │ simulator 390px dalam waktu < 100ms.                  │
│ T-ADM-05      │ Single-Click Publish & 5s Undo    │ Status berubah ke 'published' dalam 1x klik. Mengklik  │
│               │                                   │ 'Undo' dalam 5s berhasil mengembalikan ke versi lama.  │
│ T-ADM-07      │ Direct Upload Video ke Cloudflare │ File 50MB terunggah langsung ke R2 via presigned URL   │
│               │ R2 Storage                        │ tanpa melalui beban bandwidth server aplikasi.         │
│ T-ADM-09      │ Gemini Free Tier Sandbox Chat     │ Respon AI diterima dalam < 2s dengan dialek Mesir yang │
│               │                                   │ konsisten dan penanda biaya $0.00.                     │
│ T-ADM-11      │ Free Edge-TTS Audio Playback      │ Suara pelafalan bahasa Arab bersuara jernih tanpa      │
│               │                                   │ pemotongan kata dan latensi sintesis < 1.5 detik.      │
│ T-ADM-13      │ Support Override Streak User      │ Streak user bertambah di Supabase dan tercatat di      │
│               │                                   │ tabel immutable `admin_audit_logs`.                    │
└───────────────┴───────────────────────────────────┴────────────────────────────────────────────────────────┘
```
