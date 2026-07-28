# Akun Demo — Modul Kenazhiran BWI Superapps

Login sekarang **tidak lagi memakai selektor role**. Semua peran masuk melalui **email + kata sandi** yang tersimpan di **localStorage peramban** (bukan server). Akun demo di-*seed* otomatis saat halaman login pertama kali dibuka.

Halaman masuk: **`kenazhiran-login.html`**

> Tip presentasi: pada halaman login ada panel **"Akun demo (untuk presentasi)"** yang bisa dilipat. Klik salah satu akun untuk mengisi email & sandi secara otomatis.

---

## 1. Akun bawaan (seeded)

| Peran | Nama | Email | Kata Sandi | Masuk ke |
|---|---|---|---|---|
| **Nazhir Aktif** | Yayasan Wakaf Nusantara | `yayasannusantara@bwi.go.id` | `password` | `nazhir-dashboard.html` |
| **BWI Perwakilan** | Mas Udin — BWI Perwakilan Pusat | `perwakilan@bwi.go.id` | `password` | `wilayah-dashboard.html` |
| **Administrator Pusat** | Mas Raji — BWI Pusat | `adminpusat@bwi.go.id` | `password` | `pusat-dashboard.html` |

### Penjelasan peran

- **Nazhir Aktif** — Nazhir yang berkasnya sudah lengkap, sudah presentasi, dan **disetujui** BWI Pusat. Akses penuh (CUD) atas aset, program, dan pelaporan.
- **BWI Perwakilan (Mas Udin)** — Pemegang akun **BWI Perwakilan Pusat**. Di modul Kenazhiran ini perannya **read-only** — dapat melihat data namun tanpa aksi. (Ranah kerja utamanya ada di modul aplikasi **BWI Perwakilan**.)
- **Administrator Pusat (Mas Raji)** — Admin BWI Kenazhiran Pusat. Mengelola pendaftaran nazhir, cut-off periode, master NIB, portofolio, dsb.

---

## 2. Calon Nazhir — daftar sendiri

Calon Nazhir **tidak disediakan sebagai akun bawaan**. Buat sendiri lewat alur pendaftaran:

1. Buka `kenazhiran-login.html` → klik **"Daftar sebagai Nazhir Wakaf Uang"** (atau buka `kenazhiran-daftar.html`).
2. Isi **Nama Badan Hukum, Email, PIC, No. HP, Kata Sandi**, lalu setujui pernyataan.
3. Akun tersimpan di `localStorage` (`pendaftaran_nazhir_bwi`) dengan status **draft** dan langsung masuk sebagai **Calon Nazhir**.
4. Login berikutnya cukup pakai **email + kata sandi** yang tadi dibuat.

Contoh (silakan bikin bebas):

| Field | Contoh |
|---|---|
| Nama Badan Hukum | Yayasan Telkom Group |
| Email | `daftar@telkomgroup.id` |
| Kata Sandi | `telkom123` |

**Perilaku Calon Nazhir:** shell lengkap seperti nazhir, tetapi menu selain pendaftaran bersifat **read-only** (tidak bisa Create/Update/Delete) dan data masih kosong sampai akun disetujui. Setelah disetujui BWI Pusat, akun otomatis **naik menjadi Nazhir Aktif** (NIB & SK terbit, semua menu terbuka).

---

## 3. Alur lengkap yang bisa didemokan

1. **Calon Nazhir** daftar → lengkapi 16 berkas → **Ajukan Verifikasi**.
2. **Admin Pusat** (`adminpusat@bwi.go.id`) → menu **Pendaftaran Nazhir** → buka **Detail** → verifikasi dokumen → jadwalkan presentasi → input hasil → **Setujui & Generate SK**.
3. **Calon Nazhir** login lagi → dashboard menampilkan banner "Selamat, akun aktif" + kartu **Sertifikat Keputusan (SK)** (preview/download).
4. **BWI Perwakilan** (`perwakilan@bwi.go.id`) → memantau data kenazhiran (read-only).

---

## 4. Catatan teknis (localStorage)

Kunci penyimpanan yang dipakai:

| Kunci | Isi |
|---|---|
| `akun_kenazhiran_bwi_v2` | Array akun bawaan (nazhir, perwakilan, pusat) — di-seed otomatis. |
| `pendaftaran_nazhir_bwi` | Data + akun (email/sandi) Calon Nazhir yang mendaftar sendiri. |
| `sesi_kenazhiran` | Sesi aktif `{ mode, peran, nama }`. |

**Reset data demo** (mis. ingin mulai dari nol) — jalankan di Console peramban (F12):

```js
['akun_kenazhiran_bwi','akun_kenazhiran_bwi_v2','pendaftaran_nazhir_bwi','pendaftaran_dummy_bwi_v2','sesi_kenazhiran','graduasi_baru_bwi']
  .forEach(k => localStorage.removeItem(k));
location.reload();
```

Setelah reset, buka kembali `kenazhiran-login.html` agar akun bawaan ter-seed ulang.

> ⚠️ Ini mockup. Kata sandi disimpan apa adanya di localStorage (tanpa enkripsi) hanya untuk keperluan simulasi/presentasi.
