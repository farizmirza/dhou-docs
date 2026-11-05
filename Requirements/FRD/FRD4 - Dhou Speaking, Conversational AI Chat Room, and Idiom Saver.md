# Functional Requirements Document (FRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | FRD4 |
| **Source PRD** | [PRD4 — Dhou Speaking: Conversational AI Chat Room, Dialect Inheritance, Speech Practice, and Saved Idioms/Conversations, Version 1.0, Approved](../PRD/PRD4%20-%20Dhou%20Speaking,%20Conversational%20AI%20Chat%20Room,%20and%20Idiom%20Saver.md) |
| **Feature ID** | SPK-01, SPK-02, SPK-03, SPK-04, SPK-05, SPK-06 |
| **Application** | Dhou (تطبيق ضوء) — Mobile Web (PWA) & iOS Native Wrapper (CapacitorJS) |
| **Module** | Conversational AI & Communicative Production Core (Pilar 3) |
| **Sprint** | Sprint 4 |
| **Status** | Approved — disetujui sebagai kontrak implementasi teknis |
| **Approved On** | 2026-11-05 |
| **Implementation Status** | Specification only — implementasi belum diverifikasi |
| **Version** | 1.0 |
| **Last Updated** | 2026-11-05 |
| **Owner** | Product / Lead Engineering Dhou |
| **Target Boundary Version** | `services/speaking-api` v1.0.0, `apps/dhou-mobile` v1.0.0 |
| **Dependencies** | PRD4, FRD1 (Authentication), FRD2 (Path Dialect Preference), Supabase PostgreSQL 15+, Web Audio API / MediaRecorder, Whisper STT, LLM Engine (Gemini Flash), Cloud Arabic TTS |

---

## 1. Purpose, Sources, and Authority

Dokumen ini menerjemahkan seluruh kebutuhan bisnis, pengalaman pengguna, dan aturan kuota dari [PRD4 — Dhou Speaking: Conversational AI Chat Room, Dialect Inheritance, Speech Practice, and Saved Idioms/Conversations](../PRD/PRD4%20-%20Dhou%20Speaking,%20Conversational%20AI%20Chat%20Room,%20and%20Idiom%20Saver.md) menjadi spesifikasi teknis definitif, kontrak antarmuka API, skema basis data PostgreSQL, penegakan kuota penyimpanan (kuota 3 percakapan vs idiom tak terbatas), arsitektur audio STT/TTS, dan matriks pengujian bagi tim Frontend dan Backend.

PRD4 memegang otoritas atas alur pengguna, aturan bisnis kuota, dan pengalaman pedagogis. FRD4 ini memegang otoritas atas arsitektur teknis, kontrak JSON REST API, skema DDL, state machine sesi audio, dan kriteria penerimaan verifikasi kode (*code implementation verification*).

| Sumber | Baseline yang Digunakan | Peruntukan Teknis |
| :--- | :--- | :--- |
| [PRD4](../PRD/PRD4%20-%20Dhou%20Speaking,%20Conversational%20AI%20Chat%20Room,%20and%20Idiom%20Saver.md) | Versi 1.0, Approved | US-SPK-01 s/d US-SPK-06, Aturan Bisnis RL-SPK-01..05 |
| [design-system.md](../../design-system.md) | Versi 1.0, Approved | Token warna dialek, tipografi Amiri/Fredoka, tombol 3D Dhou |
| [DESIGN_INSTRUCTIONS.md](../../DESIGN_INSTRUCTIONS.md) | Versi 1.0, Approved | Standar penamaan frame Figma `[US-SPK-xx]`, variasi edge cases |
| [RESEARCH_REFERENCE_MAPPING.md](../../RESEARCH_REFERENCE_MAPPING.md) | Versi 1.0, Master Reference | Pilar 3: Communicative Production, Variety Consistency, Audio Feedback |

---

## 2. Scope and Source Reconciliation

