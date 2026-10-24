# User, Group, and File Permissions

Ubuntu Server menggunakan akun pengguna dan grup untuk mengatur siapa yang dapat masuk ke sistem dan mengakses sumber daya. Permission file dan direktori menentukan siapa yang dapat membaca, mengubah, atau mengaksesnya. Pengelolaan akun biasanya memerlukan hak administrator, sehingga beberapa perintah di bab ini menggunakan `sudo`.

> **Catatan keamanan:** Gunakan akun biasa untuk pekerjaan sehari-hari. Berikan hak administrator atau akses ke grup penting hanya kepada pengguna yang memang tepercaya dan membutuhkannya.

## Tujuan Pembelajaran

Setelah mempelajari bagian ini, kamu diharapkan dapat:

- Memahami perbedaan akun pengguna dan grup.
- Membuat, memeriksa, mengubah, dan menghapus akun pengguna.
- Membuat, memeriksa, mengubah anggota, dan menghapus grup.
- Membaca dan mengubah permission file serta direktori.
- Mengenali dampak pemberian hak administrator serta penghapusan akun.

## 1. Konsep Pengguna dan Grup

### Pengguna

Setiap pengguna memiliki nama akun dan ID numerik (UID). Akun pengguna biasa umumnya memiliki direktori home di `/home`, misalnya `/home/budi`. Akun `root` adalah administrator sistem dengan UID `0` dan dapat mengubah hampir seluruh sistem.

### Grup

Grup adalah kumpulan akun yang dapat diberi akses bersama ke file atau sumber daya. Setiap grup memiliki nama dan ID numerik (GID). Pengguna memiliki satu **grup utama** dan dapat menjadi anggota beberapa **grup tambahan**.

Menambahkan pengguna ke grup tidak otomatis membuat pengguna tersebut menjadi administrator. Namun, grup tertentu—terutama `sudo` di Ubuntu—memberikan hak istimewa yang luas.

## 2. Memeriksa Pengguna dan Grup

### Melihat pengguna yang sedang aktif: `whoami`

```bash
whoami
```

Menampilkan nama pengguna pada sesi terminal saat ini.

### Melihat UID dan grup pengguna: `id`

```bash
id
id budi
```

Tanpa argumen, `id` menampilkan informasi akun yang sedang aktif. Dengan nama akun sebagai argumen, perintah menampilkan informasi akun tersebut.

### Melihat grup pengguna: `groups`

```bash
groups
groups budi
```

Menampilkan grup yang diikuti pengguna.

### Memeriksa akun atau grup: `getent`

```bash
getent passwd budi
getent group developers
```

`getent passwd` mencari informasi akun pengguna, sedangkan `getent group` mencari informasi grup. Perintah ini memeriksa sumber akun yang dikonfigurasi pada sistem.

### Membaca daftar akun lokal: `/etc/passwd`

File `/etc/passwd` berisi daftar akun lokal. Saat menjalankan `cat /etc/passwd`, terminal menampilkan baris akun satu per satu seperti contoh berikut. Ini cuplikan ilustratif; daftar dan UID pada servermu bisa berbeda.

```console
ademulyana@ademulyana-server:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin
syslog:x:101:102::/nonexistent:/usr/sbin/nologin
sshd:x:103:65534::/run/sshd:/usr/sbin/nologin
syawal:x:1000:1000:Syawal:/home/syawal:/bin/bash
ari:x:1001:1001:Ari:/home/ari:/bin/bash
dani:x:1002:1002:Dani:/home/dani:/bin/bash
budi:x:1003:1003:Budi:/home/budi:/bin/bash
```

Kolom-kolom pada setiap baris dipisahkan dengan tanda titik dua:

```text
nama_pengguna:penanda_kata_sandi:UID:GID:GECOS:direktori_home:shell_login
```

