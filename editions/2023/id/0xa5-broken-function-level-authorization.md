# API5:2023 Otentikasi Rusak Tingkat Fungsi

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Umum** : Keterdeteksian **Mudah** | Teknis **Sangat Parah** : Spesifik Bisnis |
| Eksploitasi memerlukan penyerang mengirim pemanggilan API sah ke suatu titik akhir API yang mestinya mereka tidak punya akses sebagai seorang pengguna anonim atau pengguna biasa tanpa hak khusus. Titik akhir yang terekspos akan dapat dieksploitasi dengan mudah. | Pemeriksaan otorisasi bagi suatu fungsi atau sumber daya biasanya dikelola melalui konfigurasi atau tingkat kode. Mengimplementasi pemeriksaan yang sesuai bisa menjadi suatu tugas yang membingungkan karena aplikasi modern bisa mengandung banyak tipe peran, grup, dan hirarki pengguna yang rumit (mis. sub pengguna, atau pengguna dengan lebih dari satu peran). Lebih mudah menemukan cacat ini dalam API karena API lebih terstruktur, dan mengakses fungsi-fungsi yang berbeda lebih dapat ditebak. | Cacat seperti itu memungkinkan penyerang mengakses fungsionalitas tanpa otorisasi. Fungsi-fungsi administratif adalah target kunci bagi tipe serangan ini dan mungkin mengarah ke pengungkapan data, kehilangan data, atau korupsi data. Pada akhirnya, itu mungkin mengarah ke disrupsi layanan. |

## Apakah API Rentan?

Cara terbaik untuk menemukan masalah-masalah otorisasi tingkat fungsi yang
rusak adalah dengan melakukan analisis mendalam atas mekanisme otorisasi
sambil mengingat tentang hirarki pengguna, peran atau grup yang berbeda dalam
aplikasi, dan menanyakan pertanyaan-pertanyaan berikut:

* Dapatkah seorang pengguna biasa mengakses titik akhir administratif?
* Dapatkan seorang pengguna melakukan aksi sensitif (mis. penciptaan,
  pengubahan, atau penghapusan) yang mestinya mereka tidak punya akses
  sekadar dengan mengubah metode HTTP (mis. dari `GET` ke `DELETE`)?
* Dapatkan seorang pengguna dari grup X mengakses suatu fungsi yang
  mestinya hanya terpapar ke pengguna dari grup Y, sekadar dengan 
  menebak URL dan parameter titik akhir (mis `/api/v1/users/export_all`)?

Jangan mengasumsikan bahwa suatu titik akhir API adalah biasa atau hanya 
administratif berbasis kepada path URL.

Walaupun pengembang mungkin memilih untuk mengekspos kebanyakan dari titik
akhir administratif di bawah path relatif spesifik, seperti `/api/admins`,
sangat umum untuk menemukan titik-titik akhir administratif ini di bahwa
path relatif lain bersama dengan titik-titik akhir reguler, seperti
`/api/users`.

## Contoh Skenario Serangan

### Skenario #1

Selama proses pendaftaran untuk suatu aplikasi yang hanya mengizinkan
pengguna yang diundang untuk bergabung, aplikasi mobile memicu suatu 
pemanggilan API ke `GET /api/invites/{invite_guid}`. Responsnya memuat
suatu JSON dengan rincian tentang undangan, termasuk peran pengguna dan
surel pengguna.

Seorang penyerang menduplikasi permintaan dan memanipulasi metode HTTP
dan titik akhir ke `POST /api/invites/new`. Titik akhir ini mestinya
hanya diakses oleh administrator memakai konsol admin. Titik akhir tidak
mengimplementasikan pemeriksaan otorisasi tingkat fungsi.

Penyerang mengeksploitasi masalah tesebut dan mengirim suatu undangan
baru dengan privilese admin:

```
POST /api/invites/new

{
  "email": "attacker@somehost.com",
  "role":"admin"
}
```

Belakangan, penyerang memakai undangan yang disusun secara jahat agar
membuat sendiri sebuah akun admin dan memperoleh akses penuh ke sistem.

### Skenario #2

Suatu API memuat sebuah titik akhir yang mestinya terungkap hanya ke 
administrator - `GET /api/admin/v1/users/all`. Titik akhir ini mengembalikan
rincian dari semua pengguna aplikasi dan tidak mengimplementasikan pemeriksaan
otorisasi tingkat fungsi. Seorang penyerang yang mempelajari struktur API
tersebut membuat suatu tebakan terarah dan berhasil mengakses titik akhir ini,
yang mengungkap rincian sensitif dari para pengguna aplikasi.

## Bagaimana Cara Mencegah

Aplikasi Anda mesti punya suatu modul otorisasi yang konsisten dan mudah
dianalisis yang dipanggil dari semua fungsi bisnis Anda. Secara sering,
proteksi seperti itu disediakan oleh satu atau lebih komponen yang eksternal
ke kode aplikasi.

* Mekanisme penegakan mesti menolak semua akses secara baku, mempersyaratkan
  pemberian izin secara eksplisit ke peran spesifik untuk akses ke setiap
  fungsi.
* Tinjau titik akhir API terhadap cacat otorisasi tingkat fungsi, sambil
  mengingat logika bisnis aplikasi dan hirarki grup.
* Pastikan bahwa semua pengendali administratif Anda mewarisi dari suatu
  pengendali abstrak administratif yang mengimplementasikan pemeriksaan
  otorisasi berbasis pada peran/grup pengguna.
* Pastikan bahwa fungsi-fungsi administratif di dalam suatu pengendali
  reguler mengimplementasikan pemeriksaan otorisasi berdasarkan pada peran
  dan grup pengguna.

## Referensi

### OWASP

* [Forced Browsing][1]
* "A7: Missing Function Level Access Control", [OWASP Top 10 2013][2]
* [Access Control][3]

### Eksternal

* [CWE-285: Improper Authorization][4]

[1]: https://owasp.org/www-community/attacks/Forced_browsing
[2]: https://github.com/OWASP/Top10/raw/master/2013/OWASP%20Top%2010%20-%202013.pdf
[3]: https://owasp.org/www-community/Access_Control
[4]: https://cwe.mitre.org/data/definitions/285.html
