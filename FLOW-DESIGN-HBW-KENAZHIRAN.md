# Flow Design & UI Spec — HBW (Master & Laporan) untuk Developer

**Modul Kenazhiran — BWI Superapps (Titik Terang OS)**
Panduan tampilan & interaksi agar developer dapat membangun halaman **Master HBW** dan **Laporan HBW** (Wakaf Tanah & Wakaf Uang). Berisi wireframe ASCII, rincian komponen, state, dan alur. Data field & model → lihat [PRD-MASTER-HBW-KENAZHIRAN.md](PRD-MASTER-HBW-KENAZHIRAN.md) & [PRD-LAPORAN-HBW-KENAZHIRAN.md](PRD-LAPORAN-HBW-KENAZHIRAN.md).

---

## 0. Sistem Desain (shared)

**Stack:** HTML statis + **Tailwind CDN** (`https://cdn.tailwindcss.com`) + `tailwind.config` inline. **Font:** Outfit (Google Fonts). **Ikon:** Heroicons inline SVG **(NO emoji)**. **DB:** localStorage. Sebagian halaman React via Babel in-browser (`nazhir-aset-tambah.html`).

**Palet `brand` (teal):** `50 #f0fdfa · 100 #ccfbf1 · 200 #99f6e4 · 300 #5eead4 · 400 #2dd4bf · 500 #14b8a6 · 600 #0d9488 · 700 #0f766e · 800 #115e59 · 900 #134e4a`.

**Shell aplikasi (semua halaman Nazhir):**
```
┌───────────────────────────────────────────────────────────────┐
│ SIDEBAR (w-[290px] bg-brand-900)   │  TOPBAR (h-72px, sticky, bg-white, border-b) │
│  ── logo + "BWI Superapps"         │  [☰ mobile] Judul + subjudul     [avatar]   │
│  ── nav menu (details/submenu)     ├──────────────────────────────────────────────┤
│     • Dashboard                    │  MAIN (max-w-screen-2xl, p-4 md:p-6, bg-gray-50)│
│     • Master HBW ▾                 │                                                │
│        - Wakaf Tanah               │   <konten halaman>                             │
│        - Wakaf Uang                │                                                │
│     • Laporan HBW ▾                │                                                │
│        - Pelaporan Wakaf Tanah     │                                                │
│        - Pelaporan Wakaf Uang      │                                                │
│        - Pelaporan Mutasi Aset     │                                                │
│     • Permohonan Buka Kunci        │                                                │
│  ── [Keluar] (bottom)              │                                                │
└───────────────────────────────────────────────────────────────┘
```
- Submenu aktif: `bg-brand-600 text-white`. Menu `<details>` dibuka otomatis sesuai konteks (`terapkanJenisLapor()`).
- Mobile: sidebar `-translate-x-full`, toggle via `toggleSidebar()`, overlay `#overlay`.
- **Kartu**: `rounded-2xl border border-gray-200 bg-white shadow-card`. **Modal**: overlay `bg-black/60` + panel `modal-in` (animasi). **Toast**: `#toast` kanan-bawah, `showToast()`.

**Konvensi warna badge:**
- Kategori: **Wakaf Uang = biru** (`bg-blue-50 text-blue-700`), **Wakaf Tanah = ungu** (`bg-purple-50 text-purple-700`) — sengaja bukan teal agar tak tertukar status "Aktif".
- Status portofolio: draft=abu · review=amber · revisi=oranye · ditolak=merah · aktif=teal · nonaktif=slate.
- Status laporan: draft=abu · **dilaporkan=teal/brand** (final, laporan tidak direview).
- Perubahan nilai: naik=emerald · turun=merah · tetap=abu.

**Guard visual (semua halaman):** akun dibekukan → banner merah + semua tombol CUD nonaktif; mode calon → konten diganti empty-state read-only.

---

# BAGIAN A — MASTER HBW

## A1. Daftar Master HBW (`nazhir-aset.html`)

