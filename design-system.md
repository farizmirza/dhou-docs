# Dhou Design System (Extracted from Figma)
**Source File:** [Figma - Dhou](https://www.figma.com/design/nfm2VAX3qXP6iFYGHQAEyG/Dhou?node-id=0-1)

---

## 1. Color Palette (Tokens)

Berdasarkan inspeksi desain Figma Dhou, palet warna mengusung tema hangat & energik (*Warm Orange / Coral & Soft Amber* dipadukan dengan aksen biru/soft gray):

| Token Name | Hex Code | Deskripsi & Penggunaan di UI |
| :--- | :--- | :--- |
| `primary` | `#FF8904` | Warna tombol utama, header unit card, border aktif. |
| `primary-hover` / `primary-dark` | `#ED7500` / `#FF6900` | State pressed/hover tombol, stroke tebal bawah (3D button). |
| `primary-light` | `#FFB86A` | Border sekunder, aksen icon, progress bar glow. |
| `primary-subtle` | `#FFD6A8` | Background badge / border halus. |
| `primary-bg` | `#FFF7ED` / `#FFEDD4` | Background banner, container panel, highlight. |
| `accent-blue` | `#51A2FF` | Bubble chat lawan bicara / AI utterance, audio active wave. |
| `accent-blue-subtle` | `#8EC5FF` | Background bubble chat respon pengguna. |
| `neutral-black` | `#121214` / `#000000` | Teks judul utama, teks kontras tinggi. |
| `neutral-dark-grey` | `#4A5565` / `#858585` | Teks body, subtitle deskripsi unit, terjemahan. |
| `neutral-border` | `#F3F4F6` | Divider, border card, input background. |
| `white` | `#FFFFFF` | Background dasar, teks di atas tombol primary. |

### Dialect Variety Tokens
- **Fusha (Modern Standard Arabic):** `#51A2FF` (Blue badge / Classic Standard)
- **Egyptian ('Ammiyyah Mesir):** `#FF8904` (Orange badge / Energetic Nile)
- **Levantine ('Ammiyyah Syam):** `#10B981` / Emerald (Green badge / Olive Levant)

---

## 2. Typography

Desain Figma menggunakan kombinasi tipografi berikut:
- **Display & Gamification Numbers/Headings:** `Fredoka` (Medium, Semibold) — memberikan kesan bersahabat & gamified seperti Duolingo.
- **Body & Subtitles:** `Poppins` dan `Roboto` (Regular, SemiBold).
- **Arabic Text (Font Rekomendasi):**
  - **Noto Sans Arabic** atau **Amiri** (Google Fonts).
  - Teks Arab harus menggunakan line-height minimal `1.6` hingga `1.8` (`leading-relaxed` atau `leading-loose`) agar harakat tidak bertumpuk.

---

## 3. Layout & Mobile Shell Specification

- **Target Mobile Viewport:** Lebar default `402px` (setara iPhone Pro Max / Modern Android), responsive down to `360px`.
- **Corner Radius:**
  - Standard Cards & Buttons: `12px`
  - Rounded Pill (Level badges, avatar frames): `100px` / `9999px`
  - Bottom Navigation: Top radius `12px 12px 0px 0px` dengan subtle shadow `0px -4px 4px rgba(0, 0, 0, 0.03)`.
- **Top Header Bar:**
  - Menampilkan Status / Capaian:
    - Dialect / Level indicator
    - **Streak (Fire Icon 🔥)**
    - **Energy / Battery Charging (⚡ / 🔋)**

---

## 4. 4 Bottom Navigation Destinations

Sesuai komponen `bottom_navigation` di Figma:
1. 👣 **Learn (Gamified Path / Footprints):** Rute pohon belajar, unit card (`Bagian 1, Unit 2 - Dasar Bahasa Arab`).
2. 🎬 **Video (Contextual Video Learning):** Video card dengan subtitle Arab, terjemahan, dan tombol play.
3. 🎙️ **Dhou AI (UserSound / ChatDots):** Avatar AI Dhou dengan bubble chat interaktif bahasa Arab dan selektor dialek.
4. 👤 **Profile / Review:** Pencapaian dan pengulangan kosakata.
