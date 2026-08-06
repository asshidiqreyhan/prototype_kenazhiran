# PRD — Laporan HBW (Pelaporan Harta Benda Wakaf): Wakaf Tanah & Wakaf Uang

**Modul Kenazhiran — BWI Superapps (Titik Terang OS)**
Perincian fitur **Laporan HBW** sampai level field, id, formula, dan model data. Sumber: `nazhir-laporan-program.html` (utama), `nazhir-buka-kunci.html`, serta jalur "Mutasi Aset" (`nazhir-laporan.html`, `-baru`, `-isi`, `-detail`).

> Terkait: [PRD-MASTER-HBW-KENAZHIRAN.md](PRD-MASTER-HBW-KENAZHIRAN.md), [FLOW-DESIGN-HBW-KENAZHIRAN.md](FLOW-DESIGN-HBW-KENAZHIRAN.md), [PRD-FLOW-DESIGN-KENAZHIRAN.md](PRD-FLOW-DESIGN-KENAZHIRAN.md).

---

## 1. Ringkasan & Tujuan

**Laporan HBW** adalah pelaporan berkala kinerja tiap Harta Benda Wakaf (Sub-ID) yang sudah **Aktif** di Master HBW. Satu halaman (`nazhir-laporan-program.html`) melayani **dua jenis** lewat query param `?jenis=`:

```js
const JENIS_LAPOR = ((params.get('jenis') || 'uang').toLowerCase() === 'tanah') ? 'tanah' : 'uang';
```
→ default **'uang'** bila param kosong/tak dikenal.

**Perbedaan inti:**
- **Wakaf Uang** — laporkan penghimpunan, imbal hasil (otomatis), dan penyaluran (MAQ/Operasional/Pengembangan); nilai HBW **berubah** (kenaikan/penyusutan).
- **Wakaf Tanah** — cukup laporkan **penyaluran** & **jumlah mauquf alaih**; nilai HBW **tidak berubah** dari laporan.

### 1.1 Peta Halaman & Menu
Sidebar `<details id="menuLaporanHBW">` — submenu:
| Submenu | Tujuan | Atribut |
|---|---|---|
| Pelaporan Wakaf Tanah | `nazhir-laporan-program.html?jenis=tanah` | `data-lapor="tanah"` |
| Pelaporan Wakaf Uang | `nazhir-laporan-program.html?jenis=uang` | `data-lapor="uang"` |
| Pelaporan Mutasi Aset | `nazhir-laporan.html` | `data-lapor="mutasi"` |

Menu terpisah: **Permohonan Buka Kunci** → `nazhir-buka-kunci.html`.

> **Catatan dua jalur:** "Mutasi Aset" (`nazhir-laporan.html` + `-baru`/`-isi`/`-detail`) adalah jalur **berbeda** memakai store `data_mutasi_bwi` dengan model `{saldoAwal, penerimaan, penyaluran, saldoAkhir, satuan}`. Laporan HBW program memakai `data_laporan_program_bwi`. Keduanya berbagi `cutoff_bwi` & `buka_kunci_bwi`. Dokumen ini fokus ke **Laporan HBW program**; Mutasi Aset diringkas di §10.

### 1.2 Konteks Lembaga
```js
NIB_LEMBAGA = 'NZHR-BDG-170';  REGION_NIB = 'BDG';  PREFIX_PROGRAM = 'PRG-BDG';
NAZHIR_INI = { nazhir: 'Yayasan Wakaf Nusantara', lembaga: 'NZHR-BDG-170' };
```

---

## 2. Model Data (localStorage)

