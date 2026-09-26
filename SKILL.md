---
name: modernization-report
description: Menghasilkan Modernization Report terstruktur untuk sebuah modul/file kode legacy — mengidentifikasi bagian berisiko tinggi dan menyusun rencana modernisasi bertahap. Aktif otomatis saat mode Modernization Advisor menganalisis kode.
---

Saat diminta menganalisis modul atau file legacy, hasilkan SATU file Markdown
dengan YAML front matter berikut. Jangan menyimpang dari struktur ini — Viewer
frontend proyek ini mem-parsing field-field ini secara terprogram.

Format WAJIB:

```markdown
---
type: modernization
module: "nama-modul-atau-file"
files_analyzed:
  - "path/relatif/file1.js"
  - "path/relatif/file2.js"
overall_risk: high   # low | medium | high
generated_at: "2026-09-25T10:00:00Z"
---

## Ringkasan

[2-4 kalimat, bahasa manusia awam: apa fungsi modul ini, mengapa developer
sering takut menyentuhnya, apa risiko terbesarnya.]

## Bagian Berisiko

### [Nama area/fungsi] — Risiko: [Low/Medium/High]
**Lokasi:** `path/file.js` baris 42–67

[Jelaskan KENAPA area ini berisiko: side effect tersembunyi, tidak ada test,
dependency ke API yang sudah deprecated, dst. WAJIB konkret, sebut nama
fungsi/variabel asli dari kode, bukan generalisasi.]

[Ulangi blok ini untuk setiap area berisiko yang ditemukan, urutkan dari
risiko tertinggi ke terendah.]

## Rencana Modernisasi

1. [Langkah pertama — paling aman, paling kecil dampaknya]
2. [Langkah berikutnya]
3. [dst — urutkan dari risiko rendah ke tinggi, bukan acak]

## Estimasi Effort

[Singkat: kecil / sedang / besar, dan alasan satu kalimat.]
```

Aturan tambahan:

- Kalau modul terdiri dari banyak file, gunakan subagent `explore` per file
  (lihat `.bob/custom_modes.yaml` §modernization-advisor) sebelum menyusun
  laporan gabungan — jangan baca semua file secara berurutan di context utama.
- `overall_risk` diambil dari risiko TERTINGGI di antara seluruh bagian yang
  ditemukan, bukan rata-rata — satu fungsi berisiko tinggi cukup membuat
  seluruh modul ditandai `high`.
- Jika tidak ditemukan bagian berisiko sama sekali, bagian "Bagian Berisiko"
  tetap ada tapi isinya: "Tidak ditemukan area berisiko tinggi pada modul ini
  — kode relatif aman untuk dimodifikasi." `overall_risk: low`.
- Simpan hasil ke `reports/modernization/{module-slug}.md`, slug dari nama
  modul (huruf kecil, spasi jadi tanda hubung).
