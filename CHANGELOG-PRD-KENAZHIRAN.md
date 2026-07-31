# Changelog PRD — Modul Kenazhiran

Ringkasan perubahan yang diterapkan pada `PRD-FLOW-DESIGN-KENAZHIRAN.md` beserta dampaknya ke prototipe. Entri terbaru di atas.

---

## 2026‑07‑29 — HBW tanpa validasi Pusat: hapus kolom Status hierarki + rapikan alur Sub-ID

**Bagian PRD terdampak:** §5.6 (Hierarki), §5.3 (Master HBW).

**Apa yang berubah:**
- **`pusat-hierarki.html`** — kolom **"Status" ("Tervalidasi") dihapus** dari tabel Daftar Sub-ID (karena penambahan HBW tidak lagi divalidasi Pusat). Empty-state colspan disesuaikan (7→6).
- **Sisi Nazhir — penegasan tambah HBW langsung aktif tanpa validasi:**
  - **`nazhir-aset-detail.html`** — timeline "Tahap Proses" disederhanakan dari 4 tahap verifikasi (Diajukan → Verifikasi Sekretariat → E‑Sign Pimpinan → SK Terbit) menjadi **2 tahap: Dibuat → Aktif & Terdaftar**; keterangan status `aktif` tak lagi menyebut "Disetujui BWI Pusat" (diganti "langsung aktif tanpa perlu verifikasi").
  - **`nazhir-aset.html`** — opsi filter status **Verifikasi Pusat / Revisi / Ditolak dihapus** (HBW hanya Draft/Aktif/Nonaktif); teks footer usang diperbarui (langsung Aktif tanpa persetujuan; Master HBW menampung Tanah & Uang).
- Alur tambah HBW (`nazhir-aset-tambah.html`) memang sudah **Draft → Aktif** langsung; tidak ada status "Menunggu Verifikasi". Fungsi lain tidak diubah.

**File tersentuh:** `pusat-hierarki.html`, `nazhir-aset-detail.html`, `nazhir-aset.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Pagination semua tabel (default 5 baris/halaman)

**Bagian PRD terdampak:** Konvensi UI global (§1) — berlaku untuk seluruh role.

**Apa yang berubah:** Semua tabel data di seluruh role kini punya **pagination default 5 baris/halaman** dengan pola konsisten: footer **"Menampilkan {from}–{to} dari {total}"** + tombol **Sebelumnya/Berikutnya** (disabled di ujung) + indikator halaman; **reset ke halaman 1** saat search/filter/tab berubah; footer disembunyikan bila 0 data; nomor urut kontinu antar‑halaman. Beberapa tabel yang tadinya baris statis dikonversi ke render‑JS (mis. `pusat-master.html`, `pusat-hierarki.html`, `wilayah-master.html`, `wilayah-dashboard.html`, `nazhir-dokumen.html`) sambil mempertahankan data & tampilan.

**Tabel tercakup (16 file):**
- **Pusat:** Pendaftaran Nazhir, NIB Nazhir (Direktori), Hierarki Sub‑ID, Periode Laporan + Permohonan Buka Kunci, Kelola Broadcast, Master Pengguna.
- **Nazhir:** Master HBW (Sub‑ID), Riwayat SK, Laporan HBW, Mutasi Aset, Permohonan Buka Kunci, Log Dokumen.
- **Sekretaris & Ketua:** Daftar Pendaftaran / Antrean Pengesahan.
- **Wilayah:** Buku Induk Nazhir, Antrean Laporan Mutasi (dashboard). (`wilayah-rekap`/`wilayah-review` tak punya tabel daftar.)

**File tersentuh:** 16 `*.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Hierarki NIB (+2 kolom) · Periode (label buka kunci) · Kelola Broadcast (aksi & summary)

**Bagian PRD terdampak:** §5.6 (Hierarki), §5.7 (Kelola Broadcast), §5.8 (Periode / Buka Kunci).

**Apa yang berubah:**
- **`pusat-hierarki.html` — tabel Sub-ID +2 kolom:** **"Jumlah Laporan"** (berapa kali HBW ini dilaporkan — dihitung live dari `data_laporan_program_bwi` per Sub-ID, minimal nilai seed) & **"Mulai Dikelola"** (tanggal awal HBW dikelola/diunggah). Seed diberi `laporan` & `tglKelola`.
- **`pusat-periode.html` — Permohonan Buka Kunci:** label periode lama "Semester X ‑ YYYY" **dinormalisasi ke rentang bulan** saat render (mis. "Januari – Juni 2026") agar konsisten dengan template periode fleksibel; seed demo diperbarui ke format rentang bulan.
- **`pusat-broadcast.html` — Kelola Broadcast:**
  - Kolom **Aksi tabel disederhanakan menjadi satu tombol "Lihat Detail"**. Aksi Edit / Aktifkan‑Nonaktifkan / Hapus **dipindah ke dalam modal Detail** yang **diperbesar** (`max-w-2xl`, tinggi maks + scroll) dengan footer aksi (Hapus di kiri; Tutup · Edit · Aktifkan/Nonaktifkan di kanan; tombol toggle menyesuaikan status).
  - **Banner ringkasan hijau besar diganti 3 kartu summary** yang lebih rapi (Total Template / Aktif / Sudah Terkirim) — konsisten dengan gaya kartu ringkasan halaman lain.

