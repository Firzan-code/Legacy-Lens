# 6. UI/UX Spec — LegacyLens Viewer

## 6.1 Prinsip Desain

* **Viewer, bukan produk utama.** Analisis sesungguhnya terjadi di Bob IDE
  — Viewer cuma menyajikannya. Jangan habiskan waktu berlebihan mempercantik
  ini sampai mengorbankan waktu membangun mode/skill.
* **Bukti konkret, bukan skor abstrak.** Beda dari dashboard lama yang
  menonjolkan angka (risk score 0-100), di sini yang ditonjolkan adalah
  *lokasi kode* dan *rantai sebab-akibat* — karena itu yang sebenarnya
  meyakinkan developer, bukan angka.

## 6.2 Halaman

### `/` — Daftar Laporan

```
┌──────────────────────────────────────────────────────────┐
│  🔍 LegacyLens                                              │
├──────────────────────────────────────────────────────────┤
│  [ Modernization Reports ]   [ Debug Trace Reports ]        │  ← tab
├──────────────────────────────────────────────────────────┤
│  ┌────────────────────────┐  ┌────────────────────────┐    │
│  │ 🔴 auth-service         │  │ 🟡 payment-gateway      │    │
│  │ Risiko: TINGGI          │  │ Risiko: SEDANG          │    │
│  │ 3 file dianalisis       │  │ 2 file dianalisis       │    │
│  └────────────────────────┘  └────────────────────────┘    │
└──────────────────────────────────────────────────────────┘
```

Tab **Modernization Reports**: kartu per laporan — nama modul, badge
`overall_risk`, jumlah file dianalisis, timestamp relatif ("2 jam lalu").

Tab **Debug Trace Reports**: kartu per laporan — `error_summary`, badge
`confidence`, nama `root_cause_file`.

### `/modernization/[slug]` — Detail Modernization Report

* Header: nama modul, badge risiko besar, daftar file dianalisis.
* Section "Ringkasan" — paragraf biasa.
* Section "Bagian Berisiko" — satu kartu per area, masing-masing menampilkan
  lokasi (`file:baris`) dengan format monospace yang jelas dibedakan dari
  teks penjelasan, dan badge risiko per-item (bisa beda dari `overall_risk`
  keseluruhan).
* Section "Rencana Modernisasi" — checklist bernomor, bukan paragraf.
* Section "Estimasi Effort" — badge kecil di akhir halaman.

### `/debug/[slug]` — Detail Debug Trace Report

* Header: `error_summary`, badge `confidence`.
* Blok "Error Asli" — monospace, background gelap, scroll horizontal kalau panjang.
* "Rantai Sebab-Akibat" — divisualisasikan sebagai **timeline vertikal**:
  tiap hop adalah satu titik terhubung garis ke titik berikutnya, hop
  terakhir (root cause) ditandai berbeda (mis. titik merah lebih besar +
  label "AKAR MASALAH").
* "Penjelasan Root Cause" — paragraf biasa.
* "Usulan Perbaikan" — blok kode dengan **diff syntax highlighting**
  (baris `+` hijau, baris `-` merah) — bukan blok kode polos.

## 6.3 Komponen

| Komponen | Fungsi |
|---|---|
| `ReportCard` | Kartu ringkas di halaman daftar, dipakai untuk kedua jenis laporan (props berbeda) |
| `RiskBadge` | Badge warna untuk `overall_risk`/`confidence`, satu komponen dipakai di mana pun badge muncul — supaya warnanya konsisten di seluruh app |
| `RiskAreaCard` | Satu area berisiko di halaman detail modernization |
| `TraceTimeline` | Visualisasi rantai sebab-akibat di halaman detail debug |
| `DiffBlock` | Render blok ` ```diff ` dengan warna tambah/hapus |
| `RawFallback` | Render Markdown mentah kalau front matter gagal divalidasi |

## 6.4 Skema Warna

| Nilai | Warna | Label |
|---|---|---|
| `high` | Merah | "Tinggi" |
| `medium` | Kuning/amber | "Sedang" |
| `low` | Hijau | "Rendah" |
| tidak valid/hilang | Abu-abu | "Tidak diketahui" |

Skema yang sama dipakai untuk `overall_risk` (Modernization) dan
`confidence` (Debug) — walau labelnya sedikit beda konteks ("Risiko Tinggi"
vs "Keyakinan Tinggi"), warnanya tetap konsisten supaya juri tidak perlu
belajar dua sistem warna berbeda.

## 6.5 Empty & Error State

| Kondisi | Tampilan |
|---|---|
| Belum ada laporan sama sekali di folder | Pesan ramah: "Belum ada laporan. Jalankan mode Modernization Advisor atau Debug Tracer di Bob IDE untuk mulai." — bukan halaman kosong tanpa keterangan |
| Satu file laporan gagal di-parse | Kartu tetap muncul di daftar dengan badge "Tidak diketahui", halaman detail pakai `RawFallback` |
| Folder `reports/` tidak ada sama sekali | Pesan setup: arahkan ke `docs/2_ARCHITECTURE.md` untuk struktur folder yang diharapkan |