| Kunci | Isi |
|---|---|
| `data_laporan_program_bwi` (`KUNCI_LAPORAN`) | Array laporan HBW (tanah + uang) |
| `data_program_bwi` (`KUNCI_PROGRAM`) | Array program tambahan (selaras nazhir-program) |
| `data_portofolio_bwi` | HBW dari Master HBW (dibaca `bacaHBW`) |
| `cutoff_bwi` (`KUNCI_CUTOFF`) | Periode/cut-off aktif |
| `buka_kunci_bwi` (`KUNCI_BUKA`) | Permohonan buka kunci |
| `data_mutasi_bwi` (`KUNCI_MUTASI`) | Laporan Mutasi Aset (jalur terpisah) |
| `notifikasi_bwi` | Notifikasi lonceng |
| `status_nazhir_bwi` | Freeze guard `{lembaga:'dibekukan'}` |
| `sesi_kenazhiran` / `pendaftaran_nazhir_bwi` / `graduasi_baru_bwi` | Guard mode calon |

### 2.1 Objek Laporan HBW (dibuat `simpanLaporan()`)
**Field kanonik (kedua jenis):**
```js
id: 'RPG-' + Date.now(),   // saat edit dipertahankan dari idEdit
kodeProgram, namaProgram, periode, tanggal,
nilaiAwal,          // baseline SSOT (= "Nilai Terakhir")
nilaiAktual,        // uang: = Nilai Terbaru; tanah: = nilaiAwal
biayaOperasional,   // uang: dari Operasional; tanah: '0'
kenaikan,           // selisih>0 ? selisih : 0
penyusutan,         // selisih<0 ? -selisih : 0
nilaiAkhir,         // = nilaiAktual
status,             // 'Draft' | 'Dilaporkan' (final, laporan tidak direview)
catatan
```
**Khusus WAKAF UANG (`jenis:'uang'`):**
```js
nilaiTerbaru: aktual,
penghimpunan,                                  // inPenghimpunan
imbalHasil: (aktual - awal) - penghimpunan,
salurMAQ,           // inMAQ
salurOperasional,   // inOperasional
salurRestruktur,    // inRestruktur  (label UI: "Pengembangan")
mauqufAlaih         // inJumlahMAQ  (jumlah penerima)
```
**Khusus WAKAF TANAH (`jenis:'tanah'`):**
```js
penyaluran,   // inPenyaluranTanah (Rp)
mauqufAlaih   // inMauqufTanah (jumlah penerima)
```

### 2.2 Skema `cutoff_bwi`
```js
{ periodeAktif: '<label>', items: { '<label>': { deadline: 'YYYY-MM-DD', terkunci: <bool> } } }
```
`bacaCutoff()` mengembalikan `{ periode, deadline, terkunci }` untuk periode aktif (kompatibel shape lama; default `{ periode:'Semester 1 - 2025', deadline:'', terkunci:false }`). Rentang bulan diturunkan runtime oleh `rentangPeriode()`/`deadlineInfo()`.

### 2.3 Skema `buka_kunci_bwi` (item, dibuat `kirimPermohonanBuka()`)
```js
{ id:'BK-'+Date.now(), nazhir, lembaga, periode, alasan, tanggal, status:'menunggu', catatan:'' }
```

### 2.4 Seed
- `DEFAULT_LAPORAN`: `RPG-DEMO-1` & `RPG-DEMO-2` — **tanpa field `jenis`**, sehingga **tidak tampil** (difilter `d.jenis === JENIS_LAPOR`).
- `DEFAULT_HBW` (dropdown): `uang:[{subId:'NZHR-BDG-002', nama:'BSI KCP Sudirman', nilai:250000000}]`, `tanah:[{subId:'NZHR-BDG-001', nama:'Tanah Jl. Merdeka', nilai:2500000000}]`.

---

## 3. Form Isi Laporan (Modal `#modalTambah`)

Judul `#judulModalTambah`: "Tambah Laporan Program" / "Edit Draft Laporan".