- `nama_pengguna` adalah nama akun.
- `x` menandakan informasi kata sandi tersimpan di `/etc/shadow`, bukan sebagai teks di `/etc/passwd`.
- `UID` adalah ID numerik pengguna.
- `GID` adalah ID grup utama.
- `GECOS` adalah informasi tambahan, misalnya nama lengkap; bisa kosong.
- `direktori_home` adalah lokasi home akun.
- `shell_login` adalah shell login. Akun layanan sering menggunakan `nologin` agar tidak dapat membuka sesi login biasa.

Untuk menampilkan hanya baris akun `budi`:

```console
ademulyana@ademulyana-server:~$ grep '^budi:' /etc/passwd
budi:x:1003:1003:Budi:/home/budi:/bin/bash
```

### Melihat daftar grup: `/etc/group`

Keanggotaan grup tambahan tidak ditampilkan sebagai nama grup di `/etc/passwd`. Informasi grup disimpan di `/etc/group`. Contoh berikut memperlihatkan format output terminal dan sebuah grup `developer` yang beranggotakan `ari` dan `budi`:

```console
ademulyana@ademulyana-server:~$ cat /etc/group
root:x:0:
daemon:x:1:
adm:x:4:syslog,ari
sudo:x:27:ademulyana
users:x:100:
syawal:x:1000:
ari:x:1001:
dani:x:1002:
budi:x:1003:
developer:x:1004:ari,budi
```

Format setiap baris `/etc/group`:

```text
nama_grup:penanda_kata_sandi:GID:anggota_tambahan
```

- `nama_grup` adalah nama grup.
- `x` menandakan kata sandi grup tidak disimpan sebagai teks pada kolom ini.
- `GID` adalah ID numerik grup.
- `anggota_tambahan` adalah daftar pengguna tambahan yang menjadi anggota grup, dipisahkan koma. Grup utama pengguna terlihat dari GID di `/etc/passwd` dan tidak harus tercantum pada kolom anggota tambahan.

File `/etc/passwd` dan `/etc/group` dapat dibaca pengguna biasa. Jangan mengeditnya secara langsung; gunakan perintah administrasi seperti `adduser`, `usermod`, dan `addgroup`. Untuk mencari satu akun atau grup dari sumber yang dikonfigurasi, gunakan `getent passwd nama_pengguna` atau `getent group nama_grup`.

## 3. User Management

### Membuat pengguna: `adduser`

Di Ubuntu, `adduser` adalah cara interaktif yang mudah untuk membuat pengguna:

```bash
sudo adduser budi
```

Ikuti petunjuk untuk membuat kata sandi dan mengisi informasi opsional. Perintah ini biasanya juga membuat grup utama dan direktori home untuk pengguna.

Pilih nama akun yang sesuai kebijakan sistem. Jangan gunakan kata sandi yang mudah ditebak atau membagikan kata sandi antar pengguna.

### Perbedaan `adduser` dan `useradd`

Keduanya dapat digunakan untuk membuat akun, tetapi cara kerjanya berbeda:

| Perintah | Karakteristik |
|---|---|
| `adduser` | Utilitas tingkat tinggi di Ubuntu/Debian. Lebih interaktif dan biasanya menyiapkan direktori home, grup utama, serta meminta pembuatan kata sandi. Lebih praktis untuk penggunaan umum. |
| `useradd` | Utilitas tingkat rendah yang tersedia di banyak distribusi Linux. Tidak interaktif dan membutuhkan opsi eksplisit untuk menyiapkan hal-hal seperti direktori home dan shell login. Lebih cocok untuk skrip atau konfigurasi khusus. |

Contoh membuat akun dengan `useradd` di Ubuntu:

```bash
sudo useradd -m -s /bin/bash budi
sudo passwd budi
```

- `-m` membuat direktori home, biasanya `/home/budi`.
- `-s /bin/bash` menetapkan Bash sebagai shell login.
- `passwd` menetapkan kata sandi untuk akun tersebut.