### 2.1 Rekonsiliasi Ruang Lingkup Teknis
1. **Direct Chat Room Landing & Pewarisan Dialek (FR-SPK-01)**: Penanganan navigasi instan tanpa onboarding pop-up; resolusi dialek otomatis membaca state preferensi `user_preferences.last_active_dialect` dari modul *Path* (FRD2) dengan fallback ke `fusha`.
2. **Mesin Percakapan Lisan Dua Arah (FR-SPK-02)**: Pipeline transmisi suara via Web MediaRecorder API / Capacitor Voice Plugin $\rightarrow$ Whisper Speech-to-Text $\rightarrow$ LLM Context $\rightarrow$ Arabic Text-to-Speech Streaming $\rightarrow$ Chat Bubble Render.
3. **Gelembung Chat Dwibahasa & Replay Audio (FR-SPK-03)**: Rendering pesan dua arah (AI & User) dengan ortografi Arab berharakat RTL (`dir="rtl"`), terjemahan bahasa Indonesia, dan kontrol pemutaran audio independen (🔊).
4. **Mesin Simpan Idiom Tak Terbatas (FR-SPK-04)**: Penyimpanan idiom/frasa dari gelembung pesan ke tabel `saved_idioms` tanpa batasan kuota (*unlimited*), disertai deduplikasi *upsert*.
5. **Penegakan Kuota Maksimal 3 Percakapan (FR-SPK-05)**: Transaksi basis data atomik untuk mengarsipkan sesi percakapan utuh dengan penegakan kuota ketat `COUNT(saved_conversations) <= 3`. Menolak simpanan ke-4 dengan kode error `ERR_SAVED_CONVERSATIONS_FULL` dan memicu modal penimpaan (*replace modal*).
6. **API Navigasi Arsip (FR-SPK-06)**: Endpoint pembacaan dan penghapusan daftar percakapan tersimpan (maks 3) dan daftar idiom tersimpan (paginated).

### 2.2 Traceability Matrix (PRD4 $\rightarrow$ FRD4)

| PRD4 Source Trace | FRD ID | Operation ID | Frontend Integration Target | Test Scenario ID |
| :--- | :--- | :--- | :--- | :--- |
| US-SPK-01; AC-SPK-01-A..D; RL-SPK-01 | **FR-SPK-01** | `speaking_chat_init` | `SpeakingChatRoom`, `TopBarDialectBadge`, `DialectSwitcher` | T-SPK-01, 02 |
| US-SPK-02; AC-SPK-02-A..D; | **FR-SPK-02** | `speaking_send_voice` | `MainMicButton`, `AudioVisualizerWave`, `AudioPipelineManager` | T-SPK-03, 04, 05 |
| US-SPK-03; AC-SPK-03-A..C; RL-SPK-04 | **FR-SPK-03** | `speaking_replay_audio` | `ChatBubbleAI`, `ChatBubbleUser`, `SpeakerReplayBtn` | T-SPK-06, 07 |
| US-SPK-04; AC-SPK-04-A..C; RL-SPK-03..05 | **FR-SPK-04** | `speaking_save_idiom` | `SaveIdiomBtn`, `SavedIdiomsDrawer`, `IdiomCard` | T-SPK-08, 09 |
| US-SPK-05; AC-SPK-05-A..C; RL-SPK-02 | **FR-SPK-05** | `speaking_save_conversation`| `SaveConversationAction`, `QuotaLimitModal`, `ReplaceDrawer` | T-SPK-10, 11, 12 |
| US-SPK-06; AC-SPK-06-A..C; | **FR-SPK-06** | `speaking_list_saved` | `SavedConversationsView`, `SavedIdiomsView`, `ReadOnlyChat` | T-SPK-13, 14, 15 |

---

## 3. Actors and Authorization

| Aktor | Hak Akses Baca (*Read*) | Hak Akses Mutasi (*Write*) | Batasan Keamanan (*Scope & Security*) |
| :--- | :--- | :--- | :--- |
| **Authenticated Student** | Sesi obrolan miliknya, daftar idiom tersimpan miliknya, daftar percakapan tersimpan miliknya (maks 3) | Kirim suara/teks, simpan/hapus idiom, simpan/hapus percakapan | Header `Authorization: Bearer <JWT>` wajib ada. RLS Supabase memastikan user hanya dapat membaca & memutasi pesan dan arsip miliknya sendiri. |
| **Guest / Anonymous User**| Preview mode chat (maksimal 2 putaran ucapan) | Simpan idiom dan simpan percakapan diblokir | Dibatasi middleware; memicu pop-up registrasi jika ingin menyimpan idiom/percakapan. |

---

## 4. Functional Requirements

