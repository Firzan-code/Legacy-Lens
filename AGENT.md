# AGENT.md — LegacyLens

Dokumen master tim — satu-satunya sumber kebenaran untuk tech stack dan
pembagian kerja. Kalau ada dokumen lain (termasuk `README.md` atau
`AGENTS.md`) yang bertentangan dengan file ini, file ini yang menang.

> **Pivot dari proyek sebelumnya (SDG-16 Sentinel):** proyek lama tidak
> sesuai tema resmi hackathon ("developer workflow improvement", Bob IDE
> sebagai komponen inti — bukan API backend). Seluruh dokumen di bawah
> ini ditulis ulang total untuk konsep baru: **LegacyLens**.

---

## 1. Tech Stack

### 1.1 Mesin Inti — Bob IDE

| Komponen | Isi | Lokasi |
|---|---|---|
| Custom Modes | 🏛️ Modernization Advisor, 🕵️ Debug Tracer | `.bob/custom_modes.yaml` |
| Skills | `modernization-report`, `debug-trace-report` | `.bob/skills/*/SKILL.md` |
| Context otomatis | Dimuat Bob di setiap sesi | `AGENTS.md` (root) |
| Subagents | Tipe `explore`, dipakai untuk analisis paralel per file | Dipanggil lewat instruksi di custom mode, bukan config terpisah |

Detail lengkap desain & rasionalnya ada di [`docs/3_PROMPTS.md`](docs/3_PROMPTS.md).

### 1.2 LegacyLens Viewer (Anggota 2)

| Komponen | Pilihan | Catatan |
|---|---|---|
| Framework | Next.js 15 (App Router), TypeScript | |
| Styling | Tailwind CSS | |
| Komponen UI | shadcn/ui | |
| Ikon | lucide-react | |
| Baca data | Node `fs` langsung di Server Component | **Tidak ada fetch/API call** — baca file lokal |
| Parsing laporan | `gray-matter` (front matter) + `react-markdown` (body) | Lihat `docs/4_API_SPEC.md` |
| Deploy | Vercel | Perhatikan catatan `dynamic` di `docs/8_FRONTEND_STATE.md` |

### 1.3 Yang SENGAJA Tidak Dipakai Lagi

* ❌ **FastAPI / Python backend** — tidak ada API yang perlu dibangun. Laporan Bob sudah jadi data.
* ❌ **Supabase / database apa pun** — `reports/` git-tracked adalah "database"-nya. Alasan lengkap di `docs/2_ARCHITECTURE.md` §2.4.
* ❌ **"IBM Bob 2.0 sebagai AI Engine dipanggil lewat HTTPX"** — tidak ada API publik Bob untuk inferensi runtime seperti itu. Bob adalah alat yang dipakai untuk MEMBANGUN dan MENJALANKAN analisis, bukan API produk yang dipanggil aplikasi lain.
* ❌ Axios, Chakra UI, `requests` — sama seperti keputusan sebelumnya, tetap tidak dipakai.

## 2. Struktur Proyek

```
LegacyLens/
├── .bob/
│   ├── custom_modes.yaml
│   └── skills/{modernization-report,debug-trace-report}/SKILL.md
├── AGENTS.md
├── AGENT.md                    ← file ini
├── README.md
├── docs/                       ← 1_PRD.md s.d. 10_TIMELINE.md
├── reports/{modernization,debug}/
├── frontend/                   ← LegacyLens Viewer
└── bob_sessions/                ← WAJIB: bukti screenshot task session Bob
```

## 3. Aturan Batas Tanggung Jawab

1. Kedua custom mode HANYA boleh menulis ke `reports/` (dibatasi lewat `fileRegex`) — tidak boleh mengubah source code target secara langsung.
2. Viewer HANYA membaca `reports/` — tidak memanggil AI apa pun, tidak menghitung skor risiko sendiri.
3. Usulan perbaikan bug ditulis sebagai diff di dalam laporan, TIDAK diterapkan otomatis ke kode.

## 4. Daftar Dokumen

| Berkas | Isi |
|---|---|
| [`docs/1_PRD.md`](docs/1_PRD.md) | Visi, tema hackathon yang dijawab, in/out-of-scope |
| [`docs/2_ARCHITECTURE.md`](docs/2_ARCHITECTURE.md) | Alur sistem, kenapa tanpa backend/DB |
| [`docs/3_PROMPTS.md`](docs/3_PROMPTS.md) | Rasionalisasi desain Custom Modes & Skills |
| [`docs/4_API_SPEC.md`](docs/4_API_SPEC.md) | Skema front matter laporan (kontrak Bob ↔ Viewer) |
| [`docs/5_DATABASE_SCHEMA.md`](docs/5_DATABASE_SCHEMA.md) | Struktur folder `reports/` (pengganti DB) |
| [`docs/6_UI_UX_SPEC.md`](docs/6_UI_UX_SPEC.md) | Tata letak Viewer |
| [`docs/7_SEED_DATA.md`](docs/7_SEED_DATA.md) | Target demo & contoh laporan |
| [`docs/8_FRONTEND_STATE.md`](docs/8_FRONTEND_STATE.md) | State handling Viewer |
| [`docs/9_DEMO_SCRIPT.md`](docs/9_DEMO_SCRIPT.md) | Naskah pitch & rencana cadangan |
| [`docs/10_TIMELINE.md`](docs/10_TIMELINE.md) | Pembagian kerja 48 jam + checklist submission |
