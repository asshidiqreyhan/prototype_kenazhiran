# PRD — Master HBW (Harta Benda Wakaf): Wakaf Tanah & Wakaf Uang

**Modul Kenazhiran — BWI Superapps (Titik Terang OS)**
Dokumen ini memerinci fitur **Master HBW** sampai level field, id, opsi, validasi, dan model data. Sumber: implementasi aktual pada `nazhir-aset.html`, `nazhir-aset-tambah.html`, `nazhir-aset-detail.html`, `nazhir-aset-sk.html`.

> Dokumen turunan/terkait: [PRD-LAPORAN-HBW-KENAZHIRAN.md](PRD-LAPORAN-HBW-KENAZHIRAN.md), [FLOW-DESIGN-HBW-KENAZHIRAN.md](FLOW-DESIGN-HBW-KENAZHIRAN.md), dan PRD induk [PRD-FLOW-DESIGN-KENAZHIRAN.md](PRD-FLOW-DESIGN-KENAZHIRAN.md).

---

## 1. Ringkasan & Tujuan

**Master HBW** adalah "buku induk aset wakaf" milik Nazhir di bawah **satu NIB Induk** (`NZHR-BDG-170` — Yayasan Wakaf Nusantara). Satu NIB menaungi banyak **Sub-ID** portofolio. Master HBW menampung **dua kategori**:

- **Wakaf Tanah** (`?jenis=tanah`) — aset tidak bergerak; satuan **M²**; wajib legalitas (BPN/AIW/STBPN).
- **Wakaf Uang** (`?jenis=uang`) — dana/instrumen; satuan **Rp**; wajib rekomendasi LKS-PWU.

**Prinsip kunci:** penambahan HBW **langsung Aktif tanpa persetujuan BWI Pusat** (Draft → Aktif). Nazhir menambah, mengedit draft, mengaktifkan, menonaktifkan, dan membatalkan Sub-ID sendiri.

### 1.1 Peta Halaman
| Halaman | File | Peran |
|---|---|---|
| Daftar Master HBW | `nazhir-aset.html` | List Sub-ID (per jenis via `?jenis=`), filter, cari, aksi baris |
| Tambah/Ubah HBW | `nazhir-aset-tambah.html` | Form pendaftaran Sub-ID (React/JSX in-browser) |
| Detail Sub-ID | `nazhir-aset-detail.html` | Rincian aset, timeline, dokumen, aksi lanjut |
| Riwayat SK | `nazhir-aset-sk.html` | SK per Sub-ID aktif/nonaktif, cetak, toggle status |

### 1.2 Navigasi
- Sidebar submenu **Master HBW** → **Wakaf Tanah** (`nazhir-aset.html?jenis=tanah`) / **Wakaf Uang** (`nazhir-aset.html?jenis=uang`).
- Daftar → **"Tambah Portofolio Baru"** menulis konteks `active_nazhir_portofolio_baru = {nib, prefix, subIdBaru}` lalu redirect `nazhir-aset-tambah.html?jenis=<jenis>`.
- Daftar → baris **"Lihat Detail"** menulis `active_nazhir_aset_view = <record>` lalu redirect `nazhir-aset-detail.html`.
- Detail → **"Lanjutkan Pengisian" / "Perbaiki & Ajukan Ulang"** menulis `active_nazhir_portofolio_baru = {nib, prefix, subIdBaru: subId, edit: <record>}` → `nazhir-aset-tambah.html` (**MODE_UBAH**: Sub-ID dipertahankan, record di-update).
- Daftar → **"Riwayat SK"** → `nazhir-aset-sk.html`.
- Setelah simpan (draft/aktif) form redirect balik ke `nazhir-aset.html?jenis=<jenis>` (delay 1400 ms).

---

## 2. Model Data (localStorage)

