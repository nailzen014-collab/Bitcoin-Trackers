# Rangkuman Belajar: Dokumentasi API, Postman, dan OpenAPI

Materi pendamping untuk Quest Room Pertemuan 20: **How to Create API Documentation in Postman**.

Dokumen ini merangkum apa yang dipraktikkan di repository ini, lalu menjelaskan OpenAPI dari dasar sampai bisa dipakai.

---

## 1. Ringkasan Praktik Pertemuan 20

Yang kita lakukan:

1. Membuat collection Postman bernama **Bitcoin Tracker**.
2. Menambahkan dua request:
   - `GET Get Bitcoin Exchange Rate` ke Coinbase.
   - `POST SMS via Twilio` ke Twilio.
3. Membuat environment **Bitcoin Tracker** berisi `targetRate`, `targetCurrency`, dan variabel Twilio.
4. Menambahkan deskripsi collection, deskripsi request, contoh respons, dan test script.
5. Menguji request harga dengan Postman CLI.
6. Menulis dokumentasi dalam bentuk README.
7. Menambahkan spesifikasi OpenAPI untuk endpoint yang dipakai.
8. Menyiapkan langkah publikasi dokumentasi dan pengumpulan repository.

Inti pertemuan ini bukan sekadar "request bisa jalan", tetapi **request bisa dipahami, diuji, dan dipakai orang lain**.

---

## 2. Apa Itu Dokumentasi API?

Dokumentasi API adalah penjelasan tertulis tentang cara memakai API:

- endpoint apa saja yang tersedia;
- method HTTP dan URL-nya;
- parameter, header, dan body yang dibutuhkan;
- autentikasi yang dipakai;
- bentuk respons sukses dan error;
- contoh request dan respons;
- batasan, rate limit, dan catatan keamanan.

Dokumentasi yang baik menjawab pertanyaan berikut tanpa perlu bertanya ke pembuat API:

> "Saya mau memanggil endpoint ini. Apa yang harus saya kirim, dan apa yang akan saya terima?"

### Kenapa penting?

| Tanpa dokumentasi | Dengan dokumentasi |
| --- | --- |
| Orang harus membaca kode atau bertanya | Orang bisa langsung mencoba |
| Contoh request tidak konsisten | Ada kontrak yang jelas |
| Error membingungkan | Penyebab error terdokumentasi |
| Integrasi lambat | Integrasi lebih cepat dan aman |

---

## 3. Postman Collection vs OpenAPI

Ini dua hal yang sering dicampur. Keduanya sama-sama mendeskripsikan API, tetapi tujuannya berbeda.

| Aspek | Postman Collection | OpenAPI |
| --- | --- | --- |
| Bentuk | Kumpulan request yang bisa dijalankan | Spesifikasi/ kontrak API dalam YAML atau JSON |
| Fokus | Praktik: request, environment, test script, contoh respons | Kontrak: endpoint, schema, parameter, status code |
| Bisa menyimpan script | Ya (pre-request dan test script) | Tidak (hanya dokumentasi) |
| Bisa dijalankan langsung | Ya | Tidak, butuh tool seperti Postman, Swagger UI, atau Redoc |
| Format | JSON (Postman Collection v2.1) | YAML/JSON (OpenAPI 3.0/3.1) |
| Cocok untuk | Eksplorasi, testing, dokumentasi operasional | Kontrak tim, code generation, dokumentasi publik |
| Bisa saling konversi | Bisa import OpenAPI menjadi collection | Bisa dibuat dari collection, tetapi tidak selalu lengkap |

Cara mudah mengingatnya:

- **OpenAPI** = cetak biru API.
- **Postman Collection** = alat kerja untuk mencoba cetak biru itu.

Di repository ini, keduanya tersedia:

- `postman/Bitcoin-Tracker.postman_collection.json` untuk dijalankan di Postman.
- `postman/openapi.yaml` sebagai kontrak API yang bisa dibaca tool lain.

---

## 4. Anatomi Collection yang Kita Buat

Collection Postman berisi:

```text
Bitcoin Tracker
├── 1. Get Bitcoin Exchange Rate
│   ├── request: GET Coinbase
│   ├── test script: validasi respons + simpan currentRate
│   └── contoh respons 200
└── 2. SMS via Twilio
    ├── pre-request script: menyusun body SMS
    ├── request: POST Twilio
    ├── test script: validasi 201 atau 401
    └── contoh respons 201 dan 401
```

### Bagian penting collection

