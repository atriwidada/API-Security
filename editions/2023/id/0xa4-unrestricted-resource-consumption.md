# API4:2023 Konsumsi Sumber Daya Tak Dibatasi

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Rata-rata** | Prevalensi **Menyebar Luas** : Keterdeteksian **Mudah** | Teknis **Sangat Parah** : Spesifik Bisnis |
| Eksploatasi memerlukan permintaan-permintaan API yang sederhana. Beberapa permintaan bersamaan dapat dilakukan dari sebuah komputer lokal atau memakai sumber daya komputasi cloud. Kebanyakan perkakas terotomasi yang tersedia dirancang untuk menyebabkan DoS lewat lalu litasn beban tinggi, berdampak ke laju layanan API. | Umum untuk menemukan API yang tidak membatasi interaksi klien atau konsumsi sumber daya. Permintaan API yang disusun, seperti yang menyertakan parameter yang mengendalikan banyaknya sumber daya yang dikembalikan dan melakukan analisis status/waktu/panjang respons mestinya mengizinkan identifikasi masalah-masalah tersebut. Hal yang sama valid untuk operasi batch. Walaupun agen ancaman tidak punya visibilitas atas impak biaya, ini dapat disimpulkan berdasarkan model bisnis/penetapan harga penyedia layanan (mis. penyedia layanan cloud). | Eksploitasi bisa mengarah ke DoS karena habisnya sumber daya, tapi itu juga bisa mengarah ke kenaikan biaya operasional seperti yang terkait ke infrastruktur karena tuntutan CPU yang lebih tinggi, meningkatnya kebutuhan penyimpanan cloud, dsb. |

## Apakah API Rentan?

Memenuhi permintaan-permintaan API memerlukan sumber daya seperti bandwidth
jaringan, CPU, memori, dan penyimpanan. Kadang memerlukan sumber daya yang
diberikan oleh penyedia layanan melalaui integrasi API, dan dibayar per
permintaan, seperti misalnya mengirim surel/SMS/panggilan telepon, validasi
biometrik, dsb.

Suatu API rentan bila paling tidak satu dari batas berikut tidak ada atau
ditata secara tidak tepat (mis. terlalu rendah/tinggi):

* Tenggat waktu eksekusi
* Maksimum memori yang dapat dialokasikan
* Cacah maksimum deskriptor berkas
* Cacah maksimum proses
* Ukuran berkas unggah maksimum
* Banyaknya operasi yang akan dilaksanakan dalam satu permintaan klien (mis.
  batch GraphQL)
* Banyaknya record per halaman yang akan dikembalikan dalam satu permintaan-
  respons
* Batas pengeluaran ke layanan pihak ketiga

## Contoh Skenario Serangan

### Skenario #1

Suatu jejaring sosial mengimplementasikan suatu alur "lupa kata sandi"
memakai verifikasi SMS, memungkinkan pengguna menerima token sekali pakai 
melalui SMS untuk me-reset kata sandi mereka.

Sekali seorang pengguna mengklik "lupa kata sandi", suatu pemanggilan API
dikirim dari peramban pengguna ke API back-end:

```
POST /initiate_forgot_password

{
  "step": 1,
  "user_number": "6501113434"
}
```

Lalu, di belakang layar, suatu pemanggilan API dikirim dari back-end ke 
API pihak ketiga yang mengurusi pengiriman SMS:

```
POST /sms/send_reset_pass_code

Host: willyo.net

{
  "phone_number": "6501113434"
}
```

Penyedia pihak ketiga, Willyo, mengutip $0,05 per pemanggilan tipe ini.

Seorang penyerang menulis sebuah skrip yang mengirim pemanggilan API pertama
puluhan ribu kali. Back-end mengikuti dan meminta Willyo untuk mengirim
puluhan ribu pesan teks, membuat perusahaan kehilahan ribuan dolar hanya
dalam waktu beberapa menit.

### Skenario #2

Suatu Titik Akhir API GraphQL mengizinkan pengguna mengunggah sebuah
gambar profil.

```
POST /graphql

{
  "query": "mutation {
    uploadPic(name: \"pic1\", base64_pic: \"R0FOIEFOR0xJVA…\") {
      url
    }
  }"
}
```

Sekali pengunggahan komplit, API membangkitkan beberapa gambar mini dengan 
ukuran-ukuran yang berbeda berbasis gambar yang diunggah. Operasi grafis ini
memerlukan banyak memori dari server.

API tersebut mengimplementasikan suatu proteksi pembatasan laju tradisional -
seorang pengguna tidak dapat mengakses titik akhir GraphQL terlalu sering dalam
jangka waktu pendek. API juga memeriksa ukuran gambar yang diunggah sebelum 
membuat gambar mini untuk menghindari memroses gambar yang terlalu besar.

