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

## 7. Contoh Perintah dan Output Terminal

Contoh berikut menunjukkan bagaimana perintah dan hasilnya terlihat di terminal. Baris setelah prompt adalah output. Nilai seperti waktu, versi sistem, kapasitas, dan nama perangkat di bawah hanya ilustrasi; hasil di servermu akan berbeda.

### Identitas pengguna dan navigasi riwayat

```console
ademulyana@ademulyana-server:~$ whoami
ademulyana

ademulyana@ademulyana-server:~$ id
uid=1000(ademulyana) gid=1000(ademulyana) groups=1000(ademulyana),27(sudo)

ademulyana@ademulyana-server:~$ pwd
/home/ademulyana

ademulyana@ademulyana-server:~$ history
  1  whoami
  2  id
  3  pwd
  4  history
```

Di shell, tekan **panah atas** untuk memanggil perintah sebelumnya dan **panah bawah** untuk bergerak kembali ke perintah yang lebih baru. Untuk menggulir tampilan terminal, gunakan roda mouse atau `Shift+Page Up` dan `Shift+Page Down` jika didukung aplikasi terminal.

### Masuk dan keluar dari shell root

```console
ademulyana@ademulyana-server:~$ sudo -i
[sudo] password for ademulyana:
root@ademulyana-server:~# whoami
root

root@ademulyana-server:~# exit
logout
ademulyana@ademulyana-server:~$
```

Kata sandi tidak terlihat saat diketik. Gunakan `sudo -i` hanya saat perlu bekerja sebagai root, lalu `exit` untuk kembali ke pengguna biasa.

### Informasi server dan waktu

```console
ademulyana@ademulyana-server:~$ hostnamectl
 Static hostname: ademulyana-server
       Icon name: computer
         Chassis: server
      Machine ID: 0123456789abcdef0123456789abcdef
         Boot ID: abcdef0123456789abcdef0123456789
Operating System: Ubuntu 24.04 LTS
          Kernel: Linux 6.8.0-generic
    Architecture: x86-64

ademulyana@ademulyana-server:~$ date
Sat Oct 24 09:30:00 WIB 2026

ademulyana@ademulyana-server:~$ timedatectl
               Local time: Sat 2026-10-24 09:30:00 WIB
           Universal time: Sat 2026-10-24 02:30:00 UTC
                 RTC time: Sat 2026-10-24 02:30:00
                Time zone: Asia/Jakarta (WIB, +0700)
System clock synchronized: yes
              NTP service: active
```

Output `hostnamectl` dan `timedatectl` dapat berbeda menurut versi Ubuntu, zona waktu, dan konfigurasi server.

### Pemeriksaan perangkat keras dan penyimpanan

```console
ademulyana@ademulyana-server:~$ lscpu
Architecture:             x86_64
CPU(s):                   2
Model name:               Example Virtual CPU
```

```console
ademulyana@ademulyana-server:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
vda    252:0    0   25G  0 disk
├─vda1 252:1    0 24.9G  0 part /
└─vda2 252:2    0  100M  0 part /boot/efi
```

```console
ademulyana@ademulyana-server:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        25G  5.2G   19G  22% /
tmpfs           400M     0  400M   0% /dev/shm
```

Nama disk, partisi, ukuran, dan filesystem akan mengikuti perangkat serta konfigurasi server. `lsblk` menampilkan struktur disk dan partisi, sedangkan `df -h` menunjukkan penggunaan filesystem yang terpasang.

### Pemeriksaan kondisi server dan proses

```console
ademulyana@ademulyana-server:~$ uptime
 09:30:00 up 2 days,  3:12,  1 user,  load average: 0.05, 0.03, 0.01

ademulyana@ademulyana-server:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           1.9Gi       620Mi       410Mi        12Mi       1.0Gi       1.1Gi
Swap:          1.0Gi          0B       1.0Gi
```

Melihat beberapa proses yang sedang berjalan:

```console
ademulyana@ademulyana-server:~$ ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.3  22500 12000 ?        Ss   09:00   0:02 /sbin/init
ademulyana   820  0.0  0.1  11000  5000 pts/0    R+   09:30   0:00 ps aux
```

Untuk tampilan proses yang diperbarui secara langsung:

```console
ademulyana@ademulyana-server:~$ top
top - 09:30:00 up 2 days,  3:12,  1 user,  load average: 0.05, 0.03, 0.01
Tasks: 120 total,   1 running, 119 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.0 us,  1.0 sy, 97.0 id
MiB Mem :  1900.0 total,  410.0 free,  490.0 used, 1000.0 buff/cache
```

Tekan `q` untuk keluar dari `top`. Nilai contoh di atas tidak menggambarkan server tertentu; perintah menampilkan keadaan sistem saat dijalankan.

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