**File tersentuh:** `pusat-hierarki.html`, `pusat-periode.html`, `pusat-broadcast.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Dashboard Pusat: kosongkan Antrean Finalisasi + label status "Disetujui"

**Bagian PRD terdampak:** §5.6 (Dashboard Pusat), §5.10 (Pendaftaran Nazhir) — data/label tampilan.

**Apa yang berubah:**
- **`pusat-dashboard.html` — Antrean Finalisasi Laporan Mutasi dikosongkan.** 3 baris dummy (Yayasan Wakaf Nusantara, Baitul Maal Hidayatullah, Yayasan Dompet Peduli Ummat) dihapus → **empty‑state** "Belum ada laporan mutasi menunggu eksekusi". Badge "24 Menunggu Eksekusi" → **"0 Menunggu Eksekusi"**; footer → "Belum ada laporan menunggu eksekusi." Antrean akan terisi saat Nazhir benar‑benar mengirim laporan mutasi.
- **`pusat-pendaftaran.html` & `-detail.html` — label status `aktif` "Disetujui / Aktif" → "Disetujui"** (badge & opsi filter).

**File tersentuh:** `pusat-dashboard.html`, `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`.

---

## 2026‑07‑29 — Rename & urut ulang menu Admin Pusat + Reset Sandi pindah ke Detail

**Bagian PRD terdampak:** §3.3 (Peta Menu Pusat), §5.14 (Master Pengguna).

**Apa yang berubah:**
- **Rename menu (semua sidebar Pusat):** "Dashboard Nasional" → **"Dashboard"**; "Master NIB Tunggal" → **"NIB Nazhir"**. (Judul halaman/h1 tetap.)
- **Urutan sidebar Pusat ditata ulang** mengikuti urgensi/tupoksi (monitor → siklus nazhir & akun → konfigurasi → komunikasi): **Dashboard · Pendaftaran Nazhir · NIB Nazhir · Master Pengguna · Periode Laporan · Kelola Broadcast**. Diterapkan konsisten di **11 halaman Pusat** (active‑state & blok menu tersembunyi "Pengajuan Portofolio" tetap terjaga).
- **Master Pengguna — Reset Sandi dipindah ke Detail.** Tombol "Reset Sandi" **dihapus dari kolom Aksi** (kini hanya Lihat · Edit · Hapus). Reset sandi kini ada **di dalam modal Detail pengguna**: langsung isi **Kata Sandi Baru + Konfirmasi** → "Simpan Sandi Baru". Modal Reset terpisah dihapus.

**File tersentuh:** 11 `pusat-*.html` (sidebar rename+urut), `pusat-master-pengguna.html` (reset di detail), `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Tombol "Lihat Detail Pendaftaran" jadi secondary (pasca‑submit)

**Bagian PRD terdampak:** §5.2 (Dashboard/overlay Calon) — detail tampilan.

**Apa yang berubah (overlay Calon di seluruh `nazhir-*.html`):**
- Tombol CTA overlay dibuat **kontekstual via `CTA_WARNA`**: saat berkas masih bisa diedit (draft/revisi/ditolak) tombol tetap **primary** (Lanjutkan Pendaftaran / Perbaiki Berkas / Daftar Ulang); saat **sudah di‑submit** (diajukan/diverifikasi/terjadwal/persetujuan) → CTA **"Lihat Detail Pendaftaran"** kini **secondary** (outline brand), karena hanya untuk melihat progres. Diterapkan di banner, kartu progres dashboard, dan empty‑state.
- Tombol **"Ajukan Buka Kunci"** (banner cut‑off) tetap primary — tidak terpengaruh.

**File tersentuh:** 14 `nazhir-*.html` (overlay Calon).

---

## 2026‑07‑29 — Perbaikan progres berkas calon (106% & progres jenis lain "kebawa")

**Bagian PRD terdampak:** §5.2 (Dashboard/overlay Calon) — perbaikan bug.

**Apa yang berubah (overlay Calon di seluruh halaman `nazhir-*.html`):**
- **Fix total berkas jenis‑aware.** `progres()` sebelumnya hardcode `total:16` di 12 halaman → menghasilkan persentase mustahil (mis. **17/16 = 106%**). Kini total mengikuti jenis pendaftaran calon (**Wakaf Uang = 17, Wakaf Tanah = 10**), konsisten dengan dashboard.
- **Pisahkan progres antar jenis.** Pada halaman HBW untuk jenis **berbeda** dari jenis yang sedang didaftarkan calon (mis. buka menu Wakaf Tanah padahal mendaftar Wakaf Uang), empty‑state **tidak lagi menampilkan progress bar** pendaftaran (yang tadinya "kebawa‑bawa"). Diganti keterangan: *"Anda sedang mendaftar sebagai Nazhir Wakaf {X}. Menu Wakaf {Y} akan tersedia setelah akun aktif…"* + tombol **Ke Dashboard**. Pada halaman jenis yang sesuai / non‑HBW, progress tetap tampil normal.

**File tersentuh:** 14 `nazhir-*.html` (overlay Calon).

---

## 2026‑07‑29 — Nama berkas terunggah bisa di‑pratinjau (klik)

**Bagian PRD terdampak:** §5.1 (Fase 2 — kelengkapan berkas) — detail tampilan.

**Apa yang berubah (di `nazhir-pendaftaran.html`, berlaku untuk berkas Wakaf Tanah & Uang, termasuk pendaftaran tambahan):**
- Nama berkas yang sudah terunggah kini tampil sebagai **tautan ber‑underline** (menandakan bisa diklik) → membuka **modal pratinjau**.
- Saat unggah **gambar/PDF ≤ 1.5 MB**, `dataUrl` disimpan sehingga pratinjau menampilkan isinya (`<img>`/`<iframe>`); untuk berkas contoh (demo) atau format lain, modal menampilkan kartu informatif "pratinjau tidak tersedia pada mode demo". Modal ditutup via tombol ✕, klik latar, atau Esc.

**File tersentuh:** `nazhir-pendaftaran.html`.