| Bagian | Fungsi |
| --- | --- |
| `info` | Nama, deskripsi, dan versi collection. |
| `item` | Daftar request atau folder. |
| `event` | Script yang dijalankan sebelum/sesudah request. |
| `request` | Method, URL, header, body, dan auth. |
| `response` | Contoh respons yang ditampilkan di dokumentasi. |
| `variable` | Variabel level collection. |

### Environment

Environment menyimpan nilai yang berubah tergantung konteks:

- `targetRate`
- `targetCurrency`
- kredensial Twilio

Environment **tidak boleh** berisi kredensial asli di repository. Nilai rahasia diisi lokal di Postman.

### Test script

Test script membuat dokumentasi menjadi hidup. Contoh dari collection ini:

```js
pm.test("Status 200 OK", function () {
  pm.response.to.have.status(200);
});

var body = pm.response.json();
pm.collectionVariables.set("currentRate", body.data.amount);
```

Test script berguna untuk:

- memastikan respons sesuai harapan;
- menyimpan data antar-request;
- mendeteksi perubahan API lebih awal.

---

## 5. Apa Itu OpenAPI?

**OpenAPI Specification (OAS)** adalah standar terbuka untuk mendeskripsikan REST API dalam format YAML atau JSON.

OpenAPI menjawab pertanyaan:

- endpoint apa yang ada;
- operasi apa yang didukung tiap endpoint;
- parameter dan body apa yang dibutuhkan;
- bentuk respons dan status code;
- skema autentikasi;
- schema data yang dipakai.

OpenAPI awalnya bernama **Swagger Specification**. Pada 2015 namanya diubah menjadi OpenAPI Specification, dan sejak 2016 dikelola oleh OpenAPI Initiative.

Versi yang umum:

| Versi | Catatan |
| --- | --- |
| Swagger 2.0 | Versi lama, masih ditemukan di proyek lama. |
| OpenAPI 3.0.x | Banyak dipakai, dukungan tool luas. |
| OpenAPI 3.1.x | Lebih dekat ke JSON Schema, mendukung `summary` di `info`, `null` handling lebih baik. |

File di repository ini memakai **OpenAPI 3.0.3** agar kompatibel dengan banyak tool.

---

## 6. Struktur Dasar OpenAPI 3.0

Struktur minimal:

```yaml
openapi: 3.0.3

info:
  title: Nama API
  version: 1.0.0
  description: Penjelasan API

servers:
  - url: https://api.contoh.com

paths:
  /contoh:
    get:
      summary: Ambil data contoh
      responses:
        '200':
          description: Berhasil
```

### 6.1 `openapi`

Menentukan versi spesifikasi.

```yaml
openapi: 3.0.3
```

Bukan versi aplikasi. Versi aplikasi ada di `info.version`.

### 6.2 `info`

Metadata API.

```yaml
info:
  title: Bitcoin Tracker - External API Contract
  version: 1.0.0
  description: |
    Kontrak API eksternal yang dipakai collection Bitcoin Tracker.
```

Field penting:

- `title`: nama API.
- `version`: versi kontrak.
- `description`: penjelasan, mendukung Markdown.
- `contact`, `license`: opsional.

### 6.3 `servers`

Base URL API.

```yaml
servers:
  - url: https://api.coinbase.com
    description: Coinbase production API
```

`servers` bisa diletakkan di level root, path, atau operation. Di file kita, `servers` diletakkan per operation karena dua endpoint berasal dari domain berbeda:

```yaml
paths:
  /v2/prices/{currencyPair}/spot:
    get:
      servers:
        - url: https://api.coinbase.com
```

### 6.4 `paths`

Tempat mendefinisikan endpoint. Key-nya adalah path URL.

```yaml
paths:
  /v2/prices/{currencyPair}/spot:
    get:
      summary: Get Bitcoin Exchange Rate
```

Path parameter ditulis dengan kurung kurawal: `{currencyPair}`.

### 6.5 Operation

Method HTTP di dalam path: `get`, `post`, `put`, `patch`, `delete`, `options`, `head`, `trace`.

```yaml
get:
  tags:
    - Bitcoin Price
  summary: Get Bitcoin Exchange Rate
  description: Mengambil harga spot 1 BTC.
  operationId: getBitcoinExchangeRate
```

`operationId` sebaiknya unik dan stabil karena dipakai untuk code generation.

### 6.6 `parameters`

Parameter bisa berada di `path`, `query`, `header`, atau `cookie`.

```yaml
parameters:
  - name: currencyPair
    in: path
    required: true
    description: Pasangan mata uang, contoh BTC-USD.
    schema:
      type: string
      enum:
        - BTC-USD
    example: BTC-USD
```

Aturan penting:

- `in: path` wajib `required: true`.
- Gunakan `schema` untuk tipe dan validasi.
- Gunakan `example` untuk memberi contoh nyata.