| Kunci | Isi |
|---|---|
| `data_portofolio_bwi` (`KUNCI_PORTOFOLIO`) | Array record portofolio HBW (tanah & uang) |
| `active_nazhir_portofolio_baru` | Konteks form: `{ nib, prefix, subIdBaru, edit? }` |
| `active_nazhir_aset_view` | Record satu portofolio yang sedang dibuka di Detail |
| `dokumen_preview_<subId>` | `{ key: dataURL }` pratinjau berkas (base64; hanya file ≤ 1.5 MB) |
| `status_nazhir_bwi` | `{ 'NZHR-BDG-170': 'dibekukan' }` — freeze guard (blokir semua CUD) |
| `sesi_kenazhiran` | `{ mode:'calon'|'aktif', nama }` — mode calon = read-only |
| `pendaftaran_nazhir_bwi` | Data calon nazhir (guard) |
| `graduasi_baru_bwi` | Flag banner graduasi |

Konstanta: `NIB_LEMBAGA = 'NZHR-BDG-170'`; `PREFIX_NIB = 'NZHR-BDG'`.

### 2.1 Bentuk Record Portofolio (dari `tulisRecord`)
```js
{
  subId,          // 'NZHR-BDG-003'
  nib,            // 'NZHR-BDG-170'
  lembaga,        // 'Yayasan Wakaf Nusantara'
  wilayah,        // 'BWI Prov. Jawa Barat'
  nama,           // uang: nama rekening; tanah: lokasi aset
  lokasi,         // uang: 'Rek. Wakaf Produktif'; tanah: 'Lokasi aset wakaf'
  jenis,          // 'uang' | 'tanah'
  programUang,    // hanya jenis 'uang' (else '')
  instrumen,      // hanya jenis 'uang' (else '')
  kategori,       // nama kategori, mis. 'Wakaf Tanah'
  kategoriId,     // id kategori, mis. 'tanah'
  satuan,         // 'M²' | 'Rp'
  nilai,          // number (digit-only)
  dokumen,        // jumlah dokumen terunggah
  totalDokumen,   // jumlah dokumen wajib
  dokumenList,    // [{ key, label, nama, ukuran, tipe }]
  detailTanah,    // object detail (jenis tanah) atau null
  status,         // 'draft' | 'aktif'
  tanggal,        // 'DD Month YYYY' (id-ID)
}
```

### 2.2 `detailTanah` (khusus Wakaf Tanah)
| Field | Isi |
|---|---|
| `legalitas` | Status Legalitas Tanah (OPSI_LEGALITAS) |
| `nomorAIW` | Nomor AIW |
| `nomorSertifikat` | Nomor Sertifikat BPN |
| `bpnPenerbit` | Kantor Pertanahan (BPN) Penerbit |
| `nomorRegister` | Nomor Register (STBPN/pendaftaran nazhir) |
| `bwiPenerbit` | BWI Penerbit (combobox) |
| `nilaiAset` | Nilai Aset (Rp) — string terformat ribuan |
| `sumberPenilaian` | Sumber Penilaian (OPSI_PENILAIAN) |
| `tglPenilaian` | Tanggal Penilaian (date) |
| `koordinat` | "lat, lng" |
| `peruntukan` | Peruntukan HBW (OPSI_PERUNTUKAN) |
| `penerimaManfaat` | Penerima Manfaat (Mauquf Alaih) |
| `resume` | Resume kondisi tanah (textarea) |

### 2.3 Seed (`DEFAULT_PORTOFOLIO`)
- **NZHR-BDG-001** — Wakaf Tanah: nama `Tanah Jl. Merdeka`, lokasi `Semarang, Jawa Tengah`, satuan `M²`, nilai `500`, status `aktif`, 2/2 dokumen (`bpn`, `aiw`), tgl `12 Januari 2025`.
- **NZHR-BDG-002** — Wakaf Uang: nama `BSI KCP Sudirman`, lokasi `Rek. Wakaf Produktif`, satuan `Rp`, nilai `250000000` (di `nazhir-aset.html`; **catat**: di `nazhir-aset-sk.html` seed-nya `1000000000`), status `aktif`, 2/2 dokumen (`lkspwu`, `operasional`), tgl `20 Februari 2025`.
- `bacaPortofolio()` = gabung DEFAULT + localStorage, **dedupe by `subId`** (localStorage menimpa bawaan).

