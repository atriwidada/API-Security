# API7:2023 Pemalsuan Permintaan Sisi Server

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Umum** : Keterdeteksian **Mudah** | Teknis **Sedang** : Spesifik Bisnis |
| Eksploitasi memerlukan penyerang menemukan suatu titik akhir API yang mengakses sebuah URI yang disediakan oleh klien. Secara umum, SSRF dasar (ketika respons dikembalikan ke penyerang), lebih mudah dieksploitasi daripada SSRF Buta dimana penyerang tidak mendapatkan umpan balik atas apakah serangan berhasil atau tidak. | Konsep modern dalam pengembangan aplikasi mendorong pengembang untuk mengakses URI yang disediakan oleh klien. Ketiadaan atau tidak tepatnya validasi dari URI semacam itu adalah masalah yang umum. Anaisis permintaan dan respons API reguler akan diperlukan untuk mendeteksi masalah. Ketika respons tidak dikembalikan (SSRF Buta) mendeteksi kerentanan memerlukan kreativitas dan upaya lebih. | Eksploitasi yang berhasil dapat mengarah ke enumerasi layanan internal (mis: pemindaian port), pengungkapan informasi, melewati firewall, atau mekanisme keamanan lain. Dalam beberapa kasus, itu dapat mengarah ke DoS atau server dipakai sebagai suatu proksi untuk menyembunyikan aktivitas jahat. |

## Apakah API Rentan?

Cacat Pemalsuan Permintaan Sisi Server (Server-Side Request Forgery/SSRF) 
terjadi ketika suatu API mengambil sebuah sumber daya remote tanpa memvalidasi
URL yang diberikan oleh pengguna. Itu memungkinkan seorang penyerang memaksa
aplikasi untuk mengirim suatu permintaan yang disusun ke sebuah tujuan yang
tidak diharapkan, bahkan ketika dilindungi oleh sebuah firewall atau VPN.

Konsep modern dalam pengembangan aplikasi membuat SSRF lebih umum dan lebih
berbahaya.

Lebih umum - konsep berikut mendorong pengembang untuk mengakses suatu sumber
daya eksternal berdasarkan masukan pengguna: Webhook, mengambil berkas dari
URL, SSO ubahan, dan pratinjau URL.

Lebih berbahaya - teknologi modern seperti penyedia layanan cloud, Kubernetes,
dan Docker mengekspos kanal manajemen dan kendali melalui HTTP pada path yang
dapat ditebak dan dikenal luas. Kanal-kanal itu adalah target yang mudah bagi
serangan SSRF.

Juga lebih menantang untuk membatasi lalu lintas arah keluar dari aplikasi
Anda, karena sifat alami keterhubungan dari aplikasi modern.

Risiko SSRF tidak selalu dapat dieliminasi sepenuhnya. Saat memilih suatu
mekanisme proteksi, penting untuk mempertimbangkan kebutuhan dan risiko
bisnis.

## Contoh Skenario Serangan

### Skenario #1

Suatu jejaring sosial mengizinkan pengguna mengunggah gambar profil. Pengguna
dapat memilih mengunggah berkas citra dari mesin mereka, atau memberikan URL
dari citra. Memilih yang kedua, akan memicu pemanggilan API berikut:

```
POST /api/profile/upload_picture

{
  "picture_url": "http://example.com/profile_pic.jpg"
}
```

Seorang penyerang dapat mengirim suatu URL jahat dan memulai pemindaian port
dalam jaringan internal memakai Titik Akhir API tersebut.

```
{
  "picture_url": "localhost:8080"
}
```

Berdasarkan pada waktu respons, penyerang dapat mencari tahu apakah port
terbuka atau tidak.

### Skenario #2

Suatu produk keamanan membangkitkan kejadian ketika itu mendeteksi anomali
dalam jaringan. Beberapa tim lebih suka meninjau kejadian dalam sistem
pemantauan yang lebih generik, lebih luas, seperti suatu SIEM (Security
Information and Event Management, Manajemen Kejadian dan Informasi Keamanan).
Untuk tujuan ini, produk menyediakan integrasi dengan sistem lain memakai
webhook.

Sebagai bagian dari penciptaan suatu webhook baru, sebuah mutasi GraphQL
dikirim dengan URL dari API SIEM.

```
POST /graphql

[
  {
    "variables": {},
    "query": "mutation {
      createNotificationChannel(input: {
        channelName: \"ch_piney\",
        notificationChannelConfig: {
          customWebhookChannelConfigs: [
            {
              url: \"http://www.siem-system.com/create_new_event\",
              send_test_req: true
            }
          ]
    	  }
  	  }){
    	channelId
  	}
	}"
  }
]

```

Selama proses penciptaan, back end API mengirim suatu permintaan uji ke URL
webhook yang diberikan, dan menyajikan respons ke pengguna.

Seorang penyerang dapat memanfaatkan alur ini, dan membuat API meminta suatu
sumber daya yang sensitif, seperti misalnya sebuah layanan metadata cloud
internal yang mengekspos kredensial:

```
POST /graphql

[
  {
    "variables": {},
    "query": "mutation {
      createNotificationChannel(input: {
        channelName: \"ch_piney\",
        notificationChannelConfig: {
          customWebhookChannelConfigs: [
            {
              url: \"http://169.254.169.254/latest/meta-data/iam/security-credentials/ec2-default-ssm\",
              send_test_req: true
            }
          ]
        }
      }) {
        channelId
      }
    }
  }
]
```

Karena aplikasi menampilkan respons dari permintaan uji, penyerang dapat 
melihat kredensial dari lingkungan cloud.

## Bagaimana Cara Mencegah

* Isolasi mekanisme pengambilan sumber daya dalam jaringan Anda: biasanya
  fitur-fitur ini ditujukan untuk mengambil sumber daya remote dan bukan
  yang internal.
* Bila mungkin, gunakan daftar izin dari:
  * Asal remote dimana pengguna diharapkan mengunduh sumber daya darinya
    (mis. Google Drive, Gravatar, dsb.).
  * Skema dan port URL
  * Tipe media yang diterima untuk sebuah fungsionalitas yang diberikan
* Nonaktifkan redireksi HTTP.
* Gunakan suatu parser URL yang teruji baik dan terpelihara untuk menghindari
  masalah-masalah yang disebabkan oleh tidak konsistennya penguraian URL.
* Validasikan dan sanitasi semua data masukan yang diberikan oleh klien.
* Jangan mengirim respon mentah ke klien.

## Referensi

### OWASP

* [Server Side Request Forgery][1]
* [Server-Side Request Forgery Prevention Cheat Sheet][2]

### External

* [CWE-918: Server-Side Request Forgery (SSRF)][3]
* [URL confusion vulnerabilities in the wild: Exploring parser inconsistencies,
   Snyk][4]

[1]: https://owasp.org/www-community/attacks/Server_Side_Request_Forgery
[2]: https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html
[3]: https://cwe.mitre.org/data/definitions/918.html
[4]: https://snyk.io/blog/url-confusion-vulnerabilities/
