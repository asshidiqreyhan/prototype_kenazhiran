# Notulensi Rapat — Proyek BWI Kenazhiran (Modul Kenazhiran / e-Service)

> Dokumen internal Titik Terang Teknologi. Disusun dari transkrip rapat review progres proyek.
> Tanggal notulensi: 25 Juli 2026 · Tanggal rapat: (tidak tercantum eksplisit di transkrip)

---

## 1. INFORMASI RAPAT

- **Topik Pembahasan:** Review progres modul Nazhir (Master Aset & Portofolio, Pelaporan Mutasi Aset, Dashboard), penetapan aturan bisnis Wakaf Tanah vs Wakaf Uang, penyederhanaan model data pelaporan (anti-duplikasi nilai), definisi peran BWI Perwakilan vs BWI Pusat, mekanisme Cut-Off Periode, serta pembahasan awal alur Pendaftaran Nazhir & penerbitan SK.
- **Peserta:**
  - **(R)** Reyhan — Tim Teknis Titik Terang (host demo)
  - **(H)** Kang Hazar — Tim Teknis Titik Terang
  - **(RZ)** Mas Raji / Razi — Klien, BWI Pusat

---

## 2. EXECUTIVE SUMMARY

Rapat berupa walkthrough modul Nazhir yang sudah dibangun, dengan RZ memberi koreksi arah pada level **aturan bisnis dan model data**, bukan sekadar UI. Tiga keputusan struktural diambil dan berdampak langsung ke arsitektur:

1. **Pemisahan tegas Wakaf Tanah vs Wakaf Uang di level data.** Master Aset & Portofolio **hanya untuk aset tidak bergerak (tanah)**. Wakaf Uang / Wakaf Melalui Uang / Wakaf Bergerak **tidak lagi didata di Master Aset** karena sudah tercakup di Pelaporan Mutasi Aset — menghindari pencatatan ganda (dobel).
2. **Dropdown "Pilih Portofolio Aset" di Pelaporan Mutasi dihapus** — cukup pilih Periode. Jenis harta benda sudah tersedia di tab Penerimaan.
3. **Masalah inti proyek belum tuntas:** duplikasi nilai pada program berkesinambungan (nazhir menginput nilai kumulatif, sistem eksisting menjumlah ganda). Arah solusi = **entitas baru "Master Program"** + kolom `Program` di tabel penerimaan, tapi desainnya **belum final** dan diakui kompleks oleh kedua belah pihak.

Peran BWI Perwakilan diturunkan drastis menjadi **view + export saja (1 akun, read-only, tersaring per wilayah)** — semua aksi verifikasi/persetujuan ditarik ke Pusat. Rekomendasi Wakaf Uang tetap **manual di luar sistem** (menunggu konfirmasi Mas Udin). Mekanisme Cut-Off, notifikasi email menjelang deadline, dan alur pendaftaran nazhir (dengan progress timeline + generate sertifikat + e-sign/QR) disepakati arahnya untuk dikerjakan berikutnya.

**Status:** Modul pelaporan & dashboard sudah bisa didemokan tetapi belum siap; sejumlah keputusan bisnis krusial masih menggantung di sisi **pimpinan BWI** (bukan di tim teknis). Bola terbesar saat ini ada di **RZ** untuk mengunci keputusan pimpinan + mengirim aset (E-AIW, contoh SK, template).

---

## 3. POIN DISKUSI & KEPUTUSAN UTAMA (KEY DECISIONS)

### A. Arsitektur Data & Single ID (PENEKANAN KHUSUS)

