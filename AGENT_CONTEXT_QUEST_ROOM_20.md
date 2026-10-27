# Context Proyek — Quest Room Pertemuan 20

Dokumen ini berisi konteks dan instruksi proyek agar AI coding agent dapat membantu melanjutkan tugas dengan benar.

## 1. Konteks tugas

- Platform tugas: Quest Room, SMK Pesat.
- Pertemuan: 20.
- Materi video: **How to Create API Documentation in Postman (Step-by-Step Guide)** — Postman.
- Video: https://www.youtube.com/watch?v=XNVo9WkCoak&t=60s
- Instruksi portal: praktikkan materi video di proyek menggunakan VS Code, lalu setorkan **link repository GitHub**.
- Kolom pengumpulan meminta URL repository GitHub. Jangan menganggap link video atau link dokumentasi Postman saja sudah memenuhi pengumpulan.

## 2. Tujuan praktik

Buat proyek dokumentasi API berbasis Postman yang terorganisasi dan simpan artefaknya di repository GitHub. Fokus tugas adalah praktik dokumentasi API di Postman, bukan membangun aplikasi Bitcoin lengkap dari nol kecuali instruksi guru meminta hal itu.

Nama proyek/repository yang disarankan: `postman-api-documentation`

## 3. Materi utama dari video

Alur yang ditunjukkan video:

1. Gunakan sebuah Postman Collection yang berisi request API.
2. Jika tersedia, impor spesifikasi API; dalam demo video collection sudah tersedia.
3. Siapkan Environment dan variabel yang diperlukan.
4. Buka dokumentasi collection melalui opsi **View complete collection documentation**.
5. Tambahkan deskripsi collection, penjelasan request, dan bagian Quick Start/Get Started.
6. Dokumentasikan request beserta contoh respons, termasuk contoh sukses dan kesalahan jika tersedia.
7. Postman dapat menampilkan contoh kode untuk berbagai bahasa.
8. Atur akses dokumentasi dan, bila sesuai, publikasikan dokumentasi ke web.
9. Pilih versi collection dan Environment yang sesuai saat menerbitkan dokumentasi.
10. Periksa dokumentasi publik yang dihasilkan.

Demo video menampilkan collection bernama **Bitcoin Tracker B**, dengan dua request:
- `GET Get Bitcoin Exchange Rate`
- `POST SMS via Twilio`

Deskripsi contoh: memantau kurs Bitcoin terhadap target dan mengirim SMS melalui Twilio jika kurs turun di bawah target.

## 4. Status dan konteks yang diketahui

- Pengguna sudah membuka Postman dan membuat collection/environment.
- Screenshot sebelumnya memperlihatkan environment dengan variabel yang awalnya bernama `targetrate` dan nilai `9000`.
- Saran perapian sebelumnya: gunakan nama Environment `Bitcoin Tracker` dan nama variabel `targetRate` secara konsisten.
- Belum ada konfirmasi bahwa dua request demo telah dibuat, diuji, atau dokumentasinya telah dipublikasikan.
- Belum ada konfirmasi bahwa repository GitHub sudah dibuat.
- Jangan mengklaim request berhasil jika belum diuji.

## 5. Struktur repository yang disarankan

```text
postman-api-documentation/
├── README.md
├── postman/
│   └── Bitcoin-Tracker.postman_collection.json
└── screenshots/
    ├── collection.png
    ├── environment.png
    └── documentation.png
```

Struktur ini fleksibel. Jangan membuat file JSON collection palsu atau mengarang endpoint; ekspor collection asli dari Postman setelah tersedia.

## 6. Langkah kerja yang disarankan

1. Di Postman, rapikan nama collection menjadi `Bitcoin Tracker`.
2. Buat/rapikan Environment `Bitcoin Tracker`.
3. Pastikan variabel target konsisten, misalnya `targetRate` dengan nilai target yang dipilih pengguna.
4. Buat atau periksa request `GET Get Bitcoin Exchange Rate` dan `POST SMS via Twilio` sesuai contoh video atau materi yang tersedia.
5. Tambahkan deskripsi yang menjelaskan tujuan collection, cara mulai, variabel, dan tiap request.
6. Tambahkan contoh request/response yang benar-benar tersedia dan telah diperiksa.
7. Jika memungkinkan, terbitkan dokumentasi Postman. Jangan menampilkan nilai rahasia dalam dokumentasi publik.
8. Ekspor collection Postman sebagai file JSON ke folder `postman/`.
9. Buat `README.md` yang menjelaskan tujuan proyek, isi collection, cara mengimpor, variabel yang diperlukan, serta tautan dokumentasi Postman jika ada.
10. Tambahkan screenshot sebagai bukti praktik jika diperlukan.
11. Buka folder proyek di VS Code, periksa semua file, lalu commit dan push ke repository GitHub.
12. Masukkan URL repository GitHub ke kolom pengumpulan Quest Room.

## 7. Struktur README yang diharapkan

README sebaiknya memuat:
- Nama dan deskripsi proyek.
- Tujuan praktik.
- Daftar request API.
- Environment dan variabel yang diperlukan.
- Cara mengimpor collection ke Postman.
- Cara mengatur variabel dan menjalankan request.
- Link dokumentasi Postman (jika sudah diterbitkan).
- Catatan pengujian: tandai mana yang sudah diuji dan mana yang belum.

## 8. Keamanan dan akurasi

- **Jangan pernah commit kredensial asli** seperti Twilio Account SID, Auth Token, API key, password, atau token.
- Untuk variabel rahasia, gunakan placeholder/contoh kosong dan jelaskan bahwa pengguna harus mengisinya secara lokal.
- Jangan mengarang endpoint, hasil respons, status pengujian, URL dokumentasi publik, atau URL repository.
- Jika ada informasi yang belum tersedia, tanyakan kepada pengguna atau tandai sebagai TODO.
- Pastikan nilai variabel tidak ikut terekspos saat memublikasikan dokumentasi Postman.
- Jangan mengklaim tugas selesai sebelum repository benar-benar berisi artefak yang relevan dan URL-nya sudah tersedia.

## 9. Instruksi langsung untuk AI coding agent

Bantu pengguna menyelesaikan tugas secara bertahap dan praktis. Periksa file yang benar-benar ada sebelum mengeditnya. Utamakan artefak nyata yang dapat dikumpulkan: collection Postman hasil ekspor, README yang jelas, dan screenshot pendukung bila tersedia.

Mulai dengan memeriksa isi folder proyek saat ini. Jika belum ada file, buat kerangka `README.md` dan folder `postman/` serta `screenshots/`, tetapi jangan membuat collection JSON seolah-olah berasal dari Postman. Pandu pengguna mengekspor collection asli dari Postman, lalu bantu memvalidasi README dan struktur repository.

Jawab dalam bahasa Indonesia yang santai dan jelas. Berikan perintah terminal yang siap disalin bila diperlukan, dan jelaskan di mana perintah harus dijalankan. Jangan meminta atau menampilkan rahasia pengguna.