Seorang penyerang dapat dengan mudah melewati mekanisme-mekanisme tersebut,
dengan memanfaatkan sifat alami GraphQL yang fleksibel:

```
POST /graphql

[
  {"query": "mutation {uploadPic(name: \"pic1\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
  {"query": "mutation {uploadPic(name: \"pic2\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
  ...
  {"query": "mutation {uploadPic(name: \"pic999\", base64_pic: \"R0FOIEFOR0xJVA…\") {url}}"},
}
```

Karena API tidak membatasi berapa kali operasi `uploadPic` dapat dicoba, 
pemanggilan akan mengarah ke habisnya memori server dan Penyangkalan Layanan.

### Skenario #3

Suatu penyedia layanan mengizinkan klien mengunduh sebarang berkas besar 
memakai API-nya. Berkas-berkas ini diletakkan di penyimpanan obyek cloud dan
mereka tidak sering mengubah itu. Penyedia layanan mengandalkan layanan 
singgahan (cache) agar mempunyai tingkat layanan yang lebih baik dan untuk
menjaga konsumsi bandwidth rendah. Layanan singgahan hanya menyinggahkan
berkas-berkas sampai dengan 15GB.

Ketika satu dari berkas tersebut diperbarui, ukurannya naik menjadi 18GB.
Semua klien layanan seketika mulai mengambil versi baru. Karena tidak ada
peringatan biaya konsumsi, maupun anggaran biaya maksimum bagi layanan cloud, 
tagihan bulanan berikutnya naik dari US$13, secara rata-rata, menjadi US$8k.

## Bagaimana Cara Mencegah

* Gunakan suatu solusi yang membuat mudah membatasi [memori][1], [CPU][2], 
  [berapa banyak restart][3], [eskriptor berkas, dan proses][4] seperti
  misalnya Container / Serverless code (mis. Lambda).
* Definisikan dan tegakkan suatu ukuran maksimum data pada semua parameter
  dan payload masuk, seperti misalnya panjang maksimum bagi string, cacah
  maksimum elemen dalam larik, dan ukuran berkas unggah maksimum (tanpa
  peduli apakah itu disimpan secara lokal atau dalam penyimpanan cloud).
* Implementasikan suatu batas tentang seberapa sering suatu klien dapat
  berinteraksi dengan API dalam rentang waktu yang didefinisikan (pembatasan
  laju).
* Pembatasan lajut mesti disetel berdasarkan pada kebutuhan bisnis. Beberapa
  Titik Akhir API mungkin memerlukan kebijakan yang lebih ketat.
* Batasi/rem berapa kali atau seberapa sering suatu klien/pengguna API dapat
  mengeksekusi sebuah operasi (mis. memvalidasi suatu OTP, atau meminta 
  pemulihan kata sandi tanpa mengunjungi URL sekali pakai).
* Tambahkan validasi di sisi server yang sesuai untuk parameter body
  permintaan dan string kuiri, secara khusus yang mengendalikan banyaknya
  record yang akan dikembalikan dalam respons.
* Konfigurasikan batas pengeluaran bagi semua penyedia layanan/integrasi API.
  Ketika menata batas pengeluaran tidak mungkin, peringatan tagihan mesti
  dikonfigurasi sebagai pengganti.

## Referensi

### OWASP

* ["Availability" - Web Service Security Cheat Sheet][5]
* ["DoS Prevention" - GraphQL Cheat Sheet][6]
* ["Mitigating Batching Attacks" - GraphQL Cheat Sheet][7]

### Eksternal

* [CWE-770: Allocation of Resources Without Limits or Throttling][8]
* [CWE-400: Uncontrolled Resource Consumption][9]
* [CWE-799: Improper Control of Interaction Frequency][10]
* "Rate Limiting (Throttling)" - [Security Strategies for Microservices-based
  Application Systems][11], NIST

[1]: https://docs.docker.com/config/containers/resource_constraints/#memory
[2]: https://docs.docker.com/config/containers/resource_constraints/#cpu
[3]: https://docs.docker.com/engine/reference/commandline/run/#restart
[4]: https://docs.docker.com/engine/reference/commandline/run/#ulimit
[5]: https://cheatsheetseries.owasp.org/cheatsheets/Web_Service_Security_Cheat_Sheet.html#availability
[6]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html#dos-prevention
[7]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html#mitigating-batching-attacks
[8]: https://cwe.mitre.org/data/definitions/770.html
[9]: https://cwe.mitre.org/data/definitions/400.html
[10]: https://cwe.mitre.org/data/definitions/799.html
[11]: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-204.pdf