---

## 3. Form Tambah/Ubah HBW (`nazhir-aset-tambah.html`)

Form React (JSX via Babel), layout 2 kolom (form + ringkasan sticky). `JENIS_HBW` dari `?jenis=` (`'uang'` bila lowercase==='uang', selain itu `'tanah'`). Penomoran seksi berbeda: pada **Wakaf Uang**, Seksi 3 (detail tanah) disembunyikan → Dokumen jadi seksi 3, Legal jadi seksi 4. Pada **Wakaf Tanah**: Dokumen seksi 4, Legal seksi 5.

### SEKSI 1 — Kategori Wakaf
> "Pilih terlebih dahulu — menentukan label isian, satuan nilai, dan dokumen persyaratan."

**Kategori {Wakaf Uang/Tanah}** — `select`, state `kategori`, **wajib**. Placeholder "Pilih kategori {jenis}…". Opsi = `KATEGORI` difilter per `JENIS_HBW`; teks `"{nama} — satuan {satuan}"`. Ganti kategori mereset nama/nilai/detail/berkas. Validasi kosong: *"Silakan pilih kategori wakaf terlebih dahulu."*

**KATEGORI (8):**
| id | Nama | Jenis | Satuan | Keterangan |
|---|---|---|---|---|
| `tanah` | Wakaf Tanah | tanah | M² | Tanah wakaf permanen/selamanya, bersertifikat BPN & ber-AIW. |
| `tanah-temporer-pendek` | Wakaf Temporer Jangka Pendek | tanah | M² | Tanah wakaf berjangka waktu pendek. |
| `tanah-temporer-panjang` | Wakaf Temporer Jangka Panjang | tanah | M² | Tanah wakaf berjangka waktu panjang. |
| `uang` | Wakaf Uang | uang | Rp | Dana wakaf uang tunai yang dikelola nazhir. |
| `uang-melalui` | Wakaf Melalui Uang | uang | Rp | Benda wakaf yang diperoleh/dibeli melalui uang. |
| `uang-bergerak` | Wakaf Bergerak Selain Uang | uang | Rp | Benda bergerak selain uang (logam mulia, kendaraan, surat berharga). |
| `uang-temporer-pendek` | Wakaf Uang Temporer Jangka Pendek | uang | Rp | Wakaf uang berjangka waktu pendek. |
| `uang-temporer-panjang` | Wakaf Uang Temporer Jangka Panjang | uang | Rp | Wakaf uang berjangka waktu panjang. |

**Jenis Program** — `select`, state `programUang`, **wajib**, **HANYA Wakaf Uang**. Placeholder "Pilih jenis program…". `OPSI_PROGRAM_UANG` (5): **Wakaf Produktif · Sosial & Ibadah · Pendidikan · Kesehatan · Infrastruktur**. Validasi: *"Silakan pilih jenis program terlebih dahulu."*

### SEKSI 2 — Data Dasar Aset (label dinamis `TEKS_FIELD`)
Terkunci sampai kategori dipilih.

| | Wakaf Uang | Wakaf Tanah |
|---|---|---|
| Judul | Data Dasar Rekening Wakaf | Data Dasar Tanah Wakaf |
| Field nama (`#namaAset`, wajib) | **Nama Rekening Wakaf** — ph "mis. BSI KCP Sudirman" | **Lokasi Aset** — ph "mis. Jl. Merdeka No. 45, Semarang" |
| Field nilai (`#nilaiAset`, wajib) | **Nilai Dasar** (prefix Rp) | **Luas Dasar** (prefix M²) |
| Pesan wajib | "Nama rekening wakaf wajib diisi." / "Nilai dasar wajib diisi." | "Lokasi aset wajib diisi." / "Luas dasar wajib diisi." |