---

## 2026‑07‑29 — Hapus tombol sekunder "Kembali ke Dashboard" pada empty‑state HBW belum‑terdaftar

**Bagian PRD terdampak:** §5.3 & §5.4 (empty‑state gating) — detail tampilan.

**Apa yang berubah:** Pada empty‑state "Belum Terdaftar sebagai Nazhir Wakaf X" (halaman `nazhir-aset.html`/`nazhir-laporan-program.html` untuk jenis yang belum dimiliki), tombol sekunder **"Kembali ke Dashboard" dihapus** — kini hanya menyisakan aksi utama (**Daftar Nazhir Wakaf X** / **Lanjutkan Pendaftaran** / **Lihat Progres**). Berlaku untuk kedua jenis (Uang & Tanah). Tombol serupa di konteks lain (topbar `nazhir-pendaftaran.html`, empty‑state SK `nazhir-sk.html`) tidak terpengaruh.

**File tersentuh:** 13 `nazhir-*.html` (blok gating `__gatingHBW`).

---

## 2026‑07‑29 — Akses HBW multi‑jenis + Pendaftaran HBW Tambahan

**Bagian PRD terdampak:** §5.3 & §5.4 (gating), §5.15 (baru — Pendaftaran HBW Tambahan), §4 (Model Data).

**Apa yang berubah:**
- **Gating tidak lagi mengunci.** Menu Master HBW & Laporan HBW jenis yang belum dimiliki **tetap bisa diakses**; submenu tidak lagi dinonaktifkan/digembok. Halaman jenis lawan menampilkan **empty‑state informatif + tombol "Daftar Nazhir Wakaf X"** (bukan lagi "Belum Memenuhi Syarat" buntu).
- **Nazhir aktif kini bisa mendaftar HBW jenis kedua** (sebelumnya tak bisa). Model: `pendaftaran_nazhir_bwi.jenisAktif` (array jenis disetujui) + record baru **`pendaftaran_tambahan_bwi`**.
- **Alur tambahan:** `daftarHBWTambahan(x)` → `nazhir-pendaftaran.html?tambah=1` (berkas jenis x) → Ajukan → pipeline sebagai pemohon **`REAL2`** (badge "HBW Tambahan") di Admin Pusat/Sekretaris/Ketua → **Ketua e‑Sign** → jenis x ditambahkan ke `jenisAktif` → menu jenis itu aktif penuh. Bila tambahan sedang diproses/draft, empty‑state menampilkan status/lanjutkan, bukan tombol daftar baru.
- Graduasi utama memastikan `jenisAktif` memuat `jenisNazhir`.

**File tersentuh:** 13 `nazhir-*.html` (gating IIFE), `nazhir-pendaftaran.html`, `nazhir-dashboard.html`, `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `sekretaris-pendaftaran.html`, `ketua-pendaftaran.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Redesain UI halaman Login (split‑screen, lebih clean)

**Bagian PRD terdampak:** §5.1 (Login) — implementasi tampilan.

**Apa yang berubah (di `kenazhiran-login.html`):**
- Layout diubah dari kartu tengah di atas latar gelap → **split‑screen** ala e‑office: **panel brand kiri** (gradient teal + pola titik + logo lockup + heading + 3 poin fitur + copyright) dan **panel form kanan** (putih, bersih). Responsif: di layar kecil panel kiri disembunyikan, logo tampil versi mobile.
- **Fungsi & kredensial tidak berubah**: field **Email atau NIB** + kata sandi (show/hide), ingat perangkat, lupa sandi, pesan error ber‑ikon + animasi getar, tombol masuk + spinner, dan CTA **"Daftar sebagai Nazhir"**. Semua ID & logika `<script>` dipertahankan. **Tidak ada tombol Login SSO** (sesuai permintaan).

**File tersentuh:** `kenazhiran-login.html`.

---

## 2026‑07‑29 — Posisi QR pada SK dipindah ke area tanda tangan

**Bagian PRD terdampak:** §5.13 (Ketua — Preview SK) — implementasi tampilan.

**Apa yang berubah:** Pada preview Surat Keputusan, **QR Code terverifikasi dipindah ke dalam blok tanda tangan** (kolom kanan) — berada di bawah baris *"Ketua Badan Wakaf Indonesia,"* dan di atas nama Ketua, berfungsi sebagai stempel e‑Sign (bukan blok terpisah di kiri lagi). Label *"✓ Terverifikasi — Ketua BWI"*, nama Ketua, catatan "Ditandatangani secara elektronik", dan **ID e‑Sign** tersusun rapi di bawah QR. Varian **Draf** menampilkan placeholder "QR terbit setelah TTD" pada posisi yang sama.

**File tersentuh:** `ketua-pendaftaran.html`, `pusat-pendaftaran.html`, `sekretaris-pendaftaran.html`, `nazhir-sk.html`.

---

## 2026‑07‑29 — Master Pengguna hanya menampilkan akun AKTIF

**Bagian PRD terdampak:** §5.14 (Master Pengguna).

**Apa yang berubah (di `pusat-master-pengguna.html`):**
- Nazhir mandiri (`pendaftaran_nazhir_bwi`) kini **hanya ditampilkan bila `status === 'aktif'`** (sebelumnya semua status ikut tampil). **Calon yang masih mendaftar (draft/diajukan/diverifikasi/…) tidak lagi bocor ke Master Pengguna** — mereka tetap dikelola di menu **"Pendaftaran Nazhir"** hingga disahkan Ketua.
- Detail "Berkas Terunggah" dibuat jenis‑aware (dari 10/17 dokumen sesuai `jenisNazhir`).