- **[KEPUTUSAN — RZ] Master Aset & Portofolio = KHUSUS aset tidak bergerak (tanah).** Isinya pendataan aset tanah wakaf, angka aset, ikrar wakaf — setara data portofolio aset tidak bergerak di Siwak. Wakaf Uang **dikeluarkan** dari sini.
- **[KEPUTUSAN — RZ] Wakaf Uang / Wakaf Melalui Uang / Wakaf Bergerak selain Uang tidak didata di Master Aset.** Alasan: sudah diinput di Pelaporan Mutasi Aset (tab Penerimaan → dropdown Jenis Harta Benda Wakaf). Menaruhnya di dua tempat = **pencatatan ganda**. Nilai wakaf uang di Master Aset bersifat ikut berubah mengikuti mutasi, jadi redundan.
- **[KEPUTUSAN — RZ] Hapus dropdown "Pilih Portofolio Aset" di Pelaporan Mutasi Aset.** Cukup "Pilih Periode". Relasi laporan → portofolio digantikan oleh pemilihan jenis harta benda di tab Penerimaan.
- **[KEPUTUSAN — RZ] Upload Excel (template) harus meng-hydrate seluruh tab Pelaporan Mutasi Aset otomatis** (Penerimaan, Mutasi, dst). Template sudah memuat semua jenis harta (termasuk tidak bergerak).
- **[ARAH DESAIN — RZ & H] Entitas baru "Master Program"** untuk aset bergerak / wakaf uang. Program (mis. "Pengelolaan Sapi") didaftarkan lebih dulu, lalu pelaporan me-*link* ke program tersebut agar penambahan nilai **terdeteksi sebagai delta, bukan akumulasi ulang** → mencegah duplikasi. Tambah **kolom `Program`** di tabel tab Penerimaan (terpisah dari kolom Jenis Harta Benda). Program punya tipe: **kontinyu (berkesinambungan)** dan **sekali lapor**. *(Belum final — lihat Blocker.)*

### B. Wakaf Tanah — Kelengkapan Form (RZ)

- **[KEPUTUSAN] Tambah field & dokumen pada input Wakaf Tanah:**
  - **AIW** — input **Nomor AIW** langsung sebagai teks/form (tidak cukup hanya lampiran file).
  - **Legalitas Tanah Wakaf** — jangan hanya "Sertifikat BPN"; banyak tanah wakaf belum bersertifikat BPN.
  - **Tambah 1 file**: sertifikat tanah wakaf yang sudah balik nama/proses BPN.
  - **Koordinat lokasi** + **foto lokasi** (bukan hanya alamat teks).
  - **Peruntukan harta benda wakaf**, **Penerima manfaat** (input teks nama), **Resume/kondisi tanah wakaf saat ini**.
  - **Pertegas metadata dokumen sebagai field input** (mis. Nomor Sertifikat BPN, diterbitkan oleh BPN mana) supaya data bisa ditarik tanpa harus membuka file lagi (hindari re-entry via Excel/OCR foto).
- **[INFO — RZ] Nilai aset tanah** sudah tersedia dari **DJOP & KJPP** dan tercatat saat pengesahan — jadi acuan penilaian.
- **[TINDAK LANJUT — RZ]** Akan mengirim **E-AIW** agar isian formulir wakaf tanah bisa dilengkapi.

### C. Aturan Edit Laporan Mutasi (RZ)

- **[KEPUTUSAN] Laporan hanya boleh diedit SEBELUM dikirim ke BWI.** Setelah dikirim → edit = **pelanggaran**. Perlu didefinisikan *apa* yang boleh diedit dan *jangka waktu*-nya.
- Input penerimaan sudah bisa 1-per-1; ditambah kemampuan **copy-paste** dari data lama / sistem akuntansi nazhir.

### D. Dashboard & Frekuensi Pelaporan (RZ)

- **[KEPUTUSAN] Pelaporan per 6 bulan (semester). TIDAK ada triwulan.**
- Grafik "Pertumbuhan Aset Berkesinambungan" harus bisa **ditarik per periode/tahunan** (resume dari pelaporan). Nilai = total seluruh aset termasuk kenaikan/penurunan; agregasi jangka panjang (mis. 5 tahun: penerimaan/pengelolaan/penyaluran).
- Tab **Hasil Pengelolaan** = hasil akhir pengelolaan per semester (pendapatan − kerugian), menjadi sumber resume dashboard.
- **[KEPUTUSAN] Dua metode input:** manual (1-per-1) **dan** Excel (template). Ekspektasi mayoritas nazhir pakai Excel (copas dari sistem akuntansi).

### E. Peran BWI Perwakilan vs BWI Pusat (RZ) — PENEKANAN

