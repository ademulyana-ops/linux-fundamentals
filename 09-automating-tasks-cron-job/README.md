# Automating Tasks: Cron Job

Cron job adalah tugas yang dijadwalkan agar dijalankan secara otomatis pada waktu atau interval tertentu. Di Ubuntu Server, cron berguna untuk menjalankan skrip pemeliharaan, membuat laporan, membersihkan file sementara, atau menjalankan tugas rutin lainnya tanpa perlu mengetik perintah secara manual setiap kali.

## Tujuan Cron Job

Cron job membantu untuk:

- Menjalankan tugas berulang pada waktu yang sudah ditentukan.
- Mengurangi pekerjaan manual dan risiko lupa menjalankan tugas rutin.
- Menjadwalkan skrip backup, laporan, sinkronisasi, atau pemeliharaan.
- Menjalankan tugas dengan akun dan hak akses tertentu.

Cron job **tidak otomatis menjamin** tugas berhasil. Skrip perlu diuji, izin dan environment perlu benar, serta output dan log perlu diperiksa.

## 1. Cron dan Crontab

- **cron** adalah layanan penjadwal yang memeriksa dan menjalankan tugas terjadwal.
- **crontab** adalah daftar jadwal per pengguna.
- Tugas pada crontab pengguna berjalan dengan hak dan environment pengguna tersebut.
- Crontab root menjalankan tugas sebagai administrator; gunakan hanya jika tugas memerlukan hak root.

Periksa status layanan cron:

```bash
systemctl status cron
```

Mengaktifkan dan menjalankan layanan cron:

```bash
sudo systemctl enable --now cron
```

Pada instalasi Ubuntu Server biasa, layanan cron sering sudah aktif.

## 2. Format Jadwal Crontab

Buka crontab pengguna saat ini:

```bash
crontab -e
```

Baris jadwal memiliki lima kolom waktu diikuti perintah:

```text
menit jam tanggal-bulan bulan hari-dalam-minggu perintah
```

| Kolom | Rentang nilai |
|---|---|
| Menit | `0–59` |
| Jam | `0–23` |
| Tanggal dalam bulan | `1–31` |
| Bulan | `1–12` atau nama bulan |
| Hari dalam minggu | `0–7` (Minggu dapat ditulis `0` atau `7`) |

Karakter yang sering digunakan:

| Karakter | Arti | Contoh |
|---|---|---|
| `*` | Semua nilai yang mungkin | `*` pada kolom jam berarti setiap jam |
| `,` | Beberapa nilai | `1,15` berarti tanggal 1 dan 15 |
| `-` | Rentang nilai | `1-5` pada kolom hari berarti Senin sampai Jumat pada implementasi umum |
| `/` | Langkah atau interval | `*/10` pada menit berarti setiap 10 menit |

Contoh jadwal:

```text
30 2 * * * /home/ademulyana/scripts/backup.sh
```

Tugas tersebut dijadwalkan setiap hari pukul **02.30** menurut zona waktu sistem.

Contoh lainnya:

```text
*/5 * * * * /home/ademulyana/scripts/check-service.sh
0 8 * * 1-5 /home/ademulyana/scripts/daily-report.sh
0 0 1 * * /home/ademulyana/scripts/monthly-task.sh
```

- `*/5 * * * *` — setiap 5 menit.
- `0 8 * * 1-5` — pukul 08.00 setiap Senin sampai Jumat.
- `0 0 1 * *` — pukul 00.00 pada tanggal 1 setiap bulan.

Periksa format jadwal dengan teliti. Jadwal dan semantik hari dalam minggu sebaiknya diuji pada versi cron yang digunakan, khususnya jika memakai kombinasi kolom tanggal dan hari.

## 3. Menambahkan Cron Job Pengguna

### Menyiapkan skrip

Contoh skrip sederhana yang menulis waktu eksekusi ke file log:

```bash
#!/bin/bash
date >> /home/ademulyana/cron-log.txt
```

Buat direktori dan simpan isi di atas, misalnya menggunakan editor `nano`:

```bash
mkdir -p /home/ademulyana/scripts
nano /home/ademulyana/scripts/write-time.sh
```

Simpan lalu tutup editor. Beri hak eksekusi:

```bash
chmod +x /home/ademulyana/scripts/write-time.sh
```

Uji dengan menjalankannya langsung sebelum dijadwalkan:

```bash
/home/ademulyana/scripts/write-time.sh
```

Cron menggunakan environment yang lebih terbatas daripada shell interaktif. Gunakan path absolut untuk skrip, perintah, file input, dan output. Pastikan pengguna pemilik cron memiliki izin membaca dan menjalankan skrip serta menulis lokasi output.

### Menambahkan jadwal

Buka crontab untuk pengguna saat ini:

```bash
crontab -e
```

Tambahkan baris berikut untuk menjalankan skrip setiap hari pukul 02.30:

```cron
30 2 * * * /home/ademulyana/scripts/write-time.sh
```

Simpan dan keluar dari editor. Editor yang dibuka pertama kali dapat berbeda menurut konfigurasi sistem.

## 4. Memeriksa dan Menghapus Cron Job

Melihat crontab pengguna saat ini:

```bash
crontab -l
```

Mengedit jadwal pengguna:

```bash
crontab -e
```