**File tersentuh:** `pusat-master-pengguna.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Pilih jenis nazhir dipindah ke dashboard (setelah buat akun & login)

**Bagian PRD terdampak:** §5.1 (Login & Pendaftaran), §5.2 (Dashboard).

**Apa yang berubah:**
- **Login:** dikembalikan ke **satu tombol "Daftar sebagai Nazhir"** → `kenazhiran-daftar.html` (tanpa `?jenis`). Pemilihan jenis tidak lagi di halaman login.
- **`kenazhiran-daftar.html`** jadi **jenis‑agnostik**: pembuatan akun tidak memilih jenis; `jenisNazhir` disimpan **kosong** (diisi nanti di dashboard). Judul & teks digeneralkan ("Buat Akun Nazhir").
- **`nazhir-dashboard.html` (mode Calon):** bila `jenisNazhir` kosong, dashboard menampilkan **pemilih jenis** — keterangan "Anda sudah masuk, menu lain belum aktif" + 2 kartu **Nazhir Wakaf Uang** (17 berkas) / **Nazhir Wakaf Tanah** (10 berkas). `pilihJenisNazhir()` menyetel `jenisNazhir`+`jenis` lalu mengarahkan ke `nazhir-pendaftaran.html`. Progres berkas dashboard kini jenis‑aware (10/17).
- **`nazhir-pendaftaran.html`:** guard mengalihkan Calon yang **belum memilih jenis** kembali ke dashboard.
- Alur lanjutan tetap: lengkapi berkas → verifikasi Admin Pusat → diteruskan Sekretaris → e‑Sign Ketua.

**File tersentuh:** `kenazhiran-login.html`, `kenazhiran-daftar.html`, `nazhir-dashboard.html`, `nazhir-pendaftaran.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Jenis Nazhir saat daftar (Wakaf Tanah vs Uang) + gating menu HBW

**Bagian PRD terdampak:** §5.1 (Login & Pendaftaran), §5.3 (Master HBW), §5.4 (Laporan HBW), §4 (Model Data).

**Apa yang berubah:**
- **Login — 2 pilihan daftar:** tombol **Nazhir Wakaf Tanah** & **Nazhir Wakaf Uang** → `kenazhiran-daftar.html?jenis=tanah|uang`.
- **`kenazhiran-daftar.html`** menyimpan **`jenisNazhir`** (`'tanah'|'uang'`, fallback `'uang'`) di `pendaftaran_nazhir_bwi`; judul & teks menyesuaikan jenis.
- **`nazhir-pendaftaran.html` — checklist berkas per jenis** (layout/pengelompokan tetap):
  - Wakaf **Uang**: 17 berkas (16 wajib + 1 opsional), 4 kelompok.
  - Wakaf **Tanah**: **10 berkas** (semua wajib) acuan e‑service — 3 kelompok (Legalitas & Wakaf / Data Nazhir / Permohonan KUA): Surat Pengesahan Nazhir, Sertifikat Wakaf, AIW/APAIW, Jenis HBW, KTP Nazhir, Riwayat Hidup Nazhir, Kegiatan Nazhir, Alamat Sekretariat/Telp/Fax, Surat Permohonan ke KUA, Surat Pengantar dari KUA.
  - `SEKSI`/`BERKAS` menjadi alias set aktif; overlay progres calon jenis‑aware (10/17).
- **Verifikasi Pusat & Sekretaris jenis‑aware:** `BERKAS_UANG` + `BERKAS_TANAH` + helper `berkasUntuk(record)` di `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `sekretaris-pendaftaran.html` (dummy tetap Uang).
- **Gating menu HBW (sisi Nazhir):** bila Nazhir mandiri hanya satu jenis, submenu **Master HBW** & **Laporan HBW** jenis lawan **dinonaktifkan** (opacity + gembok "Terkunci"); halaman `nazhir-aset.html`/`nazhir-laporan-program.html?jenis=<lawan>` menampilkan empty‑state *"Belum Memenuhi Syarat…"*. Aktif hanya untuk Nazhir mandiri (`sesi.nama === pendaftaran.namaBadanHukum`); akun demo tak digating; **Pelaporan Mutasi Aset** tak digating. Skrip gating (IIFE ber‑guard `__gatingHBW`) disisipkan ke **13 halaman nazhir** (termasuk varian React‑aware di `nazhir-aset-tambah.html`).

**File tersentuh:** `kenazhiran-login.html`, `kenazhiran-daftar.html`, `nazhir-pendaftaran.html`, `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `sekretaris-pendaftaran.html`, 13 halaman `nazhir-*.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Menu "Master Pengguna" (Admin Pusat)

**Bagian PRD terdampak:** §3.3 (Peta Menu Pusat), §4 (Model Data), §5.14 (Master Pengguna).

**Apa yang berubah:**
- **Halaman baru `pusat-master-pengguna.html`** untuk mengelola akun pengguna (terutama Nazhir yang lupa email/ID atau password). **Tanpa tombol Tambah** (nazhir wajib daftar sendiri); Admin hanya **Lihat / Edit / Reset Password / Hapus**.
- **Reset Password langsung berlaku untuk login** — ditulis ke `akun_kenazhiran_bwi_v2[i].sandi` (akun bawaan) atau `pendaftaran_nazhir_bwi.sandi` (nazhir mandiri), min. 6 karakter + konfirmasi.
- Tabel memuat **timestamp pendaftaran** (`tglDaftar` → fallback `tglAjukan`). `kenazhiran-daftar.html` menstempel `tglDaftar` saat pendaftaran dibuat.
- **Proteksi:** akun `adminpusat@bwi.go.id` tidak dapat dihapus (cegah lockout).
- **Menu "Master Pengguna"** ditambahkan setelah "Pendaftaran Nazhir" di sidebar **11 halaman Pusat** (ikon user‑group), aktif hanya di halaman Master Pengguna.

**File tersentuh:** `pusat-master-pengguna.html` (baru), 10 sidebar Pusat lainnya, `kenazhiran-daftar.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Penamaan dokumen pendaftaran diselaraskan dengan e‑service BWI

