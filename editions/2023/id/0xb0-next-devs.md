# Apa Selanjutnya Untuk Para Pengembang?

Tugas untuk membuat dan memelihara aplikasi yang aman, atau memperbaiki
aplikasi yang ada, bisa sulit. Tidak berbeda halnya dengan API.

Kami percaya bahwa edukasi dan kesadaran adalah faktor-faktor kunci untuk
menulis perangkat lunak yang aman. Semua hal lain yang diperlukan untuk 
mencapai tujuan bergantung pada **menyusun, melaksanakan, dan memakai proses
keamanan yang dapat diulang dan kendali-kendali keamanan standar**.

OWASP menyediakan berbagai sumber daya bebas dan terbuka untuk membantu Anda
menjawab masalah keamanan. Harap kunjungi [halaman Proyek OWASP][1] untuk 
daftar yang komprehensif dari proyek yang tersedia.

| | |
|-|-|
| **Edukasi** | [Application Security Wayfinder][2] mestinya memberikan Anda ide baik tentang proyek apa yang tersedia bagi setiap tahap/fase dari Software Development LifeCycle (SDLC, Siklus Hidup Pengembangan Perangkat Lunak). Untuk pembelajaran/pelatihan dengan praktek Anda dapat memulai dengan [OWASP **crAPI** - **C**ompletely **R**idiculous **API**][3] atau [OWASP Juice Shop][4]: keduanya memiliki API yang disengaja rentan. [OWASP Vulnerable Web Applications Directory Project][5] menyediakan daftar yang dikurasi atas aplikasi yang disengaja rentan: Anda akan menemukan di sana beberapa API rentan lain. Anda juga bisa menghadiri sesi pelatihan [OWASP AppSec Conference][6], atau [bergabung dengan chapter lokal][7]. |
| **Persyaratan Keamanan** | Keamanan mesti menjadi bagian dari setiap proyek dari awal. Ketika menentukan persyaratan, penting untuk mendefinisikan apa arti "aman" bagi proyek itu. OWASP menyarankan Anda memakai [OWASP Application Security Verification Standard (ASVS)][8] sebagai suatu panduan bagi pengaturan persyaratan keamanan. Bila Anda melakukan outsource, pertimbangkan [OWASP Secure Software Contract Annex][9], yang mesti diadaptasi sesuai dengan hukum dan regulasi lokal. |
| **Arsitektur Keamanan** | Keamanan mesti tetap menjadi perhatian selama semua tahap proyek. [OWASP Cheat Sheet Series][10] adalah suatu titik awal yang baik bagi panduan tentang bagaimana merancang keamanan selama fase arsitektur. Di antara yang lain, Anda akan menemukan [REST Security Cheat Sheet][11], [REST Assessment Cheat Sheet][12], maupun  [GraphQL Cheat Sheet][13]. |
| **Kendali Keamanan Standar** | Mengadopsi kendali-kendali keamanan standar mengurangi risiko memperkenalkan kelemahan keamanan saat menulis logika Anda sendiri. Walaupun banyak kerangka kerja modern kini datang dengan kendali-kendali standar bawaan yang efektif, [OWASP Proactive Controls][14] memberi Anda ringkasan yang baik tentang kendali keamanan apa yang mesti Anda lihat untuk disertakan dalam proyek Anda. OWASP juga menyediakan beberapa pustaka dan perkakas yang mungkin berharga bagi Anda, seperti misalnya kendali validasi. |
| **Siklus Hidup Pengembangan Perangkat Lunak yang Aman** | Anda dapat memakai [OWASP Software Assurance Maturity Model (SAMM)][15] untuk memperbaiki proses membangun API Anda. Beberapa proyek OWASP lain tersedia untuk membantu Anda selama fase-fase pengembangan API yang berbeda, mis., [OWASP Code Review Guide][16]. |

[1]: https://owasp.org/projects/
[2]: https://owasp.org/projects/#owasp-projects-the-sdlc-and-the-security-wayfinder
[3]: https://owasp.org/www-project-crapi/
[4]: https://owasp.org/www-project-juice-shop/
[5]: https://owasp.org/www-project-vulnerable-web-applications-directory/
[6]: https://owasp.org/events/
[7]: https://owasp.org/chapters/
[8]: https://owasp.org/www-project-application-security-verification-standard/
[9]: https://owasp.org/www-community/OWASP_Secure_Software_Contract_Annex
[10]: https://cheatsheetseries.owasp.org/
[11]: https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html
[12]: https://cheatsheetseries.owasp.org/cheatsheets/REST_Assessment_Cheat_Sheet.html
[13]: https://cheatsheetseries.owasp.org/cheatsheets/GraphQL_Cheat_Sheet.html
[14]: https://owasp.org/www-project-proactive-controls/
[15]: https://owasp.org/www-project-samm/
[16]: https://owasp.org/www-project-code-review-guide/