Dibuka per jenis via `?jenis=tanah|uang` (submenu). Judul & data menyesuaikan jenis.

```
MAIN
┌─ Kartu NIB Induk ────────────────────────────────────────────┐
│  [ikon]  NZHR-BDG-170        Nomor Induk BWI (NIB)            │
│          "Satu Identitas untuk Seluruh Pengelolaan Aset…"     │
│                                   [ 2 Sub-ID · Portofolio Aktif]│
└──────────────────────────────────────────────────────────────┘

┌─ Baris aksi ────────────────────────────────────────────────┐
│  Daftar Portofolio HBW — Wakaf {Tanah/Uang}                  │
│  sub-judul per jenis          [Riwayat SK]  [+ Tambah Portofolio Baru]│
└──────────────────────────────────────────────────────────────┘

┌─ Toolbar ───────────────────────────────────────────────────┐
│  [🔎 Cari Sub-ID / nama / lokasi...]     [Filter Status ▾]   │
└──────────────────────────────────────────────────────────────┘

┌─ Tabel Portofolio (min-w-[820px], overflow-x-auto) ─────────┐
│ Sub-ID Aset │ Kategori Wakaf │ Nama/Lokasi │ Nilai/Luas │ Status │ Aksi        │
│ NZHR-BDG-001│ [ungu] W.Tanah │ Tanah Jl…   │ 500 M²     │ Aktif  │ [Lihat Detail]│
│ NZHR-BDG-002│ [biru] W.Uang  │ BSI KCP…    │ Rp 250.000…│ Aktif  │ [Lihat Detail]│
│ (draft)     │ …              │ …           │ …          │ Draft  │ [Detail][Batalkan]│
└──────────────────────────────────────────────────────────────┘
   Empty state: ikon + "Tidak ada portofolio yang cocok"
```

**Spesifikasi:**
- Kolom nilai (`nilaiTampil`): `Rp {ribuan}` (satuan Rp) atau `{ribuan} M²` (satuan M²).
- Aksi baris: **Lihat Detail** (selalu); **Batalkan** (hanya `draft`, merah) → modal konfirmasi.
- Filter jenis TIDAK berupa dropdown — berasal dari URL. Filter Status = dropdown (Semua/Draft/Aktif/Verifikasi Pusat/Revisi/Ditolak/Nonaktif).
- "Tambah Portofolio Baru" → set konteks `active_nazhir_portofolio_baru` → `nazhir-aset-tambah.html?jenis=<jenis>`.

## A2. Form Tambah/Ubah HBW (`nazhir-aset-tambah.html`) — React

Layout 2 kolom: **kolom form** (`lg:col-span-2`) + **kolom ringkasan sticky**. Seksi bernomor; **Seksi 3 (Detail Tanah) hanya untuk jenis tanah**.

```
TOPBAR: "Tambah Portofolio HBW · Wakaf {Uang/Tanah}"          [Batal]

┌─ KOLOM FORM (2/3) ───────────────────────────┐  ┌─ RINGKASAN (sticky) ─────┐
│ ① Kategori Wakaf                              │  │  Alur Status Pengajuan   │
│    [ Kategori ▾ (wajib) ]                     │  │   ● Draft ─── ● Aktif    │
│    [ Jenis Program ▾ ] ← hanya UANG           │  │  "Langsung aktif — tanpa │
│                                               │  │   verifikasi BWI Pusat"  │
│ ② Data Dasar {Rekening/Tanah}                 │  │                          │
│    [ Nama Rekening / Lokasi Aset ]  (wajib)   │  │  Ringkasan input:        │
│    [ Instrumen Investasi ▾ ] ← hanya UANG     │  │   Kategori: …            │
│    [ Nilai/Luas Dasar  (prefix Rp/M²) ]       │  │   Nama: …                │
│                                               │  │   Nilai: …               │
│ ③ Detail & Legalitas Tanah ← hanya TANAH      │  │   Dokumen: x / N          │
│    Legalitas ▾ · No AIW · No Sertifikat BPN   │  │   Syarat: ☐☐              │
│    No Register · BWI Penerbit (combobox)      │  │                          │
│    Nilai Aset(Rp) · Sumber ▾ · Tgl Penilaian  │  │  [Simpan Draft]          │
│    [ Peta koordinat (Leaflet) ] + input       │  │  [Simpan & Aktifkan]     │
│    Peruntukan ▾ · Penerima Manfaat · Resume   │  └──────────────────────────┘
│                                               │
│ ④/③ Dokumen Persyaratan                       │
│    Banner: "Kategori X mensyaratkan N dokumen"│
│    [Zona Upload drag&drop] × N (per dokumen)  │
│    (+ Dokumen Tambahan opsional — TANAH)      │
│                                               │
│ ⑤/④ Syarat & Ketentuan Legal                  │
│    ☐ Bersedia lapor bulanan                   │
│    ☐ Bersedia diaudit                         │
│                                               │
│  [Batal]     [Simpan Draft]  [Simpan & Aktifkan]│
└───────────────────────────────────────────────┘
```