### 3.1 Field Bersama (kedua jenis)
| Elemen | id | Keterangan |
|---|---|---|
| Label HBW | `lblProgram` | uang: "HBW yang Dilaporkan *"; tanah: "HBW yang Ingin Dilaporkan *" |
| Pilih HBW | `inProgram` (select, `onchange=pilihProgram()`) | opsi dari `bacaHBW(JENIS_LAPOR)`, format `subId · nama`; placeholder "Pilih HBW"; kosong → "Belum ada HBW wakaf uang/tanah" |
| Info aset | `infoAsetProgram` | info nilai terkunci |
| Periode Laporan * | `inPeriode` (select) | opsi "Semester 1 - 2025 (Jan – Jun)", "Semester 2 - 2025 (Jul – Des)", "Semester 1 - 2026 (Jan – Jun)"; **diisi & dikunci** `setPeriodeAktif()` |
| Nilai Terakhir (Rp) | `inNilaiAwal` (readonly disabled) | "Terkunci — ditarik otomatis dari nilai program (Single Source of Truth)"; ph "Pilih program dulu…" |
| (hidden) Nilai Aktual | `inNilaiAktual` | field kanonik |
| (hidden) Biaya Operasional | `inBiayaOperasional` | field kanonik |
| Catatan / Keterangan Progres | `inCatatan` (textarea rows 3) | ph "Jelaskan penyebab kenaikan/penyusutan program..." |

Tombol footer: **Kembali** (`tutupModalTambah()`) · **Simpan Draft** (`simpanLaporan('draft')`) · **Upload Laporan** (`simpanLaporan('review')`; label sebelumnya "Kirim Laporan").

### 3.2 WAKAF UANG — blok `#blokUang`
| Label | id | tipe | placeholder |
|---|---|---|---|
| Nilai Terbaru (Rp) * | `inNilaiTerbaru` | text numeric | "mis. 362.000.000" |
| Penghimpunan Baru (Rp) * | `inPenghimpunan` | text numeric | "mis. 100.000.000" |
| Imbal Hasil (Rp) (otomatis) | `inImbalHasil` | readonly disabled | "Otomatis" |
| MAQ (Mauquf Alaih) (Rp) | `inMAQ` | text numeric | "0" |
| Operasional (Rp) | `inOperasional` | text numeric | "0" |
| Pengembangan (Rp) | `inRestruktur` | text numeric | "0" |
| Jumlah MAQ (penerima) | `inJumlahMAQ` | text numeric, suffix "penerima" | "mis. 120" |

Sub-heading: **Penerimaan** (Penghimpunan + Imbal Hasil), **Penyaluran (dari imbal hasil)** (MAQ, Operasional, Pengembangan). Panel bantu: `#ketPenyaluran` (persentase per kolom), `#warnPenyaluran` (peringatan merah bila total penyaluran > imbal). Keterangan: *"Penghimpunan Baru + Imbal Hasil = kenaikan nilai (Nilai Terbaru − Nilai Terakhir). Imbal hasil boleh 0."*

**`hitungUang()` (formula):**
```
v0 = Nilai Terakhir (inNilaiAwal)   v1 = Nilai Terbaru (inNilaiTerbaru)
kenaikan = v1 - v0
imbal = kenaikan - Penghimpunan Baru        // Imbal Hasil = (v1 - v0) - penghimpunan
inNilaiAktual = format(v1>0 ? v1 : 0)
inBiayaOperasional = format(Operasional)
inImbalHasil = "Rp "±  (negatif diberi "−")
totalSalur = MAQ + Operasional + Pengembangan
pct(x) = round((x/imbal)*1000)/10   // % dari imbal hasil, saat imbal>0
warnPenyaluran aktif bila totalSalur > imbal && imbal > 0
→ akhiri dengan hitungSelisih()
```

**Badge Selisih `#badgeSelisih` (`hitungSelisih()` — UANG saja):**
- Jika `JENIS_LAPOR !== 'uang'` → badge disembunyikan & return.
- `selisih = nilaiAktual - nilaiAwal`. `naik` (emerald, judul "Kenaikan Nilai Program", "+"), `turun` (merah, "Penyusutan Nilai Program", "−"), `tetap` (abu, "Nilai Program Stabil"). Menampilkan `Rp |selisih|`. Placeholder awal: *"Selisih nilai program dihitung otomatis setelah Nilai Aktual diisi."*

