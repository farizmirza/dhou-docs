# Functional Requirements Document (FRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | FRD3 |
| **Source PRD** | [PRD3 — Video Contextual Learning, Interactive Subtitles, Word Extraction, and Sentence Shadowing, Version 1.0, Approved](../PRD/PRD3%20-%20Video%20Contextual%20Learning,%20Interactive%20Subtitles,%20and%20Shadowing.md) |
| **Feature ID** | VID-01, VID-02, VID-03, VID-04, VID-05 |
| **Application** | Dhou (تطبيق ضوء) — Mobile Web (PWA) & iOS Native Wrapper (CapacitorJS) |
| **Module** | Video Learning & Contextual Input Core (Pilar 1) |
| **Sprint** | Sprint 3 |
| **Status** | Approved — disetujui sebagai kontrak implementasi teknis |
| **Approved On** | 2026-11-04 |
| **Implementation Status** | Specification only — implementasi belum diverifikasi |
| **Version** | 1.0 |
| **Last Updated** | 2026-11-04 |
| **Owner** | Product / Lead Engineering Dhou |
| **Target Boundary Version** | `services/content-api` v1.0.0, `apps/dhou-mobile` v1.0.0 |
| **Dependencies** | PRD3, FRD1 (Authentication), Supabase PostgreSQL 15+, CapacitorJS Core & Haptics, Web MediaRecorder API / Web Audio API |

---

## 1. Purpose, Sources, and Authority

Dokumen ini menerjemahkan seluruh kebutuhan bisnis, pedagogis, dan pengalaman pengguna dari [PRD3 — Video Contextual Learning, Interactive Subtitles, Word Extraction, and Sentence Shadowing](../PRD/PRD3%20-%20Video%20Contextual%20Learning,%20Interactive%20Subtitles,%20and%20Shadowing.md) menjadi spesifikasi teknis definitif, kontrak antarmuka API, skema basis data PostgreSQL, mesin state player audio/video, arsitektur sinkronisasi transkrip, penanganan izin mikrofon mobile, dan matriks pengujian bagi tim Frontend dan Backend.

PRD3 memegang otoritas atas alur pengguna, intensi pedagogis, dan logika bisnis. FRD3 ini memegang otoritas atas arsitektur teknis, kontrak JSON REST API, skema DDL, state machine media, dan kriteria penerimaan verifikasi kode (*code implementation verification*).

| Sumber | Baseline yang Digunakan | Peruntukan Teknis |
| :--- | :--- | :--- |
| [PRD3](../PRD/PRD3%20-%20Video%20Contextual%20Learning,%20Interactive%20Subtitles,%20and%20Shadowing.md) | Versi 1.0, Approved | US-VID-01 s/d US-VID-05, Aturan Bisnis RL-VD-01..06 |
| [design-system.md](../../design-system.md) | Versi 1.0, Approved | Dialect tokens (Fusha `#10B981`, Mesir `#51A2FF`, Syam `#FF8904`), tipografi Amiri/Fredoka |
| [vibe-coding-guidelines.md](../../vibe-coding-guidelines.md) | Versi 1.0, Approved | Batasan komponen modular, ortografi Arab RTL, penanganan memory leak audio blob |
| [RESEARCH_REFERENCE_MAPPING.md](../../RESEARCH_REFERENCE_MAPPING.md) | Versi 1.0, Master Reference | Modul 1 (Contextual Input), tabel fitur: Subtitle, Shadowing, Record-and-Compare, Word Extraction |

---

## 2. Scope and Source Reconciliation

