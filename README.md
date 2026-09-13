# Cargo3D Calculator

Kalkulator & visualisasi 3D untuk optimasi muat kontainer (20FT, 40FT, 40FT High Cube). Static single-page app — HTML, Tailwind (CDN), Three.js, Chart.js, tanpa proses build.

## Menjalankan lokal

Cukup buka `index.html` langsung di browser, atau jalankan server statis kecil (disarankan, supaya semua fitur termasuk font/CDN eksternal berjalan normal):

```bash
npx serve .
# atau
python3 -m http.server 8000
```

Login demo (mode preview lokal, tanpa backend): `admin` / `admin2026`

## Struktur proyek

```
.
├── index.html              # seluruh aplikasi (markup, style, logic)
├── manifest.webmanifest    # metadata PWA (nama, ikon, warna, mode standalone)
├── sw.js                   # service worker (offline cache + installable)
├── icons/                  # ikon aplikasi (192, 512, maskable, apple-touch, favicon)
├── vercel.json             # konfigurasi Vercel (header, cache ikon/manifest/sw)
└── .gitignore
```

## Install sebagai app di browser (PWA)

Setelah live di Vercel (harus HTTPS — Vercel otomatis HTTPS), aplikasi ini bisa dipasang seperti app native:

- **Desktop (Chrome/Edge):** akan muncul ikon install di address bar, atau klik tombol **"Install Aplikasi"** di header dalam app.
- **Android (Chrome):** klik tombol **"Install Aplikasi"**, atau menu ⋮ → "Install app" / "Tambahkan ke layar utama".
- **iOS (Safari):** tombol Share → "Tambah ke Layar Utama" (Apple belum mendukung `beforeinstallprompt`, jadi harus manual lewat menu Share).

Setelah terpasang, app berjalan fullscreen tanpa address bar, punya ikon sendiri, dan tetap bisa dibuka meski koneksi lambat/offline (berkat `sw.js`).

## Membuat APK (Android)

Karena app ini adalah PWA, cara paling gampang & resmi membuat APK adalah membungkusnya jadi **TWA (Trusted Web Activity)** — bukan menulis ulang app-nya, cukup bungkus URL yang sudah live di Vercel:

**Opsi 1 — PWABuilder (tanpa install apapun, paling gampang):**
1. Deploy dulu ke Vercel sampai dapat URL (mis. `https://cargo3d.vercel.app`).
2. Buka [pwabuilder.com](https://www.pwabuilder.com), masukkan URL tersebut.
3. PWABuilder akan mengecek manifest & service worker (sudah disiapkan di proyek ini), lalu klik **Package for Android** → unduh file `.apk` / `.aab` siap install atau upload ke Play Store.

**Opsi 2 — Bubblewrap CLI (kalau mau kontrol penuh & sudah punya Android SDK/Node):**
```bash
npm i -g @bubblewrap/cli
bubblewrap init --manifest https://cargo3d.vercel.app/manifest.webmanifest
bubblewrap build
```
Hasilnya file `.apk`/`.aab` yang bisa langsung di-sideload atau naik ke Play Store.

> Catatan: proses generate APK butuh URL yang sudah live (HTTPS) — tidak bisa dari file lokal — karena TWA memverifikasi kepemilikan domain lewat file `assetlinks.json`. PWABuilder akan memandu langkah ini kalau kamu berniat publish ke Play Store.

## Push ke GitHub

```bash
git init
git add .
git commit -m "Initial commit: Cargo3D Calculator"
git branch -M main
git remote add origin https://github.com/<username>/<nama-repo>.git
git push -u origin main
```

## Deploy ke Vercel

**Lewat dashboard (paling gampang):**
1. Buka [vercel.com/new](https://vercel.com/new) → Import repo GitHub di atas.
2. Framework Preset: pilih **Other** (project ini statis, tidak butuh build command).
3. Build Command & Output Directory: biarkan kosong.
4. Deploy.

**Lewat CLI:**
```bash
npm i -g vercel
vercel login
vercel        # deploy preview
vercel --prod # deploy production
```

Setiap `git push` ke `main` setelah repo terhubung ke Vercel akan otomatis men-deploy ulang.

## Menghubungkan ke backend nyata (opsional)

Saat ini `IS_PREVIEW = true` di dalam `index.html` (dekat baris atas file), jadi semua data (login, master barang, riwayat) memakai array JavaScript lokal — cocok untuk demo, tapi **tidak tersimpan permanen** (reset tiap refresh).

Untuk data yang benar-benar persisten setelah live di Vercel, ada dua opsi:
- **Tetap pakai Google Apps Script** seperti desain awal: set `IS_PREVIEW = false` dan isi `GAS_URL` dengan URL Web App GAS kamu.
- **Ganti ke backend sendiri**: buat REST API (mis. dengan Vercel Serverless Functions di `/api`, terhubung ke database seperti Vercel Postgres/Supabase), lalu ganti fungsi `callServerAPI()` di `index.html` supaya memanggil endpoint tersebut alih-alih GAS.
