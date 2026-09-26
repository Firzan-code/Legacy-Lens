# 🔍 LegacyLens

> **Pahami kode lama. Lacak bug lintas file. Bob IDE sebagai mesinnya.**
> Proof of concept 48 jam — IBM Bob 2.0 Hackathon.

![AI Engine](https://img.shields.io/badge/Core-IBM%20Bob%202.0%20IDE-052FAD?style=for-the-badge&logo=ibm&logoColor=white)
![Viewer](https://img.shields.io/badge/Viewer-Next.js%2015-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)
![Status](https://img.shields.io/badge/status-proof%20of%20concept-orange?style=for-the-badge)

---

## 📌 Ringkasan

**LegacyLens** adalah dua workflow developer yang dijalankan langsung di
dalam **Bob IDE** lewat Custom Modes + Skills + Subagents paralel:

1. **🏛️ Modernization Advisor** — menjelaskan modul legacy dalam bahasa
   manusia, memetakan bagian berisiko tinggi dengan lokasi kode persis, dan
   menyusun rencana modernisasi bertahap.
2. **🕵️ Debug Tracer** — menelusuri root cause error lintas file, menyusun
   rantai sebab-akibat, dan mengusulkan perbaikan (sebagai diff, tidak
   diterapkan otomatis).

Hasilnya disajikan lewat **LegacyLens Viewer**, dashboard Next.js ringan
yang membaca laporan langsung dari file — **tanpa backend, tanpa
database**. Bob IDE sendiri adalah mesinnya; Viewer murni presentasi.

## 🎯 Kenapa Proyek Ini Dibangun Seperti Ini

Kriteria hackathon ini eksplisit: solusi harus menunjukkan Bob IDE sebagai
**komponen inti**, memakai Agent mode, parallel tasks, subagents, dan
document understanding — bukan sekadar alat bantu ngoding sekilas.

LegacyLens sengaja **tidak** punya "AI Engine" yang dipanggil lewat API
dari backend sendiri — karena tidak ada API publik Bob untuk itu. Sebagai
gantinya, analisis terjadi *sepenuhnya* di dalam sesi Bob IDE, dan hasilnya
adalah file. Detail lengkap alasannya ada di
[`docs/2_ARCHITECTURE.md`](docs/2_ARCHITECTURE.md).

## ✨ Fitur Utama

* **Analisis kode legacy otomatis** — penjelasan plain-language + peta risiko dengan lokasi kode konkret.
* **Debugging lintas file** — rantai sebab-akibat yang eksplisit, bukan tebakan.
* **Subagent paralel** — modul multi-file dianalisis lewat beberapa subagent `explore` sekaligus, bukan satu-satu.
* **Batasan tool eksplisit** — kedua mode hanya bisa menulis ke `reports/`, tidak pernah mengubah source code secara langsung.
* **Viewer tanpa backend** — laporan Markdown ber-front-matter langsung jadi data untuk Next.js.

## 🤖 Cara Kerja

```
Developer di Bob IDE
   │  pilih mode: Modernization Advisor / Debug Tracer
   ▼
Bob baca target (file/error) → spawn subagent paralel bila perlu
   ▼
Tulis laporan terstruktur ke reports/{modernization,debug}/*.md
   ▼
LegacyLens Viewer (Next.js) baca file itu langsung — tanpa API
   ▼
Developer/juri lihat laporan yang rapi di browser
```

Detail penuh: [`docs/2_ARCHITECTURE.md`](docs/2_ARCHITECTURE.md).

## 🛠️ Tech Stack

| Lapisan | Teknologi |
|---|---|
| **Mesin analisis** | IBM Bob IDE 2.0 — Custom Modes, Skills, Subagents |
| **Viewer** | Next.js 15 (App Router, TypeScript), Tailwind CSS, shadcn/ui |
| **"Database"** | File Markdown ber-front-matter di `reports/`, git-tracked |
| **Parsing** | `gray-matter`, `react-markdown` |
| **Deploy** | Vercel |

Tidak ada FastAPI, tidak ada Supabase, tidak ada API key AI yang dipanggil
dari backend sendiri — lihat [`AGENT.md`](AGENT.md) §1.3 untuk daftar
lengkap apa yang sengaja ditinggalkan dari iterasi sebelumnya, dan kenapa.

## 📂 Struktur Proyek

```
LegacyLens/
├── .bob/
│   ├── custom_modes.yaml
│   └── skills/
│       ├── modernization-report/SKILL.md
│       └── debug-trace-report/SKILL.md
├── AGENTS.md              # konteks yang otomatis dimuat Bob
├── AGENT.md                # dokumen master tim
├── docs/                    # 1_PRD.md s.d. 10_TIMELINE.md
├── reports/
│   ├── modernization/*.md
│   └── debug/*.md
├── frontend/                # LegacyLens Viewer (Next.js)
└── bob_sessions/             # bukti screenshot task session Bob (wajib submission)
```

## 🚀 Menjalankan Secara Lokal

### 1. Siapkan Bob IDE

* Install Bob IDE, login dengan akun hackathon-provisioned (BUKAN akun pribadi — lihat `docs/10_TIMELINE.md` §10.1).
* Clone repo ini, buka di Bob IDE. `.bob/custom_modes.yaml` dan `.bob/skills/` otomatis terbaca.
* Cek dua mode baru (🏛️ Modernization Advisor, 🕵️ Debug Tracer) muncul di mode selector.

### 2. Jalankan Analisis

```
# Di Bob IDE, ganti mode ke Modernization Advisor, lalu:
"Analisis modul src/middleware/auth.js untuk rencana modernisasi"

# Ganti mode ke Debug Tracer, lalu tempel error/stack trace
```

Laporan otomatis tersimpan ke `reports/modernization/` atau `reports/debug/`.

### 3. Jalankan Viewer

```bash
cd frontend
npm install
npm run dev
```

Buka `http://localhost:3000` — laporan yang sudah ada di `reports/`
langsung tampil.

## 📚 Dokumentasi

| Berkas | Isi |
|---|---|
| [`AGENT.md`](AGENT.md) | Tech stack final & pembagian kerja (sumber kebenaran) |
| [`docs/1_PRD.md`](docs/1_PRD.md) | Visi, tema hackathon, in/out-of-scope |
| [`docs/2_ARCHITECTURE.md`](docs/2_ARCHITECTURE.md) | Alur sistem, kenapa tanpa backend/DB |
| [`docs/3_PROMPTS.md`](docs/3_PROMPTS.md) | Rasionalisasi Custom Modes & Skills |
| [`docs/4_API_SPEC.md`](docs/4_API_SPEC.md) | Skema laporan (kontrak Bob ↔ Viewer) |
| [`docs/5_DATABASE_SCHEMA.md`](docs/5_DATABASE_SCHEMA.md) | Struktur folder `reports/` |
| [`docs/6_UI_UX_SPEC.md`](docs/6_UI_UX_SPEC.md) | Tata letak Viewer |
| [`docs/7_SEED_DATA.md`](docs/7_SEED_DATA.md) | Target demo & contoh laporan |
| [`docs/8_FRONTEND_STATE.md`](docs/8_FRONTEND_STATE.md) | State handling Viewer |
| [`docs/9_DEMO_SCRIPT.md`](docs/9_DEMO_SCRIPT.md) | Naskah pitch & rencana cadangan |
| [`docs/10_TIMELINE.md`](docs/10_TIMELINE.md) | Pembagian kerja 48 jam & checklist submission |

## 🧭 Batasan PoC

* Format laporan masih baku untuk dua workflow saja — belum ada code review atau test-generation skill.
* Usulan perbaikan bug tidak pernah diterapkan otomatis — tetap butuh review manual developer, dan memang didesain begitu.
* Viewer murni presentasi lokal/statis — belum ada riwayat lintas sesi/lintas mesin (lihat Roadmap).

## 🗺️ Roadmap

* [ ] Skill tambahan: code review otomatis, test generation
* [ ] watsonx Orchestrate untuk menjadwalkan analisis otomatis tiap PR baru
* [ ] Penyimpanan riwayat laporan lintas repo/tim (baru butuh database di titik ini)
* [ ] Integrasi ke CI (jalankan Modernization Advisor otomatis saat file lama disentuh PR)

## 👥 Tim

| Peran | Nama | Tanggung jawab |
|---|---|---|
| Bob Modes & Skills | [Nama] | `.bob/`, uji coba analisis, iterasi skill |
| Viewer | [Nama] | Next.js, tampilan laporan |

## 📄 Lisensi

MIT — lihat [`LICENSE`](LICENSE).