### 2.1 Rekonsiliasi Ruang Lingkup Teknis
1. **Pustaka & Filter Dialek (FR-VID-01)**: Penyajian feed video berkartu terkurasi dengan filter berbasis dialek (`fusha`, `egyptian`, `levantine`), pagination berbasis cursor, dan pemetaan badge visual.
2. **Mesin Pemutar Video & Kontrol Subtitle (FR-VID-02)**: Custom HTML5 Video Player dengan toggle independen dual-subtitle (Arab harakat RTL & Terjemahan ID), serta kontrol playback rate (0.75x, 0.9x, 1.0x) dengan fitur preservasi nada suara (*pitch correction*).
3. **Sinkronisasi Transkrip & Navigasi Kalimat (FR-VID-03)**: Engine pemetaan timestamp presisi tinggi ($<100\text{ ms}$) antara `currentTime` video dan array kalimat, fitur auto-scroll cerdas dengan penangguhan manual scroll (4 detik), dan fungsi *Sentence Replay*.
4. **Latihan Shadowing & Audio Player Berdampingan (FR-VID-04)**: Mesin perekam suara lokal berbasis Web MediaRecorder API / Capacitor Microphone, penyimpanan audio sementara di memori browser (*blob URL*), batas durasi dinamis ($\min(15\text{s}, 1.5\times\text{durasi})$), dan kontrol *Record-and-Compare*.
5. **Kamus Mini & Ekstraksi Kata ke Modul Review (FR-VID-05)**: Pembagian token kata Arab pada transkrip (`word segmentation`), drawer definisi instan, mutasi penyimpanan kata (*bookmark*) ke tabel review dengan penanganan deduplikasi (*upsert*).

### 2.2 Traceability Matrix (PRD3 $\rightarrow$ FRD3)

| PRD3 Source Trace | FRD ID | Operation ID | Frontend Integration Target | Test Scenario ID |
| :--- | :--- | :--- | :--- | :--- |
| US-VID-01; AC-VID-01-A..C; RL-VD-01 | **FR-VID-01** | `video_get_feed` | `VideoFeedCatalog`, `DialectFilterBar`, `VideoFeedCard` | T-VID-01, 02 |
| US-VID-02; AC-VID-02-A..C; RL-VD-02 | **FR-VID-02** | `video_get_detail` | `CustomVideoPlayer`, `SubtitleOverlay`, `SpeedControlBar` | T-VID-03, 04, 05 |
| US-VID-03; AC-VID-03-A..C; | **FR-VID-03** | `video_sync_transcript`| `InteractiveTranscript`, `SentenceRow`, `ReplayButton` | T-VID-06, 07 |
| US-VID-04; AC-VID-04-A..D; RL-VD-03..04 | **FR-VID-04** | `video_shadowing_record`| `ShadowingModal`, `AudioCompareBar`, `MicRecordButton` | T-VID-08, 09, 10 |
| US-VID-05; AC-VID-05-A..C; RL-VD-05 | **FR-VID-05** | `video_save_word`, `video_remove_word` | `WordDefinitionDrawer`, `BookmarkActionBtn`, `ReviewSync` | T-VID-11, 12 |
| Scope 4.1; RL-VD-06 | **FR-VID-06** | `video_track_progress` | `VideoProgressTracker`, `WatchedProgressBar` | T-VID-13, 14, 15 |

---

## 3. Actors and Authorization

| Aktor | Hak Akses Baca (*Read*) | Hak Akses Mutasi (*Write*) | Batasan Keamanan (*Scope & Security*) |
| :--- | :--- | :--- | :--- |
| **Authenticated User** | Katalog video, metadata transkrip lengkap, leksikon kata, status progress & bookmark miliknya | Simpan/hapus kata review, update watch progress video | Header `Authorization: Bearer <JWT>` wajib ada. RLS Supabase memastikan user hanya dapat memutasi data bookmark dan progress miliknya sendiri. |
| **Guest / Anonymous User**| Menonton preview video publik (1 video per dialek) | Tidak ada mutasi diizinkan | Dibatasi secara middleware; diarahkan untuk register/login jika ingin bookmark atau latihan shadowing. |
| **Content Curator / Admin**| Seluruh video & metadata transkrip | CRUD master video, upload transkrip, verifikasi harakat | Dilindungi role `role = 'admin'` di Supabase auth metadata. |

---

## 4. Functional Requirements

### 4.1 FR-VID-01 — Feed Video & Penyaringan Dialek
- **FR-VID-01.1**: Endpoint `GET /api/v1/videos` wajib mendukung query parameter `dialect` (`all`, `fusha`, `egyptian`, `levantine`), `level` (`all`, `beginner`, `intermediate`), `limit`, dan `cursor`.
- **FR-VID-01.2**: Respons feed menyertakan metadata inti: `id`, `title_id`, `title_ar`, `dialect`, `level`, `duration_seconds`, `thumbnail_url`, dan `video_url`.
- **FR-VID-01.3**: Frontend mengimplementasikan *client-side caching* (SWR / React Query) dengan durasi revalidasi minimal 5 menit untuk menghemat beban server.

