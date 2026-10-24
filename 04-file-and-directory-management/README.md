# File and Directory Management

Bab ini menjelaskan hierarki filesystem Linux serta perintah untuk menavigasi, melihat, membuat, menyalin, memindahkan, dan menghapus file maupun direktori di Ubuntu Server.

## Tujuan Pembelajaran

Setelah mempelajari bagian ini, kamu diharapkan dapat:

- Membaca struktur direktori utama Linux.
- Memahami path absolut dan relatif.
- Berpindah antar-direktori dan melihat isi direktori.
- Mengelola file serta direktori dengan perintah terminal.
- Menghindari perubahan atau penghapusan data yang tidak disengaja.

## 1. Filesystem Hierarchy

Linux menyusun file dan direktori dalam satu pohon yang berawal dari direktori root, ditulis sebagai `/`. Direktori root `/` berbeda dengan direktori home pengguna root, yaitu `/root`.

Diagram berikut menunjukkan direktori penting yang umum ditemukan pada Ubuntu Server. Isi persisnya dapat berbeda tergantung sistem dan paket yang dipasang.

```text
/
├── bin       Perintah dasar pengguna (pada Ubuntu modern biasanya bagian dari /usr)
├── boot      Berkas yang diperlukan untuk proses boot, termasuk kernel
├── dev       Berkas perangkat, seperti disk dan terminal
├── etc       Konfigurasi sistem dan layanan
├── home      Direktori pribadi pengguna biasa
│   ├── ademulyana
│   └── bob
├── lib       Pustaka penting (pada Ubuntu modern biasanya bagian dari /usr)
├── media     Titik kait media lepasan yang dipasang otomatis
├── mnt       Titik kait sementara untuk filesystem
├── opt       Perangkat lunak tambahan dari pihak ketiga
├── proc      Informasi proses dan kernel virtual
├── root      Direktori pribadi administrator root
├── run       Data runtime sejak sistem berjalan
├── sbin      Perintah administrasi sistem (pada Ubuntu modern biasanya bagian dari /usr)
├── srv       Data yang disediakan oleh layanan server
├── sys       Informasi perangkat dan kernel virtual
├── tmp       Berkas sementara
├── usr       Aplikasi, pustaka, dokumentasi, dan data bersama
│   ├── bin   Banyak perintah pengguna
│   ├── lib   Pustaka aplikasi
│   ├── local Perangkat lunak yang dipasang secara lokal
│   └── sbin  Banyak perintah administrasi
└── var       Data yang sering berubah saat sistem berjalan
    ├── cache Data cache aplikasi
    ├── lib   Data status aplikasi dan layanan
    ├── log   Log sistem dan layanan
    └── tmp   Berkas sementara yang perlu bertahan antarproses
```

Pada Ubuntu modern, beberapa direktori seperti `/bin`, `/sbin`, dan `/lib` dapat berupa tautan ke direktori yang sesuai di dalam `/usr`. Karena itu, hasil `ls -l /bin` mungkin menunjukkan bahwa `/bin` adalah tautan simbolis, bukan direktori fisik yang berdiri sendiri.

### Direktori yang sering digunakan

| Direktori | Fungsi |
|---|---|
| `/` | Akar seluruh pohon filesystem. Semua file dan direktori berada di bawahnya. |
| `/home` | Berisi direktori pribadi pengguna biasa, misalnya `/home/ademulyana`. |
| `/root` | Direktori pribadi untuk pengguna administrator `root`. Bukan direktori akar `/`. |
| `/etc` | Menyimpan konfigurasi sistem dan layanan. Ubah file di sini dengan hati-hati dan hak akses yang sesuai. |
| `/var/log` | Menyimpan log sistem dan aplikasi, berguna untuk pemeriksaan serta pemecahan masalah. |
| `/var` | Data yang sering berubah, termasuk log, cache, dan data layanan. |
| `/usr` | Sebagian besar program, pustaka, dokumentasi, dan data bersama yang dipasang oleh sistem atau paket. |
| `/usr/local` | Lokasi yang lazim untuk perangkat lunak yang dipasang secara lokal, di luar pengelolaan paket distro. |
| `/tmp` | Lokasi berkas sementara. Berkas di dalamnya dapat dihapus oleh sistem sesuai kebijakan dan waktu pemeliharaan. |
| `/boot` | Berkas untuk proses boot, seperti kernel dan konfigurasi bootloader. |
| `/dev` | Antarmuka perangkat yang dikelola sistem, misalnya disk dan terminal. |
| `/proc` | Filesystem virtual yang menyediakan informasi proses dan kernel. Isinya bukan berkas biasa di disk. |
| `/sys` | Filesystem virtual untuk informasi perangkat dan kernel. |
| `/media` | Lokasi yang umum digunakan untuk memasang media lepasan seperti USB. |
| `/mnt` | Titik kait yang dapat digunakan administrator untuk memasang filesystem sementara. |
| `/opt` | Lokasi untuk perangkat lunak tambahan yang dipaketkan secara mandiri. |
| `/srv` | Data yang disediakan oleh layanan pada sistem, misalnya konten layanan tertentu. |
| `/run` | Data runtime sementara, seperti informasi proses layanan; biasanya dibuat ulang saat boot. |

