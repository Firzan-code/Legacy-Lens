# 1. PRD — Product Requirements Document

## 1.1 Nama & Tagline

**LegacyLens** — Pahami kode lama, lacak bug lintas file, dengan Bob IDE sebagai mesinnya.

## 1.2 Tema Hackathon yang Dijawab

IBM Bob 2.0 Hackathon meminta solusi yang memperbaiki *developer workflow*
tertentu (onboarding, debugging, code review, testing, maintenance, atau
release/deployment) dan menjadikan **Bob IDE komponen inti** — bukan
sekadar alat bantu ngoding sekilas. LegacyLens menjawab dua workflow
sekaligus dari daftar resmi: **maintenance aplikasi** (memahami & memodernisasi
kode legacy) dan **debugging** (menelusuri bug lintas file).

## 1.3 Masalah

Dua masalah developer yang sebenarnya satu akar: **kurangnya konteks penuh
atas codebase.**

1. **Kode legacy menakutkan.** Developer mewarisi modul lama tanpa
   dokumentasi, tidak berani mengubahnya karena tidak tahu efek sampingnya,
   dan akhirnya modernisasi ditunda terus-menerus.
2. **Bug lintas file susah dilacak manual.** Ketika penyebab error menyebar
   di beberapa file yang saling memanggil, developer harus membuka satu per
   satu secara manual dan menyusun rantai sebab-akibat sendiri di kepala.

Keduanya butuh hal yang sama: seseorang (atau sesuatu) yang bisa membaca
**seluruh repo sekaligus**, bukan satu file dalam isolasi — dan itu persis
yang menjadi keunggulan utama Bob 2.0 (full repository context + subagents
paralel + document understanding).

## 1.4 Solusi

Dua mode kerja Bob (`Modernization Advisor` dan `Debug Tracer`, lihat
`docs/3_PROMPTS.md`) yang masing-masing menghasilkan laporan terstruktur:

- **Modernization Report** — penjelasan modul dalam bahasa manusia, peta
  area berisiko tinggi (dengan lokasi kode persis), dan rencana modernisasi
  bertahap.
- **Debug Trace Report** — rantai sebab-akibat lintas file dari sebuah
  error, penjelasan root cause, dan usulan perbaikan (sebagai diff, tidak
  langsung diterapkan).

Laporan-laporan ini ditampilkan lewat **LegacyLens Viewer**, dashboard
Next.js ringan yang membaca file laporan langsung dari repo — tanpa
backend, tanpa database. Perannya murni presentasi, supaya juri bisa
melihat hasil kerja Bob dalam hitungan detik, bukan menggali task session
Bob IDE satu per satu.

## 1.5 Kenapa Ini "Showcase Bob IDE sebagai Core Component"

| Syarat hackathon | Cara LegacyLens memenuhinya |
|---|---|
| Bob IDE komponen inti | Seluruh analisis (bukan sekadar penulisan kode) dilakukan oleh Bob lewat Custom Modes + Skills — bukan LLM lain yang dipanggil dari aplikasi |
| Agent mode | Kedua mode berjalan dalam Agent mode, menulis file laporan secara otonom |
| Parallel tasks & subagents | Modul/file yang dianalisis dipecah ke subagent `explore` paralel, bukan dibaca berurutan |
| Document understanding | Bob membaca lintas file untuk menyusun konteks penuh sebelum menyimpulkan risiko/root cause |
| Bukti pemakaian | Screenshot task session summary wajib diunggah ke `bob_sessions/` (lihat `10_TIMELINE.md`) |

## 1.6 Target Pengguna

* Developer yang baru bergabung ke tim dan mewarisi codebase lama.
* Tim kecil/startup tanpa waktu khusus untuk dokumentasi teknis.
* Siapa pun yang menghadapi bug lintas file dan butuh titik awal investigasi cepat.

## 1.7 In-Scope (48 Jam)

* [x] `.bob/custom_modes.yaml` — 2 custom mode (Modernization Advisor, Debug Tracer)
* [x] `.bob/skills/` — 2 skill dengan template output terstruktur
* [x] Demo live: jalankan kedua mode terhadap repo contoh (lihat `7_SEED_DATA.md`)
* [x] LegacyLens Viewer (Next.js) — daftar laporan + halaman detail per laporan
* [x] Minimal 2 Modernization Report dan 2 Debug Trace Report nyata (hasil Bob, bukan ditulis manual) sebagai isi demo
* [x] Folder `bob_sessions/` berisi screenshot task session summary

## 1.8 Out-of-Scope (48 Jam)

* ❌ Backend API / database — sengaja dihilangkan, lihat `2_ARCHITECTURE.md`
* ❌ Autentikasi & multi-user
* ❌ Menjalankan fix otomatis ke source code (usulan perbaikan tetap manual-approve)
* ❌ Mendukung bahasa pemrograman di luar yang dipakai repo contoh
* ❌ Integrasi watsonx Orchestrate/watsonx.ai — dicatat sebagai roadmap opsional, bukan wajib

## 1.9 Definisi Sukses (untuk demo)

1. Juri melihat Bob IDE benar-benar menjalankan analisis secara live (bukan hasil yang sudah disiapkan dari awal) — minimal satu demo live per workflow.
2. Laporan yang dihasilkan bisa dibaca dan masuk akal oleh orang yang tidak familiar dengan repo contohnya.
3. Viewer menampilkan laporan dengan rapi tanpa perlu penjelasan tambahan dari tim.
4. `bob_sessions/` terisi lengkap sebelum submission ditutup.

## 1.10 Risiko & Mitigasi

| Risiko | Mitigasi |
|---|---|
| Bobcoin habis sebelum demo selesai | Batasi cakupan analisis live ke 1-2 modul kecil saat demo; jalankan eksplorasi luas di luar waktu presentasi |
| Bob menghasilkan laporan di luar format skill | Skill sudah eksplisit "JANGAN menyimpang dari template" + Viewer perlu fallback tampilan mentah kalau parsing gagal (lihat `8_FRONTEND_STATE.md`) |
| Demo live gagal (network/Bob down) | Laporan hasil analisis sebelumnya tetap tersimpan di `reports/` — Viewer tetap bisa didemokan dari data yang sudah ada |
