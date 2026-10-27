# Package Management

Bab ini menjelaskan cara mengelola perangkat lunak di Ubuntu Server menggunakan APT dan `dpkg`. APT mengunduh serta mengelola paket dari repository, sedangkan `dpkg` bekerja langsung dengan paket Debian berformat `.deb`.

## Tujuan Pembelajaran

Setelah mempelajari bagian ini, kamu diharapkan dapat:

- Memahami paket dan repository Ubuntu.
- Mencari informasi paket sebelum memasangnya.
- Memperbarui daftar paket dan memasang pembaruan.
- Memasang, memeriksa, dan menghapus paket.
- Memahami kapan menggunakan APT dan `dpkg`.

## 1. Paket dan Repository

**Paket** adalah arsip perangkat lunak yang berisi program, metadata, dan instruksi instalasi. Ubuntu menggunakan format paket Debian dengan ekstensi `.deb`.

**Repository** adalah sumber paket yang dikelola dan menyediakan paket beserta metadata. APT menggunakan daftar repository yang dikonfigurasi pada sistem, mengunduh daftar paket, menangani dependensi, lalu memasang atau memperbarui paket.

APT biasanya menjadi pilihan utama untuk mengelola paket di Ubuntu karena dapat memasang dependensi secara otomatis. `dpkg` berguna untuk memeriksa atau memasang berkas `.deb` lokal, tetapi tidak menyelesaikan seluruh dependensi seperti APT.

## 2. APT

### Memperbarui daftar paket: `apt update`

```bash
sudo apt update
```

Mengunduh informasi terbaru tentang paket dari repository. Perintah ini **tidak** memasang atau memperbarui paket yang sudah terpasang.

### Memasang pembaruan: `apt upgrade`

```bash
sudo apt upgrade
```

Memasang versi baru paket yang sudah terpasang berdasarkan daftar paket terbaru. APT akan menampilkan ringkasan perubahan dan meminta konfirmasi jika diperlukan. Baca daftar paket serta kebutuhan ruang disk sebelum menyetujui.

Alur pembaruan yang umum:

```bash
sudo apt update
sudo apt upgrade
```

Pada server penting atau produksi, jadwalkan pembaruan dan tinjau dampaknya terlebih dahulu. Beberapa pembaruan dapat memerlukan restart layanan atau sistem.

### Mencari paket: `apt search`

```bash
apt search nginx
```

Mencari nama atau deskripsi paket yang cocok dengan kata kunci. Hasil pencarian dapat banyak; periksa deskripsi dan sumber paket sebelum memasang.

### Melihat rincian paket: `apt show`

```bash
apt show nginx
```

Menampilkan informasi seperti versi, deskripsi, dependensi, ukuran unduhan, dan repository sumber paket.

### Memasang paket: `apt install`

```bash
sudo apt install nginx
```

APT menghitung dependensi yang diperlukan dan menampilkan paket yang akan dipasang. Baca ringkasan lalu konfirmasikan jika sudah sesuai.

Memasang beberapa paket sekaligus:

```bash
sudo apt install curl htop
```

Untuk memasang berkas `.deb` lokal sekaligus meminta APT menyelesaikan dependensinya:

```bash
sudo apt install ./nama-paket.deb
```

Gunakan berkas dari sumber tepercaya. Paket lokal dapat menambahkan perangkat lunak atau layanan dengan hak akses sistem.

### Melihat paket yang terpasang

Mencari paket tertentu di daftar paket:

```bash
apt list --installed
apt list --installed | grep nginx
```

Untuk melihat informasi suatu paket:

```bash
dpkg -s nginx
```

Perintah `dpkg -s` menampilkan status serta informasi paket jika paket tersebut dikenal oleh `dpkg`.

### Menghapus paket: `remove` dan `purge`

Menghapus paket tetapi mempertahankan file konfigurasi sistem yang terkait:

```bash
sudo apt remove nginx
```

Menghapus paket beserta file konfigurasi paket yang dikelola sistem:

```bash
sudo apt purge nginx
```

Periksa terlebih dahulu paket yang akan dihapus. Menghapus aplikasi server dapat menghentikan layanan yang digunakan pengguna atau aplikasi lain. File data yang dibuat layanan belum tentu ikut terhapus.

### Membersihkan dependensi yang tidak diperlukan: `apt autoremove`

```bash
sudo apt autoremove
```

Menghapus paket dependensi yang dipasang otomatis dan tidak lagi dibutuhkan oleh paket lain. Baca daftar yang akan dihapus sebelum menyetujui.

### Mengunduh ulang metadata/cache

```bash
sudo apt clean
sudo apt autoclean
```

- `apt clean` menghapus arsip paket yang diunduh dari cache lokal.
- `apt autoclean` menghapus arsip paket lama yang tidak lagi dapat diunduh.

