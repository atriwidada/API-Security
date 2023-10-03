# API3:2023 Otorisasi Rusak Tingkat Properti Obyek

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Umum** : Keterdeteksian **Mudah** | Teknis **Sedang** : Spesifik Bisnis |
| API cenderung mengungkap titik akhir yang mengembalikan properti semua obyek. Ini pada khususnya valid untuk REST API. Untuk protokol lain seperti GraphQL, itu mungkin memerlukan permintaan yang disiapkan untuk menyatakan properti mana yang mesti dikembalikan. Mengidentifikasi properti-properti tambahan yang dapat dimanipulasi ini memerlukan lebih banyak upaya, tapi ada beberapa perkakas terotomasi yang tersedia untuk membantu tugas ini. | Memeriksa respons API cukup untuk mengidentifikasi informasi sensitif dalam representasi obyek yang dikembalikan. Fuzzing biasanya dipakai untuk mengidentifikasi properti tambahan (yang tersembunyi). Apakah mereka dapat diubah adalah sekadar menyusun suatu permintaan API dan menganalisis responnya. Analisis efek samping mungkin diperlukan bila properti target tidak dikembalikan dalam respons API. | Akses tanpa otorisasi ke properti obyek yang privat/sensitif mungkin menghasilkan pengungkapan data, kehilangan data, atau korupsi data. Dalam beberapa situasi, akses tanpa otorisasi ke properti obyek dapat mengarah ke eskalasi privilese atau pengambil alihan akun parsial/lengkap. |

## Apakah API Rentan?

Ketika mengizinkan seorang pengguna untuk mengakses suatu obyek memakai sebuah
titik akhir API, penting untuk memvalidasi bahwa pengguna tersebut punya akses
ke properti obyek spesifik yang mereka coba akses.

Suatu titik akhir API rentan bila:

* Titik akhir API mengungkap properti dari suatu obyek yang dianggap sensitif
  dan mestinya tidak boleh dibaca oleh pengguna. (sebelumnya dinamai: 
  "[Excessive Data Exposure][1]")
* Titik akhir API mengizinkan seorang pengguna untuk mengubah, menambah,
  dan/atau menghapus nilai dari suati properti obyek yang sensitif
  yang mestinya pengguna tidak bisa mengakses (sebelumnya dinamai: "[Mass
  Assignment][2]")

## Contoh Skenario Serangan

### Skenario #1

Suatu aplikasi kencan mengizinkan seorang pengguna melaporkan pengguna lain
untuk perilaku yang tidak sepantasnya. Sebagai bagian dari alur ini, pengguna
mengklik pada suatu tombol "laporkan", dan pemanggilan API berikut dipicu:

```
POST /graphql
{
  "operationName":"reportUser",
  "variables":{
    "userId": 313,
    "reason":["offensive behavior"]
  },
  "query":"mutation reportUser($userId: ID!, $reason: String!) {
    reportUser(userId: $userId, reason: $reason) {
      status
      message
      reportedUser {
        id
        fullName
        recentLocation
      }
    }
  }"
}
```

Titik akhir API rentan karena itu mengizinkan pengguna yang terotentikasi
untuk memiliki akses ke properti obyek sensitif pengguna (yang dilaporkan),
seperti "fullName" dan "recentLocation" yang mestinya tidak boleh diakses
oleh pengguna lain.

### Skenario #2

Suatu platform marketplace daring, yang menawarkan satu tipe pengguna ("hosts")
untuk menyewakan apartemen mereka ke tipe pengguna lain ("guests"), 
mempersyaratkan host untuk menerima pemesanan yang dibuat oleh seorang guest,
sebelum menagih guest untuk masa tinggalnya.

Sebagai bagian dari alur ini, suatu pemanggilan API dikirim oleh host ke
`POST /api/host/approve_booking` dengan payload sah berikut:

```
{
  "approved": true,
  "comment": "Check-in is after 3pm"
}
```

Host memutar ulang permintaan sah, dan menambahkan payload jahat berikut:

```
{
  "approved": true,
  "comment": "Check-in is after 3pm",
  "total_stay_price": "$1,000,000"
}
```

Titik API rentan karena tidak ada validasi bahwa host mestinya punya akses ke
properti obyek internal - `total_stay_price`, dan guest akan ditagih lebih
dari yang semestinya dia bayar.

### Skenario #3

Suatu jejaring sosial yang berbasis video pendek, menegakkan penyensoran
dan penapisan konten yang restriktif. Bahkan bila suatu video yang diunggah
diblokir, pengguna dapat mengubah deskripsi dari video memakai permintaan
API berikut:

```
PUT /api/video/update_video

{
  "description": "a funny video about cats"
}
```

Seorang pengguna yang frustrasi dapat memutar ulang permintaan sah, dan 
menambahkan payload jahat berikut:

```
{
  "description": "a funny video about cats",
  "blocked": false
}
```

Titip akhir API rentan karena tidak ada validas apakah pengguna mestinya
punya akses ke properti obyek internal - `blocked`, dan pengguna dapat
mengubah nilai dari `true` ke `false` dan membuka kunci konten mereka sendiri
yang diblokir.

## Bagaimana Cara Mencegah

* Ketika mengekspos suatu obyek memakai sebuah titik akhir API, selalu
  pastikan bahwa pengguna memang memiliki akses ke properti obyek yang Anda
  ekspos. 
* Hindari menggunakan metode generik seperti `to_json()` dan `to_string()`. 
  Sebagai pengganti, pilih properti obyek spesifik yang memang Anda ingin 
  kembalikan.
* Bila mungkin, hindari memakai fungsi yang secara otomatis mengikat suatu
  masukan klien ke dalam variabel kode, obyek internal, atau properti obyek
  ("Mass Assignment").
* Izinkan perubahan hanya ke properti obyek yang mestinya diperbarui oleh
  klien.
* Implementasikan suatu mekanisme validasi respon berbasis skema sebagai
  suatu lapisan ekstra keamanan. Sebagai bagian dari mekanisme ini, 
  definisikan dan tegakkan data yang dikembalikan oleh semua metode API.
* Pertahankan struktur data yang dikembalikan ke yang seminimum mungkin,
  sesuai dengan persyaratan bisnis/fungsional bagi titik akhir.

## Referensi

### OWASP

* [API3:2019 Excessive Data Exposure - OWASP API Security Top 10 2019][1]
* [API6:2019 - Mass Assignment - OWASP API Security Top 10 2019][2]
* [Mass Assignment Cheat Sheet][3]

### Eksternal

* [CWE-213: Exposure of Sensitive Information Due to Incompatible Policies][4]
* [CWE-915: Improperly Controlled Modification of Dynamically-Determined Object Attributes][5]

[1]: https://owasp.org/API-Security/editions/2019/en/0xa3-excessive-data-exposure/
[2]: https://owasp.org/API-Security/editions/2019/en/0xa6-mass-assignment/
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html
[4]: https://cwe.mitre.org/data/definitions/213.html
[5]: https://cwe.mitre.org/data/definitions/915.html
