# Metodologi dan Data

## Ringkasan

Untuk memperbarui daftar ini, tim Keamanan API OWASP memakai metodologi yang
sama yang dipakai untuk daftar 2019 yang sukses dan banyak diadopsi, dengan
tambahan 3 bulan [Permintaan Data Publik][1]. Sayangnya, permintaan data ini
tidak menghasilkan data yang mestinya memungkinkan analisis statistik yang
relevan dari masalah-masalah keamanan API yang paling umum.

Namun, dengan suatu industri keamanan API yang lebih dewasa yang mampu
menyediakan umpan balik dan insight langsung, proses pembaruan berlanjut
memakai metodologi yang sama dengan sebelumnya.

Akhirnya sampai di sini, kami percaya memiliki suatu dokumen kesadaran untuk
masa mendatang yang baik untuk tiga atau empat tahun ke depan, lebih berfokus
pada masalah-masalah spesifik API modern. Sasaran dari proyek ini tidak untuk
menggantikan daftar top 10 lain, tapi untuk mencakup risiko-risiko keamanan
API puncak yang ada dan akan ada, yang kami percaya bahwa industri perlu
lebih sadar dan tetap waspada atasnya.

## Metodologi

Dalam fase pertama, data yang tersedia secara publik tentang insiden-
insiden keamanan API dikumpulkan, ditinjau, dan dikategorikan. Data seperti
itu dikumpulkan dari platform-platform bug bounty dan laporan-laporan yang
tersedia secara publik. Hanya masalah-masalah yang dilaporkan antara 2019
dan 2022 yang dipertimbangkan. Data ini dipakai untuk memberi tim suatu
rasa tentang ke arah mana daftar top 10 sebelumnya mesti berkembang maupun
untuk membantu menangani kemungkinan bias data yang dikontribusikan.

Suatu [Permintaan Data][1] publik dijalankan dari 1 September sampai 30
November 2022. Secara paralel, tim proyek memulai diskusi tentang apa yang
telah berubah sejak 2019. Diskusi termasuk impak dari daftar pertama, umpan
balik yang diterima dari komunitas, dan tren-tren baru dari keamanan API.

Tim proyek mempromosikan rapat-rapat dengan para spesialis tengan ancaman-
ancaman kemanan API yang relevan untuk mendapatkan insight ke bagaimana
korban terdampak dan bagaimana ancaman-ancaman itu dapat dimitigasi.

Upaya ini menghasilkan suatu draf awal dari apa yang dipercaya oleh tim
sebagai 10 risiko keamanan API paling kritis. [Metodologi Pemeringkatan Risiko OWASP][2]
dipakai untuk melaksanakan analisis risiko. Peringkat prevalensi diputuskan
dari suatu konsensus antara para anggota proyek, berdasarkan pengalaman
mereka di lapangan. Untuk konsiderasi pada hal-hal ini, harap mengacu ke
bagian [Risiko-risiko Keamanan API][3].

Draf awal kemudian dibagikan untuk ditinjau ke para praktisi keamanan dengan
pengalaman yang relevan dalam bidang keamanan API. Komentar-komentar mereka
ditinjau, didiskusikan, dan ketika dapat diterapkan, disertakan dalam dokumen.
Dokumen hasilnya [dipublikasikan sebagai Kandidat Rilis][4] untuk 
[diskusi terbuka][5]. Beberapa [kontribusi komunitas][6] disertakan ke dalam
dokumen akhir.

Daftar kontributor tersedia dalam bagian [Penghargaan][7].

## Risiko-risiko Spesifik API

Daftar dibangun untuk menjawab risiko-risiko keamanan yang lebih spesifik
ke API.

Ini tidak menyiratkan bahwa risiko-risiko keamanan aplikasi generik lain tidak
ada dalam aplikasi-aplikasi berbasis API. Sebagai contoh, kami tidak
menyertakan risiko-risiko seperti "Komponen yang Rentan dan Kedaluwarsa" atau
"Injeksi", walaupun Anda mungkin menemukan mereka dalam aplikasi-aplikasi
berbasis API. Risiko-risiko ini generik, mereka tidak berperilaku secara
berbeda dalam API, dan eksploitasi mereka juga tidak berbeda.

Sasaran kami adalah untuk meningkatkan kesadaran atas risiko-risiko keamanan
yang layak memperoleh perhatian khusus dalam API.

[1]: https://owasp.org/www-project-api-security/announcements/cfd/2022/
[2]: https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology
[3]: ./0x10-api-security-risks.md
[4]: https://owasp.org/www-project-api-security/announcements/2023/02/api-top10-2023rc
[5]: https://github.com/OWASP/API-Security/issues?q=is%3Aissue+label%3A2023RC
[6]: https://github.com/OWASP/API-Security/pulls?q=is%3Apr+label%3A2023RC
[7]: ./0xd1-acknowledgments.md