**Instrumen Investasi** — combobox **searchable + tambah data baru**, state `instrumen`, **wajib**, **HANYA Wakaf Uang**. Placeholder "Cari / pilih instrumen…". `OPSI_INSTRUMEN` (8): **Deposito Syariah · Sukuk (SBSN) · Saham Syariah · Reksa Dana Syariah · Pembiayaan Mikro Syariah · Emas / Logam Mulia · Properti / Sewa · Lainnya**. Validasi: *"Instrumen investasi wajib dipilih."*

Field nilai memakai `formatAngka` (ribuan id-ID), inputMode numeric, prefix satuan `kategoriAktif.satuan`.

### SEKSI 3 — Detail & Legalitas Tanah Wakaf — **HANYA Wakaf Tanah** (semua opsional)
> "Dicatat sebagai field agar data dapat ditarik langsung tanpa membuka berkas."

| Field | Tipe | Opsi / Catatan |
|---|---|---|
| **Status Legalitas Tanah** | select | `OPSI_LEGALITAS` (5): Sudah Sertifikat Wakaf (BPN) · Sertifikat Hak Milik (belum balik nama wakaf) · AIW / APAIW · Girik / Letter C / Petok D · Belum Bersertifikat |
| **Nomor AIW** | text | ph "mis. W.2/12/AIW/2024" |
| **Nomor Sertifikat BPN (bila ada)** | text | ph "mis. 12.34.56.78.9.00123" |
| **Kantor Pertanahan (BPN) Penerbit (bila ada)** | text | ph "mis. Kantah Kab. Bandung" |
| **Nomor Register (bila ada)** | text (`name="nomorRegister"`) | ph "mis. REG-JB-2026-00123"; hint "Nomor register STBPN / pendaftaran nazhir dari BWI penerbit." |
| **BWI Penerbit (bila ada)** | combobox searchable + tambah baru | `OPSI_BWI` (lihat §3.1); hint "BWI setempat yang menerbitkan STBPN sesuai luasan tanah." |
| **Nilai Aset (Rp)** | text numeric (`formatAngka`) | ph "mis. 2.500.000.000" |
| **Sumber Penilaian** | select | `OPSI_PENILAIAN` (5): NJOP (SPPT PBB) · DJKN · KJPP (Penilai Publik) · Penilaian Internal Nazhir · Lainnya |
| **Tanggal Penilaian** | date | |
| **Koordinat Lokasi** | peta Leaflet + input text | default center Bandung `[-6.9147, 107.6098]`; klik pin isi "lat, lng" (6 desimal); manual ph "mis. -6.9147, 107.6098" |
| **Peruntukan Harta Benda Wakaf** | select | `OPSI_PERUNTUKAN` (7): Tempat Ibadah (Masjid/Musholla) · Pendidikan (Sekolah/Pesantren) · Sosial & Kesehatan · Pemakaman · Pertanian / Perkebunan · Ekonomi Produktif · Lainnya |
| **Penerima Manfaat (Mauquf Alaih)** | text | ph "mis. Masyarakat sekitar / Yayasan Pendidikan Al-Falah" |
| **Resume Kondisi Tanah Wakaf Saat Ini** | textarea (rows 3) | ph "Ceritakan singkat kondisi, pemanfaatan, dan status tanah wakaf saat ini." |

Catatan sub-panel penilaian: "Nilai nominal (Rp) terpisah dari Luas Dasar (M²). Siap diisi dari penilaian DJKN/KJPP saat datanya tersedia."

#### 3.1 Combobox "BWI Penerbit"
- Input `autoComplete="off"`; menampilkan `detail.bwiPenerbit` saat tertutup, `bwiCari` saat terbuka (onFocus mengosongkan pencarian & membuka panel).
- `bwiTersaring` = `bwiOpsi.filter(includes bwiCari lowercase)`; klik opsi → set nilai. Bila tak ada hasil & ada teks → tombol **"Tambahkan «X» sebagai data baru"** (`tambahBwi`, dedup case-insensitive, langsung dipilih).
- `OPSI_BWI` (12): **BWI Pusat · BWI Prov. DKI Jakarta · BWI Prov. Jawa Barat · BWI Prov. Jawa Tengah · BWI Prov. Jawa Timur · BWI Prov. Banten · BWI Prov. DI Yogyakarta · BWI Kota Bandung · BWI Kab. Bandung · BWI Kota Semarang · BWI Kota Surabaya · Lainnya**.