### 6.7 `requestBody`

Body request, terutama untuk `POST`, `PUT`, dan `PATCH`.

```yaml
requestBody:
  required: true
  content:
    application/x-www-form-urlencoded:
      schema:
        $ref: '#/components/schemas/TwilioSendMessageRequest'
```

`content` menentukan media type. Contoh umum:

- `application/json`
- `application/x-www-form-urlencoded`
- `multipart/form-data`

### 6.8 `responses`

Respons wajib punya minimal satu status code.

```yaml
responses:
  '201':
    description: Pesan berhasil dibuat.
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/TwilioMessage'
  '401':
    description: Autentikasi gagal.
```

Gunakan status code sebagai string: `'200'`, `'201'`, `'401'`.

Dokumentasikan juga error, bukan hanya sukses.

### 6.9 `components`

Tempat menyimpan schema, parameter, response, security scheme, dan contoh yang bisa dipakai ulang.

```yaml
components:
  schemas:
    TwilioError:
      type: object
      required:
        - code
        - message
        - status
      properties:
        code:
          type: integer
          example: 20003
        message:
          type: string
        status:
          type: integer
          example: 401
```

Pemakaian ulang:

```yaml
schema:
  $ref: '#/components/schemas/TwilioError'
```

`$ref` membuat spesifikasi lebih rapi dan tidak duplikat.

### 6.10 `security`

Menentukan cara autentikasi.

```yaml
components:
  securitySchemes:
    TwilioBasicAuth:
      type: http
      scheme: basic
```

Lalu dipakai di operation:

```yaml
security:
  - TwilioBasicAuth: []
```

Tipe security scheme yang umum:

| Tipe | Contoh penggunaan |
| --- | --- |
| `http` + `basic` | Username dan password. |
| `http` + `bearer` | Token Bearer/JWT. |
| `apiKey` | API key di header, query, atau cookie. |
| `oauth2` | OAuth 2.0 flow. |
| `openIdConnect` | OpenID Connect. |

### 6.11 `examples`

Contoh membuat dokumentasi jauh lebih mudah dipahami.

```yaml
content:
  application/json:
    schema:
      $ref: '#/components/schemas/CoinbaseSpotPriceResponse'
    examples:
      realResponse:
        summary: Respons nyata saat pengujian
        value:
          data:
            amount: '79690.805'
            base: BTC
            currency: USD
```

Bedakan:

- `example` (singular): satu contoh di level schema/parameter.
- `examples` (plural): kumpulan contoh dengan nama, dipakai di level media type.

---

## 7. Membaca Spesifikasi Proyek Ini

File `postman/openapi.yaml` mendokumentasikan dua endpoint:

```text
GET  https://api.coinbase.com/v2/prices/{currencyPair}/spot
POST https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Messages.json
```

Alur membacanya:

1. Lihat `info` untuk tahu konteks.
2. Lihat `paths` untuk daftar endpoint.
3. Pilih operation (`get` atau `post`).
4. Baca `parameters` dan `requestBody`.
5. Baca `responses` dan `examples`.
6. Lihat `components.schemas` untuk bentuk data lengkap.
7. Lihat `security` untuk autentikasi.

### Mengapa dua endpoint ini digabung dalam satu spec?

Karena keduanya adalah **kontrak eksternal yang dipakai satu alur aplikasi**:

1. Ambil harga BTC.
2. Kirim SMS jika harga di bawah target.

Spesifikasi ini bukan API milik proyek. Ini dokumentasi pihak ketiga yang dipakai proyek.

---

## 8. Alur Kerja OpenAPI di Postman

OpenAPI dan Postman bisa saling melengkapi.

### 8.1 Import OpenAPI ke Postman

1. Buka Postman.
2. Klik **Import**.
3. Pilih file `postman/openapi.yaml`.
4. Postman akan membaca endpoint, parameter, schema, dan contoh.
5. Pilih **Generate collection** jika ingin langsung membuat collection dari spec.

Hasilnya adalah collection baru yang mengikuti kontrak OpenAPI.

### 8.2 Menguji collection hasil import

Setelah collection dibuat:

1. Atur environment.
2. Jalankan request.
3. Tambahkan test script jika perlu.
4. Simpan contoh respons nyata.

### 8.3 Dokumentasi dari OpenAPI

Postman bisa menampilkan dokumentasi berdasarkan spec. Tool lain yang umum:

- **Swagger UI**: dokumentasi interaktif dari OpenAPI.
- **Redoc**: dokumentasi statis yang rapi.
- **Scalar**: dokumentasi API modern.