- **[KEPUTUSAN] BWI Perwakilan = VIEW + EXPORT saja. Read-only. Tidak ada aksi verifikasi laporan.** Data otomatis ter-*cluster* per wilayah; tiap wilayah hanya melihat nazhir & pelaporan di wilayahnya.
- **[KEPUTUSAN] Cukup 1 akun untuk Perwakilan** (mengoreksi rencana 2 akun sebelumnya). Aksi pemberian rekomendasi & izin nazhir wakaf tanah ada di akun operasional (Mas Udin), bukan di akun view.
- **[KEPUTUSAN] Persetujuan penambahan portofolio nazhir = PUSAT saja** (bukan daerah). Review berkas: cocok → validasi; belum → revisi. Berlaku untuk **wakaf tanah**.
- **[KEPUTUSAN] Rekomendasi Wakaf Uang = MANUAL di luar sistem.** Pendaftaran nazhir wakaf uang di Pusat = 16 syarat; salah satunya rekomendasi BWI daerah (proses manual). Data final di Pusat → baru dilempar ke daerah untuk di-*view*.
- **[KEPUTUSAN — H disetujui RZ] Tidak ada verifikasi di daerah.** Nazhir daftar → diverifikasi Pusat. Nazhir lapor → data ke Pusat → ter-cluster per wilayah untuk konsumsi (view) daerah.

### F. Master NIB & Pembekuan (RZ)

- Menu Master NIB menampilkan daftar nazhir + total portofolio. **Total portofolio = aset tanah**; wakaf uang ditampilkan sebagai **summary** (penerimaan/hasil), bukan per-item.
- **[KEPUTUSAN] Pusat bisa membekukan akun nazhir** (mis. perpanjangan nazhir wakaf uang tidak dilakukan → bekukan).

### G. Cut-Off Periode & Notifikasi (RZ & H)

- **[KEPUTUSAN] Cut-off = ~1 bulan setelah periode berakhir** (contoh yang disebut: periode Jan–Jun, batas 31 Agustus — *interpretasi tanggal perlu dikonfirmasi, lihat Open Questions*). Lewat batas → nazhir tidak bisa lapor.
- **[KEPUTUSAN] Buka kunci dilakukan DI DALAM SISTEM.** Nazhir mengajukan permohonan buka kunci ke Pusat + alasan; Pusat menilai → buka bila diterima. → butuh **menu permohonan buka kunci di role Nazhir**.
- **[KEPUTUSAN] Penguncian = sekali klik untuk semua (mass lock). Buka kunci = per-nazhir (1-per-1).**
- **[KEPUTUSAN — H & RZ] Notifikasi pengingat deadline by system + email** (email terdaftar saat registrasi). Model: buat periode aktif; mendekati akhir periode → kirim pesan otomatis (panel sistem + email). Menggantikan proses RZ menyurati manual 1-per-1.

### H. SK, Pendaftaran Nazhir, & Tanda Tangan Digital (RZ & H)

- **[KEPUTUSAN] Alur pendaftaran nazhir mengikuti pola e-service:** pengajuan → verifikasi dokumen → jadwal presentasi/wawancara → keputusan → **generate sertifikat**.
- **[KEPUTUSAN] Progress timeline pendaftaran ditampilkan di akun Nazhir** (saat ini belum ada).
- **[KEPUTUSAN] Data pengurus harus bisa di-edit / dikurangi / dihapus** (di e-service eksisting tidak bisa — harus diulang; ini bug yang harus diperbaiki).
- **[KEPUTUSAN] Sertifikat/SK bisa di-review & di-view, serta di-edit bila ada typo** sebelum terbit.
- **[KEPUTUSAN — RZ] Semua SK/surat yang terbit memakai TTD digital + QR Code** ("semuanya").
- **[BELUM FINAL] Bentuk dokumen nazhir wakaf uang:** SK penuh **atau** surat tanda bukti pendaftaran 1 lembar (gaya Kemenkumham) — menunggu keputusan pimpinan. Yang **sudah final** = SK Penggantian Nazhir (sudah dikirim ke R), tinggal e-sign.

---

## 4. ACTION ITEMS (TINDAK LANJUT)