### 4.2 FR-VID-02 — Pemutar Video Kustom & Subtitle Dwibahasa
- **FR-VID-02.1**: Video dirender menggunakan elemen `<video>` native HTML5 dengan konfigurasi `playsInline`, `preload="metadata"`, dan `controls={false}` (menggunakan kustom kontrol Dhou).
- **FR-VID-02.2**: Subtitle Arab dirender menggunakan teks HTML berorientasi RTL (`dir="rtl"`), font *Amiri*, ukuran font minimum `20px`, harakat lengkap, dengan pembungkus latar hitam transparan `rgba(0, 0, 0, 0.7)`.
- **FR-VID-02.3**: Toggle subtitle Arab dan terjemahan Indonesia disimpan dalam *local storage* pengguna (`dhou_sub_arabic_pref` dan `dhou_sub_translation_pref`) agar preferensi bertahan antar-sesi video.
- **FR-VID-02.4**: Mengubah kecepatan putar memodifikasi properti `HTMLMediaElement.playbackRate` langsung ke nilai `0.75`, `0.9`, atau `1.0`. Properti `preservesPitch` disetel `true` untuk mencegah perubahan pitch suara secara janggal.

### 4.3 FR-VID-03 — Transkrip Interaktif Berstempel Waktu & Sentence Replay
- **FR-VID-03.1**: Setiap video memiliki koleksi baris kalimat (`video_sentences`) dengan stempel waktu presisi milidetik: `start_time_ms` dan `end_time_ms`.
- **FR-VID-03.2**: Event listener `timeupdate` pada pemutar video membandingkan `currentTime * 1000` dengan interval `[start_time_ms, end_time_ms]` setiap kalimat untuk menentukan kalimat aktif (`active_sentence_id`).
- **FR-VID-03.3**: Mengetuk baris kalimat manapun memicu pemanggilan `videoRef.current.currentTime = sentence.start_time_ms / 1000` dan otomatis mengeksekusi `videoRef.current.play()`.
- **FR-VID-03.4**: Fitur **Sentence Replay (🔁)** memutar rentang satu kalimat saja: saat `currentTime * 1000 >= sentence.end_time_ms`, pemutar otomatis memanggil `videoRef.current.pause()` dan mengembalikan posisi ke `start_time_ms`.

### 4.4 FR-VID-04 — Latihan Shadowing & Audio Player Berdampingan
- **FR-VID-04.1**: Membuka modal shadowing memicu inisialisasi perekam suara `navigator.mediaDevices.getUserMedia({ audio: true })` atau plugin `@capacitor/voice-recorder` jika berjalan di native iOS.
- **FR-VID-04.2**: Rekaman audio disimpan dalam format `Blob` audio lokal di memori klien dengan MIME type yang didukung perangkat (`audio/webm;codecs=opus` di Chrome/Android, atau `audio/mp4` / `audio/aac` di Safari/iOS).
- **FR-VID-04.3**: Sistem menghitung durasi rekam maksimal $\text{max\_record\_ms} = \min(15000, 1.5 \times (\text{end\_time\_ms} - \text{start\_time\_ms}))$. Timer otomatis memanggil `mediaRecorder.stop()` saat batas waktu tercapai.
- **FR-VID-04.4**: Layar menyajikan dua kontrol pemutar audio:
  1. *Player Suara Asli*: Memutar audio dari file video pada rentang kalimat terpilih.
  2. *Player Suara Saya*: Memutar audio dari `URL.createObjectURL(recordedBlob)`.
- **FR-VID-04.5**: Menjalankan salah satu pemutar audio otomatis menghentikan (*stop & rewind*) pemutar audio lainnya untuk menghindari distorsi pendengaran.