Tanpa opsi `-m`, direktori home biasanya tidak dibuat. Pengaturan bawaan dapat berbeda menurut konfigurasi sistem. Setelah membuat akun, periksa hasilnya dengan `id budi` dan `getent passwd budi`.

Untuk kebanyakan penggunaan interaktif di Ubuntu, pilih `adduser`. Gunakan `useradd` jika memang membutuhkan kontrol tingkat rendah, misalnya membuat akun tanpa direktori home atau menyusun skrip administrasi. Jangan memberikan kata sandi dalam bentuk teks biasa pada argumen perintah atau skrip.

### Mengubah kata sandi: `passwd`

Pengguna dapat mengganti kata sandinya sendiri dengan:

```bash
passwd
```

Administrator dapat mengatur ulang kata sandi pengguna lain:

```bash
sudo passwd budi
```

Kata sandi yang diketik tidak ditampilkan di terminal. Sampaikan kata sandi sementara dengan aman dan minta pengguna menggantinya setelah masuk.

### Mengubah informasi akun atau grup: `usermod`

Menambahkan pengguna ke grup tambahan:

```bash
sudo usermod -aG developers budi
```

Opsi `-aG` menambahkan grup sebagai grup tambahan tanpa mengganti grup tambahan lain. **Jangan menghilangkan `-a` saat bermaksud menambahkan grup**, karena `-G` tanpa `-a` mengganti daftar grup tambahan pengguna.

Untuk memberikan hak administrator Ubuntu kepada pengguna:

```bash
sudo usermod -aG sudo budi
```

Lakukan ini hanya jika pengguna benar-benar membutuhkan hak administrator. Keanggotaan grup baru biasanya berlaku setelah pengguna keluar lalu masuk kembali. Periksa hasilnya dengan `id budi`.

### Mengunci atau membuka akun: `passwd`

Administrator dapat mengunci kata sandi akun atau membukanya kembali:

```bash
sudo passwd -l budi
sudo passwd -u budi
```

Mengunci kata sandi mencegah autentikasi menggunakan kata sandi, tetapi tidak selalu menonaktifkan semua cara masuk yang mungkin dikonfigurasi, seperti kunci SSH. Untuk menonaktifkan akses akun secara menyeluruh, ikuti kebijakan administrasi dan tinjau metode autentikasi yang aktif.

### Menghapus pengguna: `deluser`

Menghapus akun pengguna tetapi mempertahankan file home:

```bash
sudo deluser budi
```

Sebelum menghapus akun, pastikan akun tidak sedang digunakan dan data yang diperlukan sudah dicadangkan atau dipindahkan. Menghapus akun tidak selalu menghapus file milik pengguna di lokasi lain.

Opsi berikut juga menghapus direktori home dan mail spool pengguna:

```bash
sudo deluser --remove-home budi
```

> **Peringatan:** `--remove-home` menghapus data pengguna di home. Pastikan backup sudah tersedia dan akun yang dipilih benar sebelum menjalankannya.

## 4. Group Management

### Membuat grup: `addgroup`

```bash
sudo addgroup developers
```

Membuat grup baru bernama `developers`.

### Menambahkan pengguna ke grup

Gunakan `usermod -aG` untuk menambahkan grup tambahan tanpa menghapus keanggotaan lain:

```bash
sudo usermod -aG developers budi
```

Alternatif di Ubuntu adalah `adduser` dengan nama pengguna dan grup:

```bash
sudo adduser budi developers
```

Periksa keanggotaan setelahnya:

```bash
id budi
groups budi
```

Pengguna perlu keluar dan masuk kembali agar sesi baru memuat keanggotaan grup. `newgrp developers` dapat membuka shell baru dengan grup tersebut sebagai grup aktif untuk sesi itu.

### Menghapus pengguna dari grup

Di Ubuntu, gunakan `deluser` dengan nama pengguna dan grup:

```bash
sudo deluser budi developers
```

Perintah ini menghapus keanggotaan `budi` dari grup tambahan `developers`; perintah ini tidak menghapus akun pengguna atau grupnya. Periksa perubahan dengan `groups budi`.

