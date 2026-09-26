# 2. Architecture

## 2.1 Prinsip Utama

**Bob IDE adalah mesinnya, bukan alat bantu di pinggir.** Tidak ada
lapisan aplikasi lain yang "memanggil AI" — analisis terjadi sepenuhnya di
dalam sesi Bob IDE, hasilnya adalah file. Semua yang dibangun di luar itu
(Viewer) murni untuk menyajikan hasil tersebut secara rapi ke juri.

Ini keputusan sadar untuk **meninggalkan arsitektur lama** (FastAPI +
Supabase + "AI Engine" dipanggil lewat HTTPX) — arsitektur itu valid untuk
produk SaaS biasa, tapi tidak relevan untuk hackathon ini karena:
1. Tidak ada API publik IBM Bob yang bisa dipanggil dari backend sendiri untuk inferensi runtime.
2. Syarat penilaian eksplisit minta Bob IDE sebagai komponen inti — bukan LLM lain di baliknya.

## 2.2 Alur

```
Developer (di Bob IDE)
        │
        ▼
Pilih mode: 🏛️ Modernization Advisor   atau   🕵️ Debug Tracer
        │
        ▼
Bob membaca target (file/folder legacy, atau error/stack trace)
        │
        ▼
Target > 1 file?  ──yes──► Spawn subagent "explore" PARALEL per file/kandidat
        │no                        │
        │                          ▼
        │                  Ringkasan tiap subagent dikembalikan ke Bob
        ▼                          │
        └──────────────┬───────────┘
                        ▼
        Bob menyusun laporan sesuai skill (.bob/skills/*)
                        │
                        ▼
        Tulis file ke reports/modernization/*.md
                    atau reports/debug/*.md
                        │
                        ▼
        LegacyLens Viewer (Next.js) membaca file itu langsung
        lewat Server Component saat halaman diakses — TIDAK ADA
        API call, TIDAK ADA database
                        │
                        ▼
                Juri melihat laporan yang rapi di browser
```

## 2.3 Komponen

| Komponen | Peran | Dibangun dengan |
|---|---|---|
| **Custom Modes** (`.bob/custom_modes.yaml`) | Persona & batasan tool Bob untuk tiap workflow | Bob IDE native (YAML) |
| **Skills** (`.bob/skills/*/SKILL.md`) | Template output terstruktur, dipakai otomatis saat mode aktif | Bob IDE native (Markdown + YAML front matter) |
| **Subagents** | Analisis paralel per file/kandidat tanpa membengkakkan context utama | Bawaan Bob 2.0, dipanggil lewat instruksi di mode |
| **`reports/`** | "Database" proyek ini — satu file Markdown per laporan | Git-tracked, jadi bukti audit sekaligus data |
| **LegacyLens Viewer** | Presentasi laporan untuk demo | Next.js 15 App Router, baca file lewat `fs` di Server Component |

## 2.4 Kenapa Tidak Ada Backend/Database

Laporan Bob **sudah** berupa data terstruktur (YAML front matter + Markdown).
Menambahkan FastAPI atau Supabase di atasnya hanya menambah lapisan yang
tidak dibutuhkan siapa pun untuk demo 48 jam — dan yang lebih penting,
tidak menambah nilai untuk kriteria penilaian yang ada. Next.js App Router
bisa membaca file lokal langsung di Server Component (`fs.readdirSync`,
`fs.readFileSync`), yang berarti "API" proyek ini sesederhana fungsi
membaca folder.

Kalau nanti ada kebutuhan nyata untuk menyimpan riwayat lintas sesi/lintas
mesin, itu didorong ke roadmap (lihat `README.md` §Roadmap) — bukan
dikerjakan sekarang tanpa alasan konkret.

## 2.5 Struktur Folder

```
LegacyLens/
├── .bob/
│   ├── custom_modes.yaml
│   └── skills/
│       ├── modernization-report/SKILL.md
│       └── debug-trace-report/SKILL.md
├── AGENTS.md                  # auto-loaded context untuk Bob
├── AGENT.md                   # dokumen master tim (rujukan lengkap)
├── README.md
├── docs/                      # 1_PRD.md ... 10_TIMELINE.md
├── reports/
│   ├── modernization/*.md     # output Modernization Advisor
│   └── debug/*.md             # output Debug Tracer
├── sample-repo/                # (opsional) target legacy untuk demo, lihat 7_SEED_DATA.md
├── frontend/                   # LegacyLens Viewer (Next.js)
│   └── app/
│       ├── page.tsx            # daftar laporan
│       ├── modernization/[slug]/page.tsx
│       └── debug/[slug]/page.tsx
└── bob_sessions/                # WAJIB: screenshot task session summary
```

## 2.6 Non-Functional Notes

| Aspek | Catatan |
|---|---|
| Bobcoin | 40 coin/akun, dipakai lintas semua sesi analisis — pantau via Bob IDE Settings → General, batasi cakupan analisis live saat demo (lihat `9_DEMO_SCRIPT.md`) |
| Konsistensi format | Skill sudah eksplisit soal struktur output; Viewer tetap perlu fallback kalau parsing gagal (lihat `8_FRONTEND_STATE.md`) |
| Keamanan | Kedua mode dibatasi lewat `fileRegex` di `custom_modes.yaml` agar hanya bisa menulis ke `reports/` — tidak bisa mengubah source code secara tidak sengaja |