Kategori: **DB** = model data/relasi, **BL** = business logic, **UI** = antarmuka, **INT** = integrasi, **DEC** = keputusan/klien.

| # | Tugas | PIC | Kategori | Prioritas |
|---|-------|-----|----------|-----------|
| 1 | **Keluarkan Wakaf Uang dari Master Aset & Portofolio**; jadikan Master Aset khusus aset tanah/tidak bergerak | R | DB / BL | Tinggi |
| 2 | **Hapus dropdown "Pilih Portofolio Aset"** di Pelaporan Mutasi Aset; sisakan "Pilih Periode" | R | UI / BL | Tinggi |
| 3 | Rancang & implementasi **entitas "Master Program"** + kolom `Program` di tabel Penerimaan; dukung tipe program **kontinyu** & **sekali lapor**; pelaporan me-*link* ke program (anti-duplikasi/delta) | R (desain bareng H) | **DB / BL** | Tinggi (blok utama) |
| 4 | Lengkapi **form Wakaf Tanah**: Nomor AIW, Legalitas tanah (bukan hanya BPN), file sertifikat balik BPN, koordinat, foto lokasi, peruntukan, penerima manfaat (teks), resume kondisi, metadata sertifikat sebagai field | R | UI / DB | Tinggi |
| 5 | Buat **template Excel** dari format eksisting BWI, sesuaikan, **upload ke sistem**, uji auto-hydrate ke seluruh tab pelaporan, catat bug/error | R | BL / INT | Tinggi |
| 6 | **Aturan edit laporan**: editable sebelum kirim; terkunci setelah kirim; definisikan cakupan field & jangka waktu; tambah **copy-paste** input penerimaan | R (aturan dari RZ) | BL | Sedang |
| 7 | Dashboard: grafik pertumbuhan **filter per periode/tahunan**, sumber = resume pelaporan (semester) | R | UI / BL | Sedang |
| 8 | **Peran BWI Perwakilan → 1 akun, view + export, read-only, tersaring per wilayah**; hapus aksi verifikasi laporan di daerah | R | BL / UI | Tinggi |
| 9 | **Persetujuan/validasi/revisi porto (wakaf tanah) dipusatkan ke Admin Pusat**; daerah read-only | R | BL | Tinggi |
| 10 | Master NIB: total portofolio = aset tanah; wakaf uang tampil sebagai **summary**; tambah fitur **bekukan nazhir** (Pusat) | R | BL / UI | Sedang |
| 11 | **Cut-Off**: mass-lock sekali klik; **buka kunci per-nazhir**; **menu permohonan buka kunci di role Nazhir** | R | BL / UI | Sedang |
| 12 | **Sistem notifikasi deadline via email + panel** (email registrasi); periode aktif + reminder otomatis mendekati akhir | **H** | INT / BL | Sedang |
| 13 | **Skema periode pelaporan** (buat periode X bulan aktif → reminder otomatis) | **H** | BL | Sedang |
| 14 | **Alur Pendaftaran Nazhir** ala e-service: verifikasi dokumen → jadwal presentasi/wawancara → generate sertifikat; **progress timeline di akun nazhir** | R + H | BL / UI | Sedang |
| 15 | Data pengurus **bisa edit/kurangi/hapus** (perbaiki keterbatasan e-service) | R | UI / BL | Sedang |
| 16 | Sertifikat/SK: **review/view + edit typo** sebelum terbit | R | UI | Sedang |
| 17 | Integrasi **TTD digital + QR Code untuk SEMUA SK/surat** | R | INT | Sedang (nunggu izin pimpinan) |
| 18 | Kirim **akun dummy** + resume sementara via **Google Doc** untuk dievaluasi RZ | R | DEC | Tinggi |
| **B1** | **Kirim E-AIW** (formulir) untuk kelengkapan isian wakaf tanah | **RZ** | DEC | Tinggi |
| **B2** | **Kirim contoh format SK / surat tanda bukti pendaftaran nazhir** (selain SK Penggantian yang sudah dikirim) | **RZ** | DEC | Tinggi |
| **B3** | **Konfirmasi ke pimpinan**: penyederhanaan pelaporan (hanya penerimaan/pengelolaan/penyaluran; buang Dampak Pengukuran Ulang?) | **RZ** | DEC | Tinggi (blok arah) |
| **B4** | **Konfirmasi ke Mas Udin**: BWI daerah dilibatkan di sistem untuk rekomendasi, atau tetap manual | **RZ** | DEC | Tinggi |
| **B5** | **Konfirmasi ke pimpinan**: nazhir wakaf uang pakai SK penuh atau surat tanda bukti 1 lembar | **RZ** | DEC | Sedang |
| **B6** | **Koordinasi ke sekretaris**: biaya per-tanda tangan e-sign + izin pimpinan untuk TTD tiap SK | **RZ** | DEC | Sedang |
| **B7** | Beri **akses/akun** ke RZ untuk evaluasi tiap ada update | R (sedia akses) / RZ (review) | DEC | Sedang |

