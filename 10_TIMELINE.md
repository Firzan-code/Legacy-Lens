# 10. Timeline — 48 Jam, 2 Orang

## 10.1 Checklist Sebelum Jam Nol

- [ ] Akun Bob IDE hackathon sudah dikonfirmasi (email invite "team member to ibm-hackathon-xxxx"), Bob IDE terinstal di kedua laptop
- [ ] Di Bob IDE Settings → General, pastikan instance yang dipakai adalah yang hackathon-provisioned (`ibm-coding-challenge-xxx`, region us-east) — BUKAN akun pribadi
- [ ] Node.js 18+ terpasang di kedua laptop, versi sama
- [ ] Target demo (`7_SEED_DATA.md`) sudah di-clone dan dicoba jalan di kedua laptop
- [ ] Repo GitHub dibuat, `.bob/`, `AGENTS.md`, `AGENT.md`, `docs/` dari paket ini sudah disalin masuk
- [ ] Folder `bob_sessions/` dan `reports/{modernization,debug}/` sudah dibuat (boleh kosong dulu, `.gitkeep`)

## 10.2 Blok Waktu

### Jam 0–2 — Setup Bersama

- [ ] Salin `.bob/custom_modes.yaml`, `.bob/skills/`, `AGENTS.md` ke repo — verifikasi kedua mode custom muncul di Bob IDE
- [ ] Sepakati kontrak data di `4_API_SPEC.md` berdua sebelum siapa pun menulis kode Viewer
- [ ] Tentukan target demo final (`7_SEED_DATA.md`) dan bug yang akan disisipkan untuk Debug Tracer

### Jam 2–10 — Kerja Paralel

| Anggota 1 (Bob/Modes) | Anggota 2 (Viewer) |
|---|---|
| Uji coba mode Modernization Advisor terhadap target demo, iterasi instruksi di `customInstructions`/skill sampai hasilnya konsisten | Bangun `frontend/` dari scaffold yang sudah ada, pakai contoh laporan di `7_SEED_DATA.md` §7.2–7.3 sebagai data placeholder |
| Uji coba mode Debug Tracer dengan bug yang sudah disisipkan | Implementasikan `ReportCard`, `RiskBadge` sesuai `6_UI_UX_SPEC.md` |
| Perbaiki skill kalau output menyimpang dari `4_API_SPEC.md` | Implementasikan halaman detail (`/modernization/[slug]`, `/debug/[slug]`) |

**Checkpoint jam 10:** kedua mode menghasilkan laporan yang valid sesuai
skema, DAN Viewer bisa menampilkan laporan placeholder dengan benar.
Setelah ini baru sambungkan Viewer ke laporan asli (tinggal ganti sumber
data, karena kontraknya sudah sama).

### Jam 10–16 — Integrasi & Polish Awal

- [ ] Viewer membaca laporan hasil Bob sungguhan (bukan lagi placeholder)
- [ ] `RawFallback` diuji dengan sengaja merusak satu file laporan
- [ ] Empty state diuji dengan mengosongkan folder `reports/` sementara

### Jam 16–26 — Istirahat Bergantian + Buffer

Jadwalkan eksplisit, jangan diserahkan ke insting.

### Jam 26–36 — Perluas Cakupan Demo

- [ ] Jalankan Modernization Advisor & Debug Tracer terhadap 1-2 modul/bug
  tambahan supaya Viewer tidak kosong melompong saat demo
- [ ] Deploy Viewer (Vercel) — perhatikan catatan `dynamic` di
  `8_FRONTEND_STATE.md` §8.3 kalau reports dibaca saat build

### Jam 36–44 — Freeze Fitur, Siapkan Bukti Submission

- [ ] **Tidak ada fitur baru** setelah titik ini
- [ ] **Ambil screenshot task session summary dari Bob IDE, simpan ke
  `bob_sessions/`** — ini syarat submission eksplisit, bukan opsional
- [ ] Gladi bersih demo minimal 2× dengan stopwatch, termasuk approval subagent
- [ ] Isi placeholder nama tim, tautan repo, dsb di `README.md`

### Jam 44–48 — Submission

- [ ] Cek ulang `bob_sessions/` benar-benar berisi bukti, bukan folder kosong
- [ ] Submission form panitia terisi lengkap sebelum deadline

## 10.3 Sinyal Bahaya

- Jam 10 tapi belum ada satu pun laporan yang lolos validasi skema → hentikan penambahan fitur Viewer, fokus perbaiki skill dulu
- Bobcoin tersisa < 10 sebelum jam 36 → hentikan eksplorasi bebas, sisakan khusus untuk gladi resik & demo
- Belum ada screenshot `bob_sessions/` sampai jam 40 → ini risiko diskualifikasi teknis, prioritaskan di atas polish UI apa pun