### Mengubah nama grup: `groupmod`

```bash
sudo groupmod -n engineering developers
```

Mengubah nama grup `developers` menjadi `engineering`. Pastikan perubahan nama tidak mengganggu konfigurasi atau skrip yang merujuk pada nama grup sebelumnya.

### Menghapus grup: `delgroup`

```bash
sudo delgroup developers
```

Hapus grup hanya jika sudah tidak digunakan. Grup utama pengguna yang masih ada tidak dapat dihapus begitu saja; pindahkan pengguna ke grup utama lain terlebih dahulu bila memang diperlukan.

## 5. File and Directory Permissions

Linux menyimpan pemilik pengguna, pemilik grup, dan permission pada setiap file dan direktori. Gunakan `ls -l` untuk melihat informasi tersebut:

```bash
ls -l laporan.txt
ls -ld proyek
```

Contoh permission file:

```text
-rw-r----- 1 ademulyana developers 1200 Oct 24 10:00 laporan.txt
```

Bagian pertama `-rw-r-----` menunjukkan jenis objek dan permission:

```text
- | rwx | rwx | rwx
^   ^^^   ^^^   ^^^
|   |     |     +-- Others (pengguna lain)
|   |     +-------- Group (grup pemilik)
|   +-------------- User/Owner (pemilik)
+------------------ File type (jenis objek)
```

Tanda `|` dan spasi pada diagram hanya pemisah visual, bukan bagian dari output. `ls -l` menampilkan permission tanpa spasi, misalnya `-rwxrwxrwx`.

- Karakter pertama menunjukkan jenis objek: `-` untuk file biasa, `d` untuk direktori, dan `l` untuk symbolic link.
- Tiga karakter berikutnya menunjukkan permission pemilik (*user/owner*), tiga karakter setelahnya untuk grup (*group*), dan tiga karakter terakhir untuk pengguna lain (*others*).
- `r` berarti membaca (*read*), `w` berarti menulis atau mengubah (*write*), dan `x` berarti menjalankan file atau menelusuri direktori (*execute/search*).
- `-` berarti permission tersebut tidak diberikan.

Jenis objek lain yang dapat muncul antara lain `b` (block device), `c` (character device), `p` (named pipe), dan `s` (socket).

Pada **direktori**, `r` mengizinkan melihat nama item di dalamnya, `w` mengizinkan membuat atau menghapus item (biasanya perlu juga `x`), dan `x` mengizinkan menelusuri atau mengakses item berdasarkan namanya. Pada **file**, `r` mengizinkan membaca isi, `w` mengizinkan mengubah isi, dan `x` mengizinkan menjalankan file sebagai program atau skrip.

### Mengubah permission: `chmod`

`chmod` mengubah permission file atau direktori. Ada dua cara umum: simbolik dan numerik.

#### Cara simbolik

Simbol untuk memilih kategori pengguna:

| Simbol | Arti |
|---|---|
| `u` | User/owner (pemilik) |
| `g` | Group (grup pemilik) |
| `o` | Others (pengguna lain) |
| `a` | All (semua kategori: `u`, `g`, dan `o`) |

Operator yang digunakan:

- `+` menambahkan permission.
- `-` menghapus permission.
- `=` menetapkan permission persis sesuai yang ditulis.

Pola perintah:

```text
chmod kategori operator permission file_atau_direktori
```

Contoh:

```console
ademulyana@ademulyana-server:~$ ls -l laporan.txt
-rw-r----- 1 ademulyana developers 1200 Oct 24 10:00 laporan.txt

ademulyana@ademulyana-server:~$ chmod g+w laporan.txt
ademulyana@ademulyana-server:~$ ls -l laporan.txt
-rw-rw---- 1 ademulyana developers 1200 Oct 24 10:00 laporan.txt
```

Pada contoh, `g+w` menambahkan permission tulis (`w`) untuk grup tanpa mengubah permission pemilik dan pengguna lain.