### 4.5 FR-VID-05 — Kamus Mini Kata Arab & Bookmark Review
- **FR-VID-05.1**: Setiap kalimat pada transkrip dipecah menjadi array objek token kata (`video_sentence_words`) yang memuat `id`, `word_ar`, `transliteration`, `translation_id`, dan `root_word`.
- **FR-VID-05.2**: Mengetuk token kata memicu pembukaan `WordDefinitionDrawer` dan otomatis menghentikan sementara pemutaran video (`videoRef.current.pause()`).
- **FR-VID-05.3**: Menekan tombol 🔖 **"Simpan ke Review"** memanggil `POST /api/v1/videos/:id/save-word`. Sistem melakukan *optimistic UI update* (ikon langsung berubah hijau "Tersimpan ✓") dan getaran haptic sukses via `@capacitor/haptics`.
- **FR-VID-05.4**: Menghapus kata dari daftar bookmark memanggil `DELETE /api/v1/videos/:id/save-word/:wordId` dan mengembalikan tombol ke status semula.

---

## 5. API Interface Contracts & OpenAPI Specifications

### 5.1 Endpoint 1: Ambil Katalog Feed Video (Video Feed)

- **HTTP Method & Path**: `GET /api/v1/videos`
- **Query Parameters**:
  - `dialect` (opsional): `all` | `fusha` | `egyptian` | `levantine` (default: `all`)
  - `level` (opsional): `all` | `beginner` | `intermediate` (default: `all`)
  - `limit` (opsional): `integer` (default: `10`, max: `30`)
  - `cursor` (opsional): `string` (UUID video terakhir untuk pagination)
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": "vid-101-egyptian-cafe",
        "title_id": "Pesan Kopi di Kafe Kairo",
        "title_ar": "طلب قهوة في قهوة بلدي",
        "dialect": "egyptian",
        "level": "beginner",
        "duration_seconds": 45,
        "thumbnail_url": "https://cdn.dhou.app/thumbnails/vid-101.jpg",
        "video_url": "https://cdn.dhou.app/videos/vid-101.mp4",
        "view_count": 1420,
        "is_watched": false,
        "saved_words_count": 3
      }
    ],
    "next_cursor": "vid-101-egyptian-cafe",
    "has_more": true
  }
}
```

---

### 5.2 Endpoint 2: Ambil Detail Video & Transkrip Lengkap

- **HTTP Method & Path**: `GET /api/v1/videos/:id`
- **Operasi**: `video_get_detail`
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "id": "vid-101-egyptian-cafe",
    "title_id": "Pesan Kopi di Kafe Kairo",
    "title_ar": "طلب قهوة في قهوة بلدي",
    "dialect": "egyptian",
    "level": "beginner",
    "duration_seconds": 45,
    "video_url": "https://cdn.dhou.app/videos/vid-101.mp4",
    "sentences": [
      {
        "id": "sent-001",
        "order_index": 1,
        "start_time_ms": 1200,
        "end_time_ms": 4800,
        "arabic_text": "صَبَاح الْخَيْر! إِزَّيَّك يَا صَاحْبِي؟",
        "translation_id": "Selamat pagi! Bagaimana kabarmu kawan?",
        "words": [
          {
            "id": "w-01",
            "word_ar": "صَبَاح",
            "transliteration": "shobaah",
            "translation_id": "pagi",
            "is_saved": false
          },
          {
            "id": "w-02",
            "word_ar": "الْخَيْر",
            "transliteration": "al-khoir",
            "translation_id": "kebaikan",
            "is_saved": false
          },
          {
            "id": "w-03",
            "word_ar": "إِزَّيَّك",
            "transliteration": "izayyak",
            "translation_id": "bagaimana kabarmu (laki-laki)",
            "is_saved": true
          },
          {
            "id": "w-04",
            "word_ar": "يَا",
            "transliteration": "yaa",
            "translation_id": "wahai",
            "is_saved": false
          },
          {
            "id": "w-05",
            "word_ar": "صَاحْبِي",
            "transliteration": "sohbi",
            "translation_id": "sahabatku / kawanku",
            "is_saved": false
          }
        ]
      }
    ]
  }
}
```

---

### 5.3 Endpoint 3: Simpan Kosakata ke Modul Review (Save Word)

