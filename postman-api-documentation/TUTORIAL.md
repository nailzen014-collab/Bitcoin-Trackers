# Tutorial Lengkap — Dokumentasi API Bitcoin Tracker di Postman

Panduan ini menjelaskan **dari nol sampai hasil jadi**: membuat collection, environment, request, test script, dokumentasi, publish, screenshot, sampai push ke GitHub.

Ditulis untuk tugas Quest Room SMK Pesat Pertemuan 20 — *How to Create API Documentation in Postman*.

---

## 0. Hasil akhir yang harus kamu punya

Kalau semua langkah di bawah selesai, kamu akan punya:

| Artefak | Lokasi | Status proyek ini |
| --- | --- | --- |
| Collection Postman | Postman Cloud / `postman/Bitcoin-Tracker.postman_collection.json` | ✅ ada |
| Environment Postman | Postman Cloud / `postman/Bitcoin-Tracker.postman_environment.json` | ✅ ada |
| Dokumentasi publik | `https://documenter.getpostman.com/view/58884262/2sBYHVudef` | ✅ published |
| Spesifikasi OpenAPI | `postman/openapi.yaml` | ✅ ada |
| README | `README.md` | ✅ ada |
| Screenshot bukti | `screenshots/request-200-ok.png`, `screenshots/collection-overview.png` | ⬜ perlu disimpan |
| Repo GitHub | URL repo | ⬜ perlu dibuat |

---

## 1. Istilah dasar (biar nggak bingung)

| Istilah | Artinya |
| --- | --- |
| **Collection** | Kumpulan request API. Ibarat folder proyek di Postman. |
| **Request** | Satu panggilan API: method (`GET`/`POST`), URL, header, body. |
| **Environment** | Kumpulan variabel yang bisa dipilih sekali klik (misal `targetRate`). |
| **Variable** | Nilai yang dipakai di URL/body dengan format `{{namaVariabel}}`. |
| **Collection variable** | Variabel yang hidup di dalam collection, biasanya diisi otomatis oleh script. |
| **Test script** | Kode JavaScript yang jalan **setelah** request, untuk mengecek respons (`pm.test`). |
| **Pre-request script** | Kode JavaScript yang jalan **sebelum** request, untuk menyiapkan data. |
| **Example** | Contoh respons yang disimpan, dipakai untuk dokumentasi dan mock. |
| **Documenter** | Halaman web publik hasil publish collection. |
| **Cloud View** | Tampilan resource yang tersimpan di server Postman. |
| **Local View** | Tampilan file di folder Git lokal (fitur Native Git Postman v12). |
| **API Key** | Kunci akses akun Postman. **Rahasia**, jangan pernah di-commit. |

