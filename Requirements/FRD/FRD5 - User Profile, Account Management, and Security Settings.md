# Functional Requirement Document (FRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | FRD5 |
| **Traceable PRD** | PRD5 — User Profile, Account Management, and Security Settings |
| **Feature ID** | PROF-01, PROF-02, PROF-03, PROF-04, PROF-05, PROF-06 |
| **Feature Name** | User Profile, Account Management, and Security Settings |
| **Application** | Dhou (تطبيق ضوء) — Mobile Web (PWA) & iOS Native Wrapper (CapacitorJS) |
| **Module** | User Profile, Preferences & Account Lifecycle |
| **Sprint** | Sprint 5 |
| **Epic** | User Identity, Profile & Account Lifecycle |
| **Priority** | High (P1) |
| **Status** | Approved |
| **Implementation Status** | Planned |
| **Version** | 1.0 |
| **Last Updated** | 2026-11-09 |
| **Technical Author** | Fariz Mirza Abdillah |
| **Dependencies** | FRD1 (Auth & Session), Supabase Auth / GoTrue, PostgreSQL (`profiles` table), Web Crypto API |

---

## 1. Purpose, Sources, and Authority

Dokumen ini mendefinisikan spesifikasi fungsional dan teknis mendalam untuk modul **User Profile, Account Management, and Security Settings** pada aplikasi Dhou. Dokumen ini menjadi rujukan mutlak pengembang perangkat lunak dalam merealisasikan:
1. Layar ringkasan profil dengan status tier keanggotaan (`account_tier = 'premium'` default).
2. Mekanisme pembaruan identitas dasar (*display name* dan pemilihan avatar preset lokal).
3. Pembaruan kredensial kata sandi yang membedakan alur email/password dengan akun Google OAuth.
4. Pengaturan preferensi dialek dan pengingat belajar harian.
5. Alur keluar akun (*logout*) dan alur penghapusan akun mandiri (*self-service account deletion*) sesuai kepatuhan Apple App Store Review Guideline 5.1.1(v).

---

## 2. Scope and Source Reconciliation

### 2.1 Hubungan dengan Dokumen Sebelumnya
- **Keterikatan dengan PRD1 & FRD1**: Alur keluar akun (`FR-AUTH-06`) diintegrasikan secara mulus ke tombol Logout di tab Profil. Data `user_id` dari token sesi JWT menjadi kunci utama otorisasi baris pada tabel `profiles`.
- **Keterikatan dengan PRD2 (Gamified Path)**: Data metrik belajar (`total_xp` dan `streak_count`) disinkronkan secara konsisten di header profil.

### 2.2 Batasan Arsitektur
- **Avatar System**: Menggunakan 8 avatar ilustrasi karakter Dhou bertipe SVG/WebP lokal (`avatar_01` hingga `avatar_08`). Tidak memerlukan bucket media eksternal pada MVP.
- **Tipe Akun (Account Tier)**: Mendukung status `basic` dan `premium`. Pada rilis fase saat ini, seluruh akun baru dan migrasi akun lama otomatis memiliki nilai `premium`.

---

## 3. Actors and Authorization

| Aktor | Peran | Hak Akses (*Permissions*) | Mekanisme Otorisasi |
| :--- | :--- | :--- | :--- |
| **Authenticated Learner** | Pengguna terdaftar yang telah login | Membaca profil sendiri, mengubah nama/avatar, mengganti sandi sendiri, mengubah preferensi dialek, keluar akun, dan menghapus akun mandiri. | Token JWT Bearer (`auth.uid() = profiles.id`) via PostgreSQL Row Level Security (RLS). |
| **Unauthenticated / Guest** | Pengunjung belum login | Dilarang mengakses seluruh endpoint profil (`401 Unauthorized`). | Auth Guard di Router & API Middleware. |
| **System / Service Role** | Backend Supabase / Edge Functions | Eksekusi cascade hard-delete akun, audit log pembersihan data pengguna. | Supabase `service_role` secret key. |

---

## 4. Functional Requirements