**Bagian PRD terdampak:** §5.1 (Login & Pendaftaran), §7.2 (diagram).

**Apa yang berubah:**
- **Daftar berkas pendaftaran** (`nazhir-pendaftaran.html`) di‑*rename* mengikuti **penamaan e‑service BWI eksisting**, dari 16 → **17 dokumen** (16 wajib + **1 opsional**: "Surat Tanda Bukti Pendaftaran Nazhir Sebelumnya (jika ada)"). Contoh nama baru: *Akta Pendirian, Dokumen Pengesahan Kemenkumham, Dokumen NPWP, Dokumen Surat Keterangan Domisili dari Kelurahan, Rekomendasi BWI Perwakilan Provinsi/Kota/Kabupaten, Dokumen Rekomendasi LKSPWU, Permohonan Data Pengurus Yayasan…, Sertifikat Kompetensi Bidang Pengelola Wakaf Minimal 2 Orang, Company Profile Yayasan/Organisasi, Rencana Kerja, Biaya Operasional Minimal 30 Juta (Buku Rekening), Surat Permohonan, Setia kepada NKRI, Laporan Data Wakaf Bulanan, Bersedia Diaudit, Laporan Pelaksanaan Wakaf per 6 Bulan*.
- **Layout & pengelompokan tetap** (4 seksi + navigasi 30% / konten 70%), sesuai permintaan.
- **Sinkron lintas peran:** array `BERKAS` (key + label) disamakan di **`nazhir-pendaftaran.html`, `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `sekretaris-pendaftaran.html`** agar checklist verifikasi Pusat/Sekretaris memakai nama & `key` yang sama. Hitungan progres di overlay calon disesuaikan (16 → 17).

**File tersentuh:** `nazhir-pendaftaran.html`, `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `sekretaris-pendaftaran.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Dokumen PRD/Flow HBW terperinci + pesan kesalahan login

**Dokumen baru dibuat (field‑level, dari kode nyata):**
- **`PRD-MASTER-HBW-KENAZHIRAN.md`** — Master HBW Wakaf Tanah & Uang: seluruh seksi/field form tambah, `KATEGORI` (8), `OPSI_*` (Program/Instrumen/Legalitas/Penilaian/Peruntukan/BWI), DOKUMEN_WAJIB per jenis (termasuk STBPN), `detailTanah`, model `data_portofolio_bwi`, siklus status, daftar/detail/SK.
- **`PRD-LAPORAN-HBW-KENAZHIRAN.md`** — Laporan HBW Wakaf Tanah & Uang: form isi per jenis, formula `hitungUang()`/`hitungTanah()`/`hitungSelisih()`, `baselineSSOT`, kolom tabel dinamis, modal detail (`dtUang`/`dtTanah`), cut‑off & buka kunci, jalur Mutasi Aset.
- **`FLOW-DESIGN-HBW-KENAZHIRAN.md`** — spesifikasi UI/tampilan untuk developer: wireframe ASCII per halaman, komponen, state, alur interaksi, dan checklist implementasi.

**Perubahan kode:**
- **Login — pesan kesalahan lebih jelas** (`kenazhiran-login.html`): kotak error ber‑ikon + animasi getar (`animate-shake`), pesan spesifik per kasus (kolom kosong / Email‑NIB tidak terdaftar / kata sandi salah), dan pesan otomatis hilang saat pengguna mengetik lagi.

**File tersentuh:** `PRD-MASTER-HBW-KENAZHIRAN.md` (baru), `PRD-LAPORAN-HBW-KENAZHIRAN.md` (baru), `FLOW-DESIGN-HBW-KENAZHIRAN.md` (baru), `kenazhiran-login.html`.

---

## 2026‑07‑29 — Login: hapus selektor akun, identitas Email/NIB

**Bagian PRD terdampak:** §2 (Peran & Akun), §4 (Model Data), §5.1 (Login), §7.1 (diagram).

**Apa yang berubah (di `kenazhiran-login.html`):**
- **Panel "Akun demo" (selektor akun) dihapus.** Login murni **Email/NIB + kata sandi**; peran dikenali otomatis dari akun yang cocok (tanpa memilih peran).
- **Nazhir aktif bisa login via email ATAU NIB.** Kolom diubah menjadi **"Email atau NIB"**; logika pencocokan menerima email maupun NIB (case‑insensitive) untuk akun bawaan **dan** untuk pendaftaran mandiri yang sudah `aktif`. Akun nazhir demo diberi field **`nib: 'NZHR-BDG-170'`**.
- **Calon Nazhir yang mendaftar** langsung dikenali di login via **email + sandi** yang diisikannya saat daftar (tersimpan di `pendaftaran_nazhir_bwi`; mode `calon`, lalu `aktif` setelah disahkan Ketua).
- Fungsi khusus panel demo (`renderAkunDemo`, `toggleAkunDemo`, `isiAkun`) dihapus; helper `daftarAkun()`/`seedAkun()` tetap.

**File tersentuh:** `kenazhiran-login.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Revisi wewenang persetujuan (Admin memantau, Sekretaris & Ketua menyetujui) + Sertifikat ber‑QR

**Bagian PRD terdampak:** §5.10 (Pendaftaran Nazhir), §5.12–§5.13 (Sekretaris & Ketua), §6.1 (siklus), §7.2 (diagram).

