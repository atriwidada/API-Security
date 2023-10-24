# API6:2023 Akses Tak Dibatasi ke Alur Bisnis Sensitif

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Menyebar Luas** : Keterdeteksian **Rata-rata** | Teknis **Sedang** : Spesifik Bisnis |

| Eksploitasi biasanya melibatkan model bisnis yang didukung oleh API tersebut, menemukan alur-alur bisnis sensitif, dan mengotomasi akses ke alur-alur ini, menyebabkan cedera ke bisnis. | Kurangnya pandangan holistik dari API tersebut untuk mendukung sepenuhnya persyaratan bisnis cenderung berkontribusi ke prevalensi dari masalah ini. Penyerang secara manual mengidentifikasi sumber daya apa (mis. titik akhir) yang terlibat dalam alur kerja target dan bagaimana mereka bekerja bersama. Bila mekanisme mitigasi sudah ditempatkan, penyerang perlu mencari suatu cara untuk melewati mereka. | Secara umum dampak teknis tidak diharapkan. Eksploitasi mungkin mencederai bisnis dalam cara-cara yang berbeda, sebagai contoh: mencegah pengguna sah untuk membeli suatu produk, atau mengarah ke inflasi dalam ekonomi internal dari suatu permainan. |

## Apakah API Rentan?

Ketika menciptakan suatu titik akhir API, penting untuk memahami alur bisnis
mana yang diungkap olehnya. Beberapa alur bisnis lebih sensitif dari pada yang
lain, dalam arti bahwa akses berlebihan ke mereka dapat mencederai bisnis.

Contoh umum dari alur bisnis yang sensitif dan risiko dari akse berlebihan 
yang terkait mereka:

* Membeli suatu alur produk - seorang penyerang dapat membeli semua persediaan
  dari suatu item yang banyak dicari dan menjual ulang dengan harga yang lebih
  tinggi (**scalping**).
* Mencipta suatu alur komentar/posting - seorang penyerang dapat men-spam 
  sistem.
* Membuat suatu pemesanan - seorang penyerang dapat memesan semua slot waktu
  yang tersedia dan mencegah para pengguna lain memakai sistem

Risiko dari akses berlebihan mungkin berubah antar industri dan bisnis. 
Sebagai contoh - pembuatan posting oleh suatu skrip mungkin dianggap sebagai
suatu risiko spam oleh satu jejaring sosial, tapi didorong oleh jejaring
sosial lain.

Suatu Titik Akhir API rentan bila itu mengekspos suatu alur bisnis sensitif,
tanpa dengan baik membatasi akses ke situ.

## Contoh Skenario Serangan

### Skenario #1

Suatu perusahaan teknologi mengumumkan bahwa mereka akan merilis sebuah konsol
permainan baru pada hari Thanksgiving. Produk tersebut mengalami permintaan
sangat tinggi dan persediaan terbatas. Seorang penyerang menulis kode untuk
secara otomatis membeli produk baru dan menyelesaikan transaksi.

Pada hari rilis, penyerang menjalankan kode yang didistribusikan pada beberapa
alamat IP dan lokasi. API tidak mengimplementasikan proteksi yang sesuai dan
mengizinkan penyerang membeli mayoritas persediaan sebelum para pengguna sah
lainnya.

Belakangan, penyerang menjual produk pada platform lain dengan harga yang jauh
lebih tinggi.

### Skenario #2

Sebuah perusahaan penerbangan menawarkan pembelian tiket daring tanpa biaya
pembatalan. Seorang pengguna dengan niat jahat memesan 90% kursi dari suatu
penerbangan yang diinginkan.

Beberapa hari sebelum penerbangan, pengguna jahat tersebut membatalkan semua
tiket sekaligus, yang memaksa perusahaan penerbangan untuk mendiskon harga
tiket agar penerbangan penuh.

Pada titik ini, pengguna membeli satu tiket untuk dirinya sendiri yang jauh
lebih murah daripada yang semula.

### Skenario #3

Suatu app berbagi kendaraan menyediakan suatu program referral - pengguna
dapat mengundang teman-teman mereka dan mengumpulkan kredit bagi setiap teman
yang bergabung ke app. Kredit ini kemudian dapat dipakai sebagai uang tunai
untuk memesan kendaraan.

Seorang penyerang mengeksploitasi alur ini dengan menulis suatu skrip untuk
mengotomasi proses pendaftaran, dengan setiap pengguna baru menambahkan 
kredit ke dompet penyerang.

Penyerang tersebut nanti dapat menikmati tumpangan bebas atau menjual akunnya
dengan kredit yang berlebihan untuk uang tunai.

## Bagaimana Cara Mencegah

Perencanaan mitigasi mesti dilakukan dalam dua lapis:

* Bisnis - identifikasikan alur bisnis yang mungkin mencederai bisnis bila
  mereka dipakai secara berlebihan.
* Rekayasa - pilih mekanisme proteksi yang benar untuk memitigasi risiko-
  risiko bisnis.

  Beberapa dari mekanisme proteksi lebih sederhana sementara yang lain lebih
  sulit diimplementasikan. Metode-metode berikut dipakai untuk memperlambat
  acaman yang terotomasi:

  * Menyidik jari perangkat: menolak layanan ke perangkat klien yang tidak
    diharapkan (mis: peramban headless) cenderung membuat aktor pengancam
    memakai solusi yang lebih canggih, sehingga lebih mahal bagi mereka
  * Deteksi manusia: memakai captcha atau solusi biometrik yang lebih lanjut
    (mis: pola pengetikan)
  * Pola bukan-manusia: analisis alur pengguna untuk mendeteksi pola bukan
    manusia (mis: pengguna mengakses fungsi "tambahkan ke keranjang" atau
    "selesaikan pembelian" dalam waktu kurang dari satu detik)
  * Pertimbangkan memblokir alamat IP dari simpul-simpul keluar Tor dan 
    proksi-proksi yang dikenal luas

  Amankan dan batasi akses ke API yang dikonsumsi secara langsung oleh mesin
  (seperti misalnya API B2B dan pengembang). Mereka cenderung berupa target 
  yang mudah bagi penyerang karena mereka seringkali tidak mengimplementasi
  semua mekanisme proteksi yang diperlukan.

## Referensi

### OWASP

* [OWASP Automated Threats to Web Applications][1]
* [API10:2019 Insufficient Logging & Monitoring][2]

[1]: https://owasp.org/www-project-automated-threats-to-web-applications/
[2]: https://owasp.org/API-Security/editions/2019/en/0xaa-insufficient-logging-monitoring/