### 4.1 FR-SPK-01 — Resolusi Dialek & Inisialisasi Chat Room
- **FR-SPK-01.1**: Mengetuk menu Dhou AI memanggil `POST /api/v1/speaking/chat/init`. Sistem memeriksa kolom `last_active_dialect` pada tabel `user_preferences`.
- **FR-SPK-01.2**: Jika `last_active_dialect` bernilai `null` (pengguna baru), sistem menetapkan nilai default `fusha`.
- **FR-SPK-01.3**: Respons inisialisasi mengembalikan objek sesi aktif: `session_id`, `dialect`, dan salam pembuka awal dari AI (`initial_greeting`) beserta URL audio TTS-nya.

### 4.2 FR-SPK-02 — Pipeline Suara Interaktif (Voice-to-Chat)
- **FR-SPK-02.1**: Mengetuk tombol mikrofon utama mengaktifkan perekaman audio via `navigator.mediaDevices.getUserMedia({ audio: true })` atau Capacitor Voice Recorder.
- **FR-SPK-02.2**: Audio masukan dikirim sebagai `multipart/form-data` ke `POST /api/v1/speaking/chat/message`.
- **FR-SPK-02.3**: Backend memproses audio via Speech-to-Text (Whisper API) dengan parameter dialek terkait, lalu meneruskan teks hasil transkrip ke LLM Engine (Gemini 1.5 Flash).
- **FR-SPK-02.4**: LLM diikat oleh *Variety Consistency Guardrail* pada system prompt:
  ```text
  You are Dhou, a warm Arabic speaking partner. 
  Target dialect: {dialect} (e.g. egyptian / fusha / levantine).
  RULE: Strictly reply in the target dialect. Do NOT mix dialects.
  Always return valid JSON:
  {
    "arabic_text": "...", // Arabic with full harakat
    "translation_id": "...", // Natural Indonesian translation
    "suggested_reply": "..." // Optional hint for the user
  }
  ```
- **FR-SPK-02.5**: Respons teks Arab dari LLM otomatis dikonversi menjadi audio tuturan via TTS Engine dan disimpan ke bucket storage Supabase `speaking-audio/`.

### 4.3 FR-SPK-03 — Rendering Gelembung Chat & Pemutaran Ulang Audio
- **FR-SPK-03.1**: Setiap pesan dirender dengan orientasi RTL untuk teks Arab (`font-family: 'Amiri', serif`, `font-size: 18px`, `line-height: 1.8`).
- **FR-SPK-03.2**: Teks terjemahan bahasa Indonesia dirender di bawah teks Arab dengan warna `#4A5565` dan ukuran `14px`.
- **FR-SPK-03.3**: Mengetuk ikon speaker (🔊) memutar audio dari `audio_url` pesan tersebut. Audio player client mengelola *single audio instance*: memulai audio baru otomatis menghentikan audio yang sedang berjalan sebelumnya.

### 4.4 FR-SPK-04 — Penyimpanan Idiom Tak Terbatas (*Unlimited Saved Idioms*)
- **FR-SPK-04.1**: Endpoint `POST /api/v1/speaking/chat/save-idiom` menerima `message_id`, `arabic_phrase`, `translation_id`, dan `dialect`.
- **FR-SPK-04.2**: Sistem melakukan operasi `UPSERT` pada tabel `saved_idioms` menggunakan constraint unik `(user_id, arabic_phrase)`.
- **FR-SPK-04.3**: Tidak ada batasan kuota (*unlimited quota*) pada tabel `saved_idioms`.

### 4.5 FR-SPK-05 — Manajemen Kuota Maksimal 3 Percakapan Tersimpan
- **FR-SPK-05.1**: Endpoint `POST /api/v1/speaking/chat/save-conversation/:sessionId` mengecek kuota pengguna:
  ```sql
  SELECT COUNT(*) FROM public.saved_conversations WHERE user_id = $1;
  ```
- **FR-SPK-05.2**: Jika jumlah baris saat ini $\ge 3$, API mengembalikan respons error `409 Conflict` dengan kode `ERR_SAVED_CONVERSATIONS_FULL` disertai daftar 3 percakapan tersimpan saat ini.
- **FR-SPK-05.3**: Pengguna dapat memanggil `DELETE /api/v1/speaking/chat/save-conversation/:savedId` untuk menghapus percakapan lama, atau menyertakan parameter `replace_saved_id` pada payload `POST` untuk melakukan penggantian atomik dalam 1 transaksi DB.