**Apa yang berubah:**
- **Admin Pusat dibatasi.** Wewenang Admin di menu Pendaftaran Nazhir kini **hanya s.d. input hasil wawancara** (Verifikasi → Jadwal → Hasil). Tombol **"Setujui" dihapus** — Admin **tidak bisa menyetujui/menerbitkan SK**. Tab **"Persetujuan"** diubah menjadi **"Persetujuan & Pengesahan"** yang **read‑only** dan menampilkan progres `persetujuan → ttd → aktif` (Admin tetap tahu progres; bisa Preview SK saat `aktif`).
- **Rantai persetujuan disederhanakan:** `... → hasil Lulus → persetujuan → [Sekretaris] Teruskan ke Ketua → ttd → [Ketua] e‑Sign → aktif`. Status perantara `sekretariat` **tidak lagi dihasilkan** (Sekretaris kini beraksi langsung dari status `persetujuan`; badge lama tetap dikenali demi kompatibilitas).
- **Sekretaris (`sekretaris-pendaftaran.html`):** meneruskan dari status `persetujuan` (kolom tabel "Disetujui Pusat" → **"Hasil Wawancara"**); dapat **"Lihat Sertifikat"** (SK ber‑QR) setelah `aktif`. Timeline detail memuat presentasi → hasil → diteruskan → ditandatangani.
- **Ketua (`ketua-pendaftaran.html`):** e‑Sign menerbitkan **Sertifikat/SK dengan QR Code terverifikasi** (bukan sekadar kotak e‑Sign). Preview menampilkan QR + tanda tangan; versi Draf menampilkan "QR terbit setelah TTD".
- **Sertifikat ber‑QR muncul di semua sisi:** fungsi `qrKode()` (SVG QR bergaya, offline) ditambahkan ke render SK di **`pusat-pendaftaran.html`**, **`sekretaris-pendaftaran.html`**, **`ketua-pendaftaran.html`**, dan **`nazhir-sk.html`** (sisi calon/nazhir) — semua memakai `ttdOleh`/`ttdId` sehingga konsisten "Terverifikasi — Ketua BWI".

**File tersentuh:** `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `sekretaris-pendaftaran.html`, `ketua-pendaftaran.html`, `nazhir-sk.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Hierarki NIB: hapus tab "Semua" (hanya Wakaf Uang & Tanah)

**Bagian PRD terdampak:** §5.6 (Master NIB / Hierarki).

**Apa yang berubah (di `pusat-hierarki.html`):**
- Tabel "Daftar Sub‑ID Portofolio Aktif" kini **hanya punya 2 tab: Wakaf Uang & Wakaf Tanah** — tab **"Semua" dihapus** agar portofolio selalu terpisah per jenis. Tab aktif **default: Wakaf Uang**.
- Header kolom nilai disederhanakan: selalu **"Saldo Terkini"** (Wakaf Uang) atau **"Luas Terkini"** (Wakaf Tanah) — tidak ada lagi "Saldo / Luas Terkini". Logika `hitungSubId()`/`renderHierarki()` dibersihkan dari cabang `'semua'`.

**File tersentuh:** `pusat-hierarki.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Role baru Sekretaris & Ketua BWI + rantai tanda tangan pendaftaran

**Bagian PRD terdampak:** §2 (Peran & Akun), §3.5–§3.6 (menu role baru), §4 (Model Data), §5.10 (Pendaftaran Nazhir), §5.12–§5.13 (Sekretaris & Ketua), §6.1 (siklus pendaftaran).

**Apa yang berubah:**
- **Dua role baru** ditambahkan: **Sekretaris BWI** (`sekretaris@bwi.go.id`) dan **Ketua BWI** (`ketua@bwi.go.id`) — sandi `password`. Ditambahkan ke `AKUN_SEED` di `kenazhiran-login.html` + `MODE_PERAN`. `seedAkun()` kini **menggabungkan akun seed baru** ke `akun_kenazhiran_bwi_v2` yang sudah ada (tanpa menimpa akun lama), jadi akun baru muncul tanpa perlu clear storage. Keduanya otomatis muncul di panel "Akun demo".
- **Rantai tanda tangan berjenjang** disisipkan ke pipeline pendaftaran: `persetujuan → sekretariat → ttd → aktif`.
  - **Admin Pusat**: tombol tahap Persetujuan berubah dari "Setujui & Generate SK" → **"Setujui & Teruskan ke Sekretariat"** (status `sekretariat`, belum terbit SK). Status `sekretariat`/`ttd` tampil read‑only di panel keputusan.
  - **Halaman baru `sekretaris-pendaftaran.html`**: pantau **semua** pendaftaran + kartu ringkasan + filter; tombol **"Teruskan ke Ketua"** untuk status `sekretariat` → `ttd` (`diteruskanOleh`, `tglTeruskan`); modal detail read‑only + riwayat.
  - **Halaman baru `ketua-pendaftaran.html`**: antrean pengesahan; **TTD digital via passphrase** (demo `bwi2026`) untuk status `ttd` → `aktif`, terbit **NIB + SK** dengan metadata e‑Sign (`ttdOleh`, `tglTtd`, `ttdId`). Preview SK menampilkan blok tanda tangan elektronik + versi **Draf** sebelum ditandatangani.
- **Model data pendaftaran** bertambah field: `disetujuiPusat`, `diteruskanOleh`, `tglTeruskan`, `ttdOleh`, `tglTtd`, `ttdId`. Status dummy baru: `sekretariat`, `ttd` (+ seed contoh D8/D9). `persist()` di halaman Pusat diperluas agar field baru tersimpan untuk pemohon REAL.

**File tersentuh:** `kenazhiran-login.html`, `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `sekretaris-pendaftaran.html` (baru), `ketua-pendaftaran.html` (baru), `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Master NIB "Lihat Hierarki": tab Wakaf Uang/Tanah + kolom Saldo/Luas tak ambigu

**Bagian PRD terdampak:** §5.6 (Master NIB / Hierarki).

**Apa yang berubah (di `pusat-hierarki.html`):**
- Tabel **"Daftar Sub‑ID Portofolio Aktif"** dijadikan render‑JS dan **dipisah tab**: **Semua / Wakaf Uang / Wakaf Tanah** (badge jumlah per tab), konsisten dengan gaya tab proyek.
- Kolom **"Saldo / Luas Terkini"** yang ambigu diperbaiki: header **"Saldo Terkini"** (Rp) di tab Wakaf Uang, **"Luas Terkini"** (M²) di tab Wakaf Tanah, **"Saldo / Luas Terkini"** di tab Semua — tiap sel sesuai kategori barisnya (`Rp …` / `… M²`), tanpa mencampur satuan. Read‑only; dibedakan via field `kategori` (`'uang'`/`'tanah'`).

**File tersentuh:** `pusat-hierarki.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Sembunyikan menu "Pengajuan Portofolio" (Admin Pusat)