Contoh simbolik lainnya:

```bash
chmod u+x skrip.sh
chmod g-w laporan.txt
chmod o-r rahasia.txt
chmod u=rw,g=r,o= laporan.txt
```

- `u+x` menambahkan permission menjalankan untuk pemilik.
- `g-w` menghapus permission menulis untuk grup.
- `o-r` menghapus permission membaca untuk pengguna lain.
- `u=rw,g=r,o=` menetapkan pemilik dapat membaca/menulis, grup hanya membaca, dan pengguna lain tidak mendapat permission.

#### Cara numerik

Dalam cara numerik, nilai permission dijumlahkan untuk masing-masing kategori:

| Permission | Nilai |
|---|---:|
| `r` (read/baca) | 4 |
| `w` (write/tulis) | 2 |
| `x` (execute/jalankan atau telusuri) | 1 |
| Tidak ada permission (`-`) | 0 |

Setiap digit mewakili urutan **user/owner, group, others**. Contohnya, `7 = 4 + 2 + 1 = rwx`, `6 = 4 + 2 = rw-`, dan `1 = --x`.

```text
chmod 761 file_atau_direktori

 7       6       1
 4+2+1   4+2     1
 rwx     rw-     --x
 owner   group   others
```

Artinya, pemilik mendapat baca/tulis/jalankan, grup mendapat baca/tulis, dan pengguna lain hanya mendapat permission jalankan/telusuri.

Contoh penerapan:

```console
ademulyana@ademulyana-server:~$ chmod 761 skrip.sh
ademulyana@ademulyana-server:~$ ls -l skrip.sh
-rwxrw---x 1 ademulyana ademulyana 1200 Oct 24 10:00 skrip.sh
```

Contoh nilai umum lainnya:

```bash
chmod 640 laporan.txt
chmod 750 proyek
```

- `640` memberi pemilik baca/tulis, grup baca saja, dan pengguna lain tanpa akses.
- `750` memberi pemilik akses penuh, grup baca/telusuri, dan pengguna lain tanpa akses.

Pastikan permission sesuai kebutuhan. Digit `1` pada others di `761` tetap memberikan hak menjalankan file atau menelusuri direktori kepada pengguna lain.

### Mengubah pemilik: `chown`

Administrator dapat mengubah pemilik pengguna dan, bila diperlukan, grup file:

```bash
sudo chown budi laporan.txt
sudo chown budi:developers laporan.txt
```

Perintah kedua menjadikan `budi` sebagai pemilik dan `developers` sebagai grup pemilik.

Untuk menerapkan perubahan pada direktori beserta isi di dalamnya, `-R` menjalankan perubahan secara rekursif:

```bash
sudo chown -R budi:developers proyek
```

Pastikan path dan seluruh isi direktori yang dipilih benar sebelum menggunakan `-R`; perubahan rekursif dapat memengaruhi banyak file.

### Mengubah grup pemilik: `chgrp`

Untuk mengubah grup pemilik tanpa mengubah pemilik pengguna:

```bash
sudo chgrp developers laporan.txt
```

Pemilik file juga dapat mengganti grup file jika ia anggota grup tujuan, bergantung pada konfigurasi sistem. Gunakan `sudo` hanya jika diperlukan.

### Default permission baru: `umask`

`umask` menampilkan atau mengatur permission yang dikecualikan saat file dan direktori baru dibuat:

```bash
umask
umask 027
```

Dengan `umask 027`, file baru biasanya mendapat permission `640` dan direktori baru `750`. Ini berasal dari permission dasar file `666` dan direktori `777`, dikurangi bit yang ditutup oleh umask. Hasil akhir juga dapat dipengaruhi aplikasi yang membuat file dan konfigurasi sistem. Perubahan `umask` berlaku untuk shell dan proses turunannya, bukan secara otomatis untuk semua pengguna atau sesi.

### Contoh konsol permission

