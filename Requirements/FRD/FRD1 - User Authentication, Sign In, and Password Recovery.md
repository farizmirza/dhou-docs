# Functional Requirements Document (FRD)

## Document Information

| Item | Detail |
| :--- | :--- |
| **Document ID** | FRD1 |
| **Source PRD** | [PRD1 — User Authentication, Sign In, and Password Recovery, Version 2.0, Approved](../PRD/PRD1%20-%20User%20Authentication,%20Sign%20In,%20and%20Password%20Recovery.md) |
| **Feature ID** | AUTH-L-01, AUTH-L-02, AUTH-L-03, AUTH-L-04, AUTH-L-05 |
| **Application** | Dhou (تطبيق ضوء) — Mobile Web (PWA) & iOS Native Wrapper (CapacitorJS) |
| **Module** | Authentication & Account Security |
| **Sprint** | Sprint 1 |
| **Status** | Approved — disetujui sebagai kontrak implementasi |
| **Approved On** | 2026-10-31 |
| **Implementation Status** | Specification only — implementasi belum diverifikasi |
| **Version** | 2.0 |
| **Last Updated** | 2026-10-31 |
| **Owner** | Product / Lead Engineering Dhou |
| **Target Boundary Version** | `services/auth-api` v1.0.0, `apps/dhou-mobile` v1.0.0 |
| **Dependencies** | PRD1, Supabase GoTrue Auth Service, Google Cloud OAuth 2.0 Identity Services, CapacitorJS Core |

---

## 1. Purpose, Sources, and Authority

Dokumen ini menerjemahkan kebutuhan produk dari [PRD1 — User Authentication, Sign In, and Password Recovery](../PRD/PRD1%20-%20User%20Authentication,%20Sign%20In,%20and%20Password%20Recovery.md) menjadi spesifikasi fungsional konkret, kontrak API definitif, skema persistensi data PostgreSQL, penanganan kasus ekstrem (*edge cases*), serta rencana pengujian verifikasi yang mengikat bagi tim Frontend dan Backend.

PRD1 memegang otoritas atas alur bisnis dan intensi produk (*product intent*). FRD1 ini mengikat kontrak implementasi teknis fitur Autentikasi dan Manajemen Identitas pada Sprint 1.

