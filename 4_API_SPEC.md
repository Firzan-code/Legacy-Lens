# 4. Report Schema Spec

Tidak ada API di proyek ini — dokumen ini menggantikan peran "API spec"
lama: mendefinisikan **kontrak data** antara apa yang Bob tulis (lewat
skill) dan apa yang Viewer baca. Kontrak ini yang membuat keduanya bisa
dikerjakan oleh dua orang secara paralel tanpa saling menunggu, sama
seperti API spec pada arsitektur konvensional.

## 4.1 Modernization Report

**Lokasi:** `reports/modernization/{module-slug}.md`

**Front matter (YAML):**

```yaml
type: modernization        # string literal, selalu "modernization"
module: string              # nama modul, mis. "auth-service"
files_analyzed: string[]    # path relatif tiap file yang dianalisis
overall_risk: low|medium|high
generated_at: string        # ISO 8601
```

**Body (Markdown):** empat section wajib berurutan — `## Ringkasan`,
`## Bagian Berisiko`, `## Rencana Modernisasi`, `## Estimasi Effort`.
Lihat template lengkap di `.bob/skills/modernization-report/SKILL.md`.

**Aturan parsing untuk Viewer:**
* `overall_risk` menentukan warna badge di kartu daftar laporan (lihat `6_UI_UX_SPEC.md` §Skema Warna).
* Section `## Bagian Berisiko` di-parse sebagai daftar sub-heading `###` — masing-masing jadi satu item di UI.
* Kalau front matter tidak lengkap/tidak valid → Viewer tetap render body Markdown mentah, badge risiko ditampilkan sebagai "Tidak diketahui" (lihat `8_FRONTEND_STATE.md`).

## 4.2 Debug Trace Report

**Lokasi:** `reports/debug/{id}.md`

**Front matter (YAML):**

```yaml
type: debug                 # string literal, selalu "debug"
error_summary: string
entry_point: string         # "path/file.js:functionName:line"
root_cause_file: string
confidence: low|medium|high
generated_at: string        # ISO 8601
```

**Body (Markdown):** empat section wajib berurutan — `## Error Asli`,
`## Rantai Sebab-Akibat`, `## Penjelasan Root Cause`, `## Usulan Perbaikan`.
Lihat template lengkap di `.bob/skills/debug-trace-report/SKILL.md`.

**Aturan parsing untuk Viewer:**
* `confidence` menentukan badge di kartu daftar (bukan warna risiko — label berbeda, lihat `6_UI_UX_SPEC.md`).
* Section `## Rantai Sebab-Akibat` di-parse sebagai list bernomor — dirender sebagai trace timeline vertikal.
* Section `## Usulan Perbaikan` berisi blok kode ` ```diff ` — dirender dengan syntax highlighting diff, bukan teks polos.

## 4.3 Library yang Disarankan untuk Parsing

* **`gray-matter`** — parsing YAML front matter dari file Markdown, standar de-facto di ekosistem Next.js/Node.
* **`remark`/`react-markdown`** — render body Markdown (termasuk blok ` ```diff `) ke JSX.

```ts
import matter from "gray-matter";
import fs from "fs";

const raw = fs.readFileSync("reports/modernization/auth-service.md", "utf-8");
const { data, content } = matter(raw);
// data.overall_risk, data.module, dst — content = body markdown
```

## 4.4 Validasi Minimal (Frontend)

Sebelum render, Viewer memvalidasi field wajib ada dan `overall_risk`/
`confidence` masuk salah satu dari tiga nilai yang diizinkan. Kalau gagal
validasi, masuk ke `error` state di `8_FRONTEND_STATE.md` — bukan crash.
Ini adalah pengganti langsung dari validasi Pydantic `422` di arsitektur
lama: sama-sama menjaga agar data yang bentuknya salah tidak menembus ke
tampilan tanpa terdeteksi.
