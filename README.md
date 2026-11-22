# Sistem Pemarkahan MPU3411 — Kokurikulum Olahraga (Trek & Padang)

Backend Google Apps Script + frontend HTML. Percuma sepenuhnya, tiada had masa, tiada auto-pause.

## Struktur wajaran (mesti berjumlah 100%)

| Komponen | HPP | Wajaran | Skala | Cubaan | Penilai |
|---|---|---|---|---|---|
| Ujian Bertulis (Tugasan 1) | HP2 | 30% | **/30** | 1 | 1 |
| Fasa Permulaan — Lari Pecut | HP3 | 10% | /10 | 2 | 2 |
| Fasa Larian — Lari Pecut | HP3 | 10% | /10 | 2 | 2 |
| Fasa Penamat — Lari Pecut | HP3 | 5% | /5 | 2 | 2 |
| Penyediaan Trek | HP7 | 10% | /10 | 2 | 2 |
| Lontar Peluru — Persediaan (Gaya Gelongsor) | HP3 | 12.5% | /25 | 2 | 2 |
| Lontar Peluru — Teknik Lontaran | HP3 | 12.5% | /25 | 2 | 2 |
| Penyediaan Sektor | HP7 | 10% | /10 | 2 | 2 |
| **Jumlah** | | **100%** | | | |

**Selaras dengan fail MPU3411 MARKAH:** skala mentah = skala berwajaran, jadi `HP2 + HP3 + HP7 = Markah Akhir`.

- HP2 = Ujian (30) — jumlah Bahagian A+B+C
- HP3 = 100m (Permulaan 10 + Larian 10 + Penamat 5 = 25) + Lontar Peluru (12.5 + 12.5 = 25) = **50**
- HP7 = Penyediaan Trek 10 + Penyediaan Sektor 10 = **20**

**Pelantikan penilai:** setiap pelajar ada `penilai1` (dari lajur `Emel_Pensyarah`) dan `penilai2` (dilantik oleh koordinator). Penilai hanya nampak pelajar yang dilantik kepadanya; koordinator nampak semua.

## Fail import sedia ada

- `pelajar-import.tsv` — 321 pelajar (termasuk penilai1 dari Excel asal)
- `pensyarah-import.tsv` — 10 pensyarah (lajur: email, nama, peranan, password, aktif)

Tampal `pelajar-import.tsv` ke kotak **Import pukal** dalam tab Pelajar & Penilai. Untuk pensyarah, tampal terus ke helaian `Penilai` di bawah baris header.

Lontar peluru dipecahkan kepada **dua kriteria rubrik berasingan** (Persediaan dan Teknik Lontaran), setiap satu berskala /25 seperti dalam rubrik asal. Jumlah 12.5% + 12.5% = 25% seperti yang dikehendaki.

Agregasi HPP: HP2 = 30%, HP3 = 10+10+5+12.5+12.5 = 50%, HP7 = 10+10 = 20%.

## Kaedah pengiraan

```
Setiap penilai mengisi markah bagi setiap cubaan.
Setiap cubaan: penilai TICK satu band rubrik (Lemah / Sederhana / Kepujian / Cemerlang / Amat Cemerlang).
Sistem terus keluarkan satu markah = titik tengah band itu; penilai boleh laraskan dalam julat band.
Terbaik penilai = markah tertinggi antara cubaan miliknya sendiri.
Markah komponen = purata terbaik semua penilai.
Markah berwajaran = markah komponen × (wajaran ÷ skala maks).
Markah akhir = jumlah semua markah berwajaran.
Gred = ikut carta IPG (A+ 90, A 80, A- 75, B+ 70, B 65, B- 60, C+ 55, C 50, C- 40, D+ 30, D 20, F <20).
```

Contoh: P1 tick Cemerlang (7.5–8.9) → markah 8.2; P2 tick Cemerlang → 8.2. Komponen = 8.2 /10 → sumbangan 8.2 markah (wajaran 10%).

## Mod Demo (uji tanpa backend)

Selagi `urlAPI` dalam `index.html` masih mengandungi `GANTI_URL_EXEC_ANDA`, sistem berjalan dalam **MOD DEMO**:

- Tiada panggilan rangkaian — semua data disimpan dalam `localStorage` pelayar
- Boleh dibuka terus dari fail (`file:///.../index.html`)
- Data contoh: 6 pelajar, 8 komponen, rubrik penuh
- Akaun: `koordinator@ipgm.edu.my`, `penilai1@ipgm.edu.my`, `penilai2@ipgm.edu.my` — kata laluan `Tukar123!`
- Butang **Reset data demo** pada skrin log masuk

Selepas deploy Apps Script dan ganti `urlAPI` dengan URL `/exec`, mod demo mati sendiri.