**Aturan render & interaksi:**
- **Kondisional jenis:** UANG → tampil "Jenis Program" & "Instrumen Investasi", sembunyikan Seksi 3; TANAH → tampil Seksi 3 penuh + dokumen `stbpn`.
- **Field terkunci** hingga Kategori dipilih (Seksi 2 & Dokumen disabled).
- **Combobox** (Instrumen, BWI Penerbit): input searchable; daftar terfilter; bila tak ada hasil → tombol "Tambahkan «X» sebagai data baru".
- **Zona upload:** drag & drop / browse, `accept=".pdf,.jpg,.jpeg,.png"` maks 5 MB; setelah pilih tampil nama + ukuran + tombol hapus; pratinjau bila ≤ 1.5 MB.
- **Peta koordinat:** Leaflet + OSM, center Bandung; klik pin → isi "lat, lng" (6 desimal); ada fallback teks bila offline.
- **Tombol:** `Simpan Draft` aktif bila kategori+nama terisi; `Simpan & Aktifkan` aktif bila `bolehSimpan` (lihat PRD §3.2). Sukses → toast + redirect ke daftar (delay 1400 ms).
- **MODE_UBAH:** jika konteks berisi `edit`, Sub-ID dipertahankan & record di-update.

## A3. Detail Sub-ID (`nazhir-aset-detail.html`)

```
TOPBAR: "Detail Portofolio Aset"                              [Kembali]

┌─ Kartu Status ──────────────────────────────────────────────┐
│  NZHR-BDG-001 (mono)   Tanah Jl. Merdeka       [badge Status]│
│  keterangan status                                           │
└──────────────────────────────────────────────────────────────┘
┌─ Kartu Catatan Pusat (hanya revisi/ditolak) ────────────────┐  (amber/merah)
┌─ Timeline Tahap Proses ─────────────────────────────────────┐
│  ● Diajukan ─ ● Verifikasi Sekretariat ─ ● E-Sign ─ ● SK Terbit │
└──────────────────────────────────────────────────────────────┘
┌─ Rincian Aset ─────────────┐  ┌─ Dokumen Persyaratan ───────┐
│ NIB Induk · Kategori        │  │ [dok 1]  nama.pdf  [Preview]│
│ Nama/Lokasi · Keterangan    │  │ [dok 2]  nama.pdf  [Preview]│
│ Nilai/Luas · Dokumen x/N    │  └──────────────────────────────┘
│ Tanggal Pengajuan           │
│ ── (detail tanah bila ada) ─│
│ Legalitas · Nilai Aset      │
│ Sumber · Tgl Penilaian      │
│ No AIW · No Sertifikat BPN  │
│ BPN · Koordinat · Peruntukan│
│ Penerima Manfaat · Resume   │
└─────────────────────────────┘
   Bar aksi: [Batalkan (draft)]  [Kembali]  [Lanjutkan Pengisian / Perbaiki & Ajukan Ulang]
```
Preview dokumen: modal img/PDF/kartu info. Tombol lanjut tampil untuk `draft`/`ditolak`/`revisi`.