### 3.3 WAKAF TANAH — blok `#blokTanah`
| Label | id | tipe | placeholder |
|---|---|---|---|
| Penyaluran (Rp) * | `inPenyaluranTanah` | text numeric | "mis. 25.000.000" |
| Jumlah Mauquf Alaih | `inMauqufTanah` | text numeric, suffix "penerima" | "mis. 50" |

Keterangan: *"Wakaf tanah: cukup laporkan penyaluran & jumlah mauquf alaih yang menerima. Nilai HBW tidak berubah dari laporan ini."*

**`hitungTanah()` (formula):**
```
awal = angkaDariInput(inNilaiAwal)
inNilaiAktual = awal ? String(awal) : ''   // Nilai Aktual = Nilai Terakhir → selisih 0
inBiayaOperasional = '0'
```
→ Karena selisih selalu 0, **badge kenaikan/penyusutan disembunyikan** untuk tanah (`terapkanJenisLapor()` set `badgeSelisih.hidden = !uang`).

### 3.4 Periode Otomatis & Terkunci — `setPeriodeAktif(periodeVal)`
Target = `periodeVal || bacaCutoff().periode`. Bila belum ada di opsi → tambahkan. Set `value`, `disabled=true`, kelas `cursor-not-allowed bg-gray-100 text-gray-500`. Dipanggil oleh `bukaModalTambah()` (periode aktif) & `editLaporan(d)` (`d.periode`). Sumber: `cutoff_bwi.periodeAktif`.

### 3.5 Konteks Jenis — `terapkanJenisLapor()`
Toggle `blokTanah`/`blokUang`/`badgeSelisih`; set judul topbar & halaman; buka `#menuLaporanHBW` + tandai submenu aktif.
- Uang topbar: "Pelaporan Wakaf Uang" / "Penghimpunan, imbal hasil, penyaluran & operasional nazir".
- Tanah topbar: "Pelaporan Wakaf Tanah" / "Penyaluran & mauquf alaih HBW wakaf tanah".

### 3.6 Helper Format
`formatAngka(n)=Math.round(n).toLocaleString('id-ID')` · `nilaiRp(n)='Rp '+formatAngka(n)` · `formatRupiahInput(input)` (strip non-digit → ribuan) · `angkaDariInput(str)=Number(strip)||0` · `amankan(teks)` (escape HTML).

---

## 4. Alur Edit Draft

- `idEdit` (null = tambah baru; berisi id = edit).
- `editLaporan(d)`: cek `bolehLapor()`; `idEdit=d.id`; judul "Edit Draft Laporan"; `muatProgramAktif()`; set `inProgram=d.kodeProgram`; `pilihProgram()`; `inNilaiAwal = nilaiRp(d.nilaiAwal)` (dikunci ke nilai laporan agar stabil); `setPeriodeAktif(d.periode)`; prefill catatan.
  - **Uang** prefill: `inNilaiTerbaru=d.nilaiAktual`, `inPenghimpunan`, `inMAQ`, `inOperasional`, `inRestruktur`, `inJumlahMAQ=d.mauqufAlaih` → `hitungUang()`.
  - **Tanah** prefill: `inPenyaluranTanah=d.penyaluran`, `inMauqufTanah=d.mauqufAlaih` → `hitungTanah()`.
- `editDariDetail()`: dari `idDetail`, cari record, tutup modal detail, panggil `editLaporan(d)`.
- **Boleh edit hanya status `draft`.** Modal detail menampilkan tombol **Edit Draft** (`#btnEditLaporan`) hanya bila draft; baris tabel hanya menampilkan **Hapus** untuk draft.
- Simpan saat edit: bila `idEdit != null` → `baru.id = idEdit`, replace record (update), lalu `idEdit=null`.

---

