# API1:2023 Otorisasi Rusak Tingkat Obyek

| Agen ancaman/Vektor serangan| Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Menyebar Luas** : Keterdeteksian **Mudah** | Teknis **Sedang** : Spesifik Bisnis |
| Penyerang bisa mengeksploitasi titik akhir API yang rentan atas otorisasi tingkat obyek yang rusak dengan memanipulasi ID dari obyek yang dikirim dalam permintaan. ID obyek bisa berupa apa pun dari bilangan bulat berurutan, UUID, atau string generik. Apa pun tipe datanya, mereka mudah diidentifikasi dalam target permintaan (parameter string kuiri atau path), header permintaan, atau bahkan sebagai bagian dari payload permintaan. | Masalah ini amat sangat umum dalam aplikasi berbasis API karena komponen server biasanya sepenuhnya melacak keadaan klien, dan sebagai pengganti, lebih mengandalkan parameter seperti ID obyek, yang dikirim dari klien, untuk memutuskan obyek mana yang akan diakses. Jawaban server biasanya cukup untuk memahami apakah permintaan sukses. | Akses tanpa otorisasi ke obyek pengguna lain dapat menghasilkan pengungkapan data ke pihak yang tidak berhak, kehilangan data, atau manipulasi data. Di bawah keadaan tertentu, akses tanpa otorisasi ke obyek juga bisa mengarah ke pengambilalihan penuh atas akun. |

## Apakah API Rentan?

Otorisasi tingkat obyek adalah suatu mekanisme kendali akses yang biasanya
diimplementasikan pada tingkat kode untuk memvalidasi bahwa seorang pengguna
hanya dapat mengakses obyek-obyek yang mestinya mereka telah punya izin
untuk mengaksesnya.

Setiap titik akhir API yang menerima suatu ID dari sebuah obyek, dan melakukan
sebarang aksi pada obyek, harus mengimplementasikan pemeriksaan otorisasi
tingkat obyek. Pemeriksaan harus memvalidasi bahwa pengguna yang log masuk
punya izin untuk melakukan aksi yang diminta pada obyek yang diminta.

Kegagalan dalam mekanisme ini biasanya mengarah ke pengungkapan informasi
tanpa otorisasi, modifikasi, atau penghancuran semua data.

Membandingkan ID pengguna dari sesi saat ini (mis. dengan mengekstraknya
dari token JWT) dengan parameter ID yang rentan bukanlah solusi yang cukup
untuk memecahkan Otorisasi Rusak Tingkat Obyek (Broken Object Level
Authorization, BOLA). Pendekatan ini dapat menjawab hanya subset kecil dari
kasus.

Dalam kasus BOLA, memang secara disain bahwa pengguna akan punya akses 
ke titik akhir/fungsi API yang rentan. Pelanggaran terjadi pada tingkat
obyek, dengan memanipulasi ID. Bila seorang penyerang dapat mengakses titik
akir/fungsi API yang mestinya mereka tidak punya akses - ini adalah kasus
[Otorisasi Rusak Tingkat Fungsi][5] (Broken Function Level Authorization, 
BFLA), bukan BOLA.

## Contoh Skenario Serangan

### Skenario #1

Suatu platform e-commerce untuk toko daring menyediakan suatu halaman
daftar dengan bagan revenue untuk toko yang mereka wadahi. Dengan memeriksa
permintaan peramban, seorang penyerang bisa mengidentifikasi titik akhir API
yang dipakai sebagai sumber data untuk bagan-bagan tersebut dan pola mereka:
`/shops/{shopName}/revenue_data.json`. Memakai titik akhir API lain,
penyerang bisa mendapatkan daftar dari semua nama toko yang di-host. Dengan
suatu skrip sederhana untuk memanipulasi nama-nama dalam daftar, menggantikan
`{shopName}` dalam URL, penyerang memperoleh akses ke data penjualan dari
ribuan toko e-commerce.

### Skenario #2

Sebuah pabrikan mobil telah memfungsikan kendali remote kendaraannya 
melalui suatu API mobile untuk berkomunikasi dengan telepon genggam 
pengemudinya. API memungkinkan pengemudi memulai dan menghentikan mesin 
secara remote dan mengunci dan membuka kunci pintu. Sebagai bagian dari alur 
ini, pengguna mengirim Nomor Identifikasi Kendaraan (Vehicle Identification 
Number, VIN) ke API. 
API gagal memvalidasi bahwa VIN mewakili suatu kendaraan milik pengguna yang
log masuk, yang membawa ke kerentanan BOLA. Seorang penyerang dapat mengakses
kendaraan yang bukan miliknya.

### Skenario #3

Suatu layanan penyimpanan dokumen daring mengizinkan pengguna untuk melihat,
menyunting, menyimpan, dan menghapus dokumen-dokumen mereka. Ketika sebuah
dokumen milik pengguna dihapus, suatu mutasi GraphQL dengan ID dokumen 
dikirim ke API.

```
POST /graphql
{
  "operationName":"deleteReports",
  "variables":{
    "reportKeys":["<DOCUMENT_ID>"]
  },
  "query":"mutation deleteReports($siteId: ID!, $reportKeys: [String]!) {
    {
      deleteReports(reportKeys: $reportKeys)
    }
  }"
}
```

Karena dokumen dengan ID yang diberikan dihapus tanpa sebarang pemeriksaan
izin lebih jauh, seorang pengguna mungkin bisa menghapus dokumen milik
pengguna lain.

## Bagaimana Cara Mencegah

* Implementasikan suatu mekanisme otorisasi yang mengandalkan pada hirarki
  dan kebijakan pengguna.
* Gunakan mekanisme otorisasi untuk memeriksa apakah pengguna yang log masuk
  punya akses untuk melaksanakan aksi yang diminta pada record dalam setiap
  fungsi yang memakai suatu masukan dari klien untuk mengakse sebuah record
  dalam basis data.
* Lebih disukai penggunaan nilai yang acak dan tidak bisa ditebak sebagai
  GUID bagi ID record.
* Tulis uji untuk mengevaluasi kerentanan dari mekanisme otorisasi. Jangan
  gelar perubahan yang membuat pengujian gagal.

## Referensi

### OWASP

* [Authorization Cheat Sheet][1]
* [Authorization Testing Automation Cheat Sheet][2]

### Eksternal

* [CWE-285: Improper Authorization][3]
* [CWE-639: Authorization Bypass Through User-Controlled Key][4]

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html
[2]: https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Testing_Automation_Cheat_Sheet.html
[3]: https://cwe.mitre.org/data/definitions/285.html
[4]: https://cwe.mitre.org/data/definitions/639.html
[5]: ./0xa5-broken-function-level-authorization.md
