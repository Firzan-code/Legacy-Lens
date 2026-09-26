# 8. Frontend State — LegacyLens Viewer

## 8.1 State Machine

Karena tidak ada backend, tidak ada state `loading` dalam arti "menunggu
network" — pembacaan file terjadi di server saat request halaman (Server
Component), sehingga dari sisi browser hasilnya sudah jadi saat halaman
dikirim. State yang perlu ditangani justru soal **kualitas data**, bukan
soal network:

```ts
type ReportListState =
  | { status: "empty" }                          // folder reports/ kosong
  | { status: "ok"; reports: ReportSummary[] }    // ada laporan valid
  | { status: "partial"; reports: ReportSummary[]; invalidCount: number };
  // ada laporan, tapi sebagian gagal parsing — tetap tampil dengan badge "Tidak diketahui"

type ReportDetailState =
  | { status: "ok"; report: ParsedReport }
  | { status: "invalid"; rawContent: string }     // front matter gagal validasi → RawFallback
  | { status: "not_found" };
```

## 8.2 Pemetaan State → UI

| State | Tampilan |
|---|---|
| `empty` | Pesan ramah + instruksi menjalankan mode Bob (lihat `6_UI_UX_SPEC.md` §6.5) |
| `ok` | Grid `ReportCard` normal |
| `partial` | Grid `ReportCard` normal, ditambah catatan kecil di atas: "`{invalidCount}` laporan tidak bisa ditampilkan penuh" — supaya tim sadar ada yang perlu diperbaiki, tanpa menghentikan demo |
| detail `ok` | Render penuh sesuai `6_UI_UX_SPEC.md` |
| detail `invalid` | `RawFallback` — tampilkan isi file apa adanya dalam blok monospace, dengan catatan "Format laporan ini tidak sesuai skema, ditampilkan mentah" |
| detail `not_found` | Halaman 404 standar Next.js, dengan link kembali ke `/` |

## 8.3 Kapan Data Dibaca

Server Component Next.js membaca `reports/` setiap kali halaman diakses
(tidak di-cache secara default dalam mode development) — artinya laporan
baru yang ditulis Bob **langsung muncul** saat halaman di-refresh, tanpa
perlu restart server. Ini penting untuk demo: developer bisa menjalankan
analisis Bob secara live, lalu langsung refresh Viewer di layar sebelah
untuk menunjukkan hasilnya muncul.

**Catatan production:** kalau di-deploy ke Vercel dengan build statis,
perilaku ini berubah (file di-bundle saat build, tidak baca ulang saat
runtime). Untuk kebutuhan demo lokal ini bukan masalah — kalau nanti mau
deploy publik, gunakan `export const dynamic = "force-dynamic"` di halaman
yang membaca `reports/`, atau jadwalkan rebuild setelah laporan baru
ditambahkan.

## 8.4 Transisi yang Perlu Ditangani

* **File laporan baru muncul saat Viewer sedang terbuka.** Tidak perlu
  WebSocket/polling — cukup andalkan refresh manual saat demo (lihat
  §8.3). Jangan bangun real-time sync untuk ini, itu kompleksitas yang
  tidak sepadan untuk kebutuhan demo.
* **Dua laporan dengan nama file sama ditulis ulang** (mis. analisis
  dijalankan dua kali untuk modul yang sama). Bob akan menimpa file lama —
  ini perilaku yang diinginkan (laporan terbaru menang), tidak perlu
  ditangani khusus di Viewer.
* **Tab aktif (`Modernization`/`Debug`) hilang saat navigasi ke detail lalu
  kembali.** Simpan tab aktif di query string (`?tab=debug`) supaya
  kembali ke `/` tidak mereset ke tab default — detail kecil, tapi bikin
  demo terasa lebih halus.

## 8.5 Error Boundary

Kalau `fs.readdirSync`/`fs.readFileSync` melempar error di luar dugaan
(mis. permission issue), Next.js `error.tsx` menangkapnya dan menampilkan
pesan generik + tombol "Coba lagi" — jangan biarkan seluruh app crash
putih polos saat demo.