## Pemasangan

1. Cipta Google Sheet kosong (cth. `MPU3411_Olahraga_2026`).
2. **Extensions → Apps Script** → tampal kandungan `Code.gs`.
3. Jalankan fungsi **`setupSistem()`** sekali. Beri kebenaran Sheets.
4. **Deploy → New deployment → Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Salin URL `/exec` → ganti `GANTI_URL_EXEC_ANDA` dalam `index.html`.
6. Host `index.html` di GitHub Pages (atau buka terus dari fail untuk ujian).
7. Log masuk akaun permulaan, kemudian **tukar kata laluan** dalam helaian `Penilai`.

Akaun permulaan (kata laluan `Tukar123!`):
- `koordinator@ipgm.edu.my` — koordinator
- `penilai1@ipgm.edu.my` — penilai
- `penilai2@ipgm.edu.my` — penilai

## Markah dikongsi antara penilai (live)

Markah **bukan berasingan** — setiap penilai nampak markah penilai lain:

- Chip kecil di bawah setiap cubaan: `Penilai 1: 9.5`, `Penilai 2: 6.7`
- Baris **Purata 2 penilai: 8.10 / 10** muncul pada kad komponen apabila kedua-dua penilai sudah menanda
- Halaman memuat semula sendiri setiap **10 saat** (hanya apabila tab aktif dan bukan semasa menaip), jadi markah rakan muncul tanpa refresh manual
- Setiap penilai masih tick pada kawalan sendiri; tidak boleh mengubah markah orang lain

## Penghantaran markah (bila-bila masa)

Ujian amali dijalankan **tidak serentak**, jadi markah tidak perlu lengkap untuk dihantar:

- Markah disimpan **automatik** setiap kali tick band.
- Butang **Hantar Markah** boleh ditekan walaupun baru 1 komponen diisi.
- Status: `Belum dihantar` → `✓ Dihantar · 2/8 komponen`.
- Jika markah diubah selepas dihantar, status kembali `Belum dihantar` — penilai perlu hantar semula supaya rekod tepat.
- **Batal hantar** tersedia jika tersilap.
- Koordinator nampak lajur **Hantar** (bilangan penilai yang telah menghantar) dalam jadual semua pelajar.

## Import, templat dan padam pelajar

Dalam tab **Pelajar & Penilai → Data Pelajar**:

| Butang | Fungsi |
|---|---|
| **⬇ Templat CSV** | Muat turun `templat-pelajar-mpu3411.csv` (header + 2 contoh baris) |
| **Import pukal** | Tampal TSV/CSV; terima koma atau TAB; header diabaikan automatik |
| **Padam dipilih (n)** | Tanda kotak pada baris, tekan butang — padam pelajar + markah + rekod hantar |
| **Padam semua** | Perlu taip `HAPUS SEMUA` untuk sahkan |

Susunan lajur templat:

```
angkaGiliran, nama, noKP, kelas, kumpulan, jantina, penilai1, penilai2
```

- `jantina`: `L` atau `P`
- `penilai2`: boleh kosong
- `penilai1`: emel pensyarah (rujuk lajur `Emel_Pensyarah` dalam Excel asal)

## Mesra telefon bimbit

- Tab, butang dan input dibesarkan untuk sentuhan (input 16px — elak zoom iOS)
- Senarai pelajar dihadkan tinggi supaya borang pemarkahan mudah dicapai
- Jadual boleh skrol mendatar; header jadual kekal di atas semasa skrol
- Disahkan pada lebar 390px (saiz iPhone)

## Menu analisis & moderasi (v1.3.0 / backend v1.1.0)

Empat tab baharu (khas koordinator) — semuanya menggunakan API backend, bukan pengiraan dalam pelayar.

| Tab | API yang dipanggil | Fungsi |
|---|---|---|
| **Rekod Prestasi** | `getData` (sedia ada) | Senarai semua pelajar; klik tajuk lajur untuk susun ikut nama / angka giliran / **kelas** / jumlah / gred / mata. Ada tapisan kelas, carian dan muat turun CSV. |
| **Analisis Gred** | `analisisGred` | n dinilai, purata, median, **sisihan piawai**, min, maks, julat, % lulus, taburan gred (bilangan + %) dan pecahan ikut kelas. |
| **Simulasi** | `simulasiSisihanPiawai` | Simulasi Monte Carlo: bootstrap markah sebenar atau jana markah rawak (normal / seragam / serong kanan / serong kiri). Pulangkan purata SD, SD P5–P95, julat SD, histogram SD, taburan gred dan sampel markah. Seed sama = keputusan sama. |
| **Moderasi LAM-PT-02** | `moderasiPT02` | Pilih sampel **3 rendah / 3 sederhana / 3 tinggi** (boleh tukar calon), kemudian cetak **Borang Moderasi Pentaksiran LAM-PT-02** (A4 landskap). |

