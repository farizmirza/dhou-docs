# Dhou: Engineering & Vibe Coding Guidelines

Dokumen ini merupakan panduan utama gaya kerja, arsitektur kode, dan aturan kedisiplinan pengembangan untuk proyek **Dhou (تطبيق ضوء)**.

---

## 1. Golden Rules: Git & Version Control

> [!CAUTION]
> **ATURAN MUTLAK GIT:**
> **TIDAK ADA** aktivitas `git commit`, `git push`, ataupun `git pull` yang boleh dijalankan kecuali ada **perintah eksplisit dan langsung dari prompt pengguna**.

1. **Perintah Langsung Wajib Ada:** Jika pengguna hanya meminta membuat fitur, memperbaiki bug, atau merapikan kode, **JANGAN** lakukan commit atau push.
2. **Izin Terpisah:** Perintah commit **TIDAK** otomatis memberikan izin untuk push. Setiap aksi harus atas instruksi spesifik.
3. **Cek Status Sebelum Commit:** Ketika diinstruksikan commit:
   - Jalankan `git status` dan `git diff` terlebih dahulu.
   - Stage (`git add`) **hanya berkas yang relevan dengan tugas yang sedang dikerjakan**.
   - Biarkan perubahan di luar scope tetap untouched.
4. **Format Semantic Commit (Conventional Commits):**
   ```text
   feat: add video playback speed controls
   fix: prevent audio overlap in shadowing component
   style: update dialect badge colors in review tab
   ```

---

## 2. Vibe Coding Guidelines (Clean, Modular & Maintainable)

"Vibe coding" adalah proses pengembangan cepat dan kreatif berbantuan AI. Agar kode tidak menjadi *spaghetti code* yang sulit dirawat oleh pemula, ikuti standar berikut:

### A. Simplicity First (Hindari Over-Engineering)
- Tulis kode sesedikit mungkin yang menyelesaikan masalah.
- **Dilarang** membuat abstraksi berlebihan, konfigurasi yang belum dibutuhkan, atau antisipasi spekulatif sebelum fitur dasarnya berjalan.
- Gunakan data statis/mock lokal sebelum membangun integrasi backend yang rumit.

### B. Single Responsibility & Komponen Kecil
- Setiap komponen React maksimal 100–150 baris kode. Jika lebih panjang, pisahkan sub-komponennya ke folder terpisah.
- Struktur folder modular berbasis fitur:
  ```text
  src/
  ├── components/
  │   ├── ui/             # Komponen umum (Button, Card, Badge, Modal)
  │   ├── layout/         # Shell mobile, BottomNav, Header/TopBar
  │   └── features/
  │       ├── video/      # Video player, Transcript, Shadowing
  │       ├── learn/      # Quiz types (MatchPair, SentenceBuilder)
  │       ├── chat/       # Conversation UI, VoiceRecordButton
  │       └── review/     # Flashcard, Spaced Repetition items
  ├── hooks/              # Custom React hooks (useAudioRecorder, useStreak)
  ├── lib/                # Konfigurasi Supabase, LLM client, helper
  └── types/              # Definisi TypeScript interface
  ```

### C. Readable Code & Naming Conventions
- Gunakan nama variabel dan fungsi yang mendeskripsikan tujuan secara gamblang:
  - *Baik:* `isAudioPlaying`, `handleAnswerSelect()`, `activeDialect`
  - *Buruk:* `flag`, `chk()`, `temp`
- Berikan komentar singkat pada bagian logika non-sepele (misal: penanganan arah teks RTL bahasa Arab, kalkulasi waktu audio).

### D. TypeScript Strict & No `any`
- Selalu definisikan tipe data untuk props, state, dan respons API.
- Contoh tipe inti:
  ```typescript
  export type ArabicVariety = 'fusha' | 'egyptian' | 'levantine';

  export interface LessonNode {
    id: string;
    title: string;
    variety: ArabicVariety;
    isLocked: boolean;
    xpReward: number;
  }
  ```

### E. Penanganan Khusus Bahasa Arab (RTL & Font Rendering)
- Selalu pisahkan container teks Latin (`dir="ltr"`) dan teks Arab (`dir="rtl"`).
- Berikan kelas Tailwind `font-arabic` khusus untuk teks Arab dengan `leading-relaxed` atau `leading-loose` agar harakat (fathah, kasrah, dhammah) tidak terpotong.

---

## 3. Surgical Changes (Perubahan Presisi)
- Ubah hanya bagian kode yang relevan dengan tugas yang diminta.
- Jangan me-refactor atau memformat ulang file/bagian kode lain yang tidak berhubungan.
- Cocokkan gaya kode dengan pola yang sudah ada di proyek.

---

## 4. Verification First (Verifikasi Sebelum Klaim Sukses)
Untuk setiap penambahan fitur atau perbaikan kode:
1. Jalankan linter atau build check: `npm run build` atau `npm run lint`.
2. Uji visual di mode mobile (responsive viewport).
3. Jangan menyatakan pekerjaan selesai tanpa verifikasi nyata.
