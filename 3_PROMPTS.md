# 3. Bob Modes & Skills Spec

File konfigurasi ASLI-nya sudah jadi dan siap pakai — bukan cuma spesifikasi
di sini. Salin folder `.bob/` dan file `AGENTS.md` dari paket yang diberikan
ke root repo kalian:

```
.bob/custom_modes.yaml
.bob/skills/modernization-report/SKILL.md
.bob/skills/debug-trace-report/SKILL.md
AGENTS.md
```

Setelah disalin, buka Bob IDE — dua mode baru (**🏛️ Modernization Advisor**
dan **🕵️ Debug Tracer**) otomatis muncul di mode selector. Dokumen ini
menjelaskan *kenapa* config itu ditulis seperti itu, supaya kalian bisa
mengedit dengan pemahaman, bukan menyalin buta.

## 3.1 Custom Modes — Ringkasan Desain

Format mengikuti dokumentasi resmi Bob (`.bob/custom_modes.yaml`, field:
`slug`, `name`, `roleDefinition`, `whenToUse`, `customInstructions`,
`groups`).

| Field | Modernization Advisor | Debug Tracer |
|---|---|---|
| Fokus | Memahami & menilai risiko kode legacy | Menelusuri root cause bug lintas file |
| Tool `edit` dibatasi ke | `reports/modernization/**` | `reports/debug/**` |
| Boleh `command`? | Ya, hanya observasi (`npm outdated`, `npm test`) | Ya, hanya reproduksi (`npm test -- <nama>`) |
| Boleh ubah source code? | **Tidak** | **Tidak** — fix ditulis sebagai diff di laporan |

**Kenapa `edit` dibatasi lewat `fileRegex`, bukan dilarang sepenuhnya:**
Bob tetap perlu menulis laporan (itu poin utamanya), tapi tidak boleh
menyentuh source code demo secara tidak sengaja — terutama penting saat
demo langsung di depan juri, di mana perubahan tak terduga ke kode bisa
merusak jalannya presentasi.

**Kenapa command dibatasi ke observasi/reproduksi:** kedua mode ini adalah
*advisor*, bukan *executor*. Keputusan untuk benar-benar menjalankan
perubahan tetap di tangan developer manusia — konsisten dengan semangat
"showcase Bob meningkatkan workflow", bukan "Bob menggantikan developer".

## 3.2 Subagents — Kapan dan Kenapa

Kedua mode diinstruksikan memakai subagent tipe **`explore`** (read-only,
model lebih ringan) untuk memeriksa banyak file secara paralel:

* **Modernization Advisor:** satu subagent per file dalam modul yang
  dianalisis, dijalankan bersamaan, hasilnya digabung jadi satu laporan.
* **Debug Tracer:** satu subagent per kandidat file yang mungkin jadi
  sumber bug (kalau ada lebih dari satu kemungkinan pemanggil), sebelum
  memutuskan mana yang paling didukung bukti.

Ini bukan sekadar teknis — ini **poin demo penting**: subagent paralel
persis fitur yang diminta tema hackathon untuk ditunjukkan secara eksplisit,
dan alasannya konkret (bukan dipakai sekadar biar terlihat canggih):
membaca banyak file secara paralel lebih cepat dan tidak membengkakkan
context window percakapan utama dibanding membaca satu-satu.

## 3.3 Skills — Ringkasan Desain

Format mengikuti dokumentasi resmi Bob (`.bob/skills/<nama>/SKILL.md`,
YAML front matter `name` + `description`, diikuti instruksi bebas teks).
Skill load otomatis sekali per percakapan berdasarkan deskripsi & konteks
permintaan — kalian tidak perlu memanggilnya manual.

Isi lengkap template masing-masing skill ada di file aslinya
(`.bob/skills/modernization-report/SKILL.md` dan
`.bob/skills/debug-trace-report/SKILL.md`). Field kunci yang WAJIB
konsisten karena dipakai Viewer untuk parsing (lihat `4_API_SPEC.md`):

**Modernization Report front matter:** `type`, `module`, `files_analyzed`,
`overall_risk` (`low`/`medium`/`high`), `generated_at`.

**Debug Trace Report front matter:** `type`, `error_summary`,
`entry_point`, `root_cause_file`, `confidence` (`low`/`medium`/`high`),
`generated_at`.

## 3.4 Cara Menjalankan Saat Demo/Development

1. Buka file/folder legacy target di Bob IDE.
2. Ganti mode ke **Modernization Advisor** lewat dropdown atau slash command.
3. Prompt sederhana sudah cukup, mis.: *"Analisis modul ini untuk rencana
   modernisasi"* — mode + skill yang menangani sisanya.
4. Bob akan meminta approval sebelum spawn subagent (kalau lebih dari satu
   file) — approve satu per satu atau sekaligus sesuai preferensi.
5. Cek `reports/modernization/` untuk file yang baru ditulis.
6. Ulangi langkah 2-5 dengan mode **Debug Tracer**, kali ini prompt berupa
   paste error/stack trace, bukan menunjuk file.

## 3.5 Kalau Bob Menyimpang dari Format

Kalau hasil laporan tidak mengikuti struktur skill (field hilang, format
berbeda), itu tanda skill perlu dipertajam instruksinya — bukan tanda
Viewer harus dilonggarkan untuk menerima format bebas. Perbaiki di
`.bob/skills/*/SKILL.md`, jalankan ulang analisisnya. Viewer tetap punya
fallback tampilan mentah untuk laporan yang gagal di-parse (lihat
`8_FRONTEND_STATE.md`) supaya demo tidak macet kalau ini terjadi di menit
akhir, tapi itu jaring pengaman, bukan target.