### 4.1 US01 — Tampilan Profil & Status Akun (PROF-01)

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-PROF-01.1** | Endpoint profil mengembalikan data identitas pengguna: `id`, `email`, `display_name`, `avatar_id`, `account_tier`, `created_at`, serta agregat `streak_count` dan `total_xp`. | AC01.1; BR-PROF-01 |
| **FR-PROF-01.2** | Kolom `account_tier` bernilai `'premium'` secara default untuk seluruh akun aktif saat ini. Frontend merender badge visual emas/amber *"Premium Member"*. | AC01.1, AC01.2 |
| **FR-PROF-01.3** | Informasi waktu bergabung diformat ramah pengguna dalam Bahasa Indonesia (contoh: *"Bergabung sejak November 2026"*). | AC01.1 |
| **FR-PROF-01.4** | Komponen UI Profil menerapkan *Skeleton Loading state* saat request asinkron sedang berlangsung untuk mencegah layout shift. | AC01.3 |

### 4.2 US02 — Edit Profil Dasar & Pilihan Avatar (PROF-02)

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-PROF-02.1** | Sistem menyediakan modal pembaruan profil yang menerima field `display_name` (string) dan `avatar_id` (string). | AC02.1, AC02.2 |
| **FR-PROF-02.2** | Nilai `display_name` wajib melewati validasi: panjang 2–50 karakter, dipotong dari spasi berlebih (`.trim()`), dan disanitasi dari karakter berbahaya (`<`, `>`, `&`, `"`, `'`). | AC02.1; BR-PROF-04 |
| **FR-PROF-02.3** | Nilai `avatar_id` wajib termasuk dalam daftar enum preset valid (`avatar_01` s.d. `avatar_08`). Request dengan avatar ID tidak dikenal ditolak dengan `422 Unprocessable Entity`. | AC02.2; BR-PROF-05 |
| **FR-PROF-02.4** | Perubahan profil berhasil memperbarui record `profiles` dan memicu update state lokal di `AuthContext` secara real-time tanpa reload browser. | AC02.4 |

### 4.3 US03 — Ubah Kata Sandi Mandiri (PROF-03)

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-PROF-03.1** | Sistem memeriksa provider autentikasi akun pengguna (`identities.provider`). | AC03.1; BR-PROF-03 |
| **FR-PROF-03.2** | Jika akun murni berasal dari Google OAuth (tidak memiliki sandi lokal), sistem merender banner informatif dan menyediakan form *"Tetapkan Kata Sandi Baru"* tanpa mewajibkan input sandi lama. | AC03.1; BR-PROF-03 |
| **FR-PROF-03.3** | Untuk akun berbasis email/password lokal, sistem mewajibkan verifikasi `current_password` sebelum memproses `new_password`. Jika `current_password` salah, server merespons `403 Forbidden` *"Kata sandi saat ini tidak cocok."*. | AC03.4; BR-PROF-02 |
| **FR-PROF-03.4** | Nilai `new_password` wajib memiliki panjang minimal 8 karakter dan memuat kombinasi huruf dan angka. | AC03.1; BR-PROF-02 |
| **FR-PROF-03.5** | Sistem menolak jika `new_password` sama persis dengan `current_password` (`422 Unprocessable Entity`). | AC03.2 |

### 4.4 US04 — Pengaturan Preferensi Belajar & Dialek Utama (PROF-04)

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-PROF-04.1** | Pengguna dapat memilih dialek target utama dari nilai valid: `'fusha'`, `'egyptian'`, atau `'levantine'`. Nilai disimpan di kolom `target_dialect` tabel `profiles`. | AC04.1; BR-PROF-06 |
| **FR-PROF-04.2** | Perubahan `target_dialect` otomatis menyinkronkan selektor dialek di `TopBar` dan kurikulum video/latihan tanpa memerlukan navigasi manual. | AC04.1 |
| **FR-PROF-04.3** | Pengguna dapat mengatur sakelar `daily_reminder_enabled` (boolean) dan waktu pengingat `daily_reminder_time` (format HH:MM, default '19:30'). | AC04.2 |

