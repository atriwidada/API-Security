# Apa Selanjutnya Bagi DevSecOps

Karena pentingnya merreka dalam arsitektur aplikasi modern, membangun API yang
aman itu krusial. Keamanan tidak bisa diabaikan, dan itu mesti menjadi bagian
dari siklus hidup pengembangan keseluruhan. Pemindaian dan uji penetrasi
setiap tahun tidak lagi cukup.

DevSecOps mesti bergabung ke upaya pengembangan, memfasilitasi uji kemanan
terus menerus melitas seluruh siklus hidup pengembangan perangkat lunak. 
Sasaran Anda mesti untuk memajukan pipeline pengembangan dengan otomasi
keamanan, tapi tanpa memengaruhi kecepatan pengembangan.

Bila ragu, tetaplah mendapat informasi, dan mengacu ke [DevSecOps Manifesto][1].

| | |
|-|-|
| **Pahami Model Ancaman** | Prioritas pengujian datang dari suatu model ancaman. Bila Anda tidak memilikinya, pertimbangkan untuk memakai [OWASP Application Security Verification Standard (ASVS)][2], dan [OWASP Testing Guide][3] sebagai suatu masukan. Melibatkan tim pengembangan akan membantu agar membuat mereka lebih sadar keamanan. |
| **Pahami SDLC** | Bergabunglah dengan tim pengembangan untuk memahami secara lebih baik Software Development Life Cycle. Kontribusi Anda pada uji keamanan terus-menerus mesti kompatibel dengan orang, proses, dan peralatan. Setiap orang mesti setuju dengan prosesnya, sehingga tidak ada gesekan atau perlawanan yang tidak perlu. |
| **Strategi Pengujian** | Karena karya Anda tidak boleh memengaruhi kecepatan pengembangan, Anda mesti secara bijak memilih teknik (sederhana, tercepat, lebih akurat) terbaik untuk memverifikasi persyaratan keamanan. [OWASP Security Knowledge Framework][4] dan [OWASP Application Security Verification Standard][2] dapat menjadi sumber yang baik untuk persyaratan keamanan fungsional dan non fungsional. Ada sumber-sumber bagus lain untuk [proyek][5] dan [peralatan][6] serupa dengan yang ditawarkan oleh [komunitas DevSecOps][7]. |
| **Mencapai Cakupan dan Akurasi** | Anda adalah jembatan antara para pengembang dan tim operasi. Untuk mencapai cakupan, Anda tidak harus hanya berfokus pada fungsionalitas, tapi juga orkestrasi. Bekerjalah secara dekat ke tim pengembangan dan operasi dari awal sehingga Anda dapat mengoptimasi waktu dan upaya Anda. Anda mesti mengarah ke suatu keadaan dimana keamanan esensial diverifikasi secara terus-menerus. |
| **Secara Jelas Mengkomunikasikan Temuan** | Kontribusikan nilai dengan lebih sedikit atau tanpa gesekan. Sajikan temuan dalam waktu yang tepat, dengan peralatan yang dipakai oleh tim pengembangan (bukan berkas PDF). Bergabunglah dengan tim pengembangan untuk mengatasi temuan-temuan tersebut. Ambil kesempatan untuk mengedukasi mereka, secara jelas menguraikan kelemahan dan bagaimana itu dapat disalahgunakan, termasuk skenario serangan untuk membuatnya nyata. |

[1]: https://www.devsecops.org/
[2]: https://owasp.org/www-project-application-security-verification-standard/
[3]: https://owasp.org/www-project-web-security-testing-guide/
[4]: https://owasp.org/www-project-security-knowledge-framework/
[5]: http://devsecops.github.io/
[6]: https://github.com/devsecops/awesome-devsecops
[7]: http://devsecops.org