### 4.6 FR-SPK-06 — Pengambilan Arsip Koleksi
- **FR-SPK-06.1**: Endpoint `GET /api/v1/speaking/saved-conversations` mengembalikan maksimal 3 rekaman percakapan beserta ringkasan metadata (tanggal simpan, dialek, cuplikan dialog, durasi).
- **FR-SPK-06.2**: Endpoint `GET /api/v1/speaking/saved-idioms` mengembalikan daftar idiom berpaginasi (default: 20 item per halaman) dengan dukungan filter dialek.

---

## 5. API Interface Contracts & OpenAPI Specifications

### 5.1 Endpoint 1: Inisialisasi Sesi Chat Room

- **HTTP Method & Path**: `POST /api/v1/speaking/chat/init`
- **Operasi**: `speaking_chat_init`
- **Request Body**:
```json
{
  "manual_dialect_override": null
}
```
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "session_id": "sess-spk-992a-bc12",
    "dialect": "egyptian",
    "dialect_label": "Ammiyyah Mesir 🇪🇬",
    "initial_greeting": {
      "id": "msg-001",
      "sender": "AI",
      "arabic_text": "أَهْلاً وَسَهْلاً يَا صَدِيقِي! عَامِل إِيه النَّهَارْدَة؟",
      "translation_id": "Selamat datang kawan! Bagaimana kabarmu hari ini?",
      "audio_url": "https://cdn.dhou.app/audio/greetings/egyptian-01.mp3"
    },
    "saved_conversations_count": 1,
    "max_saved_conversations": 3
  }
}
```

---

### 5.2 Endpoint 2: Kirim Pesan Suara / Teks Pengguna

- **HTTP Method & Path**: `POST /api/v1/speaking/chat/message`
- **Content-Type**: `multipart/form-data`
- **Form Data Fields**:
  - `session_id` (string, wajib)
  - `audio_file` (file binary blob audio, opsional jika teks)
  - `text_fallback` (string teks Arab, opsional jika audio)
- **Respons Sukses (200 OK)**:
```json
{
  "success": true,
  "data": {
    "user_message": {
      "id": "msg-u-002",
      "sender": "USER",
      "arabic_text": "الحَمْدُ لِلَّه، كُلُّه تَمَام. إِزَّيَّك أَنْتَ؟",
      "translation_id": "Alhamdulillah, semua baik. Kamu bagaimana?",
      "audio_url": "https://cdn.dhou.app/audio/user/u-sess-992a-002.webm"
    },
    "ai_response": {
      "id": "msg-ai-003",
      "sender": "AI",
      "arabic_text": "أَنَا تَمَام وَفَرْحَان إِنِّي بَتْكَلِّم مَعَاك! بِتَعْمَل إِيه دِلْوَقْتِي؟",
      "translation_id": "Aku baik dan senang bisa bicara denganmu! Sedang sibuk apa sekarang?",
      "audio_url": "https://cdn.dhou.app/audio/ai/ai-sess-992a-003.mp3",
      "suggested_reply": "أَنَا بَذَاكِر عَرَبِي دِلْوَقْتِي"
    }
  }
}
```

---

### 5.3 Endpoint 3: Simpan Idiom / Ungkapan (Unlimited Quota)

- **HTTP Method & Path**: `POST /api/v1/speaking/chat/save-idiom`
- **Request Body**:
```json
{
  "message_id": "msg-ai-003",
  "arabic_phrase": "عَامِل إِيه",
  "translation_id": "bagaimana kabarmu",
  "dialect": "egyptian",
  "audio_url": "https://cdn.dhou.app/audio/ai/ai-sess-992a-003.mp3"
}
```
- **Respons Sukses (201 Created)**:
```json
{
  "success": true,
  "data": {
    "idiom_id": "idiom-uuid-4412",
    "arabic_phrase": "عَامِل إِيه",
    "saved_at": "2026-11-05T23:20:00Z",
    "status": "SAVED"
  }
}
```

---

### 5.4 Endpoint 4: Simpan Percakapan Utuh (Maksimal 3 Kuota)

- **HTTP Method & Path**: `POST /api/v1/speaking/chat/save-conversation/:sessionId`
- **Request Body (Opsional untuk replace)**:
```json
{
  "replace_saved_id": null
}
```
- **Respons Sukses (201 Created)**:
```json
{
  "success": true,
  "data": {
    "saved_id": "saved-conv-101",
    "session_id": "sess-spk-992a-bc12",
    "title": "Percakapan Sapaan Kafe Mesir",
    "total_messages": 6,
    "saved_at": "2026-11-05T23:21:00Z",
    "current_quota_used": 2,
    "max_quota": 3
  }
}
```
- **Respons Error Kuota Penuh (409 Conflict)**:
```json
{
  "success": false,
  "error": {
    "code": "ERR_SAVED_CONVERSATIONS_FULL",
    "message": "Batas simpan percakapan penuh (3/3). Hapus salah satu percakapan lama untuk menyimpan yang baru.",
    "current_saved_items": [
      { "id": "saved-conv-001", "title": "Perkenalan di Kairo", "saved_at": "2026-11-01T10:00:00Z" },
      { "id": "saved-conv-002", "title": "Tanya Arah Jalan", "saved_at": "2026-11-02T14:30:00Z" },
      { "id": "saved-conv-003", "title": "Pesan Teh Hangat", "saved_at": "2026-11-04T08:15:00Z" }
    ]
  }
}
```

---

### 5.5 Tabel Ringkasan Kode Error HTTP

| Kode HTTP | Error Code | Skenario Pemicu | Solusi / Tindakan Frontend |
| :--- | :--- | :--- | :--- |
| `400 Bad Request` | `ERR_EMPTY_AUDIO_INPUT` | Audio rekaman pengguna berukuran 0 byte atau RMS hening. | Tampilkan toast: *"Suara tidak terdengar. Coba lagi!"*. |
| `401 Unauthorized` | `ERR_SESSION_EXPIRED` | Token JWT pengguna telah kedaluwarsa. | Arahkan pengguna ke layar Masuk (PRD1). |
| `409 Conflict` | `ERR_SAVED_CONVERSATIONS_FULL` | Pengguna menyimpan percakapan saat kuota 3/3 sudah terisi penuh. | Buka modal *Replace Saved Conversation* untuk memilih 1 percakapan lama yang dihapus. |
| `409 Conflict` | `ERR_IDIOM_ALREADY_SAVED` | Menyimpan idiom yang sudah tersimpan sebelumnya. | Sistem menangani secara idempoten; kembalikan `200 OK`. |
| `504 Gateway Timeout`| `ERR_LLM_TIMEOUT` | Layanan AI LLM/STT mengalami latensi $>10$ detik. | Tampilkan tombol retry: *"AI sedang berpikir lama, ketuk untuk coba kirim ulang"*. |

---

## 6. Persistence & Data Modeling (PostgreSQL DDL)

```sql
-- 1. Tabel Sesi Obrolan Aktif
CREATE TABLE public.chat_sessions (
    id VARCHAR(64) PRIMARY KEY,
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    dialect dialect_variety NOT NULL DEFAULT 'fusha',
    title VARCHAR(255) NOT NULL DEFAULT 'Percakapan Bahasa Arab',
    message_count INT NOT NULL DEFAULT 0,
    is_active BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now()),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now())
);