### 8.4 Validasi spesifikasi

Gunakan lint untuk menangkap kesalahan sintaks:

```powershell
postman spec lint "postman\openapi.yaml" --issueType syntax
```

Catatan: lint governance lengkap membutuhkan login Postman. Lint sintaks bisa dijalankan lokal.

Untuk validasi OpenAPI secara umum, bisa juga memakai:

- `npx @redocly/cli lint postman/openapi.yaml`
- `npx swagger-cli validate postman/openapi.yaml`
- VS Code extension **OpenAPI (Swagger) Editor**

---

## 9. Mempublikasikan Dokumentasi di Postman

Langkah yang dipraktikkan di video:

1. Buka collection **Bitcoin Tracker**.
2. Klik **...** > **View complete collection documentation**.
3. Periksa:
   - deskripsi collection;
   - deskripsi tiap request;
   - contoh respons;
   - environment yang dipakai.
4. Klik **Publish**.
5. Pilih versi collection.
6. Pilih environment.
7. Atur akses (publik atau private).
8. Publikasikan.
9. Buka URL publik dan periksa hasilnya.

### Sebelum publish

- Pastikan tidak ada token, password, atau nomor telepon asli.
- Pastikan contoh respons sudah benar.
- Pastikan deskripsi menjelaskan cara mulai.
- Pastikan environment yang dipilih bukan environment berisi kredensial asli.

### Setelah publish

- Salin URL dokumentasi.
- Tempel di `README.md`.
- Buka URL di browser dan cek tampilannya.
- Simpan screenshot sebagai bukti.

---

## 10. Praktik Dokumentasi yang Baik

### 10.1 Tulis untuk orang yang baru pertama kali memakai API

Sertakan:

- apa fungsi API;
- apa yang dibutuhkan sebelum mulai;
- langkah pertama yang harus dilakukan;
- contoh request yang bisa langsung dicoba;
- penjelasan setiap variabel.

### 10.2 Dokumentasikan error

Jangan hanya menulis `200 OK`. Tulis juga:

- `400` untuk input salah;
- `401` untuk autentikasi gagal;
- `403` untuk akses ditolak;
- `404` untuk data tidak ditemukan;
- `429` untuk rate limit;
- `500` untuk error server.

### 10.3 Gunakan contoh nyata

Contoh respons nyata lebih dipercaya daripada contoh karangan. Namun:

- sensor data rahasia;
- beri catatan waktu pengambilan;
- jelaskan bahwa nilai bisa berubah.

### 10.4 Jangan mengarang

Jika endpoint belum diuji, tulis **belum diuji**. Jika dokumentasi belum dipublikasikan, tulis **TODO**. Kejujuran status lebih penting daripada terlihat selesai.

### 10.5 Jaga keamanan

- Jangan commit kredensial.
- Jangan tampilkan token di screenshot.
- Gunakan placeholder untuk nilai rahasia.
- Pisahkan environment development dan production.

### 10.6 Beri nama yang konsisten

Contoh dari proyek ini:

- Collection: `Bitcoin Tracker`
- Environment: `Bitcoin Tracker`
- Variabel: `targetRate`, `targetCurrency`
- Request: `1. Get Bitcoin Exchange Rate`, `2. SMS via Twilio`

Konsistensi nama mengurangi kebingungan.

---

## 11. Kesalahan Umum

| Kesalahan | Akibat | Perbaikan |
| --- | --- | --- |
| Menyimpan token di collection | Token bocor saat repo dibagikan | Pindahkan ke environment/secret. |
| Hanya mendokumentasikan respons sukses | Error sulit dipahami | Tambahkan contoh 4xx/5xx. |
| Tidak menjelaskan variabel | Orang lain tidak bisa menjalankan | Buat tabel variabel. |
| Request tidak diuji | Dokumentasi menyesatkan | Jalankan dan catat hasilnya. |
| Contoh respons dikarang | Kepercayaan hilang | Gunakan respons nyata atau beri label jelas. |
| `servers` salah | Request mengarah ke host yang salah | Periksa base URL. |
| Path parameter tidak `required` | Spec tidak valid | Set `required: true`. |
| Schema tidak memakai `$ref` | Duplikasi dan sulit dirawat | Pisahkan ke `components`. |
| Status code ditulis angka | YAML bisa salah tafsir | Tulis sebagai string: `'200'`. |
| Dokumentasi publik berisi kredensial | Kebocoran data | Bersihkan sebelum publish. |

---

## 12. Latihan

### Latihan 1 - Import OpenAPI

1. Import `postman/openapi.yaml` ke Postman.
2. Bandingkan collection hasil import dengan collection `Bitcoin Tracker`.
3. Catat perbedaan yang muncul.

