# Notulensi & Ringkasan — Tahapan Belajar Menjadi System Analyst Untuk Pemula

**Sumber:** [Upskilling With SUHU — YouTube](https://www.youtube.com/watch?v=Q28Egx-uMqc)
**Tanggal catat:** 24 Juli 2026
**Format:** Ringkasan eksekutif → notulensi per tahap → pendalaman mandiri

> Catatan: transkrip video berasal dari auto-caption YouTube (banyak salah ketik ASR), jadi isi di bawah adalah hasil interpretasi maknanya, bukan kutipan harfiah. Bagian **"Pendalaman"** adalah tambahan di luar video untuk bahan belajar lanjutan — sudah ditandai jelas supaya tidak tercampur dengan isi asli video.

---

## 1. Ringkasan Eksekutif (TL;DR)

Video ini membahas **learning path praktis** untuk masuk ke profesi System Analyst (SA), bukan teori akademis. Inti pesannya:

1. Profesi SA itu **"timbul tenggelam"** — tidak semua perusahaan punya posisi ini. Di banyak software house, perannya diserap oleh **Project Manager** atau langsung ke **Programmer**.
2. Skill paling dasar bukan tools atau diagram, tapi **jadi pendengar & pencatat yang baik**.
3. Alur kerja SA mengikuti urutan: **Dengar → Analisa → Breakdown Aktor → Proses Bisnis → Diagram → UI/UX → ERD → (opsional) API Blueprint**.
4. Setiap tahap **wajib divalidasi balik ke klien** (checkpoint). Revisi minor = jalan terus, revisi mayor = ada yang salah paham di awal.
5. Cara bangun portofolio: **ambil masalah nyata orang di sekitar**, tawarkan diri jadi SA (boleh gratis), kerjakan 1–4 minggu, hasilkan **1 dokumen analisis sistem** sebagai portofolio.
6. **System Analyst dan Business Analyst irisannya sangat kuat** — portofolio yang sama bisa dipakai melamar keduanya.

---

## 2. Notulensi Detail

### 2.0 Konteks Profesi

| Poin | Isi |
|---|---|
| Status profesi | Naik-turun permintaannya; tidak selalu ada posisi khusus |
| Siapa yang sering merangkap | Project Manager, atau langsung dilempar ke Programmer |
| Posisi dalam tim | Jembatan antara kebutuhan bisnis/klien dan tim engineering |
| Karier berdekatan | Business Analyst (bisa pindah jalur dengan bekal yang sama) |

---

### 2.1 Tahap 1 — Menjadi Pendengar yang Baik

Terdengar sepele, tapi ini yang paling sulit dikuasai.

**Aturan saat bertemu klien / mendampingi PM:**
- Jangan buru-buru **membantah**.
- Jangan buru-buru **berspekulasi**.
- Jangan buru-buru **berasumsi aneh-aneh**.
- Dengarkan dulu sampai tuntas.

**Catat secara detail setiap pertemuan:**
- Tanggal pertemuan
- Siapa saja yang hadir
- Apa saja yang **disepakati**
- Poin terbuka / yang belum diputuskan

> Kenapa penting: catatan ini jadi dasar hukum saat terjadi perdebatan scope di belakang hari. SA tanpa notulen = SA tanpa bukti.

---

### 2.2 Tahap 2 — Melakukan Analisa

Setelah mendengar, baru berpikir. Tiga pertanyaan kunci:

1. **Apa sebenarnya yang dibutuhkan?** (bedakan antara yang *diminta* dan yang *dibutuhkan*)
2. **Solusi apa yang bisa ditawarkan?**
3. **Apakah teknologi mampu menjawab kebutuhan itu?**

**Realita di lapangan:** klien sering tidak bisa berpikir sampai ke level teknologi, karena:
- Keterbatasan **budget**, atau
- Teknologinya memang **belum sampai** ke titik itu.

Maka SA wajib **paham teknologi**, bukan cuma paham proses bisnis. Ini yang membedakan SA dari sekadar notulen rapat.

---

### 2.3 Tahap 3 — Breakdown Kebutuhan (Identifikasi Aktor)

Pecah kebutuhan dengan cara memetakan **siapa saja aktornya**. Ada dua kategori:

| Kategori | Definisi | Contoh |
|---|---|---|
| **Aktor yang memakai aplikasi** | Orang yang berinteraksi langsung dengan sistem | Customer Service yang input data |
| **Aktor di luar aplikasi** | Orang yang memicu proses tapi tidak menyentuh sistem | Customer yang datang ke kantor, lalu CS yang mencatatkan |

**Contoh kasus dari video:** customer datang → CS yang mencatat ke aplikasi. Customer = aktor luar, CS = aktor pengguna aplikasi.

> Breakdown ini harus **benar-benar detail**. Aktor yang terlewat = fitur yang terlewat = revisi mahal di belakang.

---

### 2.4 Tahap 4 — Menyusun Proses Bisnis

- Format bebas: bisa berupa **teks naratif**, bisa berupa **daftar tahapan**.
- Buat **versi general dulu**, jangan langsung detail.
- **Kembalikan ke klien untuk divalidasi.**

**Prinsip checkpoint (penting!):**
> Setiap kali selesai satu tahap, kembalikan ke klien. Jangan kerja sendirian sampai akhir lalu baru ditunjukkan.

**Cara membaca hasil validasi:**

| Hasil review klien | Artinya | Tindakan |
|---|---|---|
| Revisi **minor** | Pemahaman sudah sejalan | Lanjut ke tahap berikutnya |
| Revisi **mayor** | Masih banyak yang salah paham | Mundur, ulangi tahap dengar & analisa |

---

### 2.5 Tahap 5 — Pembuatan Diagram

Ini urutan artefak yang dibuat, dari yang paling makro ke paling mikro.

#### a) Arsitektur Aplikasi *(dibuat pertama)*

Yang harus dijawab:
- **Pola arsitektur:** Monolith atau Microservices?
- **Server/layanan yang terhubung:** apakah berdiri sendiri di satu server, atau terintegrasi ke sistem lain?
- **Contoh integrasi eksternal:** payment gateway, SMS gateway, storage/penyimpanan file, dsb.

#### b) Diagram Proses Rinci

Preferensi narasumber:

| Diagram | Kapan dipakai | Alasan |
|---|---|---|
| **Activity Diagram** | Pilihan utama, paling sering dipakai | Paling simpel, mudah dipahami, langsung menunjukkan relasi antar aktor dan sistem |
| **Sequence Diagram** | Alternatif untuk alur interaksi | Menunjukkan urutan pertukaran pesan |
| **Flowchart** | Saat butuh mendetailkan **satu proses tertentu** | Lebih detail dan granular dibanding activity diagram yang sifatnya general |

> Pola pikirnya: pakai activity diagram untuk gambaran umum, turunkan ke flowchart hanya di proses yang rumit.

#### c) Desain UI/UX (Wireframe)

- **Tidak perlu jadi ahli desain.** SA bukan UI designer.
- Tujuannya: memberi **gambaran visual** ke user/klien — "nanti aplikasinya kira-kira seperti ini, alurnya seperti ini."
- Cukup untuk memancing feedback lebih konkret dari klien.

#### d) ERD / Rancangan Database

- Entity Relationship Diagram — struktur tabel, kolom, relasi antar entitas.
- **Untuk mayoritas proyek, pekerjaan SA berhenti di sini.**

---

### 2.6 Tahap 6 — API Blueprint & Dokumentasi *(Opsional / Kasus Tertentu)*

Dibutuhkan hanya kalau model pengembangannya **paralel** (frontend dan backend jalan bersamaan, mengandalkan kontrak API).

Yang harus disiapkan:
- **Blueprint API** — kontrak endpoint yang disepakati sebelum coding dimulai
- **Dokumentasi API** — endpoint, request/response, relasi antar API

> Kalau tim kecil dan pengembangan berurutan, tahap ini biasanya bisa dilewati.

---

### 2.7 Saran Membangun Portofolio

Ini bagian paling actionable dari video:

1. **Cari masalah nyata dari orang-orang di sekitar** (teman, keluarga, UMKM, komunitas).
2. Tanyakan: *"Masalah ini kira-kira bisa dikembangkan jadi aplikasi tidak?"*
3. Kalau bisa — **tawarkan diri jadi System Analyst**, gratis pun tidak apa-apa. Tujuannya belajar + bangun portofolio.
4. **Estimasi waktu** (sambil kerja/kuliah): 1–2 minggu, paling lama 1 bulan.
5. **Output:** satu dokumen analisis sistem yang lengkap.
6. Pakai dokumen itu untuk melamar posisi **System Analyst** atau **Business Analyst**.

**Pesan penutup narasumber:** yang penting paham **step-step**-nya, bukan menghafal tools.

---

## 3. Checklist Deliverable Seorang System Analyst

Rangkuman artefak yang harus dihasilkan, urut:

- [ ] **Notulen pertemuan** (tanggal, peserta, kesepakatan)
- [ ] **Dokumen analisa kebutuhan** (kebutuhan riil + kelayakan teknologi)
- [ ] **Daftar aktor** (pengguna aplikasi + aktor di luar aplikasi)
- [ ] **Dokumen proses bisnis** (general → tervalidasi klien)
- [ ] **Diagram arsitektur aplikasi** (monolith/microservice + integrasi eksternal)
- [ ] **Activity Diagram** (alur utama)
- [ ] **Flowchart** (proses spesifik yang kompleks)
- [ ] **Wireframe / mockup UI** (gambaran ke user)
- [ ] **ERD / rancangan database**
- [ ] **API Blueprint + dokumentasi** *(opsional, jika development paralel)*

---

## 4. Pendalaman Mandiri *(di luar isi video)*

Bagian ini bukan dari video — ini bahan tambahan supaya materi di atas bisa dipelajari lebih dalam.

### 4.1 Skill Map yang Perlu Dibangun

| Kategori | Skill konkret |
|---|---|
| **Komunikasi** | Teknik wawancara requirement, active listening, menulis notulen, presentasi ke stakeholder non-teknis |
| **Analisis** | Root cause analysis (5 Whys), gap analysis, distinguishing *want* vs *need*, feasibility study |
| **Modeling** | UML (Use Case, Activity, Sequence, Class), BPMN, Data Flow Diagram, ERD |
| **Teknis** | Dasar SQL, konsep REST API, arsitektur monolith vs microservices, dasar keamanan & integrasi |
| **Dokumentasi** | SRS (Software Requirements Specification), BRD (Business Requirements Document), User Story + Acceptance Criteria |

### 4.2 Tools yang Umum Dipakai

| Kebutuhan | Tools |
|---|---|
| Diagram (UML/BPMN/ERD) | draw.io / diagrams.net (gratis), Lucidchart, Visual Paradigm, PlantUML (diagram-as-code) |
| Wireframe / mockup | Figma, Balsamiq, Whimsical |
| Dokumentasi & kolaborasi | Notion, Confluence, Google Docs |
| Manajemen requirement | Jira, Trello, ClickUp |
| Database design | dbdiagram.io, MySQL Workbench, DBeaver |
| API blueprint | Swagger / OpenAPI, Postman |

### 4.3 Format Notulen Rapat yang Rapi

Template minimal yang bisa langsung dipakai:

```
NOTULEN RAPAT
Proyek      : [nama proyek]
Tanggal     : [dd/mm/yyyy]  |  Waktu: [hh:mm - hh:mm]
Lokasi      : [onsite / online]
Peserta     : [nama - jabatan - instansi]
Notulis     : [nama]

1. AGENDA
   - ...

2. PEMBAHASAN
   - [Poin]  → [penjelasan]

3. KESEPAKATAN (DECISION)
   - D1: ...
   - D2: ...

4. ITEM TERBUKA (OPEN ISSUE)
   - O1: ... | PIC: ... | Target: ...

5. ACTION ITEM
   | No | Aksi | PIC | Deadline |
   |----|------|-----|----------|

6. LAMPIRAN
   - ...
```

### 4.4 Pertanyaan Wajib Saat Menggali Requirement

Gunakan ini di Tahap 1–2 supaya tidak ada yang terlewat:

**Tentang masalah:**
- Proses ini sekarang berjalan bagaimana? (as-is)
- Apa yang paling sering bikin repot/salah?
- Berapa lama waktu yang terbuang karena masalah ini?

**Tentang aktor:**
- Siapa saja yang terlibat dari awal sampai akhir proses?
- Siapa yang menyetujui? Siapa yang hanya melihat?
- Ada pihak luar yang ikut memicu proses ini?

**Tentang data:**
- Data apa yang dicatat sekarang? Di mana disimpannya?
- Data mana yang wajib, mana yang opsional?
- Berapa banyak data per hari/bulan?

**Tentang batasan:**
- Ada sistem lain yang harus terhubung?
- Ada aturan/regulasi yang harus dipatuhi?
- Budget dan timeline-nya seperti apa?
- Apa yang **tidak** termasuk dalam proyek ini? (scope exclusion)

### 4.5 Urutan Belajar yang Disarankan

1. **Minggu 1–2:** Kuasai notulen + teknik wawancara requirement. Latihan: wawancarai 1 orang tentang proses kerja mereka, buat notulen rapi.
2. **Minggu 3–4:** Belajar Use Case & Activity Diagram di draw.io. Latihan: modelkan proses dari hasil wawancara tadi.
3. **Minggu 5–6:** Belajar ERD + dasar SQL. Latihan: rancang database untuk kasus yang sama.
4. **Minggu 7–8:** Belajar wireframing di Figma. Latihan: buat 5–8 layar utama.
5. **Minggu 9+:** Gabungkan semua jadi **1 dokumen analisis sistem** → jadikan portofolio (persis seperti saran di video).

### 4.6 System Analyst vs Business Analyst

| Aspek | System Analyst | Business Analyst |
|---|---|---|
| Fokus utama | Solusi teknis & desain sistem | Proses bisnis & nilai bisnis |
| Output khas | Arsitektur, ERD, spesifikasi teknis, API contract | BRD, proses bisnis, analisis dampak & ROI |
| Kedekatan | Lebih dekat ke tim developer | Lebih dekat ke manajemen/stakeholder bisnis |
| Irisan | **Besar** — requirement gathering, pemodelan proses, dokumentasi, komunikasi stakeholder |

Karena irisannya besar, satu portofolio analisis sistem yang solid bisa dipakai melamar kedua posisi.

---

## 5. Poin Kunci untuk Diingat

1. **Dengar dulu, jangan langsung solusi.** Ini pembeda SA pemula dan SA matang.
2. **Notulen adalah senjata.** Tanpa catatan, scope creep tidak bisa dilawan.
3. **Validasi bertahap, bukan di akhir.** Revisi mayor di akhir = proyek gagal.
4. **SA harus paham teknologi**, karena klien tidak akan memikirkan sampai ke sana.
5. **Aktor yang terlewat = fitur yang terlewat.** Breakdown aktor sedetail mungkin.
6. **Portofolio > sertifikat.** Satu dokumen analisis nyata lebih bernilai daripada teori.
7. **Yang penting paham step-nya**, tools bisa menyusul.