> ⚠️ **Cloud View vs Local View** adalah sumber kebingungan paling sering. Kalau sidebar kiri menampilkan file `.php`, `app/`, `vendor/` — itu **Local View**, bukan collection. Cara baliknya ada di bagian [Troubleshooting](#12-troubleshooting).

---

## 2. Prasyarat

1. **Akun Postman** (gratis cukup) — https://postman.com
2. **Aplikasi Postman Desktop** (disarankan) atau web app.
3. **Postman CLI** (opsional, untuk menjalankan/publish dari terminal):
   ```powershell
   postman --version
   ```
   Kalau belum ada, install dari https://learning.postman.com/docs/postman-cli/installation/
4. **Git** dan akun **GitHub**.
5. (Opsional untuk SMS asli) akun **Twilio** trial.

---

## 3. Bagian 1 — Siapkan workspace

1. Buka Postman.
2. Klik **dropdown workspace** di kiri atas.
3. Pilih workspace yang mau dipakai (contoh proyek ini: **Nail's Workspace**).
4. Pastikan kamu di **Cloud View**:
   - Footer kiri bawah menampilkan **Switch to Local** = kamu sedang di Cloud (benar).
   - Kalau menampilkan nama branch seperti `main` dan file explorer, kamu di Local View.

> Kalau muncul banner *"You are viewing Postman Cloud"* — itu artinya kamu **sudah** di Cloud View. Banner itu menawarkan pindah ke Local, bukan sebaliknya.

---

## 4. Bagian 2 — Buat Environment dan variabel

1. Sidebar kiri → **Environments** → **+** → beri nama **Bitcoin Tracker**.
2. Isi variabel berikut:

| Variabel | Nilai | Rahasia? | Fungsi |
| --- | --- | --- | --- |
| `targetRate` | `9000` | Tidak | Batas harga alert. Alert aktif jika harga BTC < nilai ini. |
| `targetCurrency` | `USD` | Tidak | Mata uang di URL Coinbase. |
| `twilioAccountSid` | *(kosong)* | **Ya** | Account SID Twilio. |
| `twilioAuthToken` | *(kosong)* | **Ya** | Auth Token Twilio. |
| `twilioFrom` | *(kosong)* | **Ya** | Nomor pengirim Twilio (format `+62...`). |
| `smsTo` | *(kosong)* | **Ya** | Nomor tujuan SMS (format `+62...`). |

3. Untuk 4 variabel Twilio, ubah **Type** menjadi **secret**.
4. **Save**.

> Variabel rahasia **sengaja dikosongkan di repo**. Isi hanya di Postman lokalmu. Ini supaya kredensial tidak ikut ter-commit atau tampil di dokumentasi publik.

**Kenapa `targetRate` = 9000?** Mengikuti contoh video. Harga BTC asli jauh di atas 9000, jadi `alertTriggered` akan `false`. Untuk menguji alert, naikkan `targetRate` di atas harga saat ini (misal `100000`).

---

## 5. Bagian 3 — Request 1: Get Bitcoin Exchange Rate

### 5.1 Buat request

1. Sidebar → **Collections** → **+** → **Blank Collection** → nama **Bitcoin Tracker**.
2. Hover collection → **...** → **Add request** → nama `1. Get Bitcoin Exchange Rate`.
3. Isi:

| Bagian | Nilai |
| --- | --- |
| Method | `GET` |
| URL | `https://api.coinbase.com/v2/prices/BTC-{{targetCurrency}}/spot` |

4. **Save**.

> Kenapa Coinbase? Endpoint CoinDesk lama (`api.coindesk.com`) sudah tidak dipakai. Coinbase masih publik dan tanpa API key.

### 5.2 Tambah test script

Buka tab **Scripts** → **Post-response**, lalu tempel:

```javascript
pm.test("Status 200 OK", function () {
  pm.response.to.have.status(200);
});

var body = pm.response.json();

pm.test("Respons berisi objek data", function () {
  pm.expect(body).to.have.property("data");
  pm.expect(body.data).to.have.property("amount");
  pm.expect(body.data).to.have.property("base", "BTC");
  pm.expect(body.data).to.have.property("currency");
});

var rate = Number(body.data.amount);
pm.test("amount adalah angka valid dan lebih dari 0", function () {
  pm.expect(Number.isFinite(rate)).to.be.true;
  pm.expect(rate).to.be.above(0);
});

pm.collectionVariables.set("currentRate", body.data.amount);

var rawTarget = pm.environment.get("targetRate") || pm.collectionVariables.get("targetRate");
pm.test("targetRate tersedia dan berupa angka", function () {
  pm.expect(rawTarget, "Isi targetRate pada environment Bitcoin Tracker").to.not.be.undefined;
  pm.expect(Number.isFinite(Number(rawTarget)), "targetRate harus berupa angka").to.be.true;
});

if (Number.isFinite(Number(rawTarget))) {
  var target = Number(rawTarget);
  var triggered = rate < target;
  pm.collectionVariables.set("alertTriggered", String(triggered));
  pm.test("alertTriggered dihitung dari harga terhadap target", function () {
    pm.expect(["true", "false"]).to.include(String(triggered));
  });
}

pm.test("Mata uang respons sesuai targetCurrency", function () {
  var expectedCurrency = pm.environment.get("targetCurrency");
  if (expectedCurrency) {
    pm.expect(body.data.currency).to.eql(expectedCurrency);
  }
});
```

**Apa yang dilakukan script ini, baris per baris:**

- `pm.response.to.have.status(200)` → memastikan API membalas 200.
- `pm.response.json()` → mengubah body respons jadi objek JavaScript.
- `pm.expect(...)` → assertion; kalau gagal, test jadi merah.
- `pm.collectionVariables.set("currentRate", ...)` → menyimpan harga terakhir supaya request ke-2 bisa pakai.
- `rate < target` → logika alert, hasilnya disimpan di `alertTriggered`.

Jumlah test: **6 assertion**.

### 5.3 Simpan contoh respons (Example)

1. Klik **Send** (pastikan environment **Bitcoin Tracker** dipilih).
2. Setelah `200 OK`, klik **Save Response** → **Save as example**.
3. Nama: `200 OK - respons nyata (27 Okt 2026)`.
4. **Save**.

Contoh respons nyata proyek ini:

```json
{
  "data": {
    "amount": "79543.825",
    "base": "BTC",
    "currency": "USD"
  }
}
```

> Harga berubah setiap saat. Contoh di atas hanyalah snapshot saat pengujian.

---

## 6. Bagian 4 — Request 2: SMS via Twilio

### 6.1 Buat request

1. Hover collection → **...** → **Add request** → nama `2. SMS via Twilio`.
2. Isi:

| Bagian | Nilai |
| --- | --- |
| Method | `POST` |
| URL | `https://api.twilio.com/2010-04-01/Accounts/{{twilioAccountSid}}/Messages.json` |

3. Tab **Authorization** → Type **Basic Auth**:
   - **Username**: `{{twilioAccountSid}}`
   - **Password**: `{{twilioAuthToken}}`
4. Tab **Body** → **x-www-form-urlencoded**, isi:

| Key | Value |
| --- | --- |
| `To` | `{{smsTo}}` |
| `From` | `{{twilioFrom}}` |
| `Body` | `{{smsBody}}` |

5. **Save**.

### 6.2 Pre-request script

Tab **Scripts** → **Pre-request**:

```javascript
var rate = pm.collectionVariables.get("currentRate");
var currency = pm.environment.get("targetCurrency") || "USD";
var target = pm.environment.get("targetRate") || "belum diatur";

pm.collectionVariables.set(
  "smsBody",
  "Bitcoin Tracker: harga BTC " + (rate || "belum tersedia") + " " + currency + " (target " + target + ")."
);

var required = ["twilioAccountSid", "twilioAuthToken", "twilioFrom", "smsTo"];
var missing = required.filter(function (key) {
  return !pm.environment.get(key);
});

if (missing.length > 0) {
  console.warn("Variabel environment belum diisi: " + missing.join(", ") + ". Request akan gagal autentikasi atau validasi.");
}
```

Fungsinya: menyusun isi SMS dari `currentRate` + `targetRate`, dan memperingatkan kalau kredensial belum diisi.

### 6.3 Test script

Tab **Scripts** → **Post-response**:

```javascript
pm.test("Status 201 Created", function () {
  pm.response.to.have.status(201);
});

if (pm.response.code === 201) {
  var body = pm.response.json();
  pm.test("Respons berisi sid pesan", function () {
    pm.expect(body).to.have.property("sid");
    pm.expect(body.sid).to.match(/^SM/);
  });
  pm.test("Status pesan queued atau sent", function () {
    pm.expect(body.status).to.be.oneOf(["queued", "sent"]);
  });
}

if (pm.response.code === 401) {
  pm.test("401 berisi detail error autentikasi", function () {
    var errorBody = pm.response.json();
    pm.expect(errorBody).to.have.property("code");
    pm.expect(errorBody).to.have.property("message");
    pm.expect(errorBody).to.have.property("status", 401);
  });
}
```

### 6.4 Contoh respons

Simpan dua example:

1. `201 Created - contoh bentuk respons dokumentasi` (dari dokumentasi Twilio, **belum** dijalankan dengan kredensial asli).
2. `401 Unauthorized - respons nyata dengan kredensial dummy`.

Contoh `401` nyata:

```json
{
  "code": 20003,
  "message": "auth account AC00000000000000000000000000000000 does not exist",
  "more_info": "https://www.twilio.com/docs/errors/20003",
  "status": 401
}
```

> Kalau belum punya Twilio, jangan paksakan kirim SMS asli. Cukup uji sampai `401` dengan kredensial dummy, lalu dokumentasikan apa adanya.

---

## 7. Bagian 5 — Menjalankan dan membaca hasil

### 7.1 Lewat aplikasi

1. Buka request `1. Get Bitcoin Exchange Rate`.
2. Dropdown kanan atas: pilih **Bitcoin Tracker** (bukan **No Environment**).
3. Klik **Send**.
4. Lihat tab **Test Results** → harus **6 PASS**.
5. Lihat tab **Body** → JSON harga BTC.
6. Panel **Variables** (kanan) → `currentRate` dan `alertTriggered` terisi.

### 7.2 Lewat CLI (tanpa buka app)

Dari folder `postman-api-documentation`:

```powershell
postman collection run "postman\Bitcoin-Tracker.postman_collection.json" -e "postman\Bitcoin-Tracker.postman_environment.json" -i "1. Get Bitcoin Exchange Rate"
```

Yang harus muncul: ringkasan request, 6 assertion PASS, dan harga BTC terbaru.

### 7.3 Uji jalur error SMS (opsional)

```powershell
postman collection run "postman\Bitcoin-Tracker.postman_collection.json" -e "postman\Bitcoin-Tracker.postman_environment.json" --env-var "twilioAccountSid=AC00000000000000000000000000000000" --env-var "twilioAuthToken=dummy_token" --env-var "twilioFrom=+620000000000" --env-var "smsTo=+620000000000"
```

Hasil yang diharapkan: `401` dengan pesan error autentikasi. Ini membuktikan request dan body sudah tersusun benar.

---

## 8. Bagian 6 — Menulis dokumentasi collection

Klik nama collection → tab **Overview**. Isi bagian-bagian ini:

1. **Description** — tujuan collection, cara kerja singkat.
2. **Quick Start** — langkah import, pilih environment, isi variabel, jalankan request.
3. **Tabel variabel** — nama, scope, wajib/tidak, rahasia/tidak, keterangan.
4. **Deskripsi tiap request** — buka request → tab **Docs** → tulis tujuan, parameter, dan contoh respons.
5. **Contoh respons** — pastikan setiap request punya example (200/201/401).

Contoh isi description collection (yang dipakai proyek ini):

> Collection ini mendokumentasikan dua request API yang dipakai untuk memantau harga Bitcoin dan mengirim notifikasi SMS melalui Twilio ketika harga berada di bawah target.

**Tips:** dokumentasi yang baik menjawab 3 hal — *ini API apa*, *bagaimana mulai*, *apa yang dikirim dan diterima*.

---

## 9. Bagian 7 — Publish dokumentasi

### 9.1 Lewat aplikasi

1. Buka collection → **Overview**.
2. Klik **Publish docs** (kanan atas).
3. Pilih **Postman**.
4. Pilih environment **Bitcoin Tracker**.
5. Isi judul/deskripsi SEO (opsional).
6. **Publish**.
7. Salin URL `documenter.getpostman.com/...`.

### 9.2 Lewat CLI

```powershell
postman collection publish <collection-id> --environment <environment-id> --title "Bitcoin Tracker - API Documentation" --api-key $env:POSTMAN_API_KEY
```

> Jangan pernah menulis API key langsung di file yang di-commit. Gunakan environment variable `POSTMAN_API_KEY`.

### 9.3 Sebelum publish — cek keamanan

- Pastikan variabel rahasia **kosong** di environment yang di-bind.
- Buka halaman publik, ctrl+F cari `AC`, `token`, nomor telepon. Kalau ada, **jangan** bagikan.
- Ingat: dokumentasi otomatis ikut update setiap collection diubah.

---

## 10. Bagian 8 — Export dan struktur repo

### 10.1 Export dari Postman

1. Collection → **...** → **Export** → pilih **Collection v2.1** → simpan sebagai `postman/Bitcoin-Tracker.postman_collection.json`.
2. Environments → **...** → **Export** → simpan sebagai `postman/Bitcoin-Tracker.postman_environment.json`.
3. **Cek dulu** file environment: semua nilai rahasia harus kosong sebelum di-commit.

### 10.2 Struktur folder

```text
postman-api-documentation/
├── README.md
├── TUTORIAL.md
├── docs/
│   └── rangkuman-openapi.md
├── postman/
│   ├── Bitcoin-Tracker.postman_collection.json
│   ├── Bitcoin-Tracker.postman_environment.json
│   └── openapi.yaml
└── screenshots/
    ├── request-200-ok.png
    └── collection-overview.png
```

---

## 11. Bagian 9 — Screenshot

Ambil **langsung dari Postman**, jangan diedit/dipalsukan.

| Nama file | Isi |
| --- | --- |
| `screenshots/request-200-ok.png` | Request `1. Get Bitcoin Exchange Rate` setelah **Send**: status `200 OK`, body JSON harga, panel variabel environment. |
| `screenshots/collection-overview.png` | Overview collection **Bitcoin Tracker** dengan badge **Docs published** dan daftar request. |
| `screenshots/public-docs.png` *(opsional)* | Halaman `documenter.getpostman.com/...` di browser. |

Cara ambil di Windows:

1. Siapkan tampilan di Postman.
2. Tekan `Win + Shift + S`, pilih area.
3. Simpan ke folder `screenshots/` dengan nama di atas.
4. Periksa ulang: tidak ada token, nomor telepon asli, atau Account SID.

---

## 12. Bagian 10 — Git dan GitHub

### 12.1 Inisialisasi repo (di dalam folder proyek)

```powershell
cd C:\laragon\www\Bitcoin-Tracker\postman-api-documentation
git init
git add .
git commit -m "Add Bitcoin Tracker Postman API documentation"
```

> **Penting:** jalankan `git init` **di dalam** `postman-api-documentation`, bukan di `C:\laragon\www`. Kalau di root `www`, semua project lain ikut ter-commit.

### 12.2 Buat repo di GitHub

1. Buka https://github.com/new
2. Nama: `postman-api-documentation`
3. **Jangan** centang "Add README" (biar tidak konflik).
4. **Create repository**.
5. Jalankan perintah yang ditampilkan GitHub, contoh:

```powershell
git remote add origin https://github.com/<username>/postman-api-documentation.git
git branch -M main
git push -u origin main
```

### 12.3 Sebelum push — cek rahasia

```powershell
git status
git grep -nE "AC[0-9a-f]{32}|PMAK-|Bearer |twilioAuthToken\".*[^\"]\"" 
```

Kalau ada temuan, **jangan push**. Hapus dulu nilainya.

---

## 13. Bagian 11 — Kumpulkan tugas

1. Buka Quest Room → tugas Pertemuan 20.
2. Tempel **URL repository GitHub** (bukan URL dokumentasi Postman, bukan URL video).
3. Pastikan repo bisa dibuka publik (atau sesuai instruksi guru).
4. Selesai.

---

## 14. Troubleshooting

| Gejala | Penyebab | Solusi |
| --- | --- | --- |
| Sidebar kiri menampilkan `app/`, `vendor/`, file `.php` | Sedang di **Local View** (Native Git). | Klik nama branch **`main`** di footer kiri bawah → pilih **Postman cloud**. |
| Banner "You are viewing Postman Cloud" | Kamu **sudah** di Cloud View; banner menawarkan pindah ke Local. | Abaikan, atau klik ikon **Items** (kotak) untuk melihat Collections. |
| Collection tidak ketemu saat search | Kata kunci tanpa spasi (`bitcointracker`). | Cari **`Bitcoin`** atau kosongkan search. |
| "Collection not found" | Collection yang dibuka milik workspace lain / sudah dihapus. | Pastikan workspace di dropdown = tempat collection berada. |
| `No Environment` saat Send | Environment belum dipilih. | Dropdown kanan atas → pilih **Bitcoin Tracker**. |
| `401 Unauthorized` Twilio | SID/Token salah atau kosong. | Isi variabel environment dengan benar. |
| `400 Bad Request` Twilio | Nomor bukan format E.164. | Gunakan `+62...`. |
| `alertTriggered` selalu `false` | Harga BTC jauh di atas `targetRate`. | Naikkan `targetRate` di atas harga saat ini untuk uji. |
| Publish docs gagal | Collection belum disimpan / belum di cloud. | **Save** collection, pastikan Cloud View, coba lagi. |
| Docs publik menampilkan nilai rahasia | Environment berisi secret saat publish. | Kosongkan secret, publish ulang. |

---

## 15. Checklist keamanan

- [ ] `twilioAuthToken`, `twilioAccountSid`, `twilioFrom`, `smsTo` kosong di file yang di-commit.
- [ ] API key Postman tidak ada di repo (`PMAK-...`).
- [ ] Tidak ada `.env` berisi kredensial asli.
- [ ] Screenshot tidak menampilkan nomor telepon/token.
- [ ] File environment yang diexport sudah diperiksa ulang.

---

## 16. Referensi perintah

```powershell
# cek versi CLI
postman --version

# jalankan satu request
postman collection run "postman\Bitcoin-Tracker.postman_collection.json" -e "postman\Bitcoin-Tracker.postman_environment.json" -i "1. Get Bitcoin Exchange Rate"

# jalankan semua request
postman collection run "postman\Bitcoin-Tracker.postman_collection.json" -e "postman\Bitcoin-Tracker.postman_environment.json"

# publish dokumentasi
postman collection publish <collection-id> --environment <environment-id> --title "Bitcoin Tracker - API Documentation"

# lint OpenAPI
postman spec lint "postman\openapi.yaml" --issueType syntax
```

---

## 17. Ringkasan alur (1 layar)

```text
Buat workspace
   → Buat environment + variabel
      → Buat collection
         → Buat request 1 (GET Coinbase) + test script
            → Simpan example 200 OK
               → Buat request 2 (POST Twilio) + auth + body + script
                  → Simpan example 201 & 401
                     → Isi deskripsi collection & request (Docs)
                        → Publish docs → salin URL
                           → Export collection & environment
                              → Susun README + TUTORIAL + screenshot
                                 → git init, commit, push ke GitHub
                                    → Tempel URL repo ke Quest Room
```

Kalau satu langkah gagal, balik ke bagian Troubleshooting — hampir semua masalah di tugas ini berasal dari **environment belum dipilih** atau **masih di Local View**.
