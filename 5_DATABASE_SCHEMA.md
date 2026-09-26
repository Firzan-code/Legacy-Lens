# 5. Report Storage Layout

**Tidak ada database di proyek ini.** File ini dulu berisi skema Supabase —
sekarang perannya diganti oleh struktur folder `reports/`, karena laporan
Bob *sudah* berupa data terstruktur (YAML front matter + Markdown). Ini
dokumen yang menggantikannya, sekaligus mencatat kenapa DB sengaja tidak
dipakai (lihat `2_ARCHITECTURE.md` §2.4).

## 5.1 Struktur

```
reports/
├── modernization/
│   ├── auth-service.md
│   ├── payment-gateway.md
│   └── ...
└── debug/
    ├── login-null-pointer.md
    ├── checkout-race-condition.md
    └── ...
```

## 5.2 Konvensi Penamaan

| Folder | Pola nama file | Contoh |
|---|---|---|
| `reports/modernization/` | `{module-slug}.md` — huruf kecil, spasi jadi `-` | `auth-service.md` |
| `reports/debug/` | `{id-deskriptif}.md` — bebas tapi jelas, BUKAN `bug-1.md` | `login-null-pointer.md` |

Nama file jadi bagian dari URL di Viewer (`/modernization/auth-service`,
`/debug/login-null-pointer`) — nama yang deskriptif membuat link mudah
dibagikan dan dipahami tanpa perlu buka halaman.

## 5.3 "Index" Laporan

Viewer tidak butuh file index terpisah — daftar laporan didapat langsung
dari `fs.readdirSync("reports/modernization")` dan
`fs.readdirSync("reports/debug")` saat halaman diakses. Ini setara dengan
query `SELECT * FROM transactions` di arsitektur lama, tapi tanpa perlu
database sama sekali.

## 5.4 Kenapa File, Bukan Database — dan Kapan Itu Berubah

File Markdown git-tracked punya properti yang kebetulan pas untuk hackathon
ini: **riwayatnya otomatis jadi bukti audit** (siapa membuat laporan apa,
kapan — lewat `git log`), tanpa perlu membangun `audit_logs` seperti di
arsitektur lama. Untuk skala demo (belasan laporan), ini cukup dan bahkan
lebih transparan daripada baris di database yang tersembunyi di belakang
API.

Kalau proyek ini dilanjutkan pasca-hackathon dan butuh: riwayat lintas
banyak repo/tim, pencarian penuh, atau akses multi-user dengan permission —
itu titik wajar untuk menambahkan database sungguhan. Dicatat di
`README.md` §Roadmap, bukan dikerjakan sekarang tanpa kebutuhan konkret.

## 5.5 Validasi Isi (pengganti constraint SQL)

Karena tidak ada `CHECK constraint` seperti di SQL, validasi field
(`overall_risk`, `confidence` harus salah satu dari tiga nilai; field wajib
tidak boleh kosong) dilakukan di sisi Viewer saat membaca file — lihat
`4_API_SPEC.md` §4.4 dan `8_FRONTEND_STATE.md` untuk perilaku saat validasi
gagal.