### SEKSI 4 (tanah) / 3 (uang) — Dokumen Persyaratan
> "Daftar berkas menyesuaikan kategori wakaf yang Anda pilih." Terkunci sampai kategori dipilih. Banner: *"Kategori {nama} mensyaratkan {N} dokumen. Terunggah {x} dari {N}."*

**Zona unggah:** drag & drop "Drag & Drop berkas atau Browse", "Format .pdf / .jpg / .png · maks. 5 MB", `accept=".pdf,.jpg,.jpeg,.png"`. Setelah pilih: nama + ukuran (KB) + "Siap diunggah" + tombol hapus. Pratinjau dataURL bila ≤ 1.5 MB.

**DOKUMEN_WAJIB — Wakaf Uang (2):**
| key | Label | Hint |
|---|---|---|
| `lkspwu` | Dokumen Rekomendasi LKS PWU | Surat rekomendasi dari Lembaga Keuangan Syariah Penerima Wakaf Uang |
| `operasional` | Surat Pernyataan Biaya Operasional | Pernyataan kesanggupan menanggung biaya operasional pengelolaan |

**DOKUMEN_WAJIB — Wakaf Tanah (3):**
| key | Label | Hint |
|---|---|---|
| `bpn` | Sertifikat / Bukti Legalitas Tanah | Sertifikat BPN, AIW, girik, atau bukti legalitas lain sesuai status legalitas |
| `aiw` | AIW (Akta Ikrar Wakaf) | Akta Ikrar Wakaf yang dibuat di hadapan PPAIW |
| `stbpn` | STBPN (Surat Tanda Bukti Pendaftaran Nazhir) | Surat Tanda Bukti Pendaftaran Nazhir dari BWI setempat sesuai luasan tanah |

**DOKUMEN_OPSIONAL — Wakaf Tanah (2, tidak menghalangi simpan):** `sertifikatBpn` — "Sertifikat Sudah Balik BPN (opsional)"; `fotoLokasi` — "Foto Lokasi Tanah (opsional)". (Wakaf Uang: tidak ada dokumen opsional.)

Validasi: *"Seluruh dokumen persyaratan wajib diunggah."*

### SEKSI 5 (tanah) / 4 (uang) — Syarat & Ketentuan Legal
> "Pernyataan digital ini menggantikan surat pernyataan fisik. Kedua pernyataan berikut mengikat secara hukum dan wajib disetujui sebelum portofolio dapat disimpan."

**SYARAT_LEGAL (2 checkbox, keduanya wajib):**
1. `laporBulanan` — **Bersedia lapor bulanan** — "Saya bersedia menyampaikan laporan mutasi wakaf secara rutin/bulanan sesuai periode yang ditetapkan BWI."
2. `bersediaAudit` — **Bersedia diaudit** — "Saya bersedia diaudit terkait laporan pengelolaan portofolio wakaf ini oleh BWI Wilayah maupun BWI Pusat."

Validasi: *"Kedua pernyataan legal wajib disetujui sebelum menyimpan."*

### 3.2 Bar Aksi & Aturan Validasi
- **Batal** → kembali ke daftar.
- **Simpan Sebagai Draft** (`simpanDraft`) — aktif bila `bisaDraft = kategori!=='' && nama.trim()!==''`. → `tulisRecord('draft')`. Toast: *"Draft {SubId} tersimpan/diperbarui…"*.
- **Simpan & Aktifkan** (`ajukan`) — aktif bila `bolehSimpan`. → `tulisRecord('aktif')`. Toast: *"Sub-ID {SubId} berhasil ditambahkan & langsung aktif."*

