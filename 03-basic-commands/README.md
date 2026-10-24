# Basic Commands

Bagian ini membahas perintah dasar untuk mengelola dan memeriksa Ubuntu Server melalui terminal. Urutannya dimulai dari navigasi terminal, identitas dan hak akses, lalu pemeriksaan waktu, perangkat keras, dan kondisi server.

## Tujuan Pembelajaran

Setelah mempelajari bagian ini, kamu diharapkan dapat:

- Menggunakan bantuan perintah dan navigasi riwayat di terminal.
- Mengenali pengguna aktif dan berpindah ke shell root dengan aman.
- Memeriksa identitas server, waktu, perangkat keras, dan penggunaan sumber daya.

## 1. Bantuan dan Bentuk Perintah

Perintah umumnya terdiri dari nama program, opsi, dan argumen:

```text
perintah [opsi] [argumen]
```

Contoh:

```bash
ls -l /var/log
```

Pada contoh tersebut, `ls` adalah perintah, `-l` adalah opsi untuk menampilkan daftar dalam format panjang, dan `/var/log` adalah argumen berupa lokasi yang diperiksa.

Untuk melihat ringkasan opsi perintah:

```bash
ls --help
```

Untuk membaca dokumentasi yang lebih lengkap:

```bash
man ls
```

Tekan `q` untuk keluar dari halaman manual. Jika manual tidak tersedia, coba opsi `--help`.

## 2. Navigasi Terminal dan Riwayat Perintah

### Melihat lokasi dan isi direktori

`pwd` menampilkan direktori kerja saat ini:

```bash
pwd
```

`ls` menampilkan isi direktori:

```bash
ls
ls -l
ls -a
```

- `ls` menampilkan nama file dan direktori.
- `ls -l` menampilkan informasi lebih lengkap.
- `ls -a` juga menampilkan nama tersembunyi yang diawali titik.

Navigasi serta pengelolaan file dan direktori dibahas lebih lanjut pada [File and Directory Management](../04-file-and-directory-management/README.md).

### Menggunakan tombol panah

- Tekan **panah atas** untuk menampilkan perintah sebelumnya di riwayat terminal.
- Tekan **panah bawah** untuk bergerak kembali ke perintah yang lebih baru setelah menekan panah atas.
- Untuk menggulir keluaran terminal, biasanya gunakan roda mouse atau `Shift+Page Up` dan `Shift+Page Down` (tergantung aplikasi terminal).
- Di program seperti `man` atau `top`, tombol panah atas dan bawah dapat digunakan untuk menggulir tampilan. Tekan `q` untuk keluar dari program tersebut.

Riwayat perintah juga dapat ditampilkan dengan:

```bash
history
```

Riwayat mungkin berisi informasi sensitif jika informasi tersebut pernah diketik sebagai bagian dari perintah. Periksa isinya sebelum membagikan keluaran.

## 3. Identitas Pengguna dan Akses Root

### Memeriksa pengguna

`whoami` menampilkan nama pengguna yang sedang aktif:

```bash
whoami
```

`id` menampilkan ID pengguna dan grup:

```bash
id
```

### Masuk ke shell root

Di Ubuntu, gunakan `sudo -i` untuk membuka shell root:

```bash
sudo -i
```

Masukkan kata sandi akunmu jika diminta. Prompt biasanya berubah dari `$` menjadi `#`, dan `whoami` akan menampilkan `root`.

Gunakan hak root hanya saat diperlukan. Perintah yang dijalankan sebagai root dapat mengubah atau menghapus data sistem tanpa perlindungan izin pengguna biasa.

### Keluar dari shell root

Untuk kembali ke akun semula, jalankan:

```bash
exit
```

Kamu juga dapat menekan `Ctrl+D`. Setelah keluar, prompt kembali ke shell pengguna biasa. Untuk menjalankan satu perintah administratif saja, lebih baik gunakan `sudo nama-perintah` daripada terus bekerja sebagai root.

## 4. Identitas Server dan Waktu

### Memeriksa atau mengubah hostname

`hostnamectl` menampilkan informasi hostname dan sistem operasi:

```bash
hostnamectl
```

Untuk mengubah hostname, jalankan perintah berikut dengan nama yang diinginkan:

```bash
sudo hostnamectl set-hostname nama-server
```

Ganti `nama-server` dengan hostname yang sesuai. Perubahan hostname sebaiknya mengikuti kebijakan penamaan server yang digunakan.

### Memeriksa waktu dan zona waktu

`date` menampilkan tanggal dan waktu saat ini:

```bash
date
```

`timedatectl` menampilkan waktu, zona waktu, dan status sinkronisasi waktu:

```bash
timedatectl
```

Untuk melihat daftar zona waktu yang tersedia:

```bash
timedatectl list-timezones
```

Pengaturan zona waktu dapat memengaruhi log dan jadwal tugas server. Ubah hanya jika diperlukan dan gunakan zona waktu yang benar.

## 5. Memeriksa Perangkat Keras dan Penyimpanan

### Informasi prosesor

`lscpu` menampilkan ringkasan arsitektur dan prosesor:

```bash
lscpu
```

### Perangkat penyimpanan

`lsblk` menampilkan perangkat blok seperti disk dan partisi:

```bash
lsblk
```

Perintah ini hanya menampilkan informasi; tidak mengubah partisi atau data.

### Ruang filesystem

`df -h` menampilkan kapasitas, ruang terpakai, dan ruang tersedia pada filesystem dalam format yang mudah dibaca:

```bash
df -h
```

`df` menampilkan penggunaan filesystem yang terpasang. Untuk melihat struktur disk dan partisi, gunakan `lsblk`.

## 6. Pemeriksaan Kondisi Server

### Lama server berjalan dan beban sistem

`uptime` menampilkan lama sistem berjalan, jumlah pengguna yang masuk, dan rata-rata beban sistem:

```bash
uptime
```

### Penggunaan memori

`free -h` menampilkan penggunaan memori fisik dan swap:

```bash
free -h
```

Kolom `available` memberikan perkiraan memori yang masih dapat digunakan aplikasi tanpa perlu mulai melakukan swap secara berlebihan.

### Proses yang berjalan

`ps aux` menampilkan proses yang berjalan di sistem:

```bash
ps aux
```

`top` menampilkan proses dan penggunaan sumber daya secara langsung:

```bash
top
```

Tekan `q` untuk keluar dari `top`.

### Membersihkan tampilan terminal

`clear` membersihkan tampilan terminal, tetapi tidak menghapus riwayat perintah:

```bash
clear
```

## Kebiasaan Aman

- Baca dokumentasi perintah dengan `--help` atau `man` jika belum memahaminya.
- Gunakan root hanya untuk tugas yang memerlukan hak administrator.
- Periksa perintah dan argumen sebelum menjalankannya dengan `sudo`.
- Jangan membagikan keluaran terminal sebelum memeriksa kemungkinan informasi sensitif.

## Ringkasan

- Gunakan `pwd`, `ls`, tombol panah, dan `history` untuk membantu navigasi terminal.
- Gunakan `sudo -i` untuk masuk ke shell root dan `exit` atau `Ctrl+D` untuk keluar.
- Gunakan `hostnamectl` dan `timedatectl` untuk memeriksa identitas server dan waktu.
- Gunakan `lscpu`, `lsblk`, `df -h`, `free -h`, `uptime`, `ps aux`, dan `top` untuk memeriksa perangkat serta kondisi server.

## Selanjutnya

Lanjutkan ke [File and Directory Management](../04-file-and-directory-management/README.md) untuk mempelajari navigasi serta pengelolaan file dan direktori.
