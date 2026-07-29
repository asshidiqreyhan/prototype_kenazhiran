# Changelog PRD — Modul Kenazhiran

Ringkasan perubahan yang diterapkan pada `PRD-FLOW-DESIGN-KENAZHIRAN.md` beserta dampaknya ke prototipe. Entri terbaru di atas.

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
