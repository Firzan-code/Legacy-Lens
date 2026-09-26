# 9. Demo Script

Asumsi: 5 menit presentasi + 2-3 menit tanya-jawab. Sesuaikan dengan slot panitia.

## 9.1 Struktur Pitch

### (0:00–0:30) Masalah
> "Dua hal yang selalu bikin developer takut: kode lama yang tidak ada yang
> berani sentuh, dan bug yang penyebabnya nyebar di banyak file. Keduanya
> butuh satu hal yang sama — seseorang yang bisa baca seluruh repo
> sekaligus, bukan satu file dalam isolasi."

### (0:30–1:00) Solusi dalam Satu Kalimat
> "LegacyLens memakai Bob sebagai mesinnya langsung — dua mode kerja khusus
> yang membaca konteks penuh repo lewat subagent paralel, lalu menghasilkan
> laporan yang bisa langsung dipakai developer lain."

Tunjukkan diagram alur (`2_ARCHITECTURE.md` §2.2) — satu gambar saja.

### (1:00–3:30) Live Demo — INI BAGIAN PALING PENTING

Berbeda dari proyek lama, demo di sini **harus menunjukkan Bob IDE
sungguhan berjalan**, bukan cuma UI hasil akhir — itu yang membuktikan Bob
adalah komponen inti, bukan sekadar cerita.

1. **Buka Bob IDE**, tunjukkan target modul legacy (dari `7_SEED_DATA.md`).
   Ganti ke mode **Modernization Advisor**, ketik prompt singkat.
   *Sambil menunggu:* "Perhatikan di panel kanan — Bob sedang spawn
   beberapa subagent sekaligus untuk baca tiap file secara paralel,
   bukan satu-satu."
2. **Approve subagent** yang diminta (siapkan mental note: ini titik yang
   paling sering butuh latihan supaya tidak canggung di depan juri).
3. Setelah laporan tertulis ke `reports/modernization/`, **pindah ke
   layar Viewer**, refresh, tunjukkan laporan baru langsung muncul —
   arahkan ke bagian "Bagian Berisiko" yang menyebut lokasi kode persis.
4. **Ulangi alur serupa dengan Debug Tracer** — paste error yang sudah
   disiapkan (lihat `7_SEED_DATA.md` §7.1, bug yang sengaja disisipkan),
   tunjukkan rantai sebab-akibat di laporan, tutup dengan usulan
   perbaikan dalam bentuk diff.

*Kalau waktu terbatas, prioritaskan menunjukkan SATU workflow secara live
penuh (dari Bob IDE sampai Viewer) daripada dua workflow setengah-setengah
— live demo yang meyakinkan lebih penting dari cakupan fitur.*

### (3:30–4:15) Kaitan ke Tema Hackathon

> "Ini bukan chatbot generik yang kebetulan dipakai buat ngoding — dua mode
> ini didesain khusus untuk workflow developer, dibatasi tool-nya (tidak
> bisa ubah source code langsung), dan sengaja pakai subagent paralel biar
> analisis lintas file cepat dan tidak membengkakkan context."

### (4:15–4:45) Batasan (jujur)

> "Formatnya masih baku — dua workflow, dua skill. Roadmap-nya nambah
> lebih banyak skill (code review, test generation) di pola yang sama, dan
> mungkin watsonx Orchestrate untuk menjadwalkan analisis otomatis tiap
> ada PR baru."

### (4:45–5:00) Penutup
Satu kalimat penutup, buka sesi tanya jawab.

## 9.2 Rencana Cadangan

| Risiko | Mitigasi |
|---|---|
| Bobcoin habis sebelum demo | Cadangkan minimal 5-8 coin khusus untuk sesi demo, jangan dipakai eksplorasi bebas di jam-jam terakhir |
| Bob lambat/network venue bermasalah saat live demo | Punya `reports/` yang sudah terisi laporan hasil analisis sebelumnya sebagai cadangan — tunjukkan Viewer dari data itu, jelaskan bahwa ini hasil sesi sebelumnya |
| Subagent spawn butuh approval manual dan bikin demo tersendat | Latihan dulu approval-nya sebelum naik panggung — tahu persis kapan popup approval muncul |
| Format laporan tidak sesuai skema saat demo | Viewer sudah punya `RawFallback` (lihat `8_FRONTEND_STATE.md`) — tunjukkan itu apa adanya sebagai bukti sistem tidak crash, bukan disembunyikan |

## 9.3 Checklist Sebelum Naik Panggung

- [ ] Target demo (`7_SEED_DATA.md`) sudah dites ulang di laptop presenter persis, bukan laptop lain
- [ ] Bug yang disisipkan untuk demo Debug Tracer sudah dipastikan konsisten direproduksi
- [ ] Minimal 2 laporan tersimpan di `reports/` sebagai cadangan kalau live demo gagal
- [ ] Folder `bob_sessions/` sudah berisi screenshot task session summary (syarat submission, lihat `10_TIMELINE.md`)
- [ ] Sisa Bobcoin dicek sebelum naik panggung
