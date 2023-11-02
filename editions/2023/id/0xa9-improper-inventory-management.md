# API9:2023 Manajemen Inventaris yang Tidak Tepat

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Menyebar Luas** : Keterdeteksian **Rata-rata** | Teknis **Sedang** : Spesifik Bisnis |
| Agen ancaman biasanya mendapatkan akses tanpa otorisasi melalui versi API lama atau titik akhir yang dibiarkan berjalan tanpa di-patch dan memakai persyaratan keamanan yang lebih lemah. Dalam beberapa kasus, eksploit tersedia. Sebagai alternatif, mereka mungkin mendapatkan akses ke data sensitif melalui pihak ketiga yang mestinya tidak ada alasan untuk berbagi data dengan mereka. | Dokumentasi yang kedaluwarsa membuat jadi lebih sulit untuk mencari dan/atau memperbaiki kerentanan. Ketiadaan inventaris aset dan strategi pensiun membawa ke menjalankan sistem yang tidak di-patch, menyebabkan kebocoran data sensitif. Umum untuk menemukan host API yang secara tidak perlu terekspos karena konsep modern seperti microservice, yang membuat aplikasi mudah digelar dan independen (mis. cloud computing, K8S). Google Dorking sederhana, enumerasi DNS, atau memakai mesin pencari terspesialisasi untuk berbagai tipe server (webcam, router, server, dsb) yang tersambung ke internet akan cukup untuk menemukan target. | Penyerang dapat memperoleh akses ke data sensitif, atau bahkan mengambil alih server. Terkadang versi/penggelaran API yang berbeda tersambung ke basis data yang sama dengan data riil. Agen ancaman mungkin mengeksploitasi titik akhir yang tidak dipakai lagi, yang tersedia dalam versi API lama, untuk memperoleh akses ke fungsi-fungsi administratif atau mengeksplotasi kerentanan yang diketahui. |

## Apakah API Rentan?

Sifat alami API yang menyebar dan terhubung dengan aplikasi modern membawa
tantangan-tantangan baru. Penting bagi organisasi untuk tidak hanya memiliki
pemahaman dan kenampakan yang baik atas API mereka sendiri dan titik-titik
akhir API, tapi juga bagaimana API menyimpan atau berbagi data dengan pihak
ketiga eksternal.

Menjalankan beberapa versi API memerlukan sumber daya manajemen tambahan
dari penyedia API dan memperluas permukaan serangan.

Sebuah API memiliki "<ins>titik buta dokumentasi</ins>" bila:

* Tujuan dari sebuah host API tidak jelas, dan tidak ada jawaban eksplisit
  atas pertanyaan-pertanyaan berikut:
  * API berjalan di dalam lingkungan mana (mis. produksi, staging, uji,
    pengembangan)?
  * Siapa yang mesti punya akses jaringan ke API tersebut (mis. publik,
    internal, partner)? 
  * Versi API mana yang sedang berjalan?
* Tidak ada dokumentasi atau dokumentasi yang ada tidak diperbarui.
* Tidak ada rencana pensiun bagi setiap versi API.
* Inventaris host tidak ada atau kedaluwarsa.

Kenampakan dan inventaris dari aliran data sensitif memainkan peran penting
sebagai bagian dari rencana tanggap insiden, bilamana penerobosan terjadi
di sisi pihak ketiga.

Suatu API memiliki sebuah "<ins>titik buta aliran data</ins>" bila:

* Ada sebuah "aliran data sensitif" dimana API berbagi data sensitif dengan
  pihak ketiga dan
  * Tidak ada suatu persetujuan atau justifikasi bisnis dari aliran
  * Tidak ada inventaris atau kenampakan dari aliran
  * Tidak ada kenampakan mendalam atas tipe data sensitif mana yang dibagikan

## Contoh Skenario Serangan

### Skenario #1