### Latihan 2 - Tambah Endpoint

Tambahkan endpoint CoinGecko ke `openapi.yaml`:

```text
GET https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd
```

Tentukan:

- parameter query;
- schema respons;
- contoh respons;
- status error yang mungkin.

### Latihan 3 - Perbaiki Dokumentasi

Buka collection di Postman, lalu:

1. Tambahkan deskripsi pada setiap request.
2. Tambahkan contoh respons error.
3. Tambahkan catatan rate limit.
4. Pastikan tidak ada nilai rahasia.

### Latihan 4 - Publikasikan

1. Publish dokumentasi collection.
2. Salin URL.
3. Tempel di README.
4. Ambil screenshot halaman publik.
5. Pastikan tidak ada data sensitif.

### Latihan 5 - Refactor Spec

Pindahkan schema yang berulang ke `components.schemas`, lalu gunakan `$ref`. Bandingkan panjang file sebelum dan sesudah.

---

## 13. Cheat Sheet OpenAPI 3.0

```yaml
openapi: 3.0.3

info:
  title: Nama API
  version: 1.0.0
  description: Penjelasan

servers:
  - url: https://api.contoh.com

tags:
  - name: Contoh

paths:
  /users/{id}:
    get:
      tags: [Contoh]
      summary: Ambil user
      operationId: getUserById
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Berhasil
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          description: Tidak ditemukan

components:
  schemas:
    User:
      type: object
      required: [id, name]
      properties:
        id:
          type: integer
          example: 1
        name:
          type: string
          example: Budi

  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - BearerAuth: []
```

---

## 14. Glosarium

| Istilah | Arti |
| --- | --- |
| API | Antarmuka untuk berkomunikasi antar aplikasi. |
| Endpoint | URL spesifik yang bisa dipanggil. |
| Request | Permintaan dari client ke server. |
| Response | Jawaban dari server. |
| Method | Aksi HTTP: GET, POST, PUT, PATCH, DELETE. |
| Status code | Kode hasil: 200, 201, 400, 401, 404, 500. |
| Header | Metadata request/response. |
| Body | Data yang dikirim. |
| Query parameter | Parameter di URL setelah `?`. |
| Path parameter | Parameter di dalam path, contoh `{id}`. |
| Schema | Definisi bentuk dan tipe data. |
| `$ref` | Referensi ke komponen lain. |
| Collection | Kumpulan request di Postman. |
| Environment | Kumpulan variabel sesuai konteks. |
| OpenAPI | Standar kontrak API. |
| Swagger | Nama lama OpenAPI dan ekosistem tool-nya. |
| Lint | Pemeriksaan otomatis terhadap kesalahan spesifikasi. |
| Mock server | Server palsu untuk meniru respons API. |

---

## 15. Ringkasan Satu Halaman

- Dokumentasi API menjelaskan cara memakai API, bukan hanya daftar endpoint.
- Postman Collection adalah alat kerja; OpenAPI adalah kontrak.
- OpenAPI 3.0 terdiri dari `info`, `servers`, `paths`, `components`, dan `security`.
- Setiap operation sebaiknya punya `summary`, `description`, `operationId`, parameter/body, dan responses.
- Dokumentasikan sukses dan error.
- Gunakan contoh nyata, tetapi sensor data rahasia.
- Gunakan `components` dan `$ref` agar spec tidak duplikat.
- Import OpenAPI ke Postman untuk membuat collection dan dokumentasi.
- Publikasikan dokumentasi Postman setelah memastikan tidak ada kredensial.
- Jangan mengarang hasil pengujian, endpoint, atau URL.

---

## 16. Referensi Resmi

- [OpenAPI Specification 3.0.3](https://spec.openapis.org/oas/v3.0.3)
- [OpenAPI Specification 3.1.0](https://spec.openapis.org/oas/v3.1.0)
- [Postman Learning Center - Document your APIs](https://learning.postman.com/docs/publishing-your-api/document-your-apis/)
- [Postman Learning Center - Publish documentation](https://learning.postman.com/docs/publishing-your-api/publish-documentation/)
- [Postman Learning Center - Import an OpenAPI specification](https://learning.postman.com/docs/designing-and-developing-your-api/importing-an-api/)
- [Swagger UI](https://swagger.io/tools/swagger-ui/)
- [Redoc](https://redocly.com/redoc)
- [Coinbase Exchange Rates API](https://docs.cdp.coinbase.com/exchange/reference/exchangerestapi_getrates)
- [Twilio Programmable Messaging](https://www.twilio.com/docs/messaging/api/message-resource)