## A4. Riwayat SK (`nazhir-aset-sk.html`)

```
Metrik: [Total SK] [SK Aktif] [SK Nonaktif]
Toolbar: [Filter Jenis ▾]  [Filter Status ▾]
Tabel: Nomor SK │ Sub-ID │ Kategori │ Nama/Lokasi │ Tgl Terbit │ Status │ Aksi
Aksi: [Lihat SK]   (read-only — SK adalah arsip, tanpa aksi nonaktifkan)
```
Hanya menampilkan Sub-ID aktif/nonaktif. **Read-only (arsip): SK tidak dapat dinonaktifkan dari sini** (aksi toggle dihapus). Kartu metrik dibuat compact. Nomor SK: `SK-BWI/WKF/{tahun}/{suffix}`. Modal "Lihat SK" = dokumen SK ber-kop BWI + stempel.

---

# BAGIAN B — LAPORAN HBW

## B1. Halaman Utama (`nazhir-laporan-program.html?jenis=tanah|uang`)

```
TOPBAR: "Pelaporan Wakaf {Uang/Tanah}"  / sub per jenis

┌─ Banner Cut-off (#bannerCutoff) ── kondisional ─────────────┐
│  [merah]  "Periode pelaporan terkunci"   [Ajukan Buka Kunci]│  ← terkunci
│  [amber]  "Permohonan sedang ditinjau"                      │  ← menunggu
│  [teal ]  "Akses buka kunci disetujui"                      │  ← disetujui
└──────────────────────────────────────────────────────────────┘

┌─ Metrik ─────────────────────────────────────────────────────┐
│ [Draft: n]        [Dilaporkan: n]        [Total: n]         │
└──────────────────────────────────────────────────────────────┘

┌─ Baris aksi + toolbar ──────────────────────────────────────┐
│  Riwayat Laporan          [+ Tambah Laporan Program]        │
│  [🔎 Cari]  [Status ▾] [Arah ▾ (uang)] [Periode ▾]          │
└──────────────────────────────────────────────────────────────┘

┌─ Tabel Riwayat (kolom relabel per jenis) ───────────────────┐
│ Harta Benda Wakaf │ Periode │ {Nilai Akhir / Penyaluran} │ {Perubahan / Jumlah MAQ} │ Status │ Aksi │
│ Nama program      │ S1-2025 │ Rp 1.620.000.000           │ +Rp 120.000.000 (emerald)│ Draft  │ [Detail][Hapus]│
└──────────────────────────────────────────────────────────────┘
   "Menampilkan X dari Y laporan."
```

- **Kolom dinamis:** UANG → "Nilai Akhir" + "Perubahan" (badge ±Rp); TANAH → "Penyaluran" (Rp) + "Jumlah MAQ" ("N penerima").
- Aksi baris: **Lihat Detail** (semua) + **Hapus** (draft saja).
- Bila periode terkunci & belum disetujui → tombol Tambah **disabled** (title menjelaskan).

## B2. Modal Tambah/Edit Laporan (`#modalTambah`)