- **HTTP Method & Path**: `POST /api/v1/videos/:id/save-word`
- **Request Body**:
```json
{
  "sentence_id": "sent-001",
  "word_ar": "إِزَّيَّك",
  "transliteration": "izayyak",
  "translation_id": "bagaimana kabarmu (laki-laki)",
  "dialect": "egyptian"
}
```
- **Respons Sukses (201 Created)**:
```json
{
  "success": true,
  "data": {
    "saved_id": "save-uuid-8821a",
    "word_ar": "إِزَّيَّك",
    "dialect": "egyptian",
    "saved_at": "2026-11-04T10:20:00Z",
    "status": "SAVED"
  }
}
```

---

### 5.4 Endpoint 4: Hapus Kosakata dari Review (Un-bookmark)

- **HTTP Method & Path**: `DELETE /api/v1/videos/:id/save-word/:savedId`
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "removed_id": "save-uuid-8821a",
    "status": "DELETED"
  }
}
```

---

### 5.5 Endpoint 5: Lacak Progres Tontonan Video (Track Progress)

- **HTTP Method & Path**: `POST /api/v1/videos/:id/progress`
- **Request Body**:
```json
{
  "watched_seconds": 38,
  "is_completed": true
}
```
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "video_id": "vid-101-egyptian-cafe",
    "is_completed": true,
    "last_watched_seconds": 38,
    "updated_at": "2026-11-04T10:20:45Z"
  }
}
```

---

### 5.6 Tabel Ringkasan Kode Error HTTP

| Kode HTTP | Error Code | Skenario Pemicu | Solusi / Tindakan Frontend |
| :--- | :--- | :--- | :--- |
| `400 Bad Request` | `ERR_INVALID_DIALECT` | Mengirim filter dialek yang tidak terdaftar di sistem. | Fallback ke `dialect = all`. |
| `401 Unauthorized` | `ERR_AUTH_REQUIRED` | Mencoba bookmark kata tanpa sesi login aktif. | Tampilkan Modal Autentikasi / Arahkan ke Login. |
| `404 Not Found` | `ERR_VIDEO_NOT_FOUND` | Video ID tidak ditemukan di basis data atau dihapus kurator. | Tampilkan pesan 404 ramah, navigasikan kembali ke feed. |
| `409 Conflict` | `ERR_WORD_ALREADY_SAVED` | Menyimpan kata yang identik pada video yang sama. | Ditangani secara idempoten oleh backend; return status `200 OK`. |

---

## 6. Persistence & Data Modeling (PostgreSQL DDL)

Skema basis data dirancang untuk PostgreSQL 15+ pada Supabase dengan dukungan pengindeksan efisien dan *Row Level Security (RLS)*.

