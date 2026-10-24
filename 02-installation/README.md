# Instalasi Ubuntu Server

Panduan ini menjelaskan cara memasang Ubuntu Server pada komputer fisik. Ubuntu Server juga dapat dipasang di mesin virtual untuk latihan; langkah installer pada dasarnya serupa.

Ubuntu Server umumnya berjalan tanpa antarmuka desktop grafis dan dikelola melalui terminal, baik secara langsung maupun melalui SSH. Pastikan memilih edisi **Ubuntu Server**, bukan Ubuntu Desktop.

## Persiapan

Siapkan hal-hal berikut:

- Komputer/server yang memenuhi persyaratan perangkat keras untuk rilis Ubuntu Server yang dipilih.
- Koneksi internet, jika tersedia, untuk mengunduh installer dan pembaruan.
- Flash drive kosong (disarankan minimal 8 GB).
- Komputer lain untuk mengunduh berkas ISO dan membuat media instalasi.
- Cadangan data penting dari komputer tujuan instalasi.

> **Peringatan:** Membuat USB bootable akan menghapus isi flash drive. Pilihan instalasi yang menggunakan seluruh disk juga akan menghapus data pada disk tujuan. Pastikan media dan disk yang dipilih benar, serta cadangkan data sebelum melanjutkan.

## 1. Unduh Ubuntu Server

Unduh berkas ISO dari [halaman resmi Ubuntu Server](https://ubuntu.com/download/server). Pilih rilis yang sesuai dengan perangkat keras dan kebutuhan. Untuk pemakaian jangka panjang, pertimbangkan rilis LTS dan periksa masa dukungannya di situs Ubuntu.

## 2. Buat USB Bootable

Gunakan aplikasi pembuat USB bootable, misalnya:

- **Windows:** Rufus atau balenaEtcher.
- **Ubuntu Desktop:** Startup Disk Creator atau balenaEtcher.
- **macOS:** balenaEtcher.

Pilih berkas ISO Ubuntu Server dan flash drive yang benar di aplikasi, lalu mulai proses penulisan. Proses ini menghapus data pada flash drive yang dipilih. Setelah selesai, keluarkan USB dengan aman.

## 3. Boot dari USB

1. Sambungkan USB bootable ke komputer tujuan.
2. Nyalakan atau mulai ulang komputer.
3. Buka menu boot perangkat (tombolnya bergantung pada pabrikan, sering kali `F12`, `F11`, `Esc`, atau `Del`).
4. Pilih USB sebagai perangkat boot.
5. Jika USB tidak muncul, periksa petunjuk pabrikan untuk pengaturan UEFI/BIOS dan urutan boot.

## 4. Jalankan Installer

Ikuti layar installer. Nama atau urutan menu bisa sedikit berbeda antarversi Ubuntu Server.

1. **Bahasa dan keyboard** — pilih bahasa installer serta tata letak keyboard yang sesuai.
2. **Jenis instalasi** — pilih Ubuntu Server standar, kecuali ada kebutuhan khusus untuk instalasi minimal.
3. **Jaringan** — pilih antarmuka jaringan yang tersedia. DHCP biasanya mengatur alamat IP secara otomatis. Atur IP statis hanya jika memang diperlukan dan kamu mengetahui konfigurasi jaringan yang benar.
4. **Proxy** — biarkan kosong jika jaringan tidak menggunakan proxy.
5. **Mirror arsip Ubuntu** — gunakan nilai bawaan, kecuali organisasi atau jaringan menyediakan mirror khusus.
6. **Penyimpanan** — pilih disk dan tata letak partisi.
   - Opsi **gunakan seluruh disk** akan menghapus data pada disk yang dipilih.
   - Partisi manual ditujukan untuk pengguna yang memahami partisi, mount point, dan kebutuhan boot perangkat.
   - Opsi LVM atau enkripsi disk bergantung pada kebutuhan. Jika mengaktifkan enkripsi, simpan kata sandi pemulihan dengan aman; kehilangan kata sandi dapat membuat data tidak dapat diakses.
7. **Profil pengguna** — isi nama, nama server/hostname, nama pengguna, dan kata sandi yang kuat. Simpan kredensial dengan aman.
8. **SSH** — aktifkan OpenSSH Server jika server akan dikelola dari komputer lain. Gunakan autentikasi kunci SSH bila memungkinkan dan jangan membagikan private key.
9. **Paket tambahan** — pilih paket atau *featured snaps* hanya jika dibutuhkan; paket lain dapat dipasang setelah instalasi.
10. **Konfirmasi** — baca ringkasan perubahan disk dengan teliti. Lanjutkan hanya jika disk dan tindakan yang ditampilkan sudah benar.

Installer akan menyalin sistem ke disk. Tunggu hingga proses selesai.

## 5. Selesaikan Instalasi dan Masuk

1. Pilih **Reboot Now** setelah installer menyatakan instalasi selesai.
2. Lepaskan USB ketika diminta, agar komputer tidak kembali masuk ke installer.
3. Setelah sistem menyala, masuk menggunakan nama pengguna dan kata sandi yang dibuat saat instalasi.

Ubuntu Server biasanya menampilkan antarmuka teks. Ini normal dan bukan tanda bahwa instalasi gagal.

## 6. Pemeriksaan Awal

Setelah masuk, perbarui daftar paket dan pasang pembaruan yang tersedia:

```bash
sudo apt update
sudo apt upgrade
```

Periksa informasi sistem dan alamat IP:

```bash
hostnamectl
ip address
```

Jika OpenSSH Server dipasang, alamat IP dapat digunakan untuk mencoba koneksi dari komputer lain di jaringan yang sama:

```bash
ssh nama-pengguna@alamat-ip-server
```

Ganti `nama-pengguna` dan `alamat-ip-server` dengan nilai yang sesuai. Pastikan akses SSH diizinkan oleh firewall dan kebijakan jaringan. Untuk server yang dapat diakses dari internet, lakukan pengamanan tambahan sebelum membuka layanan ke publik.

## Jika Instalasi Tidak Berjalan

- **Komputer tidak boot dari USB:** periksa menu boot dan urutan boot di pengaturan UEFI/BIOS, lalu buat ulang USB bila perlu.
- **Installer tidak mendeteksi jaringan:** periksa kabel, port, atau koneksi jaringan; beberapa perangkat mungkin memerlukan konfigurasi tambahan.
- **Disk tidak terlihat atau instalasi gagal:** periksa kompatibilitas perangkat dan pengaturan penyimpanan di UEFI/BIOS. Jangan ubah konfigurasi disk tanpa memahami dampaknya.
- **Tidak yakin dengan pilihan partisi:** hentikan proses sebelum mengonfirmasi perubahan disk, lalu minta bantuan administrator atau rujuk dokumentasi resmi.

## Selanjutnya

Lanjutkan ke [Basic Commands](../03-basic-commands/README.md) untuk mempelajari perintah terminal yang berguna saat mengelola Ubuntu Server.
