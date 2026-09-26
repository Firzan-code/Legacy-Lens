# 7. Seed Data — Target Demo & Contoh Laporan

Berbeda dari proyek lama (data dummy JSON), "seed data" di sini adalah
**target kode nyata** untuk dianalisis Bob, plus **contoh laporan** yang
menunjukkan struktur output yang benar. Contoh laporan di bawah ditulis
manual sebagai acuan format — laporan asli untuk demo HARUS dihasilkan
Bob sungguhan, bukan disalin dari sini.

## 7.1 Target Demo yang Disarankan

Pakai repo/sample yang sudah dipakai di tutorial resmi Bob — mengurangi
risiko repo "aneh" yang bikin Bob salah paham konteks, dan aman dari sisi
kebijakan dataset (bukan data pelanggan/PII, murni kode contoh publik).

| Workflow | Target yang disarankan | Kenapa |
|---|---|---|
| Modernization Advisor | Sample Node.js/Express API dari tutorial resmi Bob ("modernize Node.js application", upgrade Express API v16→v22) | Cocok persis dengan use case modernisasi; sudah jadi hands-on exercise resmi Bob, jadi ada referensi teknis siap pakai kalau butuh cek |
| Debug Tracer | Galaxium Travels demo app — `git clone -b bob-learning-path-branch https://github.com/IBM/galaxium-travels` | Aplikasi multi-file yang dipakai berulang di tutorial resmi Bob, cukup besar untuk bug yang penyebabnya lintas file |

**Verifikasi sebelum dipakai:** kedua sumber ini disebut di dokumentasi
tutorial resmi Bob — cek ulang link/nama tutorial persisnya di
`bob.ibm.com/docs` sebelum hackathon dimulai, karena URL/versi tutorial
bisa berubah. Kalau salah satu tidak cocok/tidak tersedia lagi, pilih
repo open-source kecil-menengah lain yang familiar buat tim — yang penting
ukurannya cukup untuk menunjukkan analisis lintas file, bukan satu file
saja.

**Untuk demo Debug Tracer**, siapkan bug yang **sengaja disisipkan**
sebelum hackathon (bukan bug asli yang belum tentu bisa direproduksi
konsisten) — supaya hasilnya bisa direplay identik saat gladi resik maupun
saat presentasi sungguhan.

## 7.2 Contoh Modernization Report (acuan format)

`reports/modernization/legacy-auth-middleware.md`

```markdown
---
type: modernization
module: "legacy-auth-middleware"
files_analyzed:
  - "src/middleware/auth.js"
  - "src/utils/token.js"
overall_risk: high
generated_at: "2026-09-25T09:12:00Z"
---

## Ringkasan

Middleware ini memvalidasi token di setiap request masuk. Developer
menghindarinya karena logika validasi dan refresh token bercampur dalam
satu fungsi besar tanpa test, dan memakai library JWT versi lama yang
sudah tidak menerima update keamanan.

## Bagian Berisiko

### Validasi token tercampur refresh logic — Risiko: High
**Lokasi:** `src/middleware/auth.js` baris 22–58

Fungsi `verifyAndRefresh()` melakukan validasi DAN refresh token sekaligus
tanpa pemisahan tanggung jawab, dan tidak ada test yang mengcover jalur
refresh gagal. Perubahan kecil di satu bagian berisiko merusak bagian lain
tanpa terdeteksi.

### Dependency JWT versi lama — Risiko: Medium
**Lokasi:** `src/utils/token.js` baris 3

Memakai versi library JWT yang sudah tidak menerima patch keamanan sejak
lama. Tidak langsung berbahaya untuk fungsi saat ini, tapi jadi utang
teknis yang akan menyulitkan upgrade dependency lain di masa depan.

## Rencana Modernisasi

1. Tambahkan test untuk jalur refresh gagal sebelum menyentuh logika apa pun.
2. Pisahkan `verifyAndRefresh()` jadi dua fungsi: `verifyToken()` dan `refreshToken()`.
3. Upgrade library JWT ke versi yang masih didukung, jalankan seluruh test suite.

## Estimasi Effort

Sedang — perubahan terisolasi di satu modul, tapi butuh test baru dulu sebelum aman diubah.
```

## 7.3 Contoh Debug Trace Report (acuan format)

`reports/debug/checkout-null-total.md`

```markdown
---
type: debug
error_summary: "TypeError: Cannot read properties of undefined (reading 'total')"
entry_point: "src/routes/checkout.js:handleCheckout:41"
root_cause_file: "src/services/cart.js"
confidence: high
generated_at: "2026-09-25T09:40:00Z"
---

## Error Asli

```
TypeError: Cannot read properties of undefined (reading 'total')
    at handleCheckout (src/routes/checkout.js:41:23)
    at Layer.handle (node_modules/express/lib/router/layer.js:95:5)
```

## Rantai Sebab-Akibat

1. `src/routes/checkout.js:handleCheckout` — memanggil `cart.getSummary()` dan langsung mengakses `.total` tanpa cek hasilnya.
2. `src/services/cart.js:getSummary` — mengembalikan `undefined` kalau keranjang kosong, alih-alih objek dengan `total: 0`.
3. `src/services/cart.js:getSummary` — **AKAR MASALAH**: tidak ada early return untuk kasus keranjang kosong.

## Penjelasan Root Cause

`getSummary()` diasumsikan selalu mengembalikan objek ringkasan, tapi
fungsi ini diam-diam mengembalikan `undefined` saat keranjang kosong.
`handleCheckout` tidak pernah divalidasi untuk kasus ini karena di
pengujian manual keranjang selalu berisi minimal satu item.

## Usulan Perbaikan

```diff
  function getSummary(cart) {
+   if (!cart.items.length) {
+     return { total: 0, items: [] };
+   }
    return { total: calculateTotal(cart.items), items: cart.items };
  }
```

Perbaikan ini membuat kontrak fungsi konsisten — selalu mengembalikan
objek ringkasan, tidak pernah `undefined` — sehingga pemanggil seperti
`handleCheckout` tidak perlu tahu soal kasus kosong secara khusus.
```

## 7.4 Aturan Pemakaian Contoh Ini

Dua file di atas boleh ditaruh di `reports/` sebagai **placeholder saat
membangun Viewer** (supaya frontend bisa dikerjakan sebelum laporan asli
ada) — tapi WAJIB diganti dengan hasil analisis Bob sungguhan sebelum
submission final. Laporan buatan manusia yang mengaku hasil AI adalah
representasi yang tidak jujur ke juri.