### 4.5 US05 — Manajemen Sesi & Keluar Akun (PROF-05)

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-PROF-05.1** | Opsi keluar akun memicu modal konfirmasi dengan opsi *"Batal"* dan *"Ya, Keluar"*. | AC05.1 |
| **FR-PROF-05.2** | Konfirmasi memanggil SDK `supabase.auth.signOut()` yang menghancurkan refresh token di auth server. | AC05.2 |
| **FR-PROF-05.3** | Frontend membersihkan seluruh key sesi lokal (`localStorage` dan `sessionStorage`), mereset state global, dan melakukan pengalihan (*redirect*) ke layar masuk. | AC05.2; FR-AUTH-06.2 |

### 4.6 US06 — Penghapusan Akun Mandiri (PROF-06 — App Store Compliance)

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-PROF-06.1** | Layar Hapus Akun menyediakan alur verifikasi dua tahap (*two-tier confirmation*). Tahap kedua mewajibkan input teks konfirmasi persis `"HAPUS AKUN SAYA"`. | AC06.2; BR-PROF-07 |
| **FR-PROF-06.2** | Jika teks konfirmasi tidak sesuai, tombol submit tetap nonaktif (`disabled`). | AC06.2 |
| **FR-PROF-06.3** | Permintaan hapus akun memanggil endpoint RPC aman `delete_user_account()`. Endpoint memverifikasi identitas pengguna via token JWT. | AC06.3; AC06.1 |
| **FR-PROF-06.4** | Penghapusan akun mengeksekusi *cascade delete* pada data profil, progres belajar, riwayat obrolan AI, dan identitas auth di Supabase. Catatan ringkas tanpa PII disimpan di tabel `account_deletion_audit`. | AC06.3 |
| **FR-PROF-06.5** | Sesi pengguna langsung diputus dan diarahkan ke layar pendaftaran awal dengan notifikasi pamit. | AC06.4 |

---

## 5. Concrete API Design

### 5.1 Shared Conventions
- **Base URL**: `/api/v1/profile`
- **Security / Headers**:
  - `Content-Type: application/json`
  - `Authorization: Bearer <accessToken>`
- **Format Respons Sukses**:
  ```json
  {
    "success": true,
    "data": { ... },
    "message": "Deskripsi singkat hasil aksi"
  }
  ```
- **Format Respons Error**:
  ```json
  {
    "success": false,
    "error": {
      "code": "ERROR_CODE",
      "message": "Pesan ramah pengguna",
      "details": null
    }
  }
  ```

---

### 5.2 Endpoint 1: Ambil Profil & Status Akun
- **Route**: `GET /api/v1/profile/me`
- **Otorisasi**: Wajib Bearer Token.
- **Response 200 OK**:
  ```json
  {
    "success": true,
    "data": {
      "id": "e4b1c7a2-99f1-4e78-9844-0cfa0bfa92c1",
      "email": "ahmad.dhou@example.com",
      "display_name": "Ahmad Fauzi",
      "avatar_id": "avatar_01",
      "account_tier": "premium",
      "target_dialect": "fusha",
      "daily_reminder_enabled": true,
      "daily_reminder_time": "19:30",
      "auth_provider": "email",
      "created_at": "2026-11-01T10:00:00Z",
      "stats": {
        "streak_count": 7,
        "total_xp": 1450,
        "words_reviewed": 128
      }
    },
    "message": "Profil berhasil dimuat"
  }
  ```

---

### 5.3 Endpoint 2: Perbarui Nama & Avatar
- **Route**: `PATCH /api/v1/profile/me`
- **Otorisasi**: Wajib Bearer Token.
- **Request Body**:
  ```json
  {
    "display_name": "Ahmad Fauzi Al-Azhari",
    "avatar_id": "avatar_03"
  }
  ```
- **Response 200 OK**:
  ```json
  {
    "success": true,
    "data": {
      "display_name": "Ahmad Fauzi Al-Azhari",
      "avatar_id": "avatar_03",
      "updated_at": "2026-11-09T14:30:00Z"
    },
    "message": "Profil berhasil diperbarui"
  }
  ```
- **Response 422 Unprocessable Entity**:
  ```json
  {
    "success": false,
    "error": {
      "code": "VAL_INVALID_INPUT",
      "message": "Nama tampilan harus terdiri dari 2 hingga 50 karakter."
    }
  }
  ```