Perintah tersebut tidak menghapus program yang sudah terpasang, tetapi setelah cache dibersihkan, paket perlu diunduh kembali jika diperlukan.

## 3. dpkg

`dpkg` adalah alat tingkat rendah untuk mengelola paket `.deb`. APT tetap lebih disarankan untuk instalasi rutin karena menangani dependensi dari repository.

### Memeriksa paket terpasang: `dpkg -l`

```bash
dpkg -l
dpkg -l nginx
```

Menampilkan daftar paket yang diketahui `dpkg` atau mencari status paket tertentu. Untuk hasil yang lebih ringkas, dapat digunakan `dpkg -s nama-paket`.

### Melihat file yang dipasang paket: `dpkg -L`

```bash
dpkg -L nginx
```

Menampilkan daftar file yang terdaftar sebagai bagian dari paket yang terpasang.

### Menemukan paket pemilik file: `dpkg -S`

```bash
dpkg -S /usr/sbin/nginx
```

Mencari paket yang memasang file tersebut. File yang dibuat setelah instalasi atau tidak dikelola paket mungkin tidak memiliki hasil.

### Memasang berkas `.deb`: `dpkg -i`

```bash
sudo dpkg -i ./nama-paket.deb
```

Memasang paket lokal, tetapi `dpkg` tidak mengunduh dependensi yang belum tersedia. Jika pemasangan gagal karena dependensi, cara yang biasanya lebih mudah adalah:

```bash
sudo apt install ./nama-paket.deb
```

Jika `dpkg -i` sudah dijalankan dan paket tertinggal dalam keadaan belum dikonfigurasi karena dependensi, perbaiki dependensi melalui APT:

```bash
sudo apt --fix-broken install
```

Tinjau perubahan yang akan dilakukan sebelum menyetujui.

## 4. Contoh Sesi Terminal

Contoh berikut menunjukkan alur memperbarui daftar paket, mencari paket, memeriksa informasi, lalu memasang paket. Output bersifat ilustrasi; versi, ukuran unduhan, jumlah paket, dan pesan dapat berbeda di servermu.

```console
ademulyana@ademulyana-server:~$ sudo apt update
Hit:1 http://archive.ubuntu.com/ubuntu noble InRelease
Get:2 http://archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]
Reading package lists... Done
Building dependency tree... Done
All packages are up to date.

ademulyana@ademulyana-server:~$ apt search htop
Sorting... Done
Full Text Search... Done
htop/noble 3.3.0-4build1 amd64
  interactive processes viewer

ademulyana@ademulyana-server:~$ apt show htop
Package: htop
Version: 3.3.0-4build1
Architecture: amd64
Description: interactive processes viewer

ademulyana@ademulyana-server:~$ sudo apt install htop
The following NEW packages will be installed:
  htop
Do you want to continue? [Y/n] y
Setting up htop ...

ademulyana@ademulyana-server:~$ dpkg -s htop
Status: install ok installed
```

### Contoh menghapus paket

Berikut contoh ilustratif. Pastikan paket tidak sedang dibutuhkan sebelum menjalankan perintah penghapusan.

```console
ademulyana@ademulyana-server:~$ sudo apt remove htop
The following packages will be REMOVED:
  htop
Do you want to continue? [Y/n] y
Removing htop ...

ademulyana@ademulyana-server:~$ dpkg -s htop
dpkg-query: package 'htop' is not installed
```

Perintah APT yang berhasil tidak selalu menampilkan output persis seperti contoh. Baca ringkasan perubahan yang ditampilkan APT sebelum memasukkan `y`.

## 5. Kebiasaan Aman

- Pasang paket dari repository resmi atau sumber yang tepercaya.
- Jalankan `sudo apt update` sebelum mencari atau memasang versi terbaru dari repository.
- Baca daftar paket yang akan dipasang, diperbarui, atau dihapus sebelum menyetujuinya.
- Hindari mengunduh dan memasang berkas `.deb` dari sumber yang tidak dikenal.
- Pada server produksi, jadwalkan pembaruan dan siapkan prosedur pemulihan.
- Jangan menghapus file sistem secara manual untuk mencopot paket; gunakan APT atau `dpkg`.

## Ringkasan

- `apt update` memperbarui daftar paket; `apt upgrade` memasang pembaruan.
- `apt search` dan `apt show` membantu mencari serta memeriksa paket.
- `apt install`, `apt remove`, dan `apt purge` memasang atau menghapus paket.
- `apt autoremove` membersihkan dependensi otomatis yang tidak lagi diperlukan.
- `dpkg` memeriksa isi atau status paket dan memasang paket `.deb`, sementara APT lebih baik untuk mengelola dependensi.

## Selanjutnya

Lanjutkan ke [Remote Access](../08-remote-access/README.md) untuk mempelajari akses jarak jauh ke Ubuntu Server.
