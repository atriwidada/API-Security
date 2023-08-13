# Risiko Keamanan API

[Metodologi Pemeringkatan Risiko OWASP][1] dipakai untuk melakukan analisis
risiko.

Tabel di bawah menyarikan terminologi yang terkait dengan skor risiko.

| Agen Ancaman | Eksploitabilitas | Prevalensi Kelemahan | Keterdeteksian Kelemahan | Impak Teknis | Impak Bisnis |
| :-: | :-: | :-: | :-: | :-: | :-: |
| Spesifik API | Mudah: **3** | Menyebar Luas **3** | Mudah **3** | Parah **3** | Spesifik Bisnis |
| Spesifik API | Rata-rata: **2** | Umum **2** | Rata-rata **2** | Sedang **2** | Spesifik Bisnis |
| Spesifik API | Sulit: **1** | Sulit **1** | Sulit **1** | Minor **1** | Spesifik Bisnis |

**Catatan**: Pendekatan ini tidak memperhitungkan kebolehjadian dari agen
ancaman. Tidak diperhitungkan juga berbagai rincian teknis yang berkaitan
dengan aplikasi Anda. Sebarang dari faktor-faktor ini bisa secara 
signifikan memengaruhi kebolehjadian keseluruhan dari penyerang menemukan
dan mengeksploitasi suatu kerentanan tertentu. Pemeringkatan ini tidak
memerhitungkan impak sesungguhnya pada bisnis Anda. Organisasi Anda mesti
memutuskan seberapa banyak risiko keamanan dari aplikasi dan API yang 
dapat diterima sesuai dengan kultur, industri, dan lingkungan regulasi Anda.
Tujuan dari OWASP Top 10 Keamanan API tidak untuk melakukan analisis risiko
bagi Anda. Karena edisi ini tidak didorong oleh data, prevalensi dihasilkan
dari suatu konsensus di antara para anggota tim.

## Acuan

### OWASP

* [Metodologi Pemeringkatan Risiko OWASP][1]
* [Artikel tentang Pemodelan Ancaman/Risiko][2]

### Eksternal

* [ISO 31000: Risk Management Std][3]
* [ISO 27001: ISMS][4]
* [NIST Cyber Framework (US)][5]
* [ASD Strategic Mitigations (AU)][6]
* [NIST CVSS 3.0][7]
* [Microsoft Threat Modeling Tool][8]

[1]: https://owasp.org/www-project-risk-assessment-framework/
[2]: https://owasp.org/www-community/Threat_Modeling
[3]: https://www.iso.org/iso-31000-risk-management.html
[4]: https://www.iso.org/isoiec-27001-information-security.html
[5]: https://www.nist.gov/cyberframework
[6]: https://www.asd.gov.au/infosec/mitigationstrategies.htm
[7]: https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator
[8]: https://www.microsoft.com/en-us/download/details.aspx?id=49168