## 5. Tabel Riwayat Laporan

### 5.1 Kolom (`<thead>`)
`Harta Benda Wakaf` · `Periode` · **`Nilai Akhir`** (`#thKol1`) · **`Perubahan`** (`#thKol2`) · `Status` · `Aksi`.
**Relabel per jenis:** Uang → thKol1 "Nilai Akhir", thKol2 "Perubahan"; **Tanah → thKol1 "Penyaluran", thKol2 "Jumlah MAQ"**.

### 5.2 Filter per Jenis
`daftarLaporan = bacaLaporan().filter(d => d.jenis === JENIS_LAPOR)` — seed lama tanpa `jenis` tak muncul.

### 5.3 Baris (`barisLaporan(d)`)
- **Harta Benda Wakaf**: `namaProgram` + `kodeProgram` (mono).
- **kol1**: uang → `nilaiRp(nilaiAkhir)`; tanah → `nilaiRp(penyaluran)`.
- **kol2**: uang → `badgePerubahan(d)`; tanah → `mauqufAlaih + ' penerima'`.
- **Status**: `badgeStatus(d)`. **Aksi**: Lihat Detail + Hapus (draft).

### 5.4 Toolbar Filter
- Cari: `#cariLaporan` / `#cariLaporan2` (`q = q1 || q2`; cari namaProgram+kodeProgram+periode+status).
- `#filterStatus`: "" / draft / review / disetujui.
- `#filterArah`: "" / naik (Kenaikan) / turun (Penyusutan).
- `#filterPeriode`: "" / "Semester 1 - 2026" / "Semester 2 - 2025" / "Semester 1 - 2025".

### 5.5 Metrik Kartu
- `metrikDraft` (statusKategori 'draft'), `metrikReview` ('review'), `metrikTotal` (label "Total Laporan Program"/"Terkirim" = status ≠ draft). `infoJumlah`: "Menampilkan X dari Y laporan."
- `selisih(d) = kenaikan - penyusutan`; `arahPerubahan` (naik/turun/tetap); `badgePerubahan` (emerald "+Rp…" / merah "−Rp…" / abu "Tetap").

---

## 6. Modal Detail Laporan (`bukaModalDetail(id)`)

Header: `dtNama`=namaProgram, `dtSub`=kodeProgram · periode. `uang = (d.jenis==='uang') || (d.jenis!=='tanah' && JENIS_LAPOR==='uang')`.
- Label adaptif: `dtLblAwal` = "Nilai Terakhir"(uang)/"Nilai HBW"(tanah); `dtLblAkhir` = "Nilai Terbaru"(uang)/"Nilai HBW"(tanah).
- **Tanah:** sel `cellAkhir`, `cellKenaikan`, `cellPenyusutan` disembunyikan.
- Grid umum: `dtAwal`, `dtAkhir`, `dtKenaikan` ("+Rp…"), `dtPenyusutan` ("−Rp…"), `dtStatus`, `dtTanggal`, `dtCatatan`.

**Blok `#dtUang`:** Penerimaan → `dtPenghimpunan`, `dtImbal` (Imbal Hasil). Penyaluran (dari imbal hasil) → `dtMAQ`(+`dtMAQpct`), `dtOp`(+`dtOppct`), `dtRes` (Pengembangan, +`dtRespct`). `pct(x)= round1(x/imbal*100)+'% dari imbal hasil'` bila imbal>0. Footer: `dtJumlahMAQ` = "N penerima".

**Blok `#dtTanah`:** Penyaluran → `dtSalurTanah`=nilaiRp(penyaluran) ("Nominal Penyaluran"); `dtMauqufTanah`="N penerima" ("Jumlah Mauquf Alaih").

Footer: **Edit Draft** (`#btnEditLaporan`, draft saja) + **Tutup**.

---

## 7. Siklus Status Laporan

