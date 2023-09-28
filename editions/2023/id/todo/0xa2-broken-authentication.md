# API2:2023 Otentikasi yang Rusak

| Agen ancaman/Vektor serangan | Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Umum** : Keterdeteksian **Mudah** | Teknis **Sangat Parah** : Spesifik Bisnis |
| Mekanisme otentikasi adalah target yang mudah bagi penyerang karena itu terungkap ke semua orang. Walaupun mungkin diperlukan ketrampilan teknis yang tingkatnya lebih lanjut untuk mengeksploitasi beberapa masalah otentikasi, perkakas eksploitasi biasanya tersedia. | Miskonsepsi para pengembang perangkat lunak dan petugas keamanan terkait batas-batas otentikasi dan kompleksitas implementasi yang inheren membuat masalah-masalah otentikasi prevalen. Metodologi untuk mendeteksi otentikasi yang rusak tersedia dan mudah dibuat. | Penyerang dapat memperoleh kendali lengkap dari akun pengguna lain dalam sistem, membaca data pribadi mereka, dan melakukan aksi-aksi sensitif atas nama mereka. Sistem tidak dapat membedakan aksi penyerang dari aksi pengguna sah. |

## Apakah API Rentan?

Titik akhir dan alur otentikasi adalah aset yang perlu dilindungi. Sebagai
tambahan, "Lupa kata sandi / reset kata sandi" mesti ditangani dengan cara 
yang sama dengan mekanisme otentikasi.

Suatu API rentan bila itu:

* Mengizinkan credential stuffing dimana penyerang memakai brute force
  dengan suatu daftar nama pengguna yang valid dan kata sandi.
* Mengizinkan penyerang melakukan suatu serangan brute force pada akun 
  pengguna yang sama tanpa menyajikan captcha/mekanisme penguncian akun.
* Mengizinkan kata sandi yang lemah.
* Mengirim rincian otentikasi sensitif, seperti misalnya token auth dan kata
  sandi dalam URL.
* Mengizinkan pengguna mengubah alamat surel, kata sandi saat ini, atau
  melakukan sebarang operasi sensitif lain tanpa meminta konfirmasi kata
  sandi.
* Tidak memvalidasi otentisitas token.
* Menerima token JWT yang tidak ditandatangani/ditandatanganis secara lemah
  (`{"alg":"none"}`)
* Tidak memvalidasi tanggal kedaluwarsa JWT.
* Memakai kata sandi teks polos, tidak terenkripsi, atau yang di-hash secara
  lemah. 
* Memakai kunci enkripsi yang lemah.

Di atas itu, suatu layanan mikro rentan bila:

* Layanan mikro lain dapat mengaksesnya tanpa otentikasi
* Memakai token yang lemah atau dapat ditebak untuk memaksakan otentikasi

## Contoh Skenario Serangan

## Skenario #1

Untuk melaksanakan otentikasi pengguna, klien harus membuat suatu permintaan
API seperti yang di bawah dengan kredensial pengguna:

```
POST /graphql
{
  "query":"mutation {
    login (username:\"<username>\",password:\"<password>\") {
      token
    }
   }"
}
```

Bila kredensial valid, makai suatu token auth dikembalikan, yang mesti
dipakai dalam permintaan-permintaan selanjutnya untuk mengidentifikasi
pengguna. Percobaan log masuk dikenai pembatasan laju yang restriktif:
hanya tiga permintaan yang diizinkan per menit.

Untuk melakukan brute force log masuk dengan suatu akun korban, aktor jahat
memanfaatkan batch kuiri GraphQL untuk melewati pembatasan laju permintaan,
mempercepat serangan:

```
POST /graphql
[
  {"query":"mutation{login(username:\"victim\",password:\"password\"){token}}"},
  {"query":"mutation{login(username:\"victim\",password:\"123456\"){token}}"},
  {"query":"mutation{login(username:\"victim\",password:\"qwerty\"){token}}"},
  ...
  {"query":"mutation{login(username:\"victim\",password:\"123\"){token}}"},
]
```

## Skenario #2

Untuk memperbarui alamat surel yang dikaitkan dengan suatu akun pengguna,
klien mesti membuat permintaan API seperti di bawah:

```
PUT /account
Authorization: Bearer <token>

{ "email": "<new_email_address>" }
```

Karena API tidak menuntut pengguna mengonfirmasi mengonfirmasi indentitas
mereka dengan memberikan kata sandi saat ini, aktor jahat yang dapat 
menempatkan diri mereka pada posisi untuk mencuri token auth mungkin dapat
mengambil alih akun korban dengan memulai alur kerja reset kata sandi setelah
memperbarui alamat surel dari akun pengguna.

## Bagaimana Cara Mencegah?

* Make sure you know all the possible flows to authenticate to the API
  (mobile/ web/deep links that implement one-click authentication/etc.). Ask
  your engineers what flows you missed.
* Read about your authentication mechanisms. Make sure you understand what and
  how they are used. OAuth is not authentication, and neither are API keys.
* Don't reinvent the wheel in authentication, token generation, or password
  storage. Use the standards.
* Credential recovery/forgot password endpoints should be treated as login
  endpoints in terms of brute force, rate limiting, and lockout protections.
* Require re-authentication for sensitive operations (e.g. changing the account
  owner email address/2FA phone number).
* Use the [OWASP Authentication Cheatsheet][1].
* Where possible, implement multi-factor authentication.
* Implement anti-brute force mechanisms to mitigate credential stuffing,
  dictionary attacks, and brute force attacks on your authentication endpoints.
  This mechanism should be stricter than the regular rate limiting mechanisms
  on your APIs.
* Implement [account lockout][2]/captcha mechanisms to prevent brute force
  attacks against specific users. Implement weak-password checks.
* API keys should not be used for user authentication. They should only be used
  for [API clients][3] authentication.

## Referensi

### OWASP

* [Authentication Cheat Sheet][1]
* [Key Management Cheat Sheet][4]
* [Credential Stuffing][5]

### Eksternal

* [CWE-204: Observable Response Discrepancy][6]
* [CWE-307: Improper Restriction of Excessive Authentication Attempts][7]

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
[2]: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/03-Testing_for_Weak_Lock_Out_Mechanism(OTG-AUTHN-003)
[3]: https://cloud.google.com/endpoints/docs/openapi/when-why-api-key
[4]: https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html
[5]: https://owasp.org/www-community/attacks/Credential_stuffing
[6]: https://cwe.mitre.org/data/definitions/204.html
[7]: https://cwe.mitre.org/data/definitions/307.html