Output berikut ilustratif; pemilik, grup, waktu, dan ukuran dapat berbeda di servermu:

```console
ademulyana@ademulyana-server:~$ touch laporan.txt
ademulyana@ademulyana-server:~$ mkdir proyek

ademulyana@ademulyana-server:~$ ls -l laporan.txt
-rw-rw-r-- 1 ademulyana ademulyana 0 Oct 24 10:00 laporan.txt

ademulyana@ademulyana-server:~$ chmod 640 laporan.txt
ademulyana@ademulyana-server:~$ ls -l laporan.txt
-rw-r----- 1 ademulyana ademulyana 0 Oct 24 10:00 laporan.txt

ademulyana@ademulyana-server:~$ chmod 750 proyek
ademulyana@ademulyana-server:~$ ls -ld proyek
drwxr-x--- 2 ademulyana ademulyana 4096 Oct 24 10:01 proyek

ademulyana@ademulyana-server:~$ sudo chown budi:developers laporan.txt
ademulyana@ademulyana-server:~$ ls -l laporan.txt
-rw-r----- 1 budi developers 0 Oct 24 10:00 laporan.txt
```

Perintah `chmod` dan `chown` biasanya tidak menampilkan output jika berhasil. Periksa hasilnya dengan `ls -l` untuk file atau `ls -ld` untuk direktori.

> **Peringatan:** Hindari `chmod 777` sebagai cara cepat memperbaiki masalah akses. Permission tersebut memberi semua pengguna hak baca, tulis, dan jalankan/telusuri, sehingga dapat membuka risiko perubahan atau penghapusan data.

## 6. Contoh Sesi Terminal

Contoh berikut menunjukkan perintah pengelolaan user dan grup dalam format konsol. Output merupakan ilustrasi; UID, GID, teks prompt, dan pesan sistem dapat berbeda. Kata sandi tidak ditampilkan saat diketik.

```console
ademulyana@ademulyana-server:~$ sudo addgroup developers
Adding group `developers' (GID 1002) ...
Done.

ademulyana@ademulyana-server:~$ sudo adduser budi
Adding user `budi' ...
Adding new group `budi' (1001) ...
Adding new user `budi' (1002) with group `budi' ...
Creating home directory `/home/budi' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for budi
Enter the new value, or press ENTER for the default
        Full Name []: Budi
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] Y

ademulyana@ademulyana-server:~$ sudo usermod -aG developers budi

ademulyana@ademulyana-server:~$ id budi
uid=1002(budi) gid=1001(budi) groups=1001(budi),1002(developers)

ademulyana@ademulyana-server:~$ getent group developers
developers:x:1002:budi
```

`usermod` biasanya tidak menampilkan output jika berhasil. Periksa hasilnya menggunakan `id` atau `getent`. Pengguna `budi` perlu memulai sesi baru agar keanggotaan grup tambahan berlaku pada sesi login.

### Membuat user dengan `useradd`

Contoh ini membuat akun berbeda agar tidak bentrok dengan akun `budi` pada contoh sebelumnya:

```console
ademulyana@ademulyana-server:~$ sudo useradd -m -s /bin/bash siti
ademulyana@ademulyana-server:~$ sudo passwd siti
New password:
Retype new password:
passwd: password updated successfully

ademulyana@ademulyana-server:~$ getent passwd siti
siti:x:1003:1003::/home/siti:/bin/bash

ademulyana@ademulyana-server:~$ id siti
uid=1003(siti) gid=1003(siti) groups=1003(siti)
```

`useradd` biasanya tidak menampilkan pesan jika akun berhasil dibuat. Opsi `-m` membuat home dan `-s /bin/bash` menetapkan shell login. UID dan GID pada hasil contoh bergantung pada akun yang sudah ada di sistem.

### Mengganti, mengunci, dan membuka kata sandi

Pengguna yang sedang masuk dapat mengganti kata sandinya sendiri. Administrator dapat mengatur ulang atau mengunci kata sandi akun lain:

```console
ademulyana@ademulyana-server:~$ passwd
Current password:
New password:
Retype new password:
passwd: password updated successfully

ademulyana@ademulyana-server:~$ sudo passwd budi
New password:
Retype new password:
passwd: password updated successfully

ademulyana@ademulyana-server:~$ sudo passwd -l budi
passwd: password changed.

ademulyana@ademulyana-server:~$ sudo passwd -u budi
passwd: password changed.
```

Teks output dapat berbeda menurut versi sistem. Mengunci kata sandi hanya memblokir autentikasi dengan kata sandi; tinjau juga metode autentikasi lain, seperti kunci SSH, jika ingin menonaktifkan akses akun.

### Mengelola keanggotaan dan nama grup

```console
ademulyana@ademulyana-server:~$ sudo deluser budi developers
Removing user `budi' from group `developers' ...
Done.

ademulyana@ademulyana-server:~$ groups budi
budi : budi

ademulyana@ademulyana-server:~$ sudo adduser budi developers
Adding user `budi' to group `developers' ...
Done.

ademulyana@ademulyana-server:~$ groups budi
budi : budi developers

ademulyana@ademulyana-server:~$ sudo deluser budi developers
Removing user `budi' from group `developers' ...
Done.

ademulyana@ademulyana-server:~$ sudo groupmod -n engineering developers
ademulyana@ademulyana-server:~$ getent group engineering
engineering:x:1002:

ademulyana@ademulyana-server:~$ sudo delgroup engineering
Removing group `engineering' ...
Done.
```

`groupmod` dan `groupmod -n` biasanya tidak menampilkan output jika berhasil. Pada contoh ini `budi` dikeluarkan dari `developers` sebelum grup berganti nama. Jangan hapus grup yang masih dipakai oleh file, layanan, atau akun.

### Menghapus user

Contoh berikut menghapus akun `siti` tetapi mempertahankan file home, lalu menunjukkan perintah alternatif untuk menghapus akun beserta home:

```console
ademulyana@ademulyana-server:~$ sudo deluser siti
Removing user `siti' ...
Done.

ademulyana@ademulyana-server:~$ sudo deluser --remove-home budi
Removing user `budi' ...
Removing home directory `/home/budi' ...
Done.
```

Jangan menjalankan contoh penghapusan tanpa memastikan akun, data, dan backup yang benar. `--remove-home` menghapus data di home pengguna. File milik pengguna di luar home mungkin tetap tertinggal dan perlu ditinjau secara terpisah.

## Kebiasaan Aman

- Gunakan `sudo` hanya untuk perintah administrasi yang dipahami.
- Berikan keanggotaan grup `sudo` hanya kepada pengguna tepercaya yang memerlukan hak administrator.
- Gunakan `usermod -aG` untuk menambahkan grup tambahan dan periksa hasilnya dengan `id`.
- Cadangkan atau pindahkan data sebelum menghapus akun atau home pengguna.
- Jangan membagikan kata sandi; gunakan cara yang aman untuk memberikan kredensial sementara.

## Ringkasan

- `adduser`, `passwd`, `usermod`, dan `deluser` digunakan untuk mengelola akun pengguna.
- `addgroup`, `deluser pengguna grup`, `groupmod`, dan `delgroup` digunakan untuk mengelola grup serta keanggotaannya.
- `id`, `groups`, dan `getent` membantu memeriksa pengguna dan grup.
- Permission `r`, `w`, dan `x` mengatur akses owner, group, dan others pada file serta direktori.
- `chmod` mengubah permission; `chown` dan `chgrp` mengubah pemilik file atau grup pemiliknya.
- Grup `sudo` memberikan hak administrator yang luas.
- Keanggotaan grup baru umumnya berlaku setelah pengguna memulai sesi login baru.

## Selanjutnya

Lanjutkan ke [Basic Networking](../06-basic-networking/README.md) untuk mempelajari pemeriksaan dan konfigurasi jaringan dasar.