Nilai yang dibuat: `'Draft'` (mode draft) & **`'Dilaporkan'`** (mode unggah — final, laporan tidak direview). Data lama `'Sedang Direview'` tetap kompatibel.
- `statusKategori(s)`: mengandung "draft"→'draft'; "dilaporkan"→'lapor'; "disetujui|selesai"→'setuju'; kompat "direview"→'review'.
- `badgeStatus`: draft = abu, **dilaporkan/setuju = brand/teal** (final).
- Laporan berstatus 'Dilaporkan' otomatis tampil di menu **Laporan Nazhir** (Pusat/Sekretaris/Ketua), read-only.

**Penguncian:**
1. **Status draft** = satu-satunya yang bisa Edit/Hapus; non-draft hanya Lihat Detail.
2. **Cut-off periode** (`cutoff_bwi.items[periode].terkunci`) mengunci pembuatan laporan — §8.
3. **Akun dibekukan** (`status_nazhir_bwi[lembaga]==='dibekukan'`) memblokir semua aksi CUD (guard klik capture-phase).

---

## 8. Cut-off Periode & Permohonan Buka Kunci

### 8.1 Banner Cut-off (`renderBannerCutoff()` → `#bannerCutoff`)
- `akunDibekukan()` → banner merah "Akun Anda dibekukan".
- Bila `!terkunci` → banner kosong. Bila terkunci, cek `statusBukaSaya(periode)`:
  - `disetujui` → banner brand "Akses buka kunci disetujui".
  - `menunggu` → banner amber "Permohonan sedang ditinjau".
  - else → banner merah "Periode pelaporan terkunci" + tombol **Ajukan Buka Kunci** (`bukaModalBukaKunci()`).
- `bolehLapor() = !akunDibekukan() && (!terkunci || statusBukaSaya === 'disetujui')`.
- `perbaruiAksiLapor()`: bila tak boleh → tombol tambah `disabled` + title "Periode terkunci — ajukan buka kunci terlebih dahulu."
- `bukaModalTambah()`/`editLaporan()` guard: `if(!bolehLapor()){ showToast('Periode terkunci. Ajukan buka kunci ke BWI Pusat terlebih dahulu.'); return; }`.

### 8.2 Modal Ajukan Buka Kunci
`#modalBukaKunci` → `#bkPeriode` (periode) + `<textarea #bkAlasan>` "Alasan Keterlambatan *" + tombol **Kirim Permohonan** (`kirimPermohonanBuka()`). Aksi: validasi alasan → `unshift` ke `buka_kunci_bwi` (status 'menunggu') → toast "Permohonan buka kunci terkirim ke BWI Pusat."

### 8.3 Halaman `nazhir-buka-kunci.html`
- Kartu "Status Periode Pelaporan": `scPeriode`, `scKunci` (Terkunci/Terbuka), `scPermohonan` (Disetujui/Menunggu/Ditolak/Belum Ada).
- Aksi dinamis (`areaAksi` via `kotakInfo()`): tombol **Ajukan Buka Kunci** muncul saat perlu.
- Kartu info: `infoPeriode` + `infoRentang` (`rentangPeriode()`).
- Tabel "Riwayat Permohonan Saya" (filter by lembaga): `Periode | Alasan | Diajukan | Status`.
- `rentangPeriode(periode)`: regex `Semester (\d) - (\d{4})` → "Januari – Juni YYYY" (S1) / "Juli – Desember YYYY" (S2).
- `deadlineInfo(c)`: `c.deadline` (YYYY-MM-DD → "D Bulan YYYY") atau default "31 Juli <th>" (S1) / "31 Januari <th+1>" (S2).
- `statusBukaSaya(periode)`: prioritas disetujui > menunggu > ditolak > null.

---

## 9. Helper & Perhitungan Kunci