```
┌─ Modal: "Tambah Laporan Program" / "Edit Draft Laporan" ────┐
│  HBW yang Dilaporkan *   [ Pilih HBW ▾ (subId · nama) ]      │
│  info aset (nilai terkunci)                                 │
│  Periode Laporan *       [ Semester 1 - 2025  (terkunci) ]  │
│  Nilai Terakhir (Rp)     [ Rp 1.500.000.000  (readonly) ]   │
│                                                             │
│  ══ JENIS UANG (#blokUang) ══                               │
│   Penerimaan:                                               │
│    Nilai Terbaru (Rp)*   [ 362.000.000 ]                    │
│    Penghimpunan Baru*    [ 100.000.000 ]                    │
│    Imbal Hasil (auto)    [ Rp …  (readonly) ]               │
│   Penyaluran (dari imbal hasil):                            │
│    MAQ [ … ]  Operasional [ … ]  Pengembangan [ … ]         │
│    Jumlah MAQ [ … penerima ]                                │
│    #ketPenyaluran (% per kolom) · #warnPenyaluran (merah)   │
│   ┌ Badge Selisih ─ +Rp… (emerald) / −Rp… (merah) / Stabil ┐│
│                                                             │
│  ══ JENIS TANAH (#blokTanah) ══                             │
│    Penyaluran (Rp)*      [ 25.000.000 ]                     │
│    Jumlah Mauquf Alaih   [ 50 penerima ]                    │
│    (tanpa badge selisih — nilai HBW tak berubah)            │
│                                                             │
│  Catatan / Keterangan Progres   [ textarea ]               │
│                                                             │
│      [Kembali]      [Simpan Draft]   [Upload Laporan]       │
└──────────────────────────────────────────────────────────────┘
```

**Interaksi:**
- Pilih HBW → `pilihProgram()` mengisi **Nilai Terakhir** dari `baselineSSOT` (readonly).
- Semua input Rp memakai `formatRupiahInput` (ribuan) + `hitungUang()`/`hitungTanah()` on-input.
- **Imbal Hasil** = `(Nilai Terbaru − Nilai Terakhir) − Penghimpunan` (readonly, auto).
- Peringatan bila total penyaluran > imbal hasil.
- **Periode** auto-terisi & terkunci dari periode aktif.
- Validasi simpan: UANG (Nilai Terbaru>0, Penghimpunan>0, total penyaluran ≤ imbal); TANAH (Penyaluran>0). `Simpan Draft` → status "Draft"; **`Upload Laporan`** (dulu "Kirim Laporan") → **"Dilaporkan"** (final, tanpa review).

## B3. Modal Detail Laporan (`bukaModalDetail`)

```
┌─ Detail ────────────────────────────────────────────────────┐
│  {namaProgram}          {kodeProgram · periode}   [badge]   │
│  ┌ Grid ─────────────────────────────────────────────────┐ │
│  │ {Nilai Terakhir/Nilai HBW} │ {Nilai Terbaru/Nilai HBW} │ │  ← sel akhir disembunyikan utk tanah
│  │ Kenaikan (+)  │ Penyusutan (−) │ Status │ Tanggal        │ │  ← kenaikan/penyusutan disembunyikan utk tanah
│  └───────────────────────────────────────────────────────┘ │
│  ══ #dtUang ══  Penerimaan (Penghimpunan, Imbal Hasil)      │
│                 Penyaluran (MAQ %, Operasional %, Pengemb %)│
│                 Jumlah MAQ: N penerima                      │
│  ══ #dtTanah ══ Penyaluran (Nominal), Jumlah Mauquf Alaih   │
│  Catatan: …                                                 │
│              [Edit Draft (draft saja)]     [Tutup]          │
└──────────────────────────────────────────────────────────────┘
```

## B4. Permohonan Buka Kunci (`nazhir-buka-kunci.html`)

```
┌─ Status Periode Pelaporan ──────────────────────────────────┐
│  Periode: Semester 1 - 2025    [badge Terkunci/Terbuka]     │
│  Permohonan: [Disetujui/Menunggu/Ditolak/Belum Ada]         │
│  ── area aksi dinamis ── [Ajukan Buka Kunci] (bila perlu)    │
└──────────────────────────────────────────────────────────────┘
┌─ Info Periode ──────────┐ ┌─ Rentang ──────────────────────┐
│ periode aktif           │ │ "Januari – Juni 2025"          │
└─────────────────────────┘ └────────────────────────────────┘
┌─ Riwayat Permohonan Saya ───────────────────────────────────┐
│  Periode │ Alasan │ Diajukan │ Status                       │
└──────────────────────────────────────────────────────────────┘

Modal Ajukan Buka Kunci: [Periode (readonly)] [Alasan Keterlambatan * (textarea)] [Kirim Permohonan]
```