## 2. Path dan Navigasi Direktori

### Path absolut dan relatif

**Path absolut** selalu dimulai dari `/` dan menunjukkan lokasi secara lengkap:

```text
/home/ademulyana/dokumen/catatan.txt
```

**Path relatif** dihitung dari direktori kerja saat ini dan tidak dimulai dengan `/`:

```text
dokumen/catatan.txt
```

Simbol path yang umum:

- `.` berarti direktori saat ini.
- `..` berarti direktori induk satu tingkat di atas.
- `~` berarti direktori home pengguna yang sedang aktif.
- `/` berarti direktori root filesystem.

### Menampilkan lokasi saat ini: `pwd`

```bash
pwd
```

Perintah ini menampilkan direktori kerja saat ini.

### Berpindah direktori: `cd`

```bash
cd /var/log
cd ..
cd ~
cd -
```

- `cd /var/log` berpindah ke path absolut `/var/log`.
- `cd ..` berpindah ke direktori induk.
- `cd ~` kembali ke direktori home pengguna.
- `cd -` kembali ke direktori sebelumnya.

### Melihat isi direktori: `ls`

```bash
ls
ls -l
ls -a
ls -lh
ls -lah /var/log
```

- `ls` menampilkan isi direktori.
- `ls -l` menampilkan daftar dalam format panjang, termasuk izin, pemilik, ukuran, dan waktu perubahan.
- `ls -a` menyertakan berkas tersembunyi yang namanya diawali titik.
- `ls -lh` menampilkan ukuran dalam format yang mudah dibaca.
- Opsi dapat digabungkan, seperti `ls -lah /var/log`.

## 3. File Management

### Mengenali jenis file: `file`

```bash
file nama-file
```

Menampilkan perkiraan jenis konten file. Hasilnya membantu mengenali file yang tidak memiliki ekstensi atau ekstensi yang tidak jelas.

### Melihat isi file

`cat` menampilkan seluruh isi file teks:

```bash
cat /etc/hostname
```

Untuk file panjang, gunakan `less` agar isi dapat dibaca per layar:

```bash
less /var/log/dpkg.log
```

Di dalam `less`, gunakan tombol panah atau `Page Up` dan `Page Down` untuk berpindah; tekan `q` untuk keluar.

Untuk melihat bagian awal atau akhir file:

```bash
head /var/log/dpkg.log
tail /var/log/dpkg.log
```

Secara bawaan, `head` dan `tail` menampilkan 10 baris. Jumlahnya dapat diatur, misalnya:

```bash
head -n 20 /var/log/dpkg.log
tail -n 20 /var/log/dpkg.log
```

Jika file log tersebut tersedia, `tail -f` dapat digunakan untuk memantau baris baru:

```bash
tail -f /var/log/syslog
```

Tekan `Ctrl+C` untuk berhenti memantau. File `/var/log/syslog` tidak tersedia pada semua instalasi. Ubuntu Server juga menyimpan log di systemd journal; untuk mengikutinya secara langsung, gunakan:

```bash
sudo journalctl -f
```

Tekan `Ctrl+C` untuk berhenti.

### Membuat file kosong: `touch`

```bash
touch catatan.txt
```

Membuat file kosong jika file tersebut belum ada. Jika file sudah ada, `touch` memperbarui waktu akses dan modifikasinya tanpa menghapus isi.

### Menyalin file: `cp`

```bash
cp sumber.txt salinan.txt
cp sumber.txt /tmp/sumber.txt
```

Menyalin file sumber ke file atau lokasi tujuan. Jika file tujuan sudah ada, isinya dapat tertimpa; periksa tujuan sebelum menjalankan perintah.

Untuk menyalin beberapa file ke sebuah direktori:

```bash
cp file1.txt file2.txt /tmp/
```