```sql
-- 1. Enums Dialek dan Level
CREATE TYPE dialect_variety AS ENUM ('fusha', 'egyptian', 'levantine');
CREATE TYPE content_level AS ENUM ('beginner', 'intermediate', 'advanced');

-- 2. Tabel Master Video
CREATE TABLE public.videos (
    id VARCHAR(64) PRIMARY KEY,
    title_id VARCHAR(255) NOT NULL,
    title_ar VARCHAR(255) NOT NULL,
    dialect dialect_variety NOT NULL DEFAULT 'fusha',
    level content_level NOT NULL DEFAULT 'beginner',
    duration_seconds INT NOT NULL CHECK (duration_seconds > 0 AND duration_seconds <= 300),
    thumbnail_url TEXT NOT NULL,
    video_url TEXT NOT NULL,
    view_count BIGINT NOT NULL DEFAULT 0,
    is_published BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now()),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now())
);

-- Indexing untuk query feed berbasis dialek & level
CREATE INDEX idx_videos_dialect_published ON public.videos (dialect, is_published, created_at DESC);
CREATE INDEX idx_videos_level ON public.videos (level);

-- 3. Tabel Kalimat Transkrip Video
CREATE TABLE public.video_sentences (
    id VARCHAR(64) PRIMARY KEY,
    video_id VARCHAR(64) NOT NULL REFERENCES public.videos(id) ON DELETE CASCADE,
    order_index INT NOT NULL,
    start_time_ms INT NOT NULL CHECK (start_time_ms >= 0),
    end_time_ms INT NOT NULL CHECK (end_time_ms > start_time_ms),
    arabic_text TEXT NOT NULL,
    translation_id TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now()),
    CONSTRAINT uq_video_sentence_order UNIQUE (video_id, order_index)
);

CREATE INDEX idx_video_sentences_video_id ON public.video_sentences (video_id, order_index ASC);

-- 4. Tabel Token Kata Arab per Kalimat (Leksikon Interaktif)
CREATE TABLE public.video_sentence_words (
    id VARCHAR(64) PRIMARY KEY,
    sentence_id VARCHAR(64) NOT NULL REFERENCES public.video_sentences(id) ON DELETE CASCADE,
    order_index INT NOT NULL,
    word_ar VARCHAR(100) NOT NULL,
    transliteration VARCHAR(150) NOT NULL,
    translation_id VARCHAR(255) NOT NULL,
    root_word VARCHAR(100),
    created_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now()),
    CONSTRAINT uq_sentence_word_order UNIQUE (sentence_id, order_index)
);

CREATE INDEX idx_sentence_words_lookup ON public.video_sentence_words (sentence_id, order_index ASC);

-- 5. Tabel Kosakata yang Disimpan Pengguna (Review Bridge)
CREATE TABLE public.user_saved_vocab (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    video_id VARCHAR(64) NOT NULL REFERENCES public.videos(id) ON DELETE CASCADE,
    sentence_id VARCHAR(64) REFERENCES public.video_sentences(id) ON DELETE SET NULL,
    word_ar VARCHAR(100) NOT NULL,
    transliteration VARCHAR(150),
    translation_id VARCHAR(255) NOT NULL,
    dialect dialect_variety NOT NULL DEFAULT 'fusha',
    is_mastered BOOLEAN NOT NULL DEFAULT false,
    saved_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now()),
    CONSTRAINT uq_user_saved_word_per_video UNIQUE (user_id, video_id, word_ar)
);

CREATE INDEX idx_user_saved_vocab_lookup ON public.user_saved_vocab (user_id, dialect, saved_at DESC);

-- 6. Tabel Progres Menonton Pengguna
CREATE TABLE public.user_video_progress (
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    video_id VARCHAR(64) NOT NULL REFERENCES public.videos(id) ON DELETE CASCADE,
    watched_seconds INT NOT NULL DEFAULT 0,
    is_completed BOOLEAN NOT NULL DEFAULT false,
    last_accessed_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now()),
    PRIMARY KEY (user_id, video_id)
);

-- Row Level Security (RLS)
ALTER TABLE public.videos ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.video_sentences ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.video_sentence_words ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.user_saved_vocab ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.user_video_progress ENABLE ROW LEVEL SECURITY;

-- Policy: Publik boleh membaca master video & transkrip
CREATE POLICY "Public videos are readable" ON public.videos FOR SELECT USING (is_published = true);
CREATE POLICY "Public sentences are readable" ON public.video_sentences FOR SELECT USING (true);
CREATE POLICY "Public words are readable" ON public.video_sentence_words FOR SELECT USING (true);

-- Policy: Pengguna hanya boleh membaca & menulis bookmark dan progres miliknya sendiri
CREATE POLICY "Users read own saved vocab" ON public.user_saved_vocab FOR SELECT TO authenticated USING (auth.uid() = user_id);
CREATE POLICY "Users insert own saved vocab" ON public.user_saved_vocab FOR INSERT TO authenticated WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users delete own saved vocab" ON public.user_saved_vocab FOR DELETE TO authenticated USING (auth.uid() = user_id);

CREATE POLICY "Users manage own video progress" ON public.user_video_progress FOR ALL TO authenticated USING (auth.uid() = user_id) WITH CHECK (auth.uid() = user_id);
```

---

## 7. State Machine & Media Player Engine

### 7.1 State Machine Pemutar Video Kustom