---

## C. Alur Interaksi (state flow)

### C1. Tambah HBW → Aktif (Master)
```
Daftar (?jenis) ──[Tambah]──▶ Form (set active_nazhir_portofolio_baru)
   Form: pilih kategori → isi data → (tanah: detail+peta) → upload dokumen → centang 2 syarat
   ├─[Simpan Draft]──▶ status 'draft' ──▶ Daftar (bisa dilanjutkan/di-batalkan)
   └─[Simpan & Aktifkan]──▶ status 'aktif' ──▶ Daftar  ──▶ muncul di Riwayat SK
                                              (TANPA persetujuan Pusat)
```

### C2. Buat Laporan HBW
```
Halaman Laporan (?jenis) ──[+ Tambah]──▶ Modal
   Guard: bolehLapor()? (tidak dibekukan & (tidak terkunci | buka kunci disetujui))
   ├─ tidak boleh ▶ toast "Periode terkunci…" (tombol disabled)
   └─ boleh ▶ pilih HBW → Nilai Terakhir auto (baselineSSOT)
        UANG: Nilai Terbaru + Penghimpunan → Imbal auto → penyaluran (≤ imbal)
        TANAH: Penyaluran + Jumlah Mauquf
        ├─[Simpan Draft]──▶ 'Draft' (bisa Edit/Hapus)
        └─[Upload Laporan]──▶ 'Dilaporkan' (final, tanpa review)
   Baseline berikutnya = nilaiAkhir laporan non-draft terakhir (anti-duplikasi)
```

### C3. Periode Terkunci → Buka Kunci
```
Cut-off terkunci ──▶ Banner merah + tombol Ajukan Buka Kunci
   ──[isi Alasan]──▶ buka_kunci_bwi (status 'menunggu') ──▶ Banner amber
   Admin Pusat menyetujui ──▶ statusBukaSaya='disetujui' ──▶ Banner teal ──▶ boleh lapor
```

---

## D. Checklist Implementasi (Dev)

**Master HBW**
- [ ] Daftar per `?jenis=` (judul, filter status, badge kategori biru/ungu, kolom nilai Rp/M²).
- [ ] Form: seksi kondisional (Program & Instrumen utk uang; Seksi 3 detail + STBPN utk tanah).
- [ ] Combobox searchable + "tambah data baru" (Instrumen, BWI Penerbit).
- [ ] Zona upload multi-dokumen + pratinjau; peta Leaflet koordinat (fallback offline).
- [ ] Aturan `bolehSimpan`; Draft vs Simpan & Aktifkan; MODE_UBAH.
- [ ] Detail: timeline, rincian (termasuk detail tanah), preview dokumen; aksi lanjut per status.
- [ ] Riwayat SK: filter, nomor SK deterministik, modal SK — **read-only (tanpa toggle nonaktif)**.

**Laporan HBW**
- [ ] Satu halaman dua jenis (`?jenis=`); toggle `blokUang`/`blokTanah`/`badgeSelisih`.
- [ ] Kolom tabel relabel per jenis; filter status/arah/periode; metrik.
- [ ] `hitungUang()` (imbal auto, % penyaluran, peringatan) & `hitungTanah()` (nilaiAktual=awal).
- [ ] Nilai Terakhir dari `baselineSSOT`; periode auto-terkunci dari `cutoff_bwi.periodeAktif`.
- [ ] Edit Draft (hanya draft) via tabel & modal detail; Hapus draft.
- [ ] Modal detail (`dtUang`/`dtTanah`); Banner cut-off + Modal & Halaman Buka Kunci.

**Umum**
- [ ] Guard akun dibekukan (blokir CUD + banner) & mode calon (read-only).
- [ ] Toast, modal `modal-in`, responsif (tabel `overflow-x-auto`), ikon Heroicons (no emoji).