Untuk menyalin direktori beserta isinya, gunakan opsi rekursif:

```bash
cp -r direktori-sumber direktori-salinan
```

### Memindahkan atau mengganti nama: `mv`

```bash
mv draft.txt final.txt
mv laporan.txt /tmp/laporan.txt
```

`mv` dapat mengganti nama file atau memindahkannya ke direktori lain. File dengan nama tujuan yang sama dapat tertimpa, bergantung pada opsi dan konfigurasi. Periksa nama tujuan terlebih dahulu.

### Menghapus file: `rm`

```bash
rm -i file-lama.txt
```

Opsi `-i` meminta konfirmasi sebelum menghapus. Penghapusan melalui `rm` biasanya tidak memindahkan file ke Trash dan dapat sulit dipulihkan. Jangan menjalankan perintah penghapusan sebelum memastikan path dan nama file sudah benar. Hindari menjalankan perintah hapus secara rekursif dengan hak root jika belum memahami dampaknya.

### Mencari file: `find`

```bash
find ~/dokumen -name "*.txt"
```

Mencari file dengan akhiran `.txt` di dalam direktori `~/dokumen` beserta subdirektorinya. Tanda kutip menjaga pola `*.txt` agar diproses oleh `find`, bukan diperluas oleh shell.

## 4. Directory Management

### Membuat direktori: `mkdir`

```bash
mkdir proyek
```

Untuk membuat beberapa tingkat direktori sekaligus, gunakan `-p`:

```bash
mkdir -p ~/proyek/dokumentasi/linux
```

Opsi `-p` juga tidak menampilkan kesalahan jika direktori target sudah ada.

### Menyalin direktori: `cp -r`

```bash
cp -r proyek proyek-cadangan
```

Menyalin direktori `proyek` beserta isinya ke `proyek-cadangan`. Pastikan tujuan salinan yang dipilih benar dan memiliki ruang penyimpanan cukup.

### Memindahkan atau mengganti nama direktori: `mv`

```bash
mv proyek proyek-lama
mv proyek /tmp/proyek
```

Perintah pertama mengganti nama direktori. Perintah kedua memindahkannya ke `/tmp`.

### Menghapus direktori kosong: `rmdir`

```bash
rmdir direktori-kosong
```

`rmdir` hanya menghapus direktori yang kosong. Jika direktori masih berisi file atau subdirektori, perintah ini akan menolak menghapusnya.

Untuk menghapus direktori yang berisi konten, `rm -r` menghapus direktori beserta isinya:

```bash
rm -ri direktori-lama
```

Opsi `-i` meminta konfirmasi untuk item yang dihapus. Periksa path dengan teliti: file yang dihapus tidak otomatis masuk ke Trash. Hindari opsi `-f` dan penggunaan `sudo` untuk penghapusan rekursif kecuali benar-benar diperlukan dan dampaknya sudah dipahami.

### Menampilkan struktur direktori

`tree` menampilkan isi direktori sebagai pohon, tetapi mungkin belum terpasang secara bawaan:

```bash
tree ~/proyek
```

Jika `tree` tidak ditemukan, gunakan `ls -R` untuk menampilkan isi direktori dan subdirektorinya:

```bash
ls -R ~/proyek
```

## 5. Contoh Perintah dan Output Terminal

Berikut contoh sesi terminal yang menunjukkan perintah dan hasilnya. Teks setelah prompt `$` adalah perintah yang diketik; baris berikutnya adalah output. Nama pengguna, hostname, waktu, izin, ukuran, dan isi direktori pada servermu dapat berbeda.

### Melihat lokasi dan isi direktori

```console
ademulyana@ademulyana-server:~$ pwd
/home/ademulyana

ademulyana@ademulyana-server:~$ ls
dokumen  unduhan

ademulyana@ademulyana-server:~$ ls -la
total 20
drwxr-x--- 4 ademulyana ademulyana 4096 Oct 24 09:00 .
drwxr-xr-x 3 root       root       4096 Oct 24 08:30 ..
-rw------- 1 ademulyana ademulyana  220 Oct 24 08:30 .bash_history
drwxr-xr-x 2 ademulyana ademulyana 4096 Oct 24 09:00 dokumen
drwxr-xr-x 2 ademulyana ademulyana 4096 Oct 24 08:45 unduhan
```

### Membuat dan mengelola file serta direktori

Contoh berikut bekerja di direktori latihan dalam home pengguna. Perintah `echo` dengan tanda `>` menulis teks ke file dan mengganti isi file jika file tersebut sudah ada.