**Bagian PRD terdampak:** §3.3 (Peta Menu Pusat), §5.9 (Pengajuan Portofolio).

**Apa yang berubah:**
- **Menu "Pengajuan Portofolio" disembunyikan** dari sidebar **seluruh 10 halaman Pusat**. Karena penambahan HBW oleh Nazhir kini **langsung aktif tanpa persetujuan Pusat**, halaman pengajuan tidak lagi relevan.
- Implementasi: blok `<li>` menu di‑*comment‑out* (bukan dihapus permanen) — mudah diaktifkan kembali bila diperlukan. Halaman `pusat-portofolio.html` & `pusat-portofolio-review.html` **tetap ada namun tak tertaut** dari menu.

**File tersentuh:** `pusat-dashboard.html`, `pusat-master.html`, `pusat-periode.html`, `pusat-broadcast.html`, `pusat-portofolio.html`, `pusat-portofolio-review.html`, `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `pusat-hierarki.html`, `pusat-cutoff.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Rapikan tabel Daftar Template Broadcast (kolom Judul & label Target)

**Bagian PRD terdampak:** §5.7 (Kelola Broadcast).

**Apa yang berubah (di `pusat-broadcast.html`):**
- **Kolom "Judul & Isi Pesan" → "Judul Template"**: sub‑keterangan cuplikan isi pesan **dihapus**, hanya judul yang tampil (isi lengkap tetap via tombol **Lihat**).
- **Label Target Periode multi‑pilih** tidak lagi ambigu "N periode" → kini `"<periode pertama> +N lainnya"` (mis. *"Januari – Maret 2026 +1 lainnya"*). Satu periode tampil labelnya utuh; daftar lengkap tetap muncul saat hover.

**File tersentuh:** `pusat-broadcast.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Modal Tambah Template Broadcast: lebih lebar + Target Periode dropdown searchable

**Bagian PRD terdampak:** §5.7 (Kelola Broadcast).

**Apa yang berubah (di `pusat-broadcast.html`):**
- **Modal dilebarkan** (`max-w-lg` → `max-w-2xl`) dan **layout 2‑kolom** (Judul & Target Periode sebaris, Isi Pesan penuh) agar tidak memanjang ke bawah.
- **Target Periode** dari checklist inline yang panjang → **dropdown checklist + searchbar**: bisa **dicari** sekaligus **multi‑pilih**. Trigger menampilkan ringkasan ("Semua Periode" / "N periode dipilih"), panel tertutup saat klik di luar. State pilihan disimpan di JS agar tahan re‑render saat mencari.

**File tersentuh:** `pusat-broadcast.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Penyempurnaan menu Broadcast (rename + auto‑kirim + target multi‑pilih)

**Bagian PRD terdampak:** §3.3 (Peta Menu Pusat), §4 (Model Data), §5.7 (Kelola Broadcast).

**Apa yang berubah (di `pusat-broadcast.html`):**
- **Menu di‑rename** "Kelola Pesan Alert" → **"Kelola Broadcast"** (judul halaman, header, dan label sidebar di seluruh 10 halaman Pusat).
- **Tabel di‑rename** "Daftar Template Pesan Alert" → **"Daftar Template Broadcast"**.
- **Kolom Aksi:** tombol **Kirim dihapus**. Aksi utama kini **tombol dinamis Aktifkan/Nonaktifkan** (hanya satu yang tampil, bergantian). **Pengiriman otomatis** saat template menjadi Aktif (dibuat aktif / di‑toggle aktif) → push ke `notifikasi_bwi`.
- **Target Periode:** dropdown single diganti **checklist multi‑pilih** ("Semua Periode" atau beberapa periode). Daftar periode **sinkron otomatis** dari `cutoff_bwi.items` (menu Periode Laporan), bukan data dummy.
- **Model data:** `broadcast_alert_bwi.target` kini `'semua'` **atau array label periode**.