```
     ┌────────────────┐
     │      IDLE      │
     └───────┬────────┘
             │ [Mount / Load Source]
             ▼
     ┌────────────────┐   Error / Network Fail   ┌───────────────┐
     │    LOADING     ├─────────────────────────►│     ERROR     │
     └───────┬────────┘                          └───────┬───────┘
             │ [CanPlayThrough Event]                    │ [Retry]
             ▼                                           ▼
┌────────────┴───────────┐                         [Reload Source]
│         PAUSED         │◄──────────────┐
└────────────┬───────────┘               │
             │ [Play Action]             │ [Pause Action / Interruption]
             ▼                           │
     ┌────────────────┐                  │
     │    PLAYING     ├──────────────────┘
     └───────┬────────┘
             │ [Under-run Buffer]
             ▼
     ┌────────────────┐
     │   BUFFERING    │
     └───────┬────────┘
             │ [Buffer Filled]
             ▼
       (Kembali ke PLAYING)
```

### 7.2 State Machine Latihan Shadowing & Komparasi Audio

```
     ┌────────────────────────┐
     │          IDLE          │ (Menunggu pengguna memilih kalimat)
     └───────────┬────────────┘
                 │ [Buka Modal Shadowing]
                 ▼
     ┌────────────────────────┐
     │    READY_TO_RECORD     │
     └─────┬──────────────┬───┘
           │              │ [Tekan Tombol Dengar Suara Asli]
           │              ▼
           │      ┌────────────────────────┐
           │      │    PLAYING_NATIVE      │
           │      └───────┬────────────────┘
           │              │ [Selesai Pemutaran Audio Asli]
           │              ▼
           │     (Kembali ke READY_TO_RECORD)
           │
           │ [Tekan & Tahan / Ketuk Tombol Rekam]
           ▼
     ┌────────────────────────┐
     │       RECORDING        │ (Menjalankan MediaRecorder + Visualizer Wave)
     └───────────┬────────────┘
                 │ [Lepas / Stop Rekam / Timeout 15s]
                 ▼
     ┌────────────────────────┐
     │     AUDIO_RECORDED     │ (Blob URL berhasil dibuat di memori klien)
     └─────┬──────────────┬───┘
           │              │ [Putar Suara Saya]
           │              ▼
           │      ┌────────────────────────┐
           │      │      PLAYING_USER      │
           │      └───────┬────────────────┘
           │              │ [Selesai Pemutaran Audio User]
           │              ▼
           │     (Kembali ke AUDIO_RECORDED)
           │
           │ [Tekan "Rekam Ulang"]
           ▼
     (Kembali ke READY_TO_RECORD - Revoke Blob URL lama)
```

### 7.3 Izin Mikrofon & Pembersihan Memori (*Memory Management*)
- **Pembersihan URL Blob Audio**: Setiap kali rekaman baru dimulai atau modal shadowing ditutup, aplikasi wajib memanggil:
  ```typescript
  if (previousBlobUrl) {
    URL.revokeObjectURL(previousBlobUrl);
  }
  ```
  Ini mencegah penumpukan *memory leak* di WebKit Safari saat pengguna berlatih shadowing berulang kali.
- **Deteksi Kesenyapan (*Silence Detection*)**: Menggunakan `AudioContext` & `AnalyserNode` sederhana: jika nilai RMS audio rekaman $< 0.01$ selama seluruh durasi rekam, tandai rekaman sebagai *EMPTY_AUDIO* dan beri peringatan ke pengguna.

---

## 8. Edge Case, Failure Mode & Security Handling

| Edge Case ID | Skenario Masalah | Dampak Sistem | Mitigasi & Mekanisme Solusi |
| :--- | :--- | :--- | :--- |
| **EC-VID-01** | Koneksi internet terputus saat memutar video. | Video membeku (*stalled*), transkrip bisa hilang jika tidak di-cache. | Transkrip kalimat disimpan utuh di state lokal saat video dibuka. Tampilkan overlay transparan di atas video: *"Koneksi terputus. Menunggu jaringan..."* tanpa menutup layar. |
| **EC-VID-02** | Izin mikrofon ditolak permanen oleh sistem iOS/Android. | MediaRecorder melempar `NotAllowedError`. | Tangkap error via blok `try-catch`, ubah state ke `PERMISSION_DENIED`, sajikan tombol *"Buka Pengaturan Perangkat"*. |
| **EC-VID-03** | Rekaman melebihi batas waktu maksimal. | Blob audio membengkak dan tidak fokus. | Set timer timeout otomatis pada `max_record_ms`. Sistem memanggil `mediaRecorder.stop()` secara otomatis dan menampilkan toast *"Batas waktu rekam tercapai"*. |
| **EC-VID-04** | Pengguna men-scroll transkrip secara cepat saat video berjalan. | Auto-scroll sistem berebut kendali (*scroll fight*) dengan jari pengguna. | Saat terdeteksi event `touchmove` pada kontainer transkrip, nonaktifkan auto-scroll selama 4000ms, lalu aktifkan kembali jika tidak ada interaksi sentuh baru. |
| **EC-VID-05** | Double-tap cepat pada tombol simpan review (*Rapid Tap*). | Risiko duplikasi entri atau race condition API. | Terapkan *debouncing* (300ms) pada tombol bookmark dan batasan database `uq_user_saved_word_per_video`. |