```console
ademulyana@ademulyana-server:~$ mkdir -p latihan-linux/dokumen
ademulyana@ademulyana-server:~$ cd latihan-linux
ademulyana@ademulyana-server:~/latihan-linux$ pwd
/home/ademulyana/latihan-linux

ademulyana@ademulyana-server:~/latihan-linux$ echo "Belajar mengelola file di Linux" > catatan.txt
ademulyana@ademulyana-server:~/latihan-linux$ cat catatan.txt
Belajar mengelola file di Linux

ademulyana@ademulyana-server:~/latihan-linux$ cp catatan.txt dokumen/salinan.txt
ademulyana@ademulyana-server:~/latihan-linux$ find . -type f
./catatan.txt
./dokumen/salinan.txt

ademulyana@ademulyana-server:~/latihan-linux$ mv catatan.txt catatan-lama.txt
ademulyana@ademulyana-server:~/latihan-linux$ ls -R
.:
catatan-lama.txt  dokumen

./dokumen:
salinan.txt
```

Perintah seperti `mkdir`, `cp`, `mv`, dan `touch` biasanya tidak menampilkan output saat berhasil. Gunakan `ls` atau `find` untuk memeriksa hasilnya.

Contoh menghapus file dengan konfirmasi:

```console
ademulyana@ademulyana-server:~/latihan-linux$ rm -i dokumen/salinan.txt
rm: remove regular file 'dokumen/salinan.txt'? y
ademulyana@ademulyana-server:~/latihan-linux$ find . -type f
./catatan-lama.txt
```

Contoh menghapus direktori yang sudah kosong:

```console
ademulyana@ademulyana-server:~/latihan-linux$ rmdir dokumen
ademulyana@ademulyana-server:~/latihan-linux$ ls
catatan-lama.txt
```

Output di atas hanya ilustrasi. Jangan menjalankan perintah penghapusan pada file atau direktori penting tanpa memeriksa path dan isinya terlebih dahulu.

## 6. Ukuran dan Metadata File

### Melihat ukuran file: `du`

```bash
du -sh ~/proyek
du -h ~/proyek/*
```

`du` memperkirakan ruang disk yang digunakan file atau direktori. Opsi `-s` menampilkan ringkasan, sedangkan `-h` menggunakan satuan yang mudah dibaca.

Contoh output:

```console
ademulyana@ademulyana-server:~$ du -sh ~/latihan-linux
8.0K    /home/ademulyana/latihan-linux
```

### Melihat metadata: `stat`

```bash
stat ~/latihan-linux/catatan-lama.txt
```

Menampilkan metadata seperti ukuran, izin, pemilik, dan waktu akses atau perubahan file.

Contoh output (nilai izin, pemilik, ukuran, dan waktu dapat berbeda):

```console
ademulyana@ademulyana-server:~$ stat ~/latihan-linux/catatan-lama.txt
  File: /home/ademulyana/latihan-linux/catatan-lama.txt
  Size: 32         Blocks: 8          IO Block: 4096   regular file
Device: 8,1         Inode: 123456      Links: 1
Access: (0644/-rw-r--r--)  Uid: (1000/ademulyana)   Gid: (1000/ademulyana)
```

## Catatan Keamanan dan Izin

File dan direktori memiliki izin akses yang menentukan siapa yang dapat membaca, mengubah, atau menjalankannya. Jika suatu operasi ditolak, jangan langsung menambahkan `sudo`; pastikan dahulu alasan izin ditolak dan pemilik file yang benar. Pengguna, grup, dan izin dibahas pada [Users and Permissions](../05-users-and-permissions/README.md).

## Ringkasan

- Struktur filesystem Linux berawal dari `/`; direktori seperti `/etc`, `/home`, `/var`, dan `/usr` memiliki fungsi berbeda.
- `pwd`, `cd`, dan `ls` membantu melihat lokasi, berpindah, dan menampilkan isi direktori.
- `cat`, `less`, `head`, `tail`, `touch`, `cp`, `mv`, `rm`, dan `find` digunakan untuk bekerja dengan file.
- `mkdir`, `rmdir`, `cp -r`, `mv`, dan `rm -ri` digunakan untuk mengelola direktori.
- Selalu periksa path dan nama tujuan sebelum menyalin, memindahkan, atau menghapus data.

## Selanjutnya

Lanjutkan ke [Users and Permissions](../05-users-and-permissions/README.md) untuk mempelajari akun pengguna, grup, dan izin akses file.
