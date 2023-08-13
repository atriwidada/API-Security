# Catatan Rilis

Ini adalah edisi kedua dari OWASP Top 10 Keamanan API, tepat empat tahun
setelah rilis pertamanya. Banyak yang telah berubah dalam kiprah (keamanan)
API. Lalu lintas API meningkat dengan cepat, beberapa protokol API jauh
bertambah daya tariknya, banyak vendor/solusi keamanan API baru bermunculan,
dan, tentu saja, penyerang telah mengembangkan keterampilan dan teknik baru
untuk mengkompromikan API. Sudah waktunya daftar sepuluh risiko keamanan API
yang paling kritis diperbarui.

Dengan industri keamanan API yang lebih matang, untuk pertama kalinya, ada 
[panggilan publik untuk data][1]. Sayangnya, tidak ada data yang
dikontribusikan, tetapi berdasarkan pengalaman tim proyek, tinjauan dari
para spesialis keamanan API yang cermat, dan umpan balik komunitas tentang
kandidat rilis, kami membuat daftar baru ini. Di bagian [Metodologi dan Data][2],
Anda akan menemukan detail selengkapnya tentang bagaimana versi ini dibuat.
Untuk detail selengkapnya tentang risiko keamanan, silakan lihat [bagian Risiko
Keamanan API][3].

OWASP Top 10 Keamanan API 2023 adalah dokumen kesadaran berwawasan ke depan
untuk industri yang bergerak cepat. Itu tidak menggantikan Top 10 lainnya.
Dalam edisi ini:

* Kami telah menggabungkan Paparan Data Berlebihan dan Penugasan Massal yang
  berfokus pada akar masalah umum: kegagalan validasi otorisasi tingkat 
  properti objek.
* Kami lebih memberi penekanan pada konsumsi sumber daya, daripada berfokus
  pada kecepatan mereka habis.
* Kami telah membuat kategori baru "Akses Tak Dibatasi ke Alur Bisnis Sensitif"
  untuk menjawab ancaman-ancaman baru, termasuk sebagian besar ancaman yang
  dapat dimitigasi memakai pembatasan kecepatan.
* Kami menambahkan "Konsumsi API Tanpa Peduli Keselamatan" untuk mengatasi
  sesuatu yang mulai kami lihat: para penyerang telah mulai mencari layanan
  terintegrasi target untuk mengkompromikannya, alih-alih menyerang langsung
  ke API target. Ini adalah waktu yang tepat untuk mulai menciptakan kesadaran
  tentang risiko yang meningkat ini.

API memainkan peran yang semakin penting dalam arsitektur layanan mikro
modern, Aplikasi Halaman Tunggal (Single Page Application, SPA), aplikasi
seluler, IoT, dll. OWASP Top 10 Keamanan API adalah upaya yang diperlukan
untuk menciptakan kesadaran tentang masalah-masalah keamanan API modern.

Pembaruan ini hanya mungkin terjadi berkat upaya besar dari beberapa
sukarelawan, yang tercantum di bagian [Ucapan Terima Kasih][4].

Terima kasih!

[1]: https://owasp.org/www-project-api-security/announcements/cfd/2022/
[2]: ./0xd0-about-data.md
[3]: ./0x10-api-security-risks.md
[4]: ./0xd1-acknowledgments.md