---

## 5. BLOCKER & OPEN QUESTIONS

### Blocker Teknis / Desain
1. **[BLOKER UTAMA] Duplikasi nilai program berkesinambungan.** Nazhir menginput **nilai kumulatif** (mis. program sapi Jul = 500 jt, Des input 600 jt), sistem eksisting menjumlah ganda menjadi 1,1 M. Akar masalah = **model data delta vs kumulatif** dan ketiadaan tracking per-transaksi. Arah solusi "Master Program" + pelaporan berbasis program (1 template diteruskan sepanjang tahun) **belum final dan diakui kompleks** oleh RZ maupun H. Perlu keputusan desain data sebelum implementasi tab Penerimaan/Pengelolaan diselesaikan.
2. **Beban sistem "akuntansi penuh".** Adanya *Dampak Pengukuran Ulang* + *Hasil Pengelolaan* membuat e-service berat & rawan human error. RZ ingin disederhanakan; secara teknis ini mengubah scope tab pelaporan.
3. **Interpretasi aturan Cut-Off.** Contoh RZ ("periode Jan–Jun → cutoff 31 Agustus, 1 bulan setelah") ambigu (31 Agustus ≈ 2 bulan setelah Juni). Aturan tanggal pasti (offset dari akhir periode) **belum terkunci**.

### Open Questions (menunggu keputusan — mayoritas di sisi klien/pimpinan)
- **[Pimpinan BWI]** Apakah pelaporan boleh disederhanakan (buang Dampak Pengukuran Ulang) atau **wajib mengikuti format existing**? — RZ ingin simpel, pimpinan ingin sesuai eksisting. **Belum ada keputusan; ini menentukan scope.** *(Action B3)*
- **[Mas Udin / BWI Daerah]** Apakah BWI daerah dilibatkan di sistem untuk pemberian rekomendasi, atau **tetap manual di luar sistem**? *(Action B4)*
- **[Pimpinan BWI]** Bentuk dokumen nazhir wakaf uang: **SK** atau **surat tanda bukti pendaftaran 1 lembar**? *(Action B5)*
- **[Pimpinan + Sekretaris BWI]** Izin TTD digital untuk **setiap** SK dan konsekuensi **biaya per tanda tangan** — perlu disepakati sebelum integrasi e-sign massal. *(Action B6)*
- **[Definisi bisnis]** Aturan detail "apa yang boleh diedit" dan "jangka waktu edit" pada laporan sebelum final — perlu dijabarkan RZ. *(terkait Action 6)*
- **[Data]** Kepastian ketersediaan & format nilai aset tanah dari **DJOP/KJPP** untuk auto-fill penilaian. *(terkait Action 4)*

---

### Catatan Penutup
Fokus sprint berikutnya sebaiknya diarahkan ke **penguncian model data** (Item 1–3): pemisahan Tanah/Uang di Master Aset, penghapusan relasi portofolio di pelaporan, dan **desain final Master Program**. Item UI/notifikasi/pendaftaran (12–17) dapat berjalan paralel oleh H, tetapi implementasi tab Penerimaan/Pengelolaan **tidak boleh difinalisasi sebelum keputusan pimpinan (B3)** turun, karena berpotensi rework besar.