CREATE INDEX idx_chat_sessions_user ON public.chat_sessions (user_id, is_active, created_at DESC);

-- 2. Tabel Pesan Transkrip Chat
CREATE TABLE public.chat_messages (
    id VARCHAR(64) PRIMARY KEY,
    session_id VARCHAR(64) NOT NULL REFERENCES public.chat_sessions(id) ON DELETE CASCADE,
    sender_type VARCHAR(10) NOT NULL CHECK (sender_type IN ('AI', 'USER')),
    arabic_text TEXT NOT NULL,
    translation_id TEXT NOT NULL,
    audio_url TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now())
);

CREATE INDEX idx_chat_messages_session ON public.chat_messages (session_id, created_at ASC);

-- 3. Tabel Percakapan Tersimpan (Kapasitas Maksimal 3 Sesi per User)
CREATE TABLE public.saved_conversations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    session_id VARCHAR(64) NOT NULL REFERENCES public.chat_sessions(id) ON DELETE CASCADE,
    title VARCHAR(255) NOT NULL,
    dialect dialect_variety NOT NULL,
    total_messages INT NOT NULL,
    saved_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now()),
    CONSTRAINT uq_user_saved_session UNIQUE (user_id, session_id)
);

CREATE INDEX idx_saved_conversations_user ON public.saved_conversations (user_id, saved_at DESC);

-- Fungsi & Trigger Validasi Kuota Maksimal 3 Percakapan
CREATE OR REPLACE FUNCTION check_saved_conversation_quota()
RETURNS TRIGGER AS $$
BEGIN
    IF (SELECT COUNT(*) FROM public.saved_conversations WHERE user_id = NEW.user_id) >= 3 THEN
        RAISE EXCEPTION 'Batas simpan percakapan penuh (maksimal 3). Hapus salah satu terlebih dahulu.'
            USING ERRCODE = 'check_violation';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_enforce_saved_conversation_quota