Menghapus seluruh crontab pengguna:

```bash
crontab -r
```

> **Peringatan:** `crontab -r` menghapus semua jadwal milik pengguna saat ini tanpa meminta konfirmasi. Untuk menghapus satu tugas, gunakan `crontab -e` lalu hapus hanya baris yang dimaksud.

Mengelola crontab pengguna lain sebagai administrator:

```bash
sudo crontab -u budi -l
sudo crontab -u budi -e
```

Crontab root terpisah dari crontab pengguna biasa:

```bash
sudo crontab -e
sudo crontab -l
```

Jangan menjadwalkan skrip sebagai root jika tugas dapat berjalan dengan hak pengguna biasa.

## 5. Output dan Log

Secara default, cron dapat mengirim output tugas melalui email lokal jika sistem email tersedia. Untuk menyimpan output ke file log, arahkan output standar dan error:

```cron
30 2 * * * /home/ademulyana/scripts/backup.sh >> /home/ademulyana/backup.log 2>&1
```

- `>>` menambahkan output ke akhir file, bukan mengganti isi sebelumnya.
- `2>&1` mengarahkan pesan error ke lokasi yang sama dengan output standar.

Gunakan `>` hanya jika memang ingin mengganti isi log setiap kali tugas berjalan. Pertimbangkan rotasi atau pembersihan log agar file tidak tumbuh tanpa batas.

Log layanan cron dapat diperiksa dengan:

```bash
sudo journalctl -u cron
```

Untuk melihat entri terbaru:

```bash
sudo journalctl -u cron -n 50
```

Ketersediaan dan detail log bergantung pada konfigurasi sistem. Periksa juga file log yang ditentukan pada perintah tugas.

## 6. Variabel Lingkungan dan Zona Waktu

Cron berjalan dengan environment minimal. Jika skrip membutuhkan nilai tertentu, atur di crontab atau di dalam skrip:

```cron
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
SHELL=/bin/bash
```

Baris variabel tersebut harus diletakkan sebelum baris jadwal. Meski begitu, penggunaan path absolut tetap disarankan.

Jadwal cron umumnya mengikuti zona waktu sistem. Periksa zona waktu:

```bash
timedatectl
```

Perubahan zona waktu atau jam sistem dapat memengaruhi waktu eksekusi. Jangan mengandalkan cron untuk tugas yang harus berjalan tepat pada detik tertentu.

## 7. Contoh Sesi Terminal

Output berikut ilustratif dan dapat berbeda menurut sistem:

```console
ademulyana@ademulyana-server:~$ mkdir -p ~/scripts
ademulyana@ademulyana-server:~$ nano ~/scripts/write-time.sh
```

Masukkan isi berikut di editor, lalu simpan dan keluar:

```bash
#!/bin/bash
date >> /home/ademulyana/cron-log.txt
```

Lanjutkan di terminal:

```console
ademulyana@ademulyana-server:~$ chmod +x ~/scripts/write-time.sh
ademulyana@ademulyana-server:~$ ~/scripts/write-time.sh
ademulyana@ademulyana-server:~$ cat ~/cron-log.txt
Tue Oct 27 12:00:00 WIB 2026

ademulyana@ademulyana-server:~$ crontab -e
```

Tambahkan jadwal berikut di editor, lalu simpan dan keluar:

```cron
*/5 * * * * /home/ademulyana/scripts/write-time.sh
```

Setelah menyimpan dan keluar dari editor:

```console
ademulyana@ademulyana-server:~$ crontab -l
*/5 * * * * /home/ademulyana/scripts/write-time.sh

ademulyana@ademulyana-server:~$ cat ~/cron-log.txt
Tue Oct 27 12:00:00 WIB 2026
Tue Oct 27 12:05:00 WIB 2026
Tue Oct 27 12:10:00 WIB 2026
```

Baris pada log akan muncul saat tugas dijalankan; mungkin perlu menunggu sampai waktu eksekusi berikutnya. Pastikan lokasi file skrip dan hak akses sesuai dengan konfigurasi milikmu.

## Kebiasaan Aman

- Uji skrip secara manual sebelum menambahkannya ke crontab.
- Gunakan path absolut dan batasi hak akses file skrip serta log.
- Hindari menaruh kata sandi atau rahasia langsung di crontab.
- Periksa jadwal sebelum menyimpannya dan gunakan `crontab -l` untuk verifikasi.
- Gunakan hak pengguna biasa jika tugas tidak memerlukan hak root.
- Pantau output dan log agar kegagalan tugas tidak luput diketahui.

## Ringkasan

- Cron menjalankan tugas terjadwal; crontab menyimpan jadwal per pengguna.
- Format jadwal berisi menit, jam, tanggal, bulan, hari dalam minggu, lalu perintah.
- Gunakan `crontab -e` untuk mengedit dan `crontab -l` untuk memeriksa jadwal.
- Uji skrip, gunakan path absolut, dan alihkan output ke log bila perlu.
- Tujuan cron job adalah mengotomatisasi pekerjaan rutin, tetapi hasilnya tetap perlu dipantau.

## Selesai

Kamu telah menyelesaikan materi dasar Linux dalam repository ini. Lanjutkan berlatih pada server Ubuntu milikmu, dan selalu periksa dampak perintah sebelum menjalankannya.