- **`bacaHBW(jenis)`** — baca `data_portofolio_bwi` + seed `DEFAULT_HBW[jenis]`, hanya `status==='aktif'`. **Untuk tanah**, nilai (Rp) dari `detailTanah.nilaiAset` (string → strip non-digit), **bukan luas M²**; uang pakai `p.nilai`. Klasifikasi: `p.jenis==='uang'` atau `kategoriId` diawali "uang" → uang.
- **`baselineSSOT(kode, p)`** — cari laporan terakhir non-draft utk kode tsb → pakai `nilaiAkhir`-nya; else `p.nilai`. **Inti anti-duplikasi** (baseline dari riwayat, bukan memutasi store program).
- **`pilihProgram()`** — set `inNilaiAwal = nilaiRp(baselineSSOT(kode,h))` → `hitungUang()`/`hitungTanah()`.
- **`muatProgramAktif()`** — isi `#inProgram` dari `bacaHBW`, bangun `HBW_MAP`.
- **`simpanLaporan(mode)`** — validasi per jenis (uang: NilaiTerbaru>0, Penghimpunan>0, totalSalur ≤ imbal; tanah: Penyaluran>0; umum: aktual>0), rakit objek, simpan/update `data_laporan_program_bwi`, re-render, toast.
- **`konfirmasiHapus()`** — hapus id, simpan, re-render, toast "Draft laporan program berhasil dihapus."
- **`tanggalHariIni()`** — "D Bulan YYYY".

---

## 10. Jalur Mutasi Aset (ringkas — `data_mutasi_bwi`)

Berbeda dari Laporan HBW program.
- **Model item:** `{ id:'REP-…', periode, tanggal, aset, portofolio, satuan:'rp'|'m2', saldoAwal, penerimaan, penyaluran, saldoAkhir, status, catatan }`.
- **Kalkulasi (`nazhir-laporan-baru.html`):** `PORTOFOLIO = { uang:{awal:100000000,satuan:'rp'}, tanah:{awal:5000,satuan:'m2'} }`; **Saldo Akhir = (Saldo Awal + Penerimaan) − Penyaluran**.
- `saldoTampil`: 'rp'→"Rp …", 'm2'→"… M²". `jenisAset`: 'rp'→uang, 'm2'→tanah.
- **Tabel:** `Periode Laporan | Jenis Aset | Saldo / Luas Akhir | Status | Aksi`; filter status: ""/draft/review (Review Berkas)/revisi/ditolak/disetujui; filter jenis hanya "tanah".
- **`nazhir-laporan-isi.html`** — form bertab: `overview · penerimaan (Penerimaan Harta Wakaf) · mutasi · dampak · hasil · penyaluran (Penyaluran Mauquf Alaih)`.
- **`nazhir-laporan-detail.html`** — read-only: `dSaldoAwal, dPenerimaan, dPenyaluran, dSaldoAkhir`.

---

## 11. Ringkasan Perbedaan Tanah vs Uang (Laporan HBW)

| Aspek | Wakaf Uang | Wakaf Tanah |
|---|---|---|
| Blok input | `blokUang` (Nilai Terbaru, Penghimpunan, Imbal Hasil auto, MAQ/Operasional/Pengembangan, Jumlah MAQ) | `blokTanah` (Penyaluran, Jumlah Mauquf Alaih) |
| `nilaiAktual` | = Nilai Terbaru (input) | = `nilaiAwal` (nilai HBW tak berubah) |
| Badge selisih | Ditampilkan (kenaikan/penyusutan) | Disembunyikan (selisih = 0) |
| Field simpan ekstra | penghimpunan, imbalHasil, salurMAQ, salurOperasional, salurRestruktur, mauqufAlaih | penyaluran, mauqufAlaih |
| Kolom tabel 1 / 2 | Nilai Akhir / Perubahan | Penyaluran / Jumlah MAQ |
| Blok detail | `dtUang` (Penerimaan + Penyaluran %) | `dtTanah` (Penyaluran + Jumlah Mauquf) |
| Sumber nilai HBW (`bacaHBW`) | `p.nilai` (Rp) | `detailTanah.nilaiAset` (Rp), bukan luas M² |