BEFORE INSERT ON public.saved_conversations
FOR EACH ROW
EXECUTE FUNCTION check_saved_conversation_quota();

-- 4. Tabel Idiom Tersimpan (Kuota Tak Terbatas / Unlimited)
CREATE TABLE public.saved_idioms (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    message_id VARCHAR(64) REFERENCES public.chat_messages(id) ON DELETE SET NULL,
    arabic_phrase VARCHAR(255) NOT NULL,
    translation_id VARCHAR(255) NOT NULL,
    audio_url TEXT NOT NULL,
    dialect dialect_variety NOT NULL DEFAULT 'fusha',
    saved_at TIMESTAMPTZ NOT NULL DEFAULT timezone('utc'::text, now()),
    CONSTRAINT uq_user_saved_idiom UNIQUE (user_id, arabic_phrase)
);

CREATE INDEX idx_saved_idioms_user_dialect ON public.saved_idioms (user_id, dialect, saved_at DESC);

-- Row Level Security (RLS)
ALTER TABLE public.chat_sessions ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.chat_messages ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.saved_conversations ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.saved_idioms ENABLE ROW LEVEL SECURITY;

-- Policies: Pengguna hanya boleh mengelola datanya sendiri
CREATE POLICY "Users manage own chat sessions" ON public.chat_sessions FOR ALL TO authenticated USING (auth.uid() = user_id) WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users read own messages" ON public.chat_messages FOR SELECT TO authenticated USING (EXISTS (SELECT 1 FROM public.chat_sessions WHERE chat_sessions.id = chat_messages.session_id AND chat_sessions.user_id = auth.uid()));
CREATE POLICY "Users insert own messages" ON public.chat_messages FOR INSERT TO authenticated WITH CHECK (EXISTS (SELECT 1 FROM public.chat_sessions WHERE chat_sessions.id = chat_messages.session_id AND chat_sessions.user_id = auth.uid()));
CREATE POLICY "Users manage own saved conversations" ON public.saved_conversations FOR ALL TO authenticated USING (auth.uid() = user_id) WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users manage own saved idioms" ON public.saved_idioms FOR ALL TO authenticated USING (auth.uid() = user_id) WITH CHECK (auth.uid() = user_id);
```

---

## 7. State Machine & Voice Interaction Engine

### 7.1 State Machine Interaksi Suara Chat Room

```
       ┌────────────────────────┐
       │          IDLE          │ (Menunggu pengguna menekan tombol bicara)
       └───────────┬────────────┘
                   │ [Tekan Tombol Mikrofon Utama]
                   ▼
       ┌────────────────────────┐
       │       RECORDING        │ (MediaRecorder merekam input suara pengguna)
       └───────────┬────────────┘
                   │ [Lepas Tombol / Timeout Suara]
                   ▼
       ┌────────────────────────┐
       │     TRANSCRIBING       │ (Kirim audio ke STT Whisper API)
       └─────┬──────────────┬───┘
             │              │ [Suara Hening / Error Audio]
             │              ▼
             │      ┌────────────────────────┐
             │      │   AUDIO_RETRY_TOAST    │ ──► (Kembali ke IDLE)
             │      └────────────────────────┘
             │ [Transkrip Berhasil]
             ▼
       ┌────────────────────────┐
       │      AI_THINKING       │ (Indikator animasi "Dhou sedang mengetik...")
       └───────────┬────────────┘
                   │ [LLM streaming selesai + Audio TTS siap]
                   ▼
       ┌────────────────────────┐
       │      AI_SPEAKING       │ (Render bubble chat + otomatis putar audio TTS)
       └───────────┬────────────┘
                   │ [Audio TTS selesai]
                   ▼
          (Kembali ke IDLE)