### Parameter API

```js
// Analisis
{ action: 'analisisGred' }

// Simulasi
{ action: 'simulasiSisihanPiawai',
  mod: 'sebenar' | 'rawak',
  bilangan: 30, iterasi: 300, seed: 2026,
  minMarkah: 0, maxMarkah: 100,
  taburan: 'normal' | 'seragam' | 'serongKanan' | 'serongKiri' }

// Moderasi
{ action: 'moderasiPT02', kaedah: 'tertile' | 'gred', bilangan: 3 }
```

Semua tindakan ini memerlukan log masuk **koordinator** (penilai biasa akan ditolak dengan mesej *"Hanya koordinator…"*).

### Kaedah pengiraan

- **Sisihan piawai**: sampel, `SD = √( Σ(x − x̄)² / (n − 1) )`. Jika `n = 1`, SD = 0.
- **Persentil**: interpolasi linear pada senarai tersusun (P5, P50, P95).
- **Kategori moderasi (tertile)**: sempadan pada persentil 1/3 dan 2/3 taburan markah akhir. **Atau** ikut gred: ≥70 tinggi · 50–69.99 sederhana · <50 rendah.
- **Pemilihan sampel**: bertaburan dalam setiap kumpulan (bukan 3 teratas sahaja) supaya wakil lebih adil.
- Markah 0 yang belum dinilai (tiada markah komponen) **dikecualikan** daripada analisis dan sampel.

### Selepas kemas kini

1. `Code.gs` → Apps Script → **Deploy → Manage deployments → Edit → New version → Deploy** (wajib, jika tidak tab baharu akan balas *"Tindakan tidak dikenali."*).
2. `index.html` → GitHub Pages → **Ctrl + F5**; console patut cetak `v1.3.0-analisis`.
3. Ujian logik backend: `node test-analisis.js` (43 semakan) dan `node test-kira.js`.

## Helaian yang dicipta

| Helaian | Fungsi |
|---|---|
| `Pelajar` | angkaGiliran, nama, noKP, kelas, kumpulan, aktif |
| `Penilai` | email, nama, peranan, password, aktif |
| `Komponen` | kod, nama, tugasan, hpp, wajaran, skalaMaks, bilCubaan, bilPenilai, susunan, aktif |
| `Band` | komponenKod, nama, minMarkah, maxMarkah, deskriptor, susunan |
| `Markah` | id, angkaGiliran, komponenKod, penilai, cubaan, markah, nilaiMentah, tarikh |
| `Konfigurasi` | kunci, nilai, catatan (kampus, program, nama kursus, nama tandatangan) |
| `LogAktiviti` | jejak audit |

## Aliran kerja

1. **Koordinator** → Tetapan → tambah pelajar (satu-satu atau import pukal dari Excel).
2. **Koordinator** → Tetapan → daftar penilai (emel + kata laluan).
3. **Koordinator** → Tetapan Rubrik → isi kriteria acara padang (25%) apabila rubrik diterima.
4. **Penilai 1 & Penilai 2** log masuk → pilih pelajar → isi cubaan 1 & 2 setiap komponen.
   - Band rubrik muncul serta-merta (markah live).
   - Setiap penilai hanya nampak markahnya sendiri.
5. **Koordinator** → Semua Pelajar → semak purata, jumlah, gred.
6. **Koordinator** → Cetak LAM-PT-04-01.

## Nota penting

- **Rubrik lontar peluru** sudah dimasukkan sepenuhnya: 5 band (Amat Cemerlang, Cemerlang, Kepujian, Lulus, Lemah) untuk kedua-dua kriteria Persediaan dan Teknik Lontaran, berskala /25.
- Kalau `setupSistem()` sudah dijalankan **sebelum** rubrik lontar peluru diterima, jalankan fungsi **`kemasKiniLontarPeluru()`** sekali dari editor Apps Script untuk menggantikan komponen `padang` lama.
- Semua kriteria/band boleh diubah dari UI tanpa ubah kod.
- Wajaran mesti berjumlah 100% (indikator hijau di panel komponen).
- Kata laluan disimpan sebagai teks dalam sheet (mudah urus). Kalau mahu lebih selamat, simpan hash SHA-256 — kod menerima kedua-dua bentuk.
- Cetakan LAM-PT-04-01 menggunakan A4 melintang.

## Fail

- `Code.gs` — backend
- `index.html` — frontend
- `test-kira.js` — ujian logik pengiraan (`node test-kira.js`)
