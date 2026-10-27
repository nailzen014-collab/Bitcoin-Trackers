# Panduan Screenshot

Folder ini disediakan untuk bukti praktik. Screenshot **belum** tersedia dan harus diambil langsung dari aplikasi Postman. Jangan membuat screenshot palsu atau hasil edit.

## Daftar Screenshot yang Disarankan

| Nama file | Isi yang difoto | Status |
| --- | --- | --- |
| `request-200-ok.png` | Request **Get Bitcoin Exchange Rate** setelah **Send**, menampilkan status `200 OK`, body JSON harga BTC, dan panel variabel environment. | Wajib. |
| `collection-overview.png` | Overview collection **Bitcoin Tracker** dengan badge **Docs published** dan daftar dua request beserta contoh respons. | Wajib. |
| `public-docs.png` | Halaman dokumentasi publik di `https://documenter.getpostman.com/view/58884262/2sBYHVudef`. | Opsional (pelengkap). |
| `environment.png` | Halaman **Environments > Bitcoin Tracker** yang menampilkan `targetRate` dan `targetCurrency`. Kosongkan nilai Twilio. | Opsional (pelengkap). |

## Aturan Keamanan

- Jangan menampilkan `twilioAuthToken`, `twilioAccountSid`, nomor telepon asli, atau token apa pun.
- Sebelum memotret environment, kosongkan nilai rahasia atau gunakan nilai dummy.
- Periksa ulang hasil screenshot sebelum di-commit.

## Cara Mengambil di Windows

1. Buka Postman dan siapkan tampilan yang ingin difoto.
2. Tekan `Win + Shift + S`.
3. Pilih area yang ingin diambil.
4. Simpan file ke folder ini dengan nama sesuai tabel di atas.
5. Buka file hasil screenshot untuk memastikan tidak ada data rahasia.
6. Commit file gambar ke repository.

Setelah screenshot ditambahkan, perbarui checklist di `README.md` utama.