Aturan (verbatim):
```
programOK    = JENIS_HBW !== 'uang' || programUang !== ''
instrumenOK  = JENIS_HBW !== 'uang' || instrumen !== ''
dasarLengkap = nama.trim() && nilai && kategori && programOK && instrumenOK
dokumenLengkap = dokumenWajib.length > 0 && jumlahTerunggah === dokumenWajib.length
semuaSyaratDisetujui = laporBulanan && bersediaAudit
bolehSimpan = dasarLengkap && dokumenLengkap && semuaSyaratDisetujui
```
Catatan kaki: *"Draft tersimpan tanpa masuk antrean dan masih bisa diedit. Simpan & Aktifkan menjadikan Sub-ID baru langsung Aktif dan resmi terdaftar tanpa perlu persetujuan BWI Pusat."*

---

## 4. Siklus Status Sub-ID

**Model final (form):** `draft → aktif` — **tanpa persetujuan BWI Pusat**. Panel "Alur Status Pengajuan" hanya 2 langkah (`draft`, `aktif`) + box: *"Langsung aktif — begitu disimpan & diaktifkan, Sub-ID baru resmi terdaftar tanpa menunggu verifikasi BWI Pusat."*

**Model tampilan lanjutan (`STATUS_PORTOFOLIO`, dipakai badge/filter/timeline di Daftar, Detail, SK):**
| key | Label | Warna badge |
|---|---|---|
| `draft` | Draft | abu |
| `review` | Verifikasi Pusat | amber |
| `revisi` | Revisi | oranye |
| `ditolak` | Ditolak | merah |
| `aktif` | Aktif | brand/teal |
| `nonaktif` | Nonaktif | slate |

Transisi via UI: `draft → aktif` (Simpan & Aktifkan), `draft →` hapus (Batalkan), `aktif ↔ nonaktif` (toggle di Riwayat SK). Status `review/revisi/ditolak` hanya muncul bila record disuntik langsung ke localStorage — **tidak** dihasilkan alur normal.

> **Catatan konsistensi (untuk perbaikan):** (1) Model 2-status form vs 6-status tampilan hidup berdampingan; timeline Detail masih memuat "Verifikasi Sekretariat / E-Sign Pimpinan / SK Terbit". (2) Footer `nazhir-aset.html` masih memuat teks usang ("Wakaf uang tidak didaftarkan di sini", "berstatus Verifikasi Pusat"). (3) Seed nilai NZHR-BDG-002 berbeda antar file (250 jt vs 1 M). (4) `nomorRegister` & `bwiPenerbit` disimpan tapi belum ditampilkan di Detail.

---

## 5. Daftar Master HBW (`nazhir-aset.html`)

- **Kartu NIB:** `NZHR-BDG-170`, badge "{jumlahAktif} Sub-ID · Portofolio Aktif" (hanya status `aktif`).
- **Pencarian** `#cariAset` (subId+nama+lokasi+kategori). **Filter jenis** dari URL `?jenis=` (bukan dropdown). **Filter Status** `#filterStatus`: Semua/Draft/Aktif/Verifikasi Pusat/Revisi/Ditolak/Nonaktif.
- **Kolom tabel:** `Sub-ID Aset` · `Kategori Wakaf` · `Nama / Lokasi Aset` · `Nilai / Luas Dasar` · `Status` · `Aksi`.
  - Kategori: badge **Wakaf Uang = biru**, **Wakaf Tanah = ungu** (bukan hijau agar tak tertukar badge "Aktif").
  - Kolom nilai (`nilaiTampil`): `Rp {ribuan}` bila satuan Rp; `{ribuan} M²` bila M².
  - Aksi: **Lihat Detail** (selalu) + **Batalkan** (hanya `draft`, merah).
- **Sub-ID berikutnya:** `PREFIX_NIB + '-' + (count+1) padStart(3,'0')`.
- Empty state: *"Tidak ada portofolio yang cocok"*.

---