```

---

## 8. Edge Case, Failure Mode & Security Handling

| Edge Case ID | Skenario Masalah | Dampak Sistem | Mitigasi Teknis |
| :--- | :--- | :--- | :--- |
| **EC-SPK-01** | Kuota simpan percakapan mencapai 3/3. | Trigger DB melempar error kuota. | Tangkap error `ERR_SAVED_CONVERSATIONS_FULL` di frontend, render drawer *Replace Conversation* yang menampilkan daftar 3 sesi lama dengan tombol ganti (*Replace*). |
| **EC-SPK-02** | Pengguna menolak izin mikrofon. | `getUserMedia` melempar `NotAllowedError`. | Sembunyikan tombol mikrofon besar, tampilkan banner izin di atas input bar, dan aktifkan keyboard teks secara default. |
| **EC-SPK-03** | Pengguna keluar dari aplikasi saat audio AI diputar. | Audio berjalan di background atau terputus canggung. | Pasang event listener `visibilitychange` & `pagehide`: jika halaman masuk background, otomatis panggil `audioElement.pause()`. |
| **EC-SPK-04** | Latensi jaringan tinggi saat memuat audio TTS. | Chat tampak macet menunggu audio. | Pisahkan streaming teks dan audio: teks gelembung chat dirender seketika, ikon audio menampilkan spinner loading kecil sampai file audio siap diputar. |
| **EC-SPK-05** | Pengguna menekan tombol simpan idiom berulang kali secara cepat. | Risiko request ganda ke server (*race condition*). | Terapkan *debouncing* (300ms) di UI dan proteksi constraint unik `(user_id, arabic_phrase)` pada level DB. |

---

## 9. Performance & Audio Storage Optimization

1. **Kompresi Audio Rekaman Klien**:
   - Audio suara pengguna dikompresi ke format Opus (`audio/webm; codecs=opus`) pada bitrate 24–32 kbps (ukuran $\le 20\text{ KB}$ per pesan 5 detik) untuk menghemat kuota seluler pengguna.
2. **Streaming Latency Budget**:
   - Respon transkripsi teks STT: $< 800\text{ ms}$.
   - Waktu respons teks LLM pertama: $< 1.2\text{ detik}$.
   - Waktu mulai pemutaran audio TTS: $< 2.0\text{ detik}$.
3. **Pembersihan Sesi Usang (Garbage Collection)**:
   - Sesi chat harian yang **tidak disimpan** oleh pengguna (*not saved*) otomatis dibersihkan oleh cron worker setelah 7 hari untuk menghemat kapasitas storage Supabase.

---

## 10. Verification Matrix & Test Scenarios

| Test Scenario ID | Area Pengujian | Kondisi Awal (*Pre-condition*) | Aksi / Prosedur Uji | Kriteria Hasil yang Diharapkan (*Expected Output*) |
| :--- | :--- | :--- | :--- | :--- |
| **T-SPK-01** | Pewarisan Dialek | Unit terakhir di menu Path adalah dialek Mesir. | Buka tab Dhou AI di bottom navigation. | Chat room langsung terbuka dengan badge dialek `Mesir 🇪🇬`, AI menyapa dalam dialek Mesir. |
| **T-SPK-02** | Fallback Dialek Baru | User baru belum membuka Path. | Buka tab Dhou AI. | Chat room terbuka dengan dialek default `Fusha`. |
| **T-SPK-03** | Mic Play Start | Layar Chat Room aktif. | Tekan tombol mikrofon utama 🎙️. | Fitur AI aktif, mendengarkan input suara pengguna, dan membalas dengan audio TTS. |
| **T-SPK-04** | Audio Replay | Gelembung pesan AI tampil di layar. | Mengetuk ikon speaker (🔊) pada gelembung AI. | Audio pengucapan pesan tersebut diputar ulang secara jernih. |
| **T-SPK-05** | Save Idiom | Muncul ungkapan baru di chat. | Mengetuk tombol 🔖 Simpan Idiom. | Tombol berubah menjadi hijau *"Tersimpan ✓"*, data tersimpan di tabel `saved_idioms`. |
| **T-SPK-06** | Save Idiom Unlimited | User sudah menyimpan 50 idiom. | Menyimpan idiom ke-51. | Berhasil tersimpan tanpa hambatan kuota (*201 Created*). |
| **T-SPK-07** | Quota 3 Sesi Penuh | User sudah memiliki 3 percakapan tersimpan. | Mengetuk "Simpan Percakapan Ini". | Muncul modal *Quota Limit Reached (3/3)* dengan opsi menghapus salah satu percakapan lama. |
| **T-SPK-08** | Replace Conversation | Modal kuota penuh terbuka. | Memilih percakapan lama ke-1 untuk diganti. | Percakapan lama terhapus, percakapan baru tersimpan; kuota tetap 3/3. |