---

### 5.4 Endpoint 3: Ubah Kata Sandi Mandiri
- **Route**: `PUT /api/v1/profile/password`
- **Otorisasi**: Wajib Bearer Token.
- **Request Body (Akun Email/Password)**:
  ```json
  {
    "current_password": "OldPassword123",
    "new_password": "NewSecurePassword456",
    "confirm_new_password": "NewSecurePassword456"
  }
  ```
- **Request Body (Akun Google OAuth yang membuat kata sandi pertama kali)**:
  ```json
  {
    "current_password": null,
    "new_password": "NewSecurePassword456",
    "confirm_new_password": "NewSecurePassword456"
  }
  ```
- **Response 200 OK**:
  ```json
  {
    "success": true,
    "data": {
      "password_updated": true,
      "updated_at": "2026-11-09T14:32:00Z"
    },
    "message": "Kata sandi Anda berhasil diperbarui"
  }
  ```
- **Response 403 Forbidden**:
  ```json
  {
    "success": false,
    "error": {
      "code": "AUTH_INCORRECT_CURRENT_PASSWORD",
      "message": "Kata sandi saat ini yang Anda masukkan salah."
    }
  }
  ```

---

### 5.5 Endpoint 4: Perbarui Preferensi Belajar & Dialek
- **Route**: `PATCH /api/v1/profile/preferences`
- **Otorisasi**: Wajib Bearer Token.
- **Request Body**:
  ```json
  {
    "target_dialect": "egyptian",
    "daily_reminder_enabled": true,
    "daily_reminder_time": "20:00"
  }
  ```
- **Response 200 OK**:
  ```json
  {
    "success": true,
    "data": {
      "target_dialect": "egyptian",
      "daily_reminder_enabled": true,
      "daily_reminder_time": "20:00"
    },
    "message": "Preferensi belajar berhasil disimpan"
  }
  ```

---

### 5.6 Endpoint 5: Hapus Akun Mandiri (App Store Compliance)
- **Route**: `POST /api/v1/profile/delete-account`
- **Otorisasi**: Wajib Bearer Token.
- **Request Body**:
  ```json
  {
    "confirmation_text": "HAPUS AKUN SAYA"
  }
  ```
- **Response 200 OK**:
  ```json
  {
    "success": true,
    "data": {
      "account_deleted": true,
      "deleted_at": "2026-11-09T14:35:00Z"
    },
    "message": "Akun dan data Anda telah dihapus secara permanen."
  }
  ```
- **Response 400 Bad Request**:
  ```json
  {
    "success": false,
    "error": {
      "code": "VAL_CONFIRMATION_MISMATCH",
      "message": "Teks konfirmasi tidak sesuai. Harap ketik persis 'HAPUS AKUN SAYA'."
    }
  }
  ```

---

## 6. Persistence and Ownership

### 6.1 Module Ownership & Boundary
- **ProfileModule** memiliki tabel `profiles` dan tabel audit `account_deletion_audit`.
- Modul ini berinteraksi dengan `auth.users` Supabase melalui RPC terproteksi untuk operasi kata sandi dan penghapusan akun.

### 6.2 Skema Tabel Database (PostgreSQL DDL)

