# AGENTS.md

Konteks proyek yang Bob muat otomatis di setiap sesi. Ini ringkasan —
spesifikasi lengkap ada di `AGENT.md` (dokumen master tim) dan `docs/`.

## Proyek

**LegacyLens** — alat bantu developer memahami kode legacy dan menelusuri
bug lintas file, dengan Bob IDE (Custom Modes + Skills + Subagents) sebagai
mesin analisisnya. Dibangun untuk IBM Bob 2.0 Hackathon (tema: memperbaiki
developer workflow, bukan aplikasi bisnis biasa).

## Dua Mode Inti (sudah dikonfigurasi di `.bob/custom_modes.yaml`)

1. **🏛️ Modernization Advisor** — analisis kode legacy → `reports/modernization/*.md`
2. **🕵️ Debug Tracer** — telusuri error/stack trace lintas file → `reports/debug/*.md`

Kedua mode mengikuti skill di `.bob/skills/` untuk struktur output —
JANGAN menulis laporan di luar format yang didefinisikan di sana, karena
Viewer (frontend) mem-parsing field-fieldnya secara terprogram.

## Aturan Global

- Zero Hallucination: klaim risiko/root cause WAJIB merujuk lokasi kode
  konkret (file + baris), bukan generalisasi.
- Kedua mode analisis TIDAK BOLEH mengubah source code secara langsung —
  hanya menulis ke `reports/`. Usulan perbaikan ditulis sebagai diff di
  dalam laporan.
- Untuk target lebih dari satu file, gunakan subagent (`explore` type)
  secara paralel, bukan membaca file satu-satu di context utama.
- Frontend (`frontend/`) adalah Next.js App Router yang membaca file di
  `reports/` langsung lewat Server Component + `fs` — TIDAK ADA backend
  API, TIDAK ADA database. Jangan menyarankan atau membangun FastAPI/
  Supabase untuk proyek ini — itu keputusan arsitektur lama yang sudah
  ditinggalkan.
- Tech stack frontend terkunci: Next.js 15 App Router, TypeScript,
  Tailwind CSS, shadcn/ui, fetch bawaan. Lihat `AGENT.md` §1 untuk detail.

## Referensi

- `AGENT.md` — tech stack lengkap & struktur proyek
- `docs/1_PRD.md` s.d. `docs/10_TIMELINE.md` — spesifikasi lengkap tiap bagian
- `docs/7_SEED_DATA.md` — target demo (repo contoh) dan contoh laporan
