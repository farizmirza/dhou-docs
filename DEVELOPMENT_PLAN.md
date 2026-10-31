# Dhou: Development Guideline & Action Plan
**Project:** Dhou (تطبيق ضوء) — Arabic Language Learning (Fusha & 'Ammiyyah)  
**Methodology:** Design-Driven Development (DDD) & Vertical Slice Architecture  
**Target Output:** Mobile PWA (Progressive Web App) & Native iOS Packaging (CapacitorJS for App Store)

---

## 1. Project Overview & Core Philosophy

Mengacu pada paper riset *Dhou*, aplikasi ini memecahkan masalah kesenjangan diglosia Arab (jarak antara kemampuan *fusha* formal dan *'ammiyyah* percakapan sehari-hari). Pemetaan lengkap seluruh tabel dan fungsi pedagogis dari paper terdokumentasi di [`docs/RESEARCH_REFERENCE_MAPPING.md`](file:///Users/farizmirza/Documents/Project/dhou/docs/RESEARCH_REFERENCE_MAPPING.md).

### Pedagogical Learning Loop
$$\text{Video Learning (Input)} \longrightarrow \text{Gamified Path (Practice)} \longrightarrow \text{AI Conversation (Production)} \longrightarrow \text{Review (Retention)}$$
$$\text{Exposure} \rightarrow \text{Noticing} \rightarrow \text{Recognition} \rightarrow \text{Retrieval} \rightarrow \text{Controlled Production} \rightarrow \text{Communicative Production} \rightarrow \text{Feedback} \rightarrow \text{Review}$$

### Prinsip Design-Driven Development (DDD) untuk UI/UX Designer
1. **Design Tokens First:** Token desain (warna, tipografi bilingual Latin-Arab, elevasi, spacing, variasi dialek) didefinisikan di awal sebagai fondasi CSS.
2. **Interactive Shell First:** Membangun kerangka visual mobile-first (viewport smartphone, safe area, navigation) sebelum menyentuh logika rumit.
3. **Mock Data Before Backend:** Logika UI, micro-animations, dan audio player dimatangkan dengan data JSON statis terlebih dahulu sebelum menghubungkan API/Supabase.
4. **Surgical & Vertical Implementation:** Setiap fitur dikembangkan secara tuntas (UI $\rightarrow$ Interaksi $\rightarrow$ State $\rightarrow$ Integrasi) per modul.
5. **Vibe Coding Standards (Clean & Maintainable):** Mengikuti panduan modularitas di [`docs/vibe-coding-guidelines.md`](file:///Users/farizmirza/Documents/Project/dhou/docs/vibe-coding-guidelines.md). Kode tetap rapi, komponen kecil (100–150 baris), nama variabel deskriptif, dan TypeScript strict.
6. **Git Protection Rule:** **TIDAK ADA** `git commit`, `git push`, atau `git pull` tanpa instruksi eksplisit dari prompt pengguna. Semua perubahan kode lokal dipertahankan sampai diminta di-commit.

---

## 2. Technical Stack Specification

| Area | Pilihan Teknologi | Deskripsi & Peran |
| :--- | :--- | :--- |
| **Framework** | Next.js (App Router, TypeScript) | Struktur halaman modern, performa tinggi, SSR/SSG untuk PWA. |
| **Styling & UI Kit** | Tailwind CSS + shadcn/ui + Framer Motion | Desain berbasis utilitas, komponen siap pakai, micro-interactions halus ala Duolingo. |
| **PWA & Native Bridge**| `@ducanh2912/next-pwa` + CapacitorJS | Dukungan instalasi PWA di mobile dan packaging ke project Xcode iOS (App Store). |
| **Bilingual Typography** | Inter / Geist (Latin) + Amiri / Noto Sans Arabic | Dukungan teks bahasa Arab dengan harakat dan perataan RTL (*Right-to-Left*). |
| **Backend & Database** | Supabase (PostgreSQL, Auth, Storage) | Database pengguna/pelajaran, autentikasi, dan hosting aset audio/video. |
| **Audio Engine** | Web Audio API / MediaRecorder + Whisper API | Pemutaran native, rekaman latihan shadowing, STT (Speech-to-Text). |
| **Conversational AI** | Gemini 1.5 Flash API / OpenAI GPT-4o-mini | Engine obrolan interaktif dengan *variety consistency guardrails*. |

---

## 3. Tahapan Action Plan & Deliverables Berkas Guideline

Rencana kerja persiapan berkas guideline dibagi menjadi 4 tahap:

```text
Tahap 1: Setup Berkas Fondasi Design System & Atribut Dialek
Tahap 2: Spesifikasi Arsitektur Informasi & Interaksi (4 Pilar)
Tahap 3: Konfigurasi Teknis PWA & Spesifikasi iOS Packaging
Tahap 4: Inisialisasi Repositori & Boilerplate Project
```

### Tahap 1: Berkas Design System & Design Tokens (`docs/design-system.md`)
*Tujuan: Standarisasi elemen visual sebelum menulis kode komponen.*
- [ ] **Color Palette & Variety Identity:**
  - Primary / Brand Accent (e.g., Emerald/Teal cerah untuk nuansa Timur Tengah modern).
  - Background, Surface, Card, Border, Muted colors.
  - **Dialect Badges:** Warna pembeda jelas untuk **Fusha (Standard)**, **Egyptian ('Ammiyyah Mesir)**, dan **Levantine ('Ammiyyah Syam)**.
- [ ] **Typography & Arabic Handling:**
  - Konfigurasi font Latin vs font Arab (Amiri/Noto Sans Arabic).
  - Skala ukuran font, line-height (font Arab membutuhkan line-height lebih tinggi untuk harakat).
  - Arah teks: LTR untuk instruksi umum, RTL untuk kartu materi Arab.
- [ ] **Audio & Gamification Tokens:**
  - Suara feedback: Success chime, Error buzzer, Click pop.
  - Haptic feedback pattern (Success: light vibration, Error: warning buzz).
  - Hearts / Lives indicator, Streak flame, XP badge.

### Tahap 2: Spesifikasi 4 Modul Inti (`docs/features-spec.md`)
*Tujuan: Menjelaskan alur interaksi per tab (sesuai paper Dhou).*
- [ ] **Tab 1: Video Learning (Contextual Input)**
  - Tampilan card video pendek dengan label variasi dialek.
  - Video player controller: Subtitle Arab on/off, toggle terjemahan, kecepatan playback (0.75x, 1x).
  - Interaksi "Sentence Replay", "Shadowing (Record & Compare)", dan "Save to Review".
- [ ] **Tab 2: Gamified Learn (Structured Practice)**
  - Path pohon level (Nodes ala Duolingo).
  - Exercise Engine types:
    1. *Word-Meaning Matching*
    2. *Fill-in-the-Blank*
    3. *Sentence Scramble / Arrangement*
    4. *Listen & Pick*
- [ ] **Tab 3: Conversational AI (Production Practice)**
  - Dialect selector sebelum mulai (Fusha / Egyptian / Levantine).
  - Chat interface dengan tombol mikrofon (input suara / teks).
  - Prompt Guardrails: Menjaga AI agar tidak mencampur dialek (*unmarked code-switching*).
  - Fitur koreksi instan (feedback gramatikal/idiom).
- [ ] **Tab 4: Review (Reinforcement / SRS)**
  - Daftar kosakata & frasa yang disimpan dari video atau salah saat kuis.
  - Mode flashcard / kuis kilat untuk pengulangan berkala.

### Tahap 3: Konfigurasi PWA & App Store Guideline (`docs/pwa-appstore.md`)
*Tujuan: Memastikan standar PWA dan kepatuhan review Apple App Store sejak awal.*
- [ ] **PWA Manifest:** Setup `manifest.json` (name, short_name, icons 192x192 & 512x512, background_color, theme_color, display: "standalone").
- [ ] **Mobile Frame & Safe Areas:** Standar viewport CSS `env(safe-area-inset-top)` & `env(safe-area-inset-bottom)` agar rapi di iPhone dengan Notch / Dynamic Island.
- [ ] **App Store Preparation (CapacitorJS):**
  - Setup plugin Capacitor (Haptics, Microphone permission, Status Bar).
  - Checklist guideline Apple Review: Pastikan aplikasi terasa seperti aplikasi native (gesture fluid, offline caching dasar, izin mic yang jelas).

### Tahap 4: Roadmap Eksekusi Kode (Surgical Implementation)
- [ ] Inisialisasi Next.js 14/15 + Tailwind CSS di workspace.
- [ ] Setup folder `docs/` berisi 3 berkas spesifikasi di atas.
- [ ] Implementasi Mobile Layout Shell & Bottom Navigation.

---

## 4. Hasil yang Diharapkan (Definition of Done)
1. Tim/Anda memiliki dokumentasi desain & arsitektur teknis yang jelas sebelum mulai mengetik kode fitur.
2. Setiap kali membuat komponen baru, Anda cukup merujuk ke berkas guideline tanpa kebingungan menentukan warna, font, maupun alur logika.