---

## 9. Performance, Audio/Media & Mobile Constraints

1. **Kebijakan Autoplay iOS Safari (WebKit Autoplay Policy)**:
   - Video dimulai dalam status `muted` atau menunggu interaksi gestur pengguna pertama (*user-initiated touch event*) sebelum menyalakan audio untuk menghindari blokir pemutaran otomatis oleh browser iOS.
2. **Safe Area Insets**:
   - Layout video player dan bottom sheet shadowing mematuhi variabel CSS `env(safe-area-inset-top)` dan `env(safe-area-inset-bottom)` agar tidak tertutup notch atau home bar iPhone.
3. **Optimasi Aset Video**:
   - Video di-encode dengan codec H.264 / AAC, resolusi 720p pada bitrate adaptif $\le 800\text{ kbps}$ untuk klip 30–90 detik (ukuran file berkisar 3 MB – 8 MB per klip), memastikan waktu muat $< 1.5\text{ detik}$ pada jaringan seluler 4G.

---

## 10. Verification Matrix & Test Scenarios

| Test Scenario ID | Area Pengujian | Kondisi Awal (*Pre-condition*) | Aksi / Prosedur Uji | Kriteria Hasil yang Diharapkan (*Expected Output*) |
| :--- | :--- | :--- | :--- | :--- |
| **T-VID-01** | Filter Dialek | Pengguna berada di feed video utama. | Mengetuk pill dialek `[Mesir]`. | Hanya video dengan badge `dialect = 'egyptian'` yang ditampilkan. |
| **T-VID-02** | Dual Subtitle | Video sedang diputar. | Mematikan toggle "Arti ID" dan menyalakan "Sub Arab". | Teks Arab tetap tampil berharakat di atas video; teks terjemahan bahasa Indonesia menghilang. |
| **T-VID-03** | Speed Control | Video diputar pada kecepatan normal 1.0x. | Mengetuk pill kecepatan `0.75x`. | Kecepatan audio melambat secara nyata tanpa terdengar penurunan pitch nada bicara. |
| **T-VID-04** | Sentence Replay | Video sedang berada di kalimat ke-2. | Mengetuk ikon Replay (🔁) pada baris kalimat ke-2. | Video melompat ke awal kalimat ke-2, memutarnya hingga selesai, lalu otomatis ter-pause. |
| **T-VID-05** | Word Tap Drawer | Subtitle Arab sedang aktif. | Mengetuk kata *عامل* pada teks. | Video otomatis ter-pause, muncul bottom drawer yang menampilkan arti kata dan tombol bookmark. |
| **T-VID-06** | Bookmark Sync | Kata belum pernah disimpan. | Mengetuk tombol 🔖 "Simpan ke Review". | Tombol berubah menjadi hijau "Tersimpan ✓", getaran haptic aktif, record bertambah di `user_saved_vocab`. |
| **T-VID-07** | Shadowing Flow | Modal shadowing terbuka. | Tekan rekam $\rightarrow$ bicara $\rightarrow$ tekan stop. | Blob audio terbentuk, tombol "Suara Asli" dan "Suara Saya" aktif dan dapat diputar bergantian. |
| **T-VID-08** | Mic Denied | Izin mikrofon browser dimatikan. | Mengetuk tombol rekam shadowing. | Sistem menampilkan dialog panduan izin mikrofon tanpa memicu crash aplikasi. |
