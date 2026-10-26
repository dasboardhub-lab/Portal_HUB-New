# Portal HUB — GitHub Pages + Supabase

Portal operasional HUB Kalsulamp dengan 33 contoh layanan, pencarian, filter HUB, favorit, riwayat akses, pengumuman, kategori, dan ekspor/impor cadangan.

**Buka `PANDUAN.html` untuk panduan pemasangan dengan tombol salin SQL.** Project URL dan publishable key yang diberikan sudah dikonfigurasi. Tidak diperlukan secret key atau password database.

## 1. Siapkan database Supabase

1. Buka proyek `svmgwupvjybfmatjfafp` di [Dashboard Supabase](https://supabase.com/dashboard/project/svmgwupvjybfmatjfafp).
2. Buka **SQL Editor**, buat query baru, lalu jalankan seluruh isi `setup-supabase.sql`.
3. Instalasi membuat lima tabel berawalan `hub_portal_`, aturan RLS, fungsi pembacaan dan perubahan data, serta 33 layanan contoh.
4. Data aplikasi lain dalam proyek tersebut tetap dipertahankan. Menjalankan ulang installer tidak mengembalikan layanan yang sudah sengaja dihapus atau menimpa perubahan informasi portal.

## 2. Buat akun admin

1. Buka **Authentication → Users**. Buat akun dengan email dan password milik Anda, atau gunakan akun yang sudah ada. Pastikan email akun sudah dikonfirmasi sebelum login.
2. Buka `aktifkan-admin.sql`. Ganti `GANTI_EMAIL_ADMIN` dengan email akun tadi.
3. Jalankan isi file tersebut melalui SQL Editor. Skrip memberikan pesan error jika email tidak ditemukan.
4. Gunakan tombol **Masuk portal** di website. Setelah berhasil masuk sebagai admin, tombol **Kelola portal** tersedia.

Tidak ada akun admin atau password bawaan. Pengguna yang sekadar berhasil mendaftar atau masuk tidak otomatis memperoleh akses portal. Untuk menambahkan tim, buat akun pada Authentication → Users dan jalankan `tambahkan-anggota.sql` dengan email anggota tersebut. Anggota dapat membaca; admin dapat mengelola.

## 3. Upload ke GitHub

Repository tujuan: [dasboardhub-lab/Portal_HUB-New](https://github.com/dasboardhub-lab/Portal_HUB-New).

1. Ekstrak ZIP jika Anda menggunakan paket unduhan.
2. Buka repository, pilih **Add file → Upload files**.
3. Upload seluruh isi folder proyek, termasuk folder **`docs`** dan **`docs/assets`**, kemudian commit perubahan.
4. Buka **Settings → Pages**.
5. Pilih **Deploy from a branch**, branch **main**, folder **/docs**, lalu **Save**.
6. Gunakan URL yang ditampilkan GitHub setelah penerbitan selesai.

Folder `docs` sudah berisi hasil build. Anda tidak perlu menjalankan npm untuk pemasangan awal ini. `index.html` di akar proyek adalah kode sumber; website yang diterbitkan adalah **`docs/index.html`**.

Jika repository privat dan paket GitHub Anda tidak menyediakan Pages untuk repository privat, gunakan paket yang mendukungnya atau hosting statis lain untuk folder `docs`. Jangan mengubah visibilitas repository tanpa mempertimbangkan akses yang diinginkan.

## Penyimpanan dan hak akses

- Link, kategori, pengumuman, dan log perubahan tersimpan di Supabase, sehingga perubahan tersedia lintas perangkat.
- Favorit dan riwayat akses tersimpan di browser masing-masing pengguna.
- Tampilan statis website dapat diakses melalui URL GitHub Pages. Daftar link operasional hanya dikirim setelah pengguna masuk dan tercatat sebagai anggota aktif portal.
- Anggota ditentukan dalam `hub_portal_members`; peran admin tidak dibaca dari metadata akun yang dapat diubah pengguna.
- Tabel memakai RLS. Akun frontend tidak memperoleh izin menulis tabel secara langsung. Perubahan melalui fungsi database yang memeriksa admin, memvalidasi data, dan mencatat aktivitas.
- Kode privileged berada dalam schema `hub_portal_private`, bukan schema API publik. Jangan tambahkan schema ini ke daftar exposed schemas pada Data API.
- Publishable key memang digunakan di browser. Jangan memasukkan secret key, service_role key, atau password database ke file proyek.

## Cadangan dan data portal sebelumnya

Admin dapat membuka **Kelola portal → Pengaturan portal → Cadangan** untuk mengunduh atau memulihkan file JSON. Impor mengganti layanan dan kategori secara atomik; akun serta informasi portal tetap dipertahankan. Cadangan mendukung maksimal 500 layanan, 40 kategori, dan ukuran payload 1 MB.

Jika Anda sudah mengisi link pada portal sebelumnya, ekspor cadangan dari sana lalu impor setelah masuk sebagai admin di portal ini. Pengumuman dapat disalin melalui pengaturan portal. Jangan menyimpan cadangan berisi informasi internal tim di repository publik.

## Mengembangkan kode

Gunakan Node.js 22.13+ dan pnpm. Versi dependensi dikunci dalam `package.json` dan `pnpm-lock.yaml`.

```sh
pnpm install --frozen-lockfile
pnpm run dev
pnpm run check
pnpm test
pnpm run build
```

Build memperbarui folder `docs`. Setelah mengubah kode sumber, commit sumber beserta hasil build `docs` agar GitHub Pages menampilkan versi terbaru. Untuk mengganti konfigurasi proyek, ubah `public/config.js`, jalankan build, lalu commit hasilnya. Konfigurasi runtime website ada pada `docs/config.js`.

## Pemeriksaan yang dilakukan

TypeScript dan build produksi; delapan pengujian integrasi menggunakan PostgreSQL lokal melalui PGlite: RLS semua tabel, pembatasan anggota, pencegahan eskalasi peran, validasi URL dan beberapa tombol akses, kategori, transaksi pemulihan cadangan, perlindungan kategori terakhir, serta installer yang mempertahankan perubahan.

Uji lokal tidak menggantikan penerapan dan pemeriksaan pada proyek Supabase Anda. Instalasi SQL, pembuatan akun admin, dan penerbitan GitHub Pages tetap harus berhasil sebelum portal siap dipakai tim.