```sql
-- 1. Tipe Enum untuk Variasi Dialek & Tier Akun
DO $$ BEGIN
    CREATE TYPE user_account_tier AS ENUM ('basic', 'premium');
EXCEPTION
    WHEN duplicate_object THEN NULL;
END $$;

DO $$ BEGIN
    CREATE TYPE target_dialect_type AS ENUM ('fusha', 'egyptian', 'levantine');
EXCEPTION
    WHEN duplicate_object THEN NULL;
END $$;

-- 2. Tabel Profil Pengguna (Profiles)
CREATE TABLE IF NOT EXISTS public.profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    display_name VARCHAR(100) NOT NULL,
    avatar_id VARCHAR(50) NOT NULL DEFAULT 'avatar_01',
    account_tier user_account_tier NOT NULL DEFAULT 'premium', -- Seluruh akun saat ini otomatis premium
    target_dialect target_dialect_type NOT NULL DEFAULT 'fusha',
    daily_reminder_enabled BOOLEAN NOT NULL DEFAULT true,
    daily_reminder_time TIME NOT NULL DEFAULT '19:30:00',
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX IF NOT EXISTS idx_profiles_account_tier ON public.profiles(account_tier);

-- 3. Row Level Security (RLS) pada Tabel Profiles
ALTER TABLE public.profiles ENABLE ROW LEVEL SECURITY;

-- Pengguna hanya dapat membaca profil miliknya
CREATE POLICY "Users can view own profile" 
ON public.profiles FOR SELECT 
USING (auth.uid() = id);

-- Pengguna hanya dapat mengupdate profil miliknya
CREATE POLICY "Users can update own profile" 
ON public.profiles FOR UPDATE 
USING (auth.uid() = id)
WITH CHECK (auth.uid() = id);

-- 4. Trigger Otomatis: Buat Profil Baru saat Pengguna Mendaftar di Supabase Auth
CREATE OR REPLACE FUNCTION public.handle_new_user_profile()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO public.profiles (
        id, 
        display_name, 
        avatar_id, 
        account_tier, 
        target_dialect
    )
    VALUES (
        NEW.id,
        COALESCE(NEW.raw_user_meta_data->>'full_name', NEW.raw_user_meta_data->>'name', 'Pelajar Dhou'),
        'avatar_01',
        'premium', -- Default premium untuk semua akun fase rilis saat ini
        'fusha'
    );
    RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

DROP TRIGGER IF EXISTS on_auth_user_created_profile ON auth.users;
CREATE TRIGGER on_auth_user_created_profile
    AFTER INSERT ON auth.users
    FOR EACH ROW EXECUTE FUNCTION public.handle_new_user_profile();

-- 5. Tabel Audit Penghapusan Akun (Tanpa Data PII)
CREATE TABLE IF NOT EXISTS public.account_deletion_audit (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    deleted_user_id UUID NOT NULL,
    deletion_reason VARCHAR(255) DEFAULT 'Self-service in-app deletion',
    deleted_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- 6. Stored Procedure RPC: Hapus Akun Mandiri (App Store 5.1.1v)
CREATE OR REPLACE FUNCTION public.delete_user_account(confirm_text TEXT)
RETURNS BOOLEAN AS $$
DECLARE
    current_user_id UUID := auth.uid();
BEGIN
    IF current_user_id IS NULL THEN
        RAISE EXCEPTION 'Akses ditolak: Pengguna tidak terotentikasi.';
    END IF;

    IF confirm_text <> 'HAPUS AKUN SAYA' THEN
        RAISE EXCEPTION 'Konfirmasi tidak valid. Harap ketik teks persis.';
    END IF;

    -- Catat audit
    INSERT INTO public.account_deletion_audit (deleted_user_id)
    VALUES (current_user_id);

    -- Hapus entri auth.users (akan cascade ke profiles dan data belajar)
    DELETE FROM auth.users WHERE id = current_user_id;

    RETURN TRUE;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

---

## 7. Telemetry, Audit, and Recovery

### 7.1 Telemetri Produk Frontend
| Event Name | Trigger | Payload Properties | Tujuan Analisis |
| :--- | :--- | :--- | :--- |
| `profile_viewed` | Membuka tab Profil | `{ account_tier, target_dialect }` | Mengukur frekuensi kunjungan halaman profil. |
| `profile_updated` | Berhasil ubah nama/avatar | `{ avatar_id, name_length }` | Mengukur keterlibatan personalisasi akun. |
| `password_changed` | Berhasil ubah kata sandi | `{ auth_provider, timestamp }` | Audit keamanan kredensial. |
| `preferences_saved` | Ubah dialek atau reminder | `{ target_dialect, daily_reminder_enabled }` | Memantau sebaran dialek terpopuler. |
| `account_deletion_initiated` | Mengetuk menu hapus akun | `{ step: 1 \| 2 }` | Menganalisis drop-off pada alur hapus akun. |
| `account_deleted` | Sukses hapus akun | `{ user_id_hash, timestamp }` | Metrik churn dan kepatuhan privasi. |

---

## 8. Acceptance and Verification Plan

| ID Pengujian | Skenario Uji | Hasil yang Diharapkan | Trace FR |
| :--- | :--- | :--- | :--- |
| **T-PROF-01** | Pengguna membuka tab Profil saat sesi aktif | Kartu profil menampilkan nama, email, avatar, dan badge **"Premium Member"**. | FR-PROF-01.1, 01.2 |
| **T-PROF-02** | Pengguna baru terdaftar di sistem | Record di tabel `profiles` otomatis dibuat dengan `account_tier = 'premium'`. | FR-PROF-01.2 |
| **T-PROF-03** | Pengguna mengganti nama menjadi "Khalid" dan memilih avatar `avatar_04` | Perubahan tersimpan ke database, nama di TopBar dan chat langsung berubah. | FR-PROF-02.1–02.4 |
| **T-PROF-04** | Pengguna memasukkan nama kosong atau $> 50$ karakter | Tombol simpan dinonaktifkan / menampilkan pesan validasi ramah. | FR-PROF-02.2 |
| **T-PROF-05** | Pengguna email/password mengubah sandi dengan sandi lama yang salah | Server menolak dengan `403 Forbidden` dan pesan error ramah. | FR-PROF-03.3 |
| **T-PROF-06** | Pengguna memasukkan kata sandi baru yang sama dengan sandi lama | Validasi menolak dengan pesan *"Sandi baru tidak boleh sama"*. | FR-PROF-03.5 |
| **T-PROF-07** | Pengguna masuk via Google OAuth membuka menu Ubah Sandi | Ditampilkan banner informasi Google SSO, tidak diminta memasukkan sandi lama. | FR-PROF-03.2 |
| **T-PROF-08** | Pengguna mengubah dialek utama ke 'egyptian' | Selektor TopBar dan rekomendasi video otomatis bergeser ke dialek Mesir. | FR-PROF-04.1, 04.2 |
| **T-PROF-09** | Pengguna menekan Keluar Akun dan konfirmasi | Sesi lokal dibersihkan, refresh token dibatalkan, redirect ke halaman Masuk. | FR-PROF-05.1–05.3 |
| **T-PROF-10** | Pengguna mengetik 'HAPUS AKUN SAYA' pada modal hapus akun | Data auth dan profil terhapus bersih, sesi terputus, diarahkan ke onboarding. | FR-PROF-06.1–06.5 |

---

## 9. Approval and Remaining Work

- **Status Persetujuan**: Disetujui untuk sprint implementasi antarmuka profil Dhou.
- **Pekerjaan Lanjutan Terkait**:
  1. Penyiapan aset grafis SVG 8 preset avatar di direktori aset web/Capacitor.
  2. Implementasi komponen UI React/Next.js `ProfileView.tsx` dan subkomponen modal dialognya.
  3. Integrasi Supabase RPC `delete_user_account` pada migration script database.

---

## 10. Lampiran Teknis Tambahan

### 10.1 Spesifikasi Preset Avatar Karakter Dhou
Untuk menjaga performa dan mematuhi prinsip hemat beban komputasi:
- `avatar_01`: Karakter Pelajar Pria Fusha (Ikon Thobe/Keffiyeh ceria)
- `avatar_02`: Karakter Pelajar Wanita Fusha (Ikon Hijab modern warna emerald)
- `avatar_03`: Karakter Traveler Kairo (Ikon Dialek Mesir)
- `avatar_04`: Karakter Pelajar Damaskus (Ikon Dialek Syam)
- `avatar_05` s.d. `avatar_08`: Varian ekspresi maskot Dhou (Burung Bulbul Arab / Lentera Dhou).

### 10.2 Standar Tipografi & Safe Area Mobile
- Komponen profil menggunakan **Fredoka** untuk label numerik (XP, Streak) dan teks Latin, serta **Amiri** (line-height minimal 1.6) untuk nama atau frasa Arab.
- Padding atas dan bawah wajib membungkus area notch dan dynamic island:
  ```css
  padding-top: calc(16px + env(safe-area-inset-top));
  padding-bottom: calc(80px + env(safe-area-inset-bottom));
  ```