Suatu jejaring sosial mengimplementasikan sebuah mekanisme pembatasan laju
yang memblokir penyerang yang melakukan brute force untuk menebak token reset
kata sandi. Mekanisme ini tidak diimplementasikan sebagai bagian dari kode API
itu sendiri tapi dalam komponen terpisah antara klien dan API resmi 
(`api.socialnetwork.owasp.org`). Seorang peneliti menemukan sebuah host API
beta (`beta.api.socialnetwork.owasp.org`) yang menjalankan API yang sama,
termasuk mekanisme reset kata sandi, tapi mekanisme pembatasan laju tidak
dipasang. Peneliti tersebut berhasil me-reset kata sandi dari sebarang 
pengguna dengan memakai brute force sederhana untuk menebak token 6 digit.

### Skenario #2

Sebuah jejaring sosial mengizinkan para pengembang aplikasi independen 
mengintegrasikan dengannya. Sebagai bagian dari proses ini, suatu persetujuan
diminta dari pengguna akhir, sehingga jejaring sosial dapat berbagi informasi 
pribadi pengguna dengan aplikasi independen.

Aliran data antara jejaring sosial dan aplikasi independen tidak cukup dipantau
atau dibatasi, mengizinkan aplikasi independen untuk mengakses bukan hanya
informasi pengguna tapi juga informasi pribadi dari semua teman mereka.

Suatu firma konsultan membangun sebuah aplikasi jahat dan berhasil mendapatkan
persetujuan dari 270.000 pengguna. Karena cacat tersebut, firma konsultan
berhasil memperoleh akses ke informasi pribadi dari 50.000.000 pengguna. 
Belakangan, firma konsultan menjual informasi tersebut untuk tujuan jahat.

## Bagaimana Cara Mencegah
* Inventarisasikan semua <ins>host API</ins> dan dokumentasikan aspek-aspek
  penting mereka masing-masing, berfokus ke lingkungan API (mis. produksi, 
  staging, uji, pengembangan), siapa mesti punya akses jaringan ke host (mis.
  publik, internal, partner), dan versi API.
* Inventarisasikan <ins>layanan terintegrasi</ins> dan dokumentasikan aspek-
  aspek penting seperti misalnya peran mereka dalam sistem, data apa yang
  dipertukarkan (aliran data), dan sensitivitas mereka. 
* Dokumentasikan semua aspek API Anda seperti otentikasi, kesalahan, 
  redireksi, pembatasan laju, kebijakan cross-origin resource sharing (CORS)
  dan titik-titik akhir, termasuk parameter, permintaan, dan respons mereka.
* Buat dokumentasi secara otomatis dengan mengadopsi standar terbuka. Sertakan
  build dokumentasi dalam pipeline CI/CD Anda.
* Jadikan dokumentasi API hanya tersedia bagi mereka yang punya otorisasi
  untuk memakai API tersebut.
* Gunakan langkah-langkah proteksi eksternal seperti misalnya solusi spesifik
  keamanan API bagi semua versi API Anda yang terekspos, tidak hanya bagi
  versi produksi saat ini.
* Hindari memakai data prodiksi dengan penggelaran API bukan produksi. Bila
  ini tidak bisa dihindari, titik-titik akhir ini mesti mendapatkan perlakuan
  keamanan yang sama dengan produksi.
* Ketika versi lebih baru dari API menyertakan perbaikan keamanan, lakukan
  suatu analisis risiko untuk menginformasikan aksi mitigasi yang diperlukan
  bagi versi-versi lebih lama. Sebagai contoh, apakah mungkin untuk 
  mem-backport perbaikan tanpa merusak kompatibilitas API atau apakah Anda
  perlu mematikan versi yang lebih lama dengan cepat dan memaksa semua klien
  berpindah ke versi terakhir.

## Referensi

### Eksternal

* [CWE-1059: Incomplete Documentation][1]

[1]: https://cwe.mitre.org/data/definitions/1059.html