**File tersentuh:** `pusat-broadcast.html`, 9 sidebar Pusat lainnya (rename menu), `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — HBW tanpa persetujuan Pusat + field khusus Wakaf Tanah + rapikan tabel Periode

**Bagian PRD terdampak:** §5.3 (Master HBW), §5.9 (Pengajuan Portofolio → legacy), §6.2 (siklus Portofolio), §7.3 (flow).

**Apa yang berubah:**
- **Penambahan HBW tidak perlu persetujuan BWI Pusat.** Di `nazhir-aset-tambah.html`, aksi Ajukan kini menyetel Sub‑ID **langsung `aktif`** (bukan `review`). Tombol utama menjadi **"Simpan & Aktifkan"**; panel Alur Status menjadi **Draft → Aktif** (tanpa "Verifikasi Pusat"/"Ditolak"). Halaman Pusat **Pengajuan Portofolio** jadi legacy (tak lagi menerima pengajuan).
- **Wakaf Tanah — dokumen & field baru** (di `nazhir-aset-tambah.html`, khusus jenis tanah):
  - Dokumen wajib **STBPN** (Surat Tanda Bukti Pendaftaran Nazhir dari BWI setempat sesuai luasan tanah) di section Dokumen Persyaratan.
  - Field **Nomor Register** (input teks) dan **BWI Penerbit** (combobox searchable + "tambah data baru") di detail legalitas; tersimpan di `detailTanah`.
- **Periode Laporan — rapikan tabel:** kolom **Periode** tidak lagi menampilkan sub‑keterangan jenis ("Semester · 6 bulan" / "Triwulan · 3 bulan"). Kartu ringkasan periode aktif tetap menampilkan jenis.

**File tersentuh:** `nazhir-aset-tambah.html`, `pusat-periode.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Menu "Kelola Pesan Alert" (broadcast) + penghapusan menu "Kelola Cut‑Off Periode"

**Bagian PRD terdampak:** §3.3 (Peta Menu Pusat), §4 (Model Data), §5.7 (kini Kelola Pesan Alert + catatan Cut‑Off tak tertaut).

**Apa yang berubah:**
- **Halaman baru `pusat-broadcast.html` — "Kelola Pesan Alert"** (role Admin Pusat): mengelola **template pesan alert/broadcast** yang mengingatkan Nazhir *"sudah saatnya menyiapkan laporan"*.
  - Modal Tambah/Edit: **Judul/Nama Template**, **Isi Pesan**, **Target Periode** (*Semua Periode* atau periode tertentu dari `cutoff_bwi.items`, dengan fallback bila kosong), **toggle Aktif/Nonaktif**.
  - **Status pengaktifan:** toggle Aktif/Nonaktif per template; template **Aktif** dianggap berlaku & siap dikirim. Aksi **Kirim Broadcast Sekarang** hanya untuk template Aktif.
  - **CRUD lengkap** (tabel: Judul + cuplikan Isi, Target Periode, Status Aktif, Terkirim, Aksi): lihat, edit, hapus, toggle aktif, kirim. + kartu ringkasan Total/Aktif/Terkirim.
  - **Integrasi notifikasi Nazhir:** aksi Kirim mem‑*push* entri ke `notifikasi_bwi` dengan skema kompatibel (`{ id, lembaga: '*', judul, pesan, periode, tanggal, tipe: 'broadcast' }`) → tampil di lonceng Nazhir. Cara Nazhir membaca notif tidak diubah.
  - **Penyimpanan baru:** kunci `broadcast_alert_bwi`.
- **Menu "Kelola Cut‑Off Periode" dihapus** dari sidebar seluruh halaman Pusat (dinilai tidak relevan / beririsan dengan Periode Laporan). Halaman `pusat-cutoff.html` **tetap ada** namun **tidak lagi ditautkan**.
- **Menu "Kelola Pesan Alert" ditambahkan** tepat setelah "Periode Laporan" di sidebar semua halaman Pusat (ikon megaphone), aktif hanya di `pusat-broadcast.html`.

**File tersentuh:** `pusat-broadcast.html` (baru), `pusat-dashboard.html`, `pusat-master.html`, `pusat-cutoff.html`, `pusat-periode.html`, `pusat-portofolio.html`, `pusat-portofolio-review.html`, `pusat-pendaftaran.html`, `pusat-pendaftaran-detail.html`, `pusat-hierarki.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.

---

## 2026‑07‑29 — Periode Laporan menjadi fleksibel (tanpa deadline)

**Bagian PRD terdampak:** §5.8 (Periode Laporan), §6.4 (Periode / Cut‑off).

**Apa yang berubah:**
- **Periode kini fleksibel** — tidak lagi terkunci ke "Semester 1/2". Admin Pusat memilih **rentang bulan bebas** via **2 dropdown bulan** (bulan mulai → bulan akhir): bisa **bulanan, triwulan (mis. Januari – Maret), semester (Januari – Juni), tahunan**, dst.
- **Datepicker Deadline dihapus** dari form Tambah/Edit Periode (tidak ada lagi batas akhir per periode di menu ini).
- **Label periode** kini berupa rentang bulan + tahun (mis. *"Januari – Juni 2027"*, *"April 2027"*).
- **Model data** `cutoff_bwi.items[label]` berubah menjadi `{ bulanMulai, bulanAkhir, tahun, terkunci }` (parser tetap kompatibel dengan format lama `Semester X - YYYY`).
- **Tabel Daftar Periode:** kolom **Batas Akhir** & **Rentang Bulan** dihapus (label sudah memuat rentang); kolom **Periode** menampilkan label + jenis (Bulanan/Triwulan/Semester/…).
- **Ringkasan periode aktif:** baris Batas Akhir dihilangkan.

**Dampak ke sisi Nazhir (diterapkan):**
- Form pelaporan tetap mengisi & mengunci kolom **Periode** otomatis dari periode aktif (mendukung label fleksibel).
- Halaman **Permohonan Buka Kunci**: tampilan **Batas Akhir (Deadline)** dihapus (di kartu status atas & kartu info); label rentang mengikuti format baru.

**File tersentuh:** `pusat-periode.html`, `nazhir-buka-kunci.html`, `PRD-FLOW-DESIGN-KENAZHIRAN.md`.
