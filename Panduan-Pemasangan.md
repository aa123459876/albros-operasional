# Panduan Pemasangan Albros Operasional

Semua langkah di bawah gratis. Perkiraan waktu: 20 sampai 30 menit.

Isi paket:

- `index.html` : website lengkap
- `config.js` : tempat mengisi alamat dan kunci Supabase
- `supabase/01-skema.sql` : membuat tabel, keamanan, dan 46 pangkalan awal
- `supabase/02-kode-reset.sql` : mengatur kode reset dan kode hapus
- `.github/workflows/keepalive.yml` : jadwal otomatis agar database tidak tertidur

## Langkah 1. Buat project Supabase

1. Buka supabase.com, masuk (bisa memakai akun GitHub), pilih **New project**.
2. Isi nama project (misalnya `albros`), buat **Database Password** lalu simpan di tempat aman, pilih region **Southeast Asia (Singapore)**, paket **Free**.
3. Tunggu sekitar 2 menit sampai project siap.

## Langkah 2. Buat tabel dan keamanan

1. Di Supabase, buka **SQL Editor** lalu **New query**.
2. Buka file `supabase/01-skema.sql`, salin seluruh isinya, tempel, lalu tekan **Run**. Hasil yang benar: "Success. No rows returned".
3. Buka file `supabase/02-kode-reset.sql`. Ganti `ISI_KODE_ANDA` dengan kode yang Anda mau (angka saja, misalnya `123456789`), tempel di query baru, lalu **Run**.

Kode ini dipakai untuk reset angka salin dan hapus permanen pangkalan. Kode disimpan terenkripsi dan dicek di server, jadi tidak tertulis di website. Setelah 10 kali salah dalam 10 menit, percobaan ditolak sementara. Untuk mengganti kode, jalankan ulang file ini dengan kode baru.

## Langkah 3. Atur login

1. Buka **Authentication**, lalu **Sign In / Providers** (atau **Providers**), pilih **Email**.
   - Matikan **Confirm email**.
2. Di pengaturan yang sama (**Sign In / Providers**, bagian atas), **matikan "Allow new users to sign up"**. Langkah ini penting. Tanpa ini, orang lain yang tahu alamat project bisa mendaftar sendiri dan ikut membaca data.
3. Buka **Authentication > Users > Add user > Create new user**:
   - Email: `albros@albros.app`
   - Password: isi password pilihan Anda sendiri (jangan ditulis di file mana pun)
   - Centang **Auto Confirm User**
4. Di halaman login website, user diketik `Albros` dan password sesuai yang Anda isi di atas. Website mengubah user menjadi email `albros@albros.app` otomatis.

Untuk menambah akun lain, buat user baru dengan email `namauser@albros.app`. Lalu login memakai `namauser`.

## Langkah 4. Sambungkan website ke Supabase

1. Buka **Project Settings > API** (atau **Data API / API Keys**).
2. Salin **Project URL** dan kunci **anon public** (atau **publishable**). **Jangan** pakai kunci `service_role`.
3. Buka `config.js` dan ganti `ISI_PROJECT_URL` dan `ISI_ANON_KEY` dengan dua nilai tadi.

Kunci anon memang dipakai di sisi website dan aman dibagikan. Data tetap terlindungi karena hanya pengguna yang sudah login yang bisa membaca atau menulis.

## Langkah 5. Tayangkan di GitHub Pages

1. Di github.com, buat repository baru (misalnya `albros-operasional`).
2. Upload semua isi folder ini (`index.html`, `config.js`, folder `supabase`, folder `.github`, dan file panduan ini).
3. Buka **Settings > Pages**. Pada **Build and deployment**, pilih **Deploy from a branch**, branch `main`, folder `/ (root)`, lalu **Save**.
4. Setelah 1 sampai 2 menit, alamat website muncul di halaman yang sama (bentuknya `https://namaanda.github.io/albros-operasional/`). Bagikan alamat ini ke karyawan.

## Langkah 6. Jaga database tetap aktif

Project Supabase gratis tertidur jika tidak ada aktivitas sekitar seminggu. Jadwal otomatis di folder `.github` mencegahnya.

1. Di repository, buka **Settings > Secrets and variables > Actions > New repository secret**.
2. Buat dua secret: `SUPABASE_URL` (isi Project URL) dan `SUPABASE_ANON_KEY` (isi kunci anon).
3. Buka tab **Actions**, pilih **Jaga Supabase tetap aktif**, tekan **Run workflow** sekali untuk mencoba. Hasil yang benar: centang hijau.

GitHub menonaktifkan jadwal otomatis pada repository yang tidak ada aktivitas selama 60 hari. Jika itu terjadi, tab Actions menampilkan tombol untuk mengaktifkannya lagi.

## Langkah 7. Uji dengan dua perangkat

1. Buka website di HP dan laptop, login keduanya.
2. Salin satu kode di HP. Angka di laptop harus ikut berubah tanpa refresh.
3. Di pojok kanan atas tertulis **Realtime**. Jika tertulis **Terputus**, data dimuat ulang otomatis begitu koneksi kembali.

## Cara kerja singkat

- Setiap perubahan baru dianggap tersimpan setelah server menjawab berhasil. Jika koneksi gagal, muncul pesan merah dan data tidak berubah, jadi tidak ada data yang tampak tersimpan padahal belum.
- Hitungan salin dinaikkan langsung di database. Dua orang yang menyalin bersamaan tidak saling menimpa.
- Pangkalan yang dinonaktifkan hilang dari semua menu, tetapi kode dan hasilnya tetap tersimpan. Aktifkan lagi dari **Daftar Pangkalan > Nonaktif**.
- Hapus permanen hanya bisa untuk pangkalan yang sudah nonaktif dan butuh kode. Kode pelanggan dan hasil pengerjaannya ikut terhapus.
- **Unduh Cadangan** di menu menyimpan semua data ke file Excel. Paket gratis tidak punya cadangan otomatis, jadi unduh berkala (misalnya tiap minggu).

## Batas paket gratis

Database 500 MB, sekitar 200 koneksi realtime bersamaan, dan 50.000 pengguna aktif bulanan. Semuanya jauh di atas kebutuhan website ini.
