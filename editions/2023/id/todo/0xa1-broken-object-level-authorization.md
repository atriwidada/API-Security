# API1:2023 Otorisasi Rusak Tingkat Obyek

| Agen ancaman/Vektor serangan| Kelemahan Keamanan | Impak |
| - | - | - |
| Spesifik API : Eksploitabilitas **Mudah** | Prevalensi **Menyebar Luas** : Keterdeteksian **Mudah** | Teknis **Sedang** : Spesifik Bisnis |
| Penyerang bisa mengeksploitasi titik akhir API yang rentan atas otorisasi tingkat obyek yang rusak dengan memanipulasi ID dari obyek yang dikirim dalam permintaan. ID obyek bisa berupa apa pun dari bilangan bulat berurutan, UUID, atau string generik. Apa pun tipe datanya, mereka mudah diidentifikasi dalam target permintaan (parameter string kuiri atau path), header permintaan, atau bahkan sebagai bagian dari payload permintaan. | Masalah ini amat sangat umum dalam aplikasi berbasis API karena komponen server biasanya sepenuhnya melacak keadaan klien, dan sebagai pengganti, lebih mengandalkan parameter seperti ID obyek, yang dikirim dari klien, untuk memutuskan obyek mana yang akan diakses. Jawaban server biasanya cukup untuk memahami apakah permintaan sukses. | Akses tanpa otorisasi ke obyek pengguna lain dapat menghasilkan pengungkapan data ke pihak yang tidak berhak, kehilangan data, atau manipulasi data. Di bawah keadaan tertentu, akses tanpa otorisasi ke obyek juga bisa mengarah ke pengambilalihan penuh atas akun. |

## Apakah API Rentan?

Object level authorization is an access control mechanism that is usually
implemented at the code level to validate that a user can only access the
objects that they should have permissions to access.

Every API endpoint that receives an ID of an object, and performs any action
on the object, should implement object-level authorization checks. The checks
should validate that the logged-in user has permissions to perform the
requested action on the requested object.

Failures in this mechanism typically lead to unauthorized information
disclosure, modification, or destruction of all data.

Comparing the user ID of the current session (e.g. by extracting it from the
JWT token) with the vulnerable ID parameter isn't a sufficient solution to
solve Broken Object Level Authorization (BOLA). This approach could address
only a small subset of cases.

In the case of BOLA, it's by design that the user will have access to the
vulnerable API endpoint/function. The violation happens at the object level,
by manipulating the ID. If an attacker manages to access an API
endpoint/function they should not have access to - this is a case of [Broken
Function Level Authorization][5] (BFLA) rather than BOLA.

## Example Attack Scenarios

### Scenario #1

An e-commerce platform for online stores (shops) provides a listing page with
the revenue charts for their hosted shops. Inspecting the browser requests, an
attacker can identify the API endpoints used as a data source for those charts
and their pattern: `/shops/{shopName}/revenue_data.json`. Using another API
endpoint, the attacker can get the list of all hosted shop names. With a
simple script to manipulate the names in the list, replacing `{shopName}` in
the URL, the attacker gains access to the sales data of thousands of e-commerce
stores.

### Scenario #2

An automobile manufacturer has enabled remote control of its vehicles via a
mobile API for communication with the driver's mobile phone. The API enables
the driver to remotely start and stop the engine and lock and unlock the doors.
As part of this flow, the user sends the Vehicle Identification Number (VIN) to
the API.
The API fails to validate that the VIN represents a vehicle that belongs to the
logged in user, which leads to a BOLA vulnerability. An attacker can access
vehicles that don't belong to him.

### Scenario #3

An online document storage service allows users to view, edit, store and delete
their documents. When a user's document is deleted, a GraphQL mutation with the
document ID is sent to the API.

```
POST /graphql
{
  "operationName":"deleteReports",
  "variables":{
    "reportKeys":["<DOCUMENT_ID>"]
  },
  "query":"mutation deleteReports($siteId: ID!, $reportKeys: [String]!) {
    {
      deleteReports(reportKeys: $reportKeys)
    }
  }"
}
```

Since the document with the given ID is deleted without any further permission
checks, a user may be able to delete another user's document.

## How To Prevent

* Implement a proper authorization mechanism that relies on the user policies
  and hierarchy.
* Use the authorization mechanism to check if the logged-in user has access to
  perform the requested action on the record in every function that uses an
  input from the client to access a record in the database.
* Prefer the use of random and unpredictable values as GUIDs for records' IDs.
* Write tests to evaluate the vulnerability of the authorization mechanism. Do
  not deploy changes that make the tests fail.

## References

### OWASP

* [Authorization Cheat Sheet][1]
* [Authorization Testing Automation Cheat Sheet][2]

### External

* [CWE-285: Improper Authorization][3]
* [CWE-639: Authorization Bypass Through User-Controlled Key][4]

[1]: https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html
[2]: https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Testing_Automation_Cheat_Sheet.html
[3]: https://cwe.mitre.org/data/definitions/285.html
[4]: https://cwe.mitre.org/data/definitions/639.html
[5]: ./0xa5-broken-function-level-authorization.md