| Sumber | Baseline yang Dibaca | Penggunaan |
| :--- | :--- | :--- |
| [PRD1](../PRD/PRD1%20-%20User%20Authentication,%20Sign%20In,%20and%20Password%20Recovery.md) | Versi 2.0, Approved | US01 s/d US06, Acceptance Criteria, Business Rules, UI Specs |
| [design-system.md](../../design-system.md) | Versi 1.0, Approved | Token warna (`#FF8904`, `#ED7500`), tipografi (Fredoka, Poppins), tombol 3D |
| [vibe-coding-guidelines.md](../../vibe-coding-guidelines.md) | Versi 1.0, Approved | Batasan komponen modular, penanganan RTL bahasa Arab, safe areas |
| [Supabase Auth Architecture](https://supabase.com/docs) | v2.0 Client SDK | JWT token rotation, PKCE OAuth flow, RLS policies |
| [Capacitor Security Guidelines](https://capacitorjs.com) | v6.0 Core | Penanganan native OAuth tanpa embedded WebView |

---

## 2. Scope and Source Reconciliation

### 2.1 Cakupan Fitur
FRD ini mencakup implementasi end-to-end fitur Autentikasi Dhou:
1. **Masuk Email & Sandi (US01)**: Input email, kata sandi, toggle intip sandi, checkbox Remember Me, proteksi brute-force lockout 5 menit setelah 5x gagal, dan anti-user-enumeration.
2. **Google Single Sign-On (US02)**: Login/daftar 1-klik via Google OAuth PKCE di web browser dan Native Google Plugin di iOS Capacitor, auto-verifikasi email, sinkronisasi avatar profil, dan penautan akun cerdas (*account linking*).
3. **Pendaftaran Akun Baru (US03)**: Validasi multi-field real-time, meter kekuatan sandi 3 tingkat, deteksi cerdas salah ketik domain (@gmail.com), dan persetujuan Ketentuan Layanan.
4. **Pemulihan Kata Sandi Mandiri (US04 & US05)**: Permintaan email reset sandi, penanganan khusus akun Google SSO, token URL 15 menit (single-use), tolak kata sandi lama, dan limitasi cooldown kirim ulang 60 detik.
5. **Keluar Akun / Logout (US06)**: Dialog konfirmasi, pemutusan token JWT di server, dan pembersihan menyeluruh kredensial lokal dari perangkat.

### 2.2 Hasil Rekonsiliasi Sumber & Keputusan Teknis
- **S1-FRD-AUTH-001 (Peniadaan Guest Mode)**: Sesuai arahan efisiensi, Guest Mode ditiadakan. Sistem menerapkan *Auth Gate* wajib di awal agar seluruh streak 🔥 dan XP pengguna langsung terikat ke akun cloud.
- **S1-FRD-AUTH-002 (Eliminasi Apple ID)**: Metode masuk difokuskan secara solid pada dua saluran: Email/Kata Sandi dan Google SSO.
- **S1-FRD-AUTH-003 (Solusi Error 403 Google WebView di iOS)**: Google melarang OAuth pada embedded WebView (`disallowed_useragent`). Pada build Capacitor iOS, sistem wajib menggunakan `@codetrix-studio/capacitor-google-auth` atau browser eksternal `ASWebAuthenticationSession`.
- **S1-FRD-AUTH-004 (Single-Use & 15-Min Lifespan Token Reset)**: Token pemulihan kata sandi kedaluwarsa dalam 15 menit dan langsung dihanguskan seketika setelah dipakai untuk mencegah serangan replay.

### 2.3 Requirement Traceability

| Source Story / Rule Trace | FRD ID | Operation IDs | Frontend Integration | Verification Scenarios |
| :--- | :--- | :--- | :--- | :--- |
| US01; AC01.1–06; BR-AUTH-01, 02, 06 | FR-AUTH-01 | `auth_login_password` | §5.4 Form Masuk & Alert Lockout | T-AUTH-01, 02, 03 |
| US02; AC02.1–05; BR-AUTH-03, 04 | FR-AUTH-02 | `auth_google_signin`, `auth_google_callback` | §5.4 Tombol Google SSO | T-AUTH-04, 05, 06 |
| US03; AC03.1–05; BR-AUTH-02, 05 | FR-AUTH-03 | `auth_register` | §5.4 Form Daftar & Typo Banner | T-AUTH-07, 08, 09 |
| US04; AC04.1–04; BR-AUTH-07, 08 | FR-AUTH-04 | `auth_forgot_password` | §5.4 Form Lupa Sandi & Cooldown | T-AUTH-10, 11 |
| US05; AC05.1–04; BR-AUTH-05, 07 | FR-AUTH-05 | `auth_reset_password` | §5.4 Form Buat Kata Sandi Baru | T-AUTH-12, 13 |
| US06; AC06.1–03 | FR-AUTH-06 | `auth_logout` | §5.4 Menu Profil & Dialog Logout | T-AUTH-14 |

---

## 3. Actors and Authorization

| Aktor | Read Access | Mutation Access | Tenant & Security Scope |
| :--- | :--- | :--- | :--- |
| **Unauthenticated User (Guest)** | Halaman login, register, lupa sandi, reset password | Mengirim form masuk, daftar, request reset, eksekusi reset sandi | Public rate-limited endpoint (IP-based throttling). |
| **Authenticated Student / User** | Profil diri, riwayat streak, XP, materi belajar Dhou | Mengubah profil, mengganti kata sandi, logout | Terikat ketat pada `user_id` dari token JWT. RLS database PostgreSQL membatasi akses data. |
| **Dhou System / Scheduler** | Audit log autentikasi, status token | Mengubah status lockout kadaluarsa, menghanguskan token lama | System-level authority. |

Backend mewajibkan header `Authorization: Bearer <accessToken>` untuk seluruh endpoint terproteksi `/api/v1/user/*`. Token yang tidak valid atau kedaluwarsa menghasilkan `401 Unauthorized`.

---

## 4. Functional Requirements

### 4.1 US01 — Masuk dengan Email & Kata Sandi

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-AUTH-01.1** | Sistem menyediakan form masuk dengan input email dan password. Field email otomatis dilakukan normalisasi `.trim().toLowerCase()`. | AC01.1; VAL-AUTH-01 |
| **FR-AUTH-01.2** | Tombol toggle visibilitas sandi mengubah input antara `type="password"` dan `type="text"` tanpa mereset fokus kursor. | AC01.2 |
| **FR-AUTH-01.3** | Kredensial valid menghasilkan token akses JWT dan refresh token. Jika Remember Me aktif, sesi disimpan ke `localStorage`; jika tidak, disimpan ke `sessionStorage`. | AC01.3 |
| **FR-AUTH-01.4** | Kegagalan verifikasi email/password menghasilkan respons generic `401 Unauthorized` dengan pesan *"Email atau kata sandi yang Anda masukkan salah."* (*anti-user-enumeration*). Nilai input email tidak dihapus. | AC01.4; BR-AUTH-02 |
| **FR-AUTH-01.5** | Sistem melacak counter percobaan salah. Jika mencapai 5 kali berturut-turut pada akun yang sama, sistem mengaktifkan lockout 300 detik (5 menit). Endpoint menolak request dengan `429 Too Many Requests` dan frontend merender countdown timer. | AC01.5; BR-AUTH-06 |
| **FR-AUTH-01.6** | Selama request verifikasi berlangsung, tombol submit dinonaktifkan (`disabled`) dan menampilkan status spinner animasi (*anti double-submit*). | AC01.6 |

### 4.2 US02 — Masuk & Registrasi Cepat via Google SSO

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-AUTH-02.1** | Frontend menyediakan tombol resmi Google dengan SVG multicolor. Mengklik tombol memicu alur otorisasi OAuth 2.0 PKCE dengan parameter `prompt: 'select_account'`. | AC02.1; UI 8.2.4 |
| **FR-AUTH-02.2** | Pada platform iOS Native Capacitor, sistem mendeteksi runtime via `Capacitor.isNativePlatform()` dan mengalihkan alur ke `@codetrix-studio/capacitor-google-auth` untuk mencegah error 403 `disallowed_useragent`. | AC02.4; S1-FRD-AUTH-003 |
| **FR-AUTH-02.3** | Backend mengekstrak `email`, `full_name`, dan `avatar_url` dari klaim token Google. Akun otomatis memiliki flag `email_verified = true`. | AC02.3; BR-AUTH-03 |
| **FR-AUTH-02.4** | *Account Linking*: Jika email dari provider Google sudah terdaftar di database via registrasi email lokal, backend otomatis menautkan identitas Google ke user record yang ada tanpa membuat record duplikat. | AC02.2; BR-AUTH-04 |
| **FR-AUTH-02.5** | Jika pengguna menutup window popup Google sebelum otorisasi selesai (`popup_closed_by_user`), state loading di-reset ke kondisi normal tanpa menampilkan alert error. | AC02.5; EC-G02 |

### 4.3 US03 — Pendaftaran Akun Baru & Validasi Cerdas

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-AUTH-03.1** | Form registrasi mewajibkan input Nama Panggilan (2–50 char, alfabet Latin/Arab), Email berformat valid, Kata Sandi, Konfirmasi Kata Sandi, dan Checkbox persetujuan Ketentuan Layanan. | AC03.1; VAL-AUTH-01 |
| **FR-AUTH-03.2** | Kolom kata sandi dilengkapi kalkulator kekuatan real-time (kombinasi panjang, huruf besar, huruf kecil, dan angka) dengan visual bar 3 warna. | AC03.2; BR-AUTH-05 |
| **FR-AUTH-03.3** | Kolom email menerapkan utilitas `detectDomainTypo(email)`. Jika ditemukan typo domain populer (misal `@gmai.com`, `@yaho.co`), sistem merender banner saran pintar yang dapat diklik pengguna. | AC03.3 |
| **FR-AUTH-03.4** | Jika email sudah terdaftar, backend merespons dengan `409 Conflict`. Frontend menampilkan modal ramah berisi opsi pintas *"Masuk ke Akun"* atau *"Reset Kata Sandi"*. | AC03.4; ERR-AUTH-03 |

### 4.4 US04 & US05 — Pemulihan Kata Sandi Mandiri

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-AUTH-04.1** | Form lupa kata sandi menerima input email. Jika email terdaftar murni via Google SSO (tanpa password lokal), sistem mengembalikan instruksi ramah untuk masuk via Google. | AC04.2 |
| **FR-AUTH-04.2** | Untuk akun email lokal, backend mengenerate token acak kriptografis (cryptographic random token) dengan masa kedaluwarsa 15 menit (`expires_at = NOW() + INTERVAL '15 minutes'`) dan mengirimkan email instruksi pemulihan. | AC04.3; BR-AUTH-07 |
| **FR-AUTH-04.3** | Layar konfirmasi cek email mengunci tombol kirim ulang selama 60 detik (*cooldown timer*) di client dan menerapkan rate limiting di server. | AC04.4; BR-AUTH-08 |
| **FR-AUTH-05.1** | Halaman reset kata sandi (`/auth/reset-password?token=XYZ`) memvalidasi token sebelum merender form. Jika token kedaluwarsa atau pernah dipakai (`is_used = true`), sistem menampilkan status error *"Tautan Kadaluarsa"* dengan CTA meminta tautan baru. | AC05.1, AC05.2; ERR-AUTH-05 |
| **FR-AUTH-05.2** | Validasi menolak kata sandi baru yang sama persis dengan hash kata sandi lama (`422 Unprocessable Entity`). | AC05.3; ERR-AUTH-06 |
| **FR-AUTH-05.3** | Setelah kata sandi baru tersimpan, token di-flag `is_used = true`, seluruh sesi aktif dibatalkan, dan pengguna diarahkan ke login dengan notifikasi sukses. | AC05.4 |

### 4.5 US06 — Keluar Akun (Logout)

| ID | Requirement | Trace PRD |
| :--- | :--- | :--- |
| **FR-AUTH-06.1** | Opsi *"Keluar Akun"* di menu Profil memicu modal dialog konfirmasi sebelum eksekusi. | AC06.1, AC06.2 |
| **FR-AUTH-06.2** | Saat dikonfirmasi, sistem memanggil endpoint `POST /api/v1/auth/logout` untuk membatalkan refresh token di server, menghapus seluruh storage lokal (`localStorage.clear()`), dan mengarahkan pengguna ke halaman login. | AC06.3 |

---

## 5. Concrete API Design

### 5.1 Shared Conventions
- **Base URL**: `/api/v1/auth`
- **Security / Headers**:
  - `Content-Type: application/json`
  - `Authorization: Bearer <accessToken>` (pada endpoint terproteksi)
- **Format Response Standar**:
  ```json
  {
    "success": true,
    "data": { ... },
    "message": "Operasi berhasil dieksekusi"
  }
  ```
- **Format Error Standar**:
  ```json
  {
    "success": false,
    "error": {
      "code": "AUTH_401_INVALID_CREDENTIALS",
      "message": "Email atau kata sandi yang Anda masukkan salah.",
      "details": []
    }
  }
  ```

### 5.2 Daftar Operasi API Definitif

#### 1. Masuk Akun (`POST /api/v1/auth/login`)
- **Tujuan**: Memverifikasi kredensial email & sandi (`US01`).
- **Request Body**:
  ```json
  {
    "email": "fariz@example.com",
    "password": "PasswordRahasia123!",
    "remember_me": true
  }
  ```
- **Response `200 OK`**:
  ```json
  {
    "success": true,
    "data": {
      "user": {
        "id": "usr_78a1bc90",
        "email": "fariz@example.com",
        "name": "Fariz Mirza",
        "avatar_url": null,
        "is_verified": true
      },
      "session": {
        "access_token": "eyJhbGciOiJIUzI1Ni...",
        "refresh_token": "rt_89a0bc...",
        "expires_in": 3600
      }
    }
  }
  ```
- **Error `401 Unauthorized`**: `{ "code": "AUTH_401_INVALID_CREDENTIALS", "message": "Email atau kata sandi salah." }`
- **Error `429 Too Many Requests`**: `{ "code": "AUTH_429_LOCKOUT", "message": "Akun dikunci sementara.", "retry_after": 298 }`

#### 2. Pendaftaran Akun Baru (`POST /api/v1/auth/register`)
- **Tujuan**: Mendaftarkan akun pembelajar baru (`US03`).
- **Request Body**:
  ```json
  {
    "name": "Fariz Mirza",
    "email": "fariz@example.com",
    "password": "PasswordRahasia123!",
    "terms_accepted": true
  }
  ```
- **Response `201 Created`**:
  ```json
  {
    "success": true,
    "message": "Akun berhasil dibuat. Selamat datang di Dhou!",
    "data": {
      "user": { "id": "usr_78a1bc90", "email": "fariz@example.com", "name": "Fariz Mirza" },
      "session": { "access_token": "eyJhbGci...", "refresh_token": "rt_89a...", "expires_in": 3600 }
    }
  }
  ```
- **Error `409 Conflict`**: `{ "code": "AUTH_409_EMAIL_EXISTS", "message": "Email sudah terdaftar." }`
- **Error `422 Unprocessable`**: `{ "code": "AUTH_422_WEAK_PASSWORD", "message": "Sandi minimal 8 karakter dan kombinasi huruf/angka." }`

#### 3. Masuk dengan Google Token (`POST /api/v1/auth/google`)
- **Tujuan**: Memverifikasi idToken dari Google OAuth / Native SDK (`US02`).
- **Request Body**:
  ```json
  {
    "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6...",
    "platform": "web" // atau "ios"
  }
  ```
- **Response `200 OK`**: Mengembalikan user object dan session JWT identik dengan endpoint login.

#### 4. Permintaan Reset Sandi (`POST /api/v1/auth/forgot-password`)
- **Tujuan**: Meminta tautan pemulihan kata sandi (`US04`).
- **Request Body**: `{ "email": "fariz@example.com" }`
- **Response `200 OK`**:
  ```json
  {
    "success": true,
    "data": { "provider": "email" },
    "message": "Tautan pemulihan kata sandi telah dikirim ke email Anda."
  }
  ```
- **Response Khusus Akun Google (`200 OK`)**:
  ```json
  {
    "success": true,
    "data": { "provider": "google" },
    "message": "Akun ini terdaftar via Google. Silakan masuk menggunakan tombol Google."
  }
  ```

#### 5. Eksekusi Reset Sandi (`POST /api/v1/auth/reset-password`)
- **Tujuan**: Memperbarui kata sandi baru menggunakan token (`US05`).
- **Request Body**:
  ```json
  {
    "token": "tok_reset_9a8b7c6d5e",
    "new_password": "PasswordBaru456!"
  }
  ```
- **Response `200 OK`**: `{ "success": true, "message": "Kata sandi Anda berhasil diperbarui." }`
- **Error `400 Bad Request`**: `{ "code": "AUTH_400_TOKEN_EXPIRED", "message": "Tautan sudah kedaluwarsa atau pernah digunakan." }`
- **Error `422 Unprocessable`**: `{ "code": "AUTH_422_SAME_PASSWORD", "message": "Kata sandi baru tidak boleh sama dengan kata sandi lama." }`

#### 6. Keluar Akun (`POST /api/v1/auth/logout`)
- **Tujuan**: Mengakhiri sesi pengguna (`US06`).
- **Headers**: `Authorization: Bearer <accessToken>`
- **Response `200 OK`**: `{ "success": true, "message": "Sesi Anda berhasil diakhiri." }`

---

### 5.3 HTTP and Error Code Recap

| Operation ID | HTTP Status | Stable Code | Trigger Kondisi | Error Body Message | Tindakan Frontend |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `auth_login_password` | `401` | `AUTH_401_INVALID_CREDENTIALS` | Password salah atau email tidak cocok | "Email atau kata sandi yang Anda masukkan salah." | Tampilkan alert merah di atas form; pertahankan teks email |
| `auth_login_password` | `429` | `AUTH_429_LOCKOUT` | Percobaan gagal mencapai 5 kali | "Terlalu banyak percobaan gagal. Akun dikunci sementara." | Nonaktifkan tombol login dan tampilkan banner countdown 5 menit |
| `auth_register` | `409` | `AUTH_409_EMAIL_EXISTS` | Email sudah ada di database | "Alamat email ini sudah terdaftar di Dhou." | Buka modal dialog saran login langsung atau reset sandi |
| `auth_register` | `422` | `AUTH_422_VALIDATION_ERROR` | Format email salah atau sandi lemah | "Periksa kembali format data yang Anda masukkan." | Highlight field yang bermasalah dengan pesan inline |
| `auth_reset_password` | `400` | `AUTH_400_TOKEN_INVALID` | Token kedaluwarsa ($>15$ mnt) / sudah dipakai | "Tautan pemulihan tidak valid atau telah kedaluwarsa." | Sembunyikan form sandi; render layar error dengan CTA minta baru |
| `auth_reset_password` | `422` | `AUTH_422_SAME_PASSWORD` | Sandi baru identik dengan sandi lama | "Kata sandi baru tidak boleh sama dengan kata sandi lama." | Tampilkan pesan error di bawah input kata sandi baru |
| Any mutation | `500` | `AUTH_500_SYSTEM_ERROR` | Database down / Auth provider error | "Layanan autentikasi mengalami kendala. Silakan coba lagi." | Toast error ramah pengguna dengan tombol Coba Lagi |

---

### 5.4 Frontend Integration Mapping

| Layar / Aksi | Operation ID | Konsumsi Input / Output | State Transisi & Handling | Error Handling | State Preservation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Layar Masuk** | `auth_login_password`, `auth_google_signin` | In: `email`, `password`, `remember_me`<br/>Out: User & JWT tokens | Spinner di tombol CTA $\rightarrow$ Redirect ke dashboard | Alert banner merah di atas form | Email tetap terisi saat kredensial salah |
| **Layar Daftar** | `auth_register` | In: `name`, `email`, `password`, `terms`<br/>Out: User record | Meter kekuatan sandi dinamis $\rightarrow$ Redirect onboarding | Dialog saran login jika email sudah ada | Input data form tidak di-reset saat gagal |
| **Layar Lupa Sandi** | `auth_forgot_password` | In: `email`<br/>Out: Provider detection | Submit $\rightarrow$ Arahkan ke `/auth/check-email` | Validasi inline jika email kosong/salah | Input email tersimpan |
| **Layar Cek Email** | - | In: `email` param | Countdown timer 60s aktif $\rightarrow$ Aktifkan tombol kirim ulang | - | Email tujuan ditampilkan jelas |
| **Layar Reset Sandi** | `auth_reset_password` | In: `token` URL, `new_password`<br/>Out: Success confirmation | Validasi token awal $\rightarrow$ Form input $\rightarrow$ Redirect ke login | Layar status tautan kadaluarsa jika token rusak | Token URL terjaga |
| **Menu Profil Logout** | `auth_logout` | In: Bearer Token | Buka dialog konfirmasi $\rightarrow$ Bersihkan token $\rightarrow$ Redirect login | Tetap logout lokal jika jaringan error | - |

---

## 6. Persistence and Ownership

### 6.1 Module Ownership & Boundary
- **AuthModule** memiliki kepemilikan penuh atas otentikasi identitas dan tabel `users`, `user_identities`, `password_reset_tokens`, dan `auth_lockouts`.
- Modul profil belajar dan gamifikasi membaca referensi `user_id` secara read-only.

### 6.2 Skema Tabel Database (PostgreSQL DDL)

```sql
-- 1. Tabel Utama Pengguna (Users)
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    encrypted_password VARCHAR(255) NULL, -- Nullable untuk pengguna murni Google SSO
    full_name VARCHAR(100) NOT NULL,
    avatar_url VARCHAR(500) NULL,
    is_verified BOOLEAN NOT NULL DEFAULT false,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
CREATE INDEX idx_users_email ON users(email);

-- 2. Tabel Identitas Provider OAuth (Google SSO)
CREATE TABLE user_identities (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    provider VARCHAR(50) NOT NULL, -- 'google'
    provider_id VARCHAR(255) NOT NULL, -- Google sub ID
    provider_email VARCHAR(255) NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_provider_identity UNIQUE (provider, provider_id)
);
CREATE INDEX idx_identities_lookup ON user_identities(user_id, provider);

-- 3. Tabel Token Pemulihan Kata Sandi (Single-Use, 15-Min)
CREATE TABLE password_reset_tokens (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    token_hash VARCHAR(255) NOT NULL,
    expires_at TIMESTAMP WITH TIME ZONE NOT NULL,
    is_used BOOLEAN NOT NULL DEFAULT false,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);
CREATE INDEX idx_reset_token ON password_reset_tokens(token_hash) WHERE is_used = false;

-- 4. Tabel Pelacakan Lockout Brute-Force
CREATE TABLE auth_lockouts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) NOT NULL,
    failed_attempts INT NOT NULL DEFAULT 1,
    locked_until TIMESTAMP WITH TIME ZONE NULL,
    last_attempt_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_lockout_email UNIQUE (email)
);
```

---

## 7. Telemetry, Audit, and Recovery

### 7.1 Frontend Product Telemetry
| Event Name | Trigger | Payload Properties | Analisis Produk |
| :--- | :--- | :--- | :--- |
| `auth_login_initiated` | Klik tombol Masuk | `method: "email"` | Mengukur niat masuk |
| `auth_login_success` | Sukses masuk ke dashboard | `method: "email" / "google"`, `duration_ms` | Kecepatan time-to-auth |
| `auth_login_failed` | Gagal masuk | `reason`, `attempts_count` | Error drop-off |
| `auth_google_clicked` | Klik tombol Google | `platform: "web" / "ios"` | Adopsi Google SSO |
| `auth_register_success`| Sukses membuat akun | `method: "email" / "google"` | Akuisisi pengguna baru |
| `auth_forgot_requested`| Kirim permintaan reset | `email_domain` | Permintaan pemulihan |
| `auth_reset_completed` | Sukses simpan sandi baru | - | Pemulihan mandiri |

### 7.2 Backend Audit Events
Pencatatan aktivitas audit pada tabel `audit_logs`:
- `USER_LOGIN_SUCCESS`: Mencatat IP, user agent, dan waktu masuk.
- `USER_LOGIN_FAILED`: Mencatat kegagalan dan penambahan counter percobaan salah.
- `USER_LOCKED_OUT`: Audit pembekuan sementara akun selama 5 menit.
- `USER_REGISTERED`: Pencatatan akun baru.
- `PASSWORD_RESET_REQUESTED`: Pengiriman token pemulihan ke email.
- `PASSWORD_RESET_SUCCESS`: Pembaruan kata sandi dan pembatalan sesi lama.
- `ACCOUNT_LINKED`: Penautan identitas Google ke akun email lokal.

---

## 8. Acceptance and Verification Plan

| Test ID | Skenario Uji (Given / When) | Hasil yang Diharapkan (Then) | Cakupan FRD |
| :--- | :--- | :--- | :--- |
| **T-AUTH-01** | Pengguna memasukkan email valid dan sandi benar | Login sukses (`200 OK`), token JWT tersimpan, masuk dashboard dalam $< 2$ detik. | FR-AUTH-01.1, 01.3 |
| **T-AUTH-02** | Pengguna memasukkan kata sandi salah | Alert merah muncul, teks email tetap ada, dan counter gagal bertambah 1. | FR-AUTH-01.4 |
| **T-AUTH-03** | Pengguna gagal login 5 kali berturut-turut | Akun terkunci, tombol masuk nonaktif dengan timer hitung mundur 5 menit. | FR-AUTH-01.5 |
| **T-AUTH-04** | Pengguna menekan "Lanjutkan dengan Google" di web browser | Dialog Google terbuka, memilih akun berhasil, akun dibuat dan masuk dashboard. | FR-AUTH-02.1, 02.3 |
| **T-AUTH-05** | Pengguna menjalankan Google SSO di build iOS Capacitor | Menggunakan SDK native; tidak terjadi error 403 `disallowed_useragent`. | FR-AUTH-02.2 |
| **T-AUTH-06** | Akun `user@gmail.com` ada via sandi, lalu login via Google | Sistem menautkan akun otomatis (*account linking*), tidak ada duplikasi akun. | FR-AUTH-02.4 |
| **T-AUTH-07** | Pengguna mendaftar dengan nama valid, email valid, sandi kuat | Akun tersimpan (`201 Created`), langsung masuk ke dashboard belajar. | FR-AUTH-03.1, 03.2 |
| **T-AUTH-08** | Pengguna mengetik `fariz@gmai.com` di form pendaftaran | Muncul banner saran pintar *"Maksud Anda fariz@gmail.com? [Gunakan Ini]"*. | FR-AUTH-03.3 |
| **T-AUTH-09** | Pengguna mendaftar dengan email yang sudah ada | Sistem menolak (`409 Conflict`) dan menampilkan dialog opsi login langsung. | FR-AUTH-03.4 |
| **T-AUTH-10** | Pengguna minta reset sandi untuk email Google murni | Sistem menampilkan dialog ramah bahwa akun terhubung via Google Sign-In. | FR-AUTH-04.1 |
| **T-AUTH-11** | Pengguna di layar cek email mencoba klik kirim ulang | Tombol nonaktif selama 60 detik pertama dan aktif kembali saat countdown 0s. | FR-AUTH-04.3 |
| **T-AUTH-12** | Pengguna mengklik tautan reset sandi setelah 20 menit | Halaman menolak token kedaluwarsa dan menampilkan CTA meminta tautan baru. | FR-AUTH-05.1 |
| **T-AUTH-13** | Pengguna menginput kata sandi baru yang sama dengan sandi lama | Sistem menolak dengan pesan *"Kata sandi baru tidak boleh sama dengan sebelumnya"*. | FR-AUTH-05.2 |
| **T-AUTH-14** | Pengguna menekan Keluar Akun di menu Profil dan konfirmasi | Sesi diputus di backend, token lokal terhapus bersih, redirect ke login. | FR-AUTH-06.1, 06.2 |

---

## 9. Approval and Remaining Work

### 9.1 Status Persetujuan
- **Otoritas**: Product Management & Lead Engineering Dhou.
- **Disetujui Pada**: 2026-10-31.
- **Dokumen Terkait**: [PRD1 — User Authentication, Sign In, and Password Recovery](../PRD/PRD1%20-%20User%20Authentication,%20Sign%20In,%20and%20Password%20Recovery.md).

### 9.2 Pekerjaan Implementasi Tersisa
1. **Supabase Schema Migration**: Menjalankan skrip DDL tabel `user_identities`, `password_reset_tokens`, dan `auth_lockouts`.
2. **Capacitor Google Auth Config**: Menambahkan client ID Google iOS di `capacitor.config.ts`.
3. **Frontend Component Integration**: Mengintegrasikan `LoginForm.tsx` dan `RegisterForm.tsx` dengan skema Zod terverifikasi.
4. **End-to-End Testing**: Eksekusi pengujian otomatis untuk skenario T-AUTH-01 s/d T-AUTH-14.

---

## 10. Lampiran Teknis Tambahan

### 10.1 Skema Validasi Zod

```typescript
import { z } from 'zod';

const passwordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$/;

export const loginSchema = z.object({
  email: z
    .string()
    .min(1, { message: 'Alamat email wajib diisi' })
    .email({ message: 'Format alamat email tidak valid' })
    .transform((val) => val.trim().toLowerCase()),
  password: z
    .string()
    .min(1, { message: 'Kata sandi wajib diisi' })
    .min(8, { message: 'Kata sandi minimal 8 karakter' }),
  rememberMe: z.boolean().default(true),
});

export const registerSchema = z
  .object({
    name: z
      .string()
      .min(1, { message: 'Nama panggilan wajib diisi' })
      .min(2, { message: 'Nama panggilan minimal 2 karakter' })
      .max(50, { message: 'Nama panggilan maksimal 50 karakter' })
      .regex(/^[a-zA-Z\s\u0600-\u06FF]+$/, { 
        message: 'Nama hanya boleh berupa huruf Latin atau Arab' 
      })
      .transform((val) => val.trim()),
    email: z
      .string()
      .min(1, { message: 'Alamat email wajib diisi' })
      .email({ message: 'Format alamat email tidak valid' })
      .transform((val) => val.trim().toLowerCase()),
    password: z
      .string()
      .min(8, { message: 'Kata sandi minimal 8 karakter' })
      .regex(passwordRegex, {
        message: 'Kata sandi harus mengandung huruf besar, huruf kecil, dan angka',
      }),
    confirmPassword: z
      .string()
      .min(1, { message: 'Konfirmasi kata sandi wajib diisi' }),
    termsAccepted: z.literal(true, {
      errorMap: () => ({ message: 'Anda harus menyetujui Ketentuan Layanan' }),
    }),
  })
  .refine((data) => data.password === data.confirmPassword, {
    message: 'Konfirmasi kata sandi tidak cocok',
    path: ['confirmPassword'],
  });

export const forgotPasswordSchema = z.object({
  email: z
    .string()
    .min(1, { message: 'Alamat email wajib diisi' })
    .email({ message: 'Format alamat email tidak valid' })
    .transform((val) => val.trim().toLowerCase()),
});

export const resetPasswordSchema = z
  .object({
    newPassword: z
      .string()
      .min(8, { message: 'Kata sandi baru minimal 8 karakter' })
      .regex(passwordRegex, {
        message: 'Kata sandi harus mengandung huruf besar, huruf kecil, dan angka',
      }),
    confirmNewPassword: z
      .string()
      .min(1, { message: 'Ulangi kata sandi baru Anda' }),
  })
  .refine((data) => data.newPassword === data.confirmNewPassword, {
    message: 'Kata sandi baru tidak cocok',
    path: ['confirmNewPassword'],
  });
```

### 10.2 Matriks Kasus Ekstrem Fungsional (Edge Cases Matrix)

| No | Kategori | Skenario Kasus Ekstrem | Solusi Fungsional Sistem | Tampilan Antarmuka UI (Bahasa Indonesia) |
| :-: | :--- | :--- | :--- | :--- |
| **EC-01** | Input Email | Pengguna memasukkan spasi di awal/akhir atau huruf kapital. | Sistem otomatis melakukan `.trim().toLowerCase()` sebelum validasi. | Nilai otomatis dibersihkan tanpa memicu pesan error merah. |
| **EC-02** | Input Email | Typo domain umum (misal: `@gmai.com`, `@yaho.co`). | Utilitas `detectDomainTypo(email)` memeriksa daftar domain populer. | Banner saran biru: *"Maksud Anda **fariz@gmail.com**? [Gunakan Ini]"*. |
| **EC-03** | Login | Kredensial tidak cocok (sandi salah atau email tidak ada). | Backend merespons generic 401 (*anti-user-enumeration*). | Alert merah: *"Email atau kata sandi yang Anda masukkan salah."* |
| **EC-04** | Keamanan | Gagal login 5 kali berturut-turut pada satu akun. | Kunci form selama 300 detik (5 menit). Simpan timestamp `locked_until`. | Banner kuning dengan countdown timer: *"Akun dikunci sementara. Coba lagi dalam **04:59**."* |
| **EC-05** | Register | Email sudah terdaftar di database. | Backend merespons `409 Conflict`. | Modal dialog: *"Email ini sudah terdaftar di Dhou. Ingin langsung masuk?"* dengan tombol Masuk. |
| **EC-06** | Reset Sandi | Klik tautan reset yang sudah kadaluarsa ($>15$ menit). | Token ditolak backend (`token_expired`). | Layar status jam pasir: *"Tautan Kadaluarsa (berlaku 15 menit)."* Tombol: `[Kirim Tautan Baru]`. |
| **EC-07** | Reset Sandi | Klik tautan yang sudah pernah dipakai (*single-use*). | Backend menolak token (`token_already_consumed`). | Layar status: *"Tautan Tidak Valid karena telah digunakan sebelumnya."* Tombol: `[Masuk ke Dhou]`. |
| **EC-08** | Reset Sandi | Sandi baru sama persis dengan sandi lama. | Backend menolak dengan `422 Unprocessable`. | Error di bawah input sandi: *"Kata sandi baru tidak boleh sama dengan kata sandi sebelumnya."* |
| **EC-09** | Reset Sandi | Spam klik kirim ulang email reset berulang-kali. | Client menonaktifkan tombol selama 60 detik; server membatasi 3x per 10 menit. | Teks hitung mundur: *"Kirim ulang tersedia dalam 45s"*. |
| **EC-10** | Jaringan | Koneksi internet terputus di tengah proses submit. | Interceptor menangkap network error / timeout $> 10000$ms. | Toast floating abu-abu: *"Koneksi terputus. [Coba Lagi]"*. State form tidak di-reset. |
| **EC-11** | Backend | Server down / respons HTTP 5xx. | Tangkap error 5xx secara global. | Banner: *"Layanan sedang mengalami gangguan sejenak. Silakan coba beberapa saat lagi."* |
| **EC-12** | Form UX | Double submit klik tombol berkali-kali. | Handler di-debounce 300ms dan flag `isSubmitting = true`. | Tombol langsung dinonaktifkan sejak klik pertama dan menampilkan spinner animasi. |
| **EC-13** | Mobile UX | Keyboard virtual ponsel menutupi tombol submit. | CSS `scroll-margin-bottom` dan auto scroll saat event `onFocus`. | Layar otomatis menggulir ke atas sehingga input aktif dan tombol tetap terlihat. |
| **EC-14** | OAuth SSO | Pengguna membatalkan alur login Google (*Popup Closed*). | Tangkap status `popup_closed_by_user`. | Reset state tombol loading Google ke normal secara tenang tanpa alert error. |
| **EC-15** | Sesi JWT | Access token kedaluwarsa saat aplikasi dibuka dari background. | Interceptor melakukan *silent refresh* menggunakan refresh token. | Pengguna tetap dapat melanjutkan aktivitas belajar tanpa gangguan logout. |
| **EC-16** | Sesi JWT | Refresh token kedaluwarsa atau dicabut server. | Refresh token gagal mengembalikan access token baru. | Bersihkan seluruh storage lokal dan tampilkan toast: *"Sesi Anda telah berakhir. Silakan masuk kembali."* |
| **EC-17** | Multi-Tab | Pengguna logout di Tab A browser. | Event listener `storage` atau `BroadcastChannel('auth_channel')`. | Tab B otomatis diarahkan ke halaman login tanpa menunggu aksi pengguna. |
| **EC-18** | Keamanan | Percobaan injeksi skrip HTML/XSS di form. | Validasi Zod menolak karakter non-nama dan React melakukan auto-escaping. | Input menolak karakter berbahaya dengan error: *"Nama hanya boleh memuat huruf."* |
| **EC-19** | Autofill | Pengisian otomatis iCloud Keychain tanpa trigger event `input`. | Listener `animationstart` untuk `:-webkit-autofill` terintegrasi React Hook Form. | Nilai form terdeteksi valid dan tombol submit langsung aktif tanpa harus diklik ulang. |
| **EC-G01** | Google SSO | Error 403 `disallowed_useragent` pada iOS Capacitor. | Kebijakan Google melarang OAuth di dalam WebView. | Gunakan SDK Native Google Auth Plugin pada build Capacitor iOS. | Antarmuka sheet Google native sistem operasi berjalan aman tanpa blokir. |
| **EC-G02** | Google SSO | Pengguna menutup sheet login Google sebelum memilih akun. | Tangkap error code 12501 (Capacitor) atau popup closed. | Reset status loading tombol Google ke kondisi siap klik. |
| **EC-G03** | Google SSO | Popup Google diblokir browser Safari/Chrome Mobile. | Tangkap error popup blocker. | Fallback otomatis ke mode full-page redirect (`redirectTo`). |
| **EC-G04** | Google SSO | Email Google sudah terdaftar via password manual. | Backend menjalankan *Automatic Account Linking*. | Modal info ramah: *"Akun ditautkan. Anda kini dapat masuk dengan Google maupun kata sandi."* |
| **EC-G05** | Google SSO | Pengguna Google SSO mencoba meminta lupa kata sandi. | Sistem mendeteksi akun hanya memiliki provider Google. | Layar informasi: *"Akun Anda terdaftar via Google. Silakan masuk dengan tombol Google di bawah."* |
| **EC-G06** | Google SSO | Pengguna memiliki multi-akun Google di perangkat. | Parameter `prompt: 'select_account'` aktif. | Dialog pemilih akun Google selalu disajikan setiap kali tombol ditekan. |
