# Acuan Resmi Pemetaan Aplikasi Dhou (Berdasarkan Paper Riset)

**Judul Paper:** *Dhou: A Concept of Developing an Arabic Language Learning Application for fusha and 'ammiyyah in the Age of Artificial Intelligence*  
**Peneliti/Author:** Fariz Mirza Abdillah (Universitas Abdul Chalim)  
**Dokumen:** Master Reference & Architectural Mapping  
**Lokasi Berkas:** `docs/RESEARCH_REFERENCE_MAPPING.md`

---

## 1. Landasan Filosofis & Masalah Inti (Problem Statement)

### 1.1 Fenomena Diglosia Bahasa Arab (*Arabic Diglossia*)
- Bahasa Arab memiliki dualisme fungsional yang tajam antara:
  - **Fusha (Modern Standard Arabic / الفصحى):** Bahasa tulisan, literatur, media resmi, peribadatan, dan lingkungan akademis.
  - **'Ammiyyah (Spoken Arabic / العامية):** Bahasa komunikasi lisan sehari-hari yang bervariasi secara geografis (Mesir, Syam/Levantine, Teluk, dll.).
- **Kesenjangan Pembelajar (*Learner Gap*):**
  Banyak pembelajar non-native yang memiliki penguasaan tata bahasa *fusha* memadai di bangku pendidikan formal tetap mengalami hambatan besar saat berkomunikasi di dunia nyata di Timur Tengah. Hambatan ini bukan semata kurangnya kosakata formal, melainkan minimnya eksposur terhadap ragam bahasa lisan (*'ammiyyah*), kecepatan bicara (*speech rate*), ritme, intonasi, dan pola interaksi sosial kontekstual.
- **Prinsip Solusi:**
  *Fusha* tidak digantikan oleh *'ammiyyah*, melainkan dibangun jembatan transisi adaptif: memanfaatkan fondasi *fusha* yang sudah dimiliki pembelajar lalu melengkapinya dengan eksposur dialek percakapan sehari-hari.

---

## 2. Alur Pedagogis Terpadu (*Integrated Learning Cycle*)

Paper Dhou merumuskan arsitektur produk yang menghubungkan 4 komponen utama secara siklis dan terintegrasi:

$$\text{Video Learning} \longrightarrow \text{Gamified Learning Path} \longrightarrow \text{Conversational AI} \longrightarrow \text{Review}$$

### 2.1 Pemetaan Tahapan Pedagogis (*Pedagogical Sequence*)
| Komponen Dhou | Fungsi Utama di Paper | Proses Pembelajar (*Learner Process*) | Tahap Pedagogis Detail |
| :--- | :--- | :--- | :--- |
| **1. Video Learning** | Contextual Language Input | Observe, listen, notice, and understand | Exposure $\rightarrow$ Noticing |
| **2. Gamified Path** | Structured Language Practice | Recognize, retrieve, construct, pronounce, reinforce | Recognition $\rightarrow$ Retrieval $\rightarrow$ Controlled Production |
| **3. Conversational AI** | Communicative Language Production | Formulate, respond, interact, maintain communication | Communicative Production $\rightarrow$ Feedback |
| **4. Review (SRS)** | Learning Reinforcement | Revisit, correct, repeat, consolidate | Review $\rightarrow$ Repeated Use |

---

## 3. Matriks Spesifikasi Fitur Detail (Mengacu Tabel Paper)

### 3.1 Modul 1: Video-Based Contextual Learning (Tabel Hal. 9–10)
Tujuan: Mengubah konten audiovisual otentik dari sekadar tontonan pasif menjadi input belajar aktif dan terpandu.

| Fitur / Fungsi | Tujuan Pedagogis dalam Paper | Implementasi Teknis di App |
| :--- | :--- | :--- |
| **Arabic Subtitles** | Menghubungkan bahasa lisan dengan ortografi Arab (harakat jelas) | Subtitle teks Arab berharakat (`dir="rtl"`) |
| **Optional Translation** | Memberikan bantuan makna saat dibutuhkan | Toggle terjemahan Bahasa Indonesia on/off |
| **Interactive Transcript** | Memeriksa teks lisan secara detail per segmen | Transkrip interaktif yang dapat diklik per kalimat |
| **Vocabulary Extraction** | Mengidentifikasi butir-butir leksikal penting | Klik kata untuk melihat arti kata per kata |
| **Expression Extraction** | Mengidentifikasi ungkapan percakapan praktis kontekstual | Highlight ungkapan idiomatik dalam video |
| **Sentence-Level Replay** | Mendukung pendengaran terfokus dan pengulangan | Tombol replay per kalimat tanpa scroll manual |
| **Playback-Speed Control**| Bantuan pendengaran tambahan bagi pemula | Kontrol kecepatan: 0.75x, 0.9x, 1.0x |
| **Listening Comprehension**| Mengevaluasi pemahaman input audiovisual | Mini-kuis pemahaman isi klip setelah menonton |
| **Expression Recognition** | Melatih pengenalan bahasa dalam konteks | Deteksi pola frasa dalam ucapan penutur asli |
| **Subtitle On/Off** | Mendorong kemandirian mendengar tanpa membaca teks | Tombol sembunyikan subtitle untuk melatih telinga |
| **Shadowing** | Melatih ritme, kefasihan, intonasi, dan pelafalan | Fitur dengar $\rightarrow$ tirukan langsung |
| **Voice Recording** | Mendorong produksi lisan aktif | Perekam suara pengguna (MediaRecorder API) |
| **Record-and-Compare** | Membantu refleksi perbandingan suara user vs penutur asli | Pemutar berdampingan (suara asli vs rekaman user) |
| **Pronunciation Feedback**| Memberikan panduan pelafalan jika akurasi teknis andal | Evaluasi audio STT / kejelasan artikulasi |
| **Save to Review** | Memindahkan bahasa penting/sulit ke sesi Review | Tombol bookmark/simpan langsung ke modul Review |
| **Arabic-Variety Label** | Menandai apakah materi menggunakan fusha atau dialek tertentu | Badge eksplisit (Fusha / Egyptian / Levantine) |
| **Related Practice** | Menghubungkan materi video ke latihan terstruktur | Tautan langsung dari video ke node kuis di Learn |

---

### 3.2 Modul 2: Gamified Learning Path (Tabel 20 Tipe Aktivitas Hal. 10–11)
Tujuan: Latihan terstruktur progresif yang variatif agar tidak monoton dan tidak terjebak hanya pada kuis pilihan ganda pasif.

| No | Tipe Aktivitas (*Activity Type*) | Fungsi Belajar Utama (*Primary Learning Function*) |
| :---: | :--- | :--- |
| **1** | **Vocabulary learning** | Memperkenalkan dan memperkuat pengetahuan leksikal baru |
| **2** | **Word–meaning matching** | Melatih rekognisi semantik (mencocokkan kata Arab & artinya) |
| **3** | **Fill in the blank** | Melatih pemilihan kata dan struktur secara kontekstual |
| **4** | **Sentence arrangement** | Membangun kesadaran sintaksis/struktur kalimat (menyusun balok kata) |
| **5** | **Word pairing** | Memperkuat relasi leksikal dan kolokasi kata |
| **6** | **Listen and choose** | Mengembangkan diskriminasi auditori (dengar audio, pilih jawaban) |
| **7** | **Audio-to-text dictation** | Menghubungkan pemahaman mendengar dengan produksi tulisan Arab |
| **8** | **Text-to-audio recognition**| Menghubungkan bentuk tulisan teks dengan bentuk bunyi lisan |
| **9** | **Controlled translation** | Melatih rekognisi makna lintas bahasa (Indo $\leftrightarrow$ Arab) |
| **10**| **Contextual response selection** | Melatih pemahaman pragmatik sosial (memilih respons situasi yang tepat) |
| **11**| **Dialogue completion** | Memahami urutan interaksi percakapan dua arah |
| **12**| **Pronunciation repetition**| Mendukung produksi lisan lewat imitasi model audio penutur |
| **13**| **Voice recording** | Memungkinkan pembelajar memonitor suara mereka sendiri |
| **14**| **Record-and-compare** | Refleksi perbandingan pengucapan user dengan standar penutur |
| **15**| **Shadowing** | Melatih kelancaran, tempo bicara, dan prosodi |
| **16**| **Listening comprehension**| Evaluasi pemahaman terhadap tuturan lisan yang lebih panjang |
| **17**| **Active recall** | Memperkuat pemanggilan kembali memori (*retrieval*) tanpa bantuan opsi |
| **18**| **Mixed-skill practice** | Mengintegrasikan beberapa keterampilan sekaligus dalam satu sesi |
| **19**| **Personalized error review** | Mengembalikan materi yang salah terjawab ke sesi latihan terstruktur |
| **20**| **Spaced review** | Mengulang materi dalam interval waktu bertahap (Spaced Repetition) |

---

### 3.3 Modul 3: Conversational AI (Tabel Hal. 12–13)
Tujuan: Menyediakan ruang interaktif untuk memproduksi bahasa secara fleksibel tanpa rasa malu atau cemas.

| Fitur / Fungsi | Tujuan dalam Paper | Implementasi di Dhou |
| :--- | :--- | :--- |
| **Arabic-Variety Selection** | Latihan spesifik sesuai dialek pilihan pengguna | Selektor dialek sebelum sesi dimulai |
| **Interactive Conversation** | Praktik produksi bahasa Arab aktif dua arah | Chat interaktif berbasis suara & teks |
| **Contextual Suggestion** | AI menawarkan konteks situasi (misal: di bandara, restoran) | Prompt kartu skenario cepat tanpa konfigurasi rumit |
| **Variety Consistency** | **KUNCI:** Menjaga agar AI tidak mencampur Fusha & dialek tanpa tanda | Guardrail ketat pada system prompt LLM per dialek |
| **Adaptive Language Level** | Menjaga tingkat kesulitan bahasa sesuai kemampuan user | Kalimat AI proporsional dengan level user |
| **Learning Support** | Menyediakan scaffold / bantuan saat pembelajar bingung | Tombol bantuan terjemahan & saran kata |
| **Feedback** | Mengidentifikasi area tata bahasa atau diksi yang keliru | Catatan koreksi ramah setelah user menjawab |
| **Review Connection** | Kesulitan komunikasi otomatis dikirim ke modul Review | Frasa yang salah/sulit langsung masuk antrean review |

---

### 3.4 Modul 4: Review and Learning Continuity (Hal. 12–13)
Tujuan: Lapisan penguatan retensi memori jangka panjang (*Spaced Repetition System / SRS*).
- **Sumber Materi Masuk:**
  1. Kosakata yang di-bookmark dari modul **Video**.
  2. Kesalahan kuis dari **Gamified Path**.
  3. Kesulitan tata bahasa/idiom yang terdeteksi saat obrolan **Conversational AI**.
- **Logika Pengulangan Berjarak:**
  - Materi yang sulit/sering salah muncul dengan frekuensi lebih sering.
  - Materi yang berhasil diingat (*remembered*) dijadwalkan ulang dengan interval waktu yang semakin renggang.
  - Setiap kartu review tetap membawa label varietas asalnya (Fusha / Mesir / Syam).

---

## 4. Karakteristik Sistemik: *Arabic-Variety Awareness*

Sesuai penekanan di paper:
> *"An Arabic variety functions as a system-wide attribute rather than a separate module."*

Variasi dialek **bukan** modul yang terpisah atau terisolasi, melainkan **atribut global** yang melekat di seluruh entitas aplikasi:
- Setiap item video memiliki metadata varietas dialek.
- Setiap aktivitas di learning path mengetahui dialek targetnya.
- Modul AI conversation beroperasi dalam ragam dialek yang dipilih.
- Modul review mempertahankan informasi dialek dari setiap kartu kosakata.

### Batasan Ruang Lingkup MVP (*Scope Limitation*):
1. **Fusha (Modern Standard Arabic)**
2. **Egyptian Arabic ('Ammiyyah Mesir)**: Dipilih karena dipahami luas melalui industri film/musik.
3. **Levantine Arabic ('Ammiyyah Syam)**: Dipilih karena mewakili kluster regional penting (Yordania, Palestina, Suriah, Lebanon).

---

## 5. Status Keselarasan Kode Saat Ini (Traceability)

| Aspek Paper | Status di Repositori Saat Ini | Berkas Terkait |
| :--- | :--- | :--- |
| **Identitas Desain & Tokens** | Selesai (Figma + Fredoka + Amiri) | [`docs/design-system.md`](file:///Users/farizmirza/Documents/Project/dhou/docs/design-system.md) |
| **Layout Mobile Shell & 4 Tabs** | Selesai (Learn, Video, AI, Review, Profile) | [`src/app/page.tsx`](file:///Users/farizmirza/Documents/Project/dhou/src/app/page.tsx), [`BottomNav.tsx`](file:///Users/farizmirza/Documents/Project/dhou/src/components/layout/BottomNav.tsx) |
| **Variety Awareness System** | Selesai (Type `ArabicVariety` & TopBar selector) | [`src/types/index.ts`](file:///Users/farizmirza/Documents/Project/dhou/src/types/index.ts), [`TopBar.tsx`](file:///Users/farizmirza/Documents/Project/dhou/src/components/layout/TopBar.tsx) |
| **Auth & Settings Awal** | Selesai (Local Session & Preferences) | [`src/context/AuthContext.tsx`](file:///Users/farizmirza/Documents/Project/dhou/src/context/AuthContext.tsx) |
| **20 Tipe Kuis Gamified Path** | Mockup Tree selesai $\rightarrow$ Tahap implementasi engine kuis | [`LearnView.tsx`](file:///Users/farizmirza/Documents/Project/dhou/src/components/features/learn/LearnView.tsx) |
| **Video & Shadowing Engine** | Mockup Card selesai $\rightarrow$ Tahap implementasi player audio/video | [`VideoView.tsx`](file:///Users/farizmirza/Documents/Project/dhou/src/components/features/video/VideoView.tsx) |
| **Conversational AI Guardrail** | Mockup Bubble selesai $\rightarrow$ Tahap integrasi LLM prompt | [`AIConversationView.tsx`](file:///Users/farizmirza/Documents/Project/dhou/src/components/features/chat/AIConversationView.tsx) |
| **Spaced Repetition Review** | Mockup Flashcard selesai $\rightarrow$ Tahap logika jadwal interval | [`ReviewView.tsx`](file:///Users/farizmirza/Documents/Project/dhou/src/components/features/review/ReviewView.tsx) |

---

*Dokumen ini menjadi acuan mutlak dalam pengembangan seluruh fitur Dhou ke depan agar 100% konsisten dengan kontribusi akademis dan metodologi paper Fariz Mirza Abdillah (2026).*