## 6. Detail Sub-ID (`nazhir-aset-detail.html`)

Membaca `active_nazhir_aset_view`; `uang = (kategoriId === 'uang')`.
- **Kartu status** (Sub-ID mono, nama, badge + keterangan status).
- **Kartu catatan Pusat** — hanya bila `catatanPusat` ada & status `revisi`/`ditolak` ("Alasan Penolakan…" / "Catatan Revisi…").
- **Timeline Tahap Proses:** `Diajukan · Verifikasi Sekretariat · E-Sign Pimpinan · SK Terbit` (perilaku per status; `aktif`/`nonaktif` = 4 tahap selesai).
- **Rincian Aset:** NIB Induk · Kategori · **Nama Rekening/Lokasi Aset** · Keterangan · **Nilai/Luas Dasar** · Dokumen Terunggah (x dari N) · Tanggal Pengajuan. **Baris detail tanah** (bila terisi): Status Legalitas · Nilai Aset (DJKN/KJPP) · Sumber Penilaian · Tanggal Penilaian · Nomor AIW · Nomor Sertifikat BPN · Kantor Pertanahan (BPN) · Koordinat · Peruntukan · Penerima Manfaat · Resume Kondisi.
- **Dokumen Persyaratan:** daftar `dokumenList` + **Preview** (img/PDF/kartu info).
- **Bar aksi:** Batalkan Pengajuan (hanya `draft`) · Kembali · **Lanjutkan Pengisian** (`draft`) / **Perbaiki & Ajukan Ulang** (`ditolak`/`revisi`).

---

## 7. Riwayat SK (`nazhir-aset-sk.html`)

- Menampilkan hanya Sub-ID **aktif/nonaktif**. Filter Jenis (Semua/Tanah/Uang) & Status (Semua/Aktif/Nonaktif). Metrik: Total SK · SK Aktif · SK Nonaktif.
- **Kolom:** `Nomor SK` · `Sub-ID` · `Kategori` · `Nama / Lokasi Aset` · `Tgl Terbit` · `Status` · `Aksi`.
- **Nomor SK** (`nomorSK`): `SK-BWI/WKF/{tahun}/{suffix}` (suffix = segmen akhir subId; tahun dari `tanggal` atau 2026). Contoh `SK-BWI/WKF/2025/001`.
- **Aksi:** Lihat SK (modal dokumen) · Toggle Aktif/Nonaktif (konfirmasi → update `status`) · Cetak (dummy toast).
- **Isi SK** (modal): kop BWI, "Surat Keputusan Nomor {nomorSK}", "Pengesahan Portofolio Aset Wakaf (Sub-ID) di bawah NIB NZHR-BDG-170", tabel aset, stempel "Aktif & Sah"/"Nonaktif", TTD "Administrator BWI Pusat — Mas Raji".

---

## 8. Helper & Perhitungan
- `formatAngka(n)` — digit-only → `toLocaleString('id-ID')`.
- `nilaiTampil(p)` — Rp vs M² sesuai `satuan`.
- `rpFmt(v)` — "Rp {ribuan}" untuk nilai detail tanah.
- `jenisAset(p)` — `p.jenis` atau derive dari `kategoriId`.
- `subIdBerikutnya()` — nomor Sub-ID berikutnya.
- `nomorSK(p)` — nomor SK deterministik.
- `bacaPortofolio()` — merge DEFAULT + localStorage, dedupe by subId.
- `tulisRecord(status)` — bangun record + simpan ke `data_portofolio_bwi` + `dokumen_preview_<subId>`.
- `PetaKoordinat` — Leaflet + OpenStreetMap (fallback pesan bila offline).

## 9. Aturan Akses (Guard)
- **Freeze:** bila `status_nazhir_bwi['NZHR-BDG-170']==='dibekukan'` → semua aksi CUD diblokir + banner "Akun Anda dibekukan".
- **Mode Calon:** bila `sesi_kenazhiran.mode==='calon'` → konten diganti banner read-only (kecuali dashboard/pendaftaran).
