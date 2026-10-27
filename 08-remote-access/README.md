# Remote Access

Remote access memungkinkan pengguna mengelola komputer atau server dari perangkat lain melalui jaringan. Bab ini membahas empat metode: SSH, Telnet, RDP, dan VNC.

## Tujuan Pembelajaran

Setelah mempelajari bagian ini, kamu diharapkan dapat:

- Mengenali perbedaan akses terminal dan desktop jarak jauh.
- Terhubung ke server menggunakan SSH.
- Memahami keterbatasan keamanan Telnet.
- Mengakses desktop Ubuntu menggunakan RDP atau VNC.
- Memeriksa layanan dan membatasi akses jaringan dengan aman.

## 1. Perbandingan Metode

| Metode | Akses | Port standar | Enkripsi | Penggunaan umum |
|---|---|---:|---|---|
| SSH | Terminal/command line | TCP 22 | Ya | Administrasi Linux dan transfer file |
| Telnet | Terminal/teks | TCP 23 | Tidak | Sistem lama atau lab terisolasi; bukan untuk login melalui jaringan tepercaya |
| RDP | Desktop grafis | TCP/UDP 3389 | Bergantung konfigurasi | Desktop jarak jauh, sering dipakai dari Windows |
| VNC | Desktop grafis | Biasanya TCP 5900 + nomor display | Bergantung konfigurasi | Berbagi atau mengendalikan desktop grafis |

Gunakan remote access hanya ke server yang kamu miliki atau berwenang kelola. Jangan membuka layanan administrasi langsung ke internet tanpa kontrol akses dan perlindungan yang sesuai.

## 2. SSH (Secure Shell)

SSH adalah pilihan umum untuk mengelola Ubuntu Server melalui terminal. Koneksi SSH mengenkripsi komunikasi antara client dan server.

### Memasang dan memeriksa server SSH

Pada server Ubuntu:

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl status ssh
```

Jika layanan belum berjalan, mulai dan atur agar aktif saat boot:

```bash
sudo systemctl enable --now ssh
```

### Terhubung dari client

Cari alamat IP server dengan `ip a`, lalu dari komputer client jalankan:

```bash
ssh nama-pengguna@alamat-ip-server
```

Contoh:

```bash
ssh ademulyana@192.168.1.50
```

Saat pertama terhubung, SSH dapat meminta konfirmasi fingerprint host. Pastikan fingerprint sesuai dengan server yang dimaksud sebelum menyetujuinya. Setelah autentikasi berhasil, kamu akan mendapatkan shell di server.

Jika SSH server menggunakan port selain port standar 22:

```bash
ssh -p 2222 nama-pengguna@alamat-ip-server
```

### Mengubah port SSH server

Contoh berikut mengubah port SSH menjadi `2222`. Lakukan dari console lokal atau pastikan ada akses console/out-of-band. **Jangan tutup sesi SSH yang sedang aktif sampai koneksi baru lewat port 2222 berhasil.**

1. Buat konfigurasi tambahan:

   ```bash
   sudoedit /etc/ssh/sshd_config.d/60-custom-port.conf
   ```

   Isi file dengan:

   ```text
   Port 2222
   ```

   Ubuntu umumnya memuat file konfigurasi tambahan dari direktori ini. Periksa file konfigurasi lain jika server memiliki pengaturan port khusus atau dikelola cloud-init.

2. Validasi konfigurasi sebelum menerapkannya:

   ```bash
   sudo sshd -t
   sudo sshd -T | grep '^port '
   ```

   `sshd -t` yang berhasil tidak menampilkan output. Pastikan hasil perintah kedua mencantumkan `port 2222`. Jika masih menampilkan port lain atau lebih dari satu port, periksa konfigurasi aktif dan jangan lanjut sebelum jelas port yang akan digunakan.

3. Izinkan port baru di firewall **sebelum** restart SSH. Jika UFW digunakan, batasi sumber ke alamat atau subnet client tepercaya. Contoh untuk satu client:

   ```bash
   sudo ufw allow from 192.168.1.25 to any port 2222 proto tcp
   sudo ufw status
   ```

   Ganti `192.168.1.25` dengan alamat client tepercaya. Jika penyedia cloud memiliki firewall/security group, tambahkan aturan TCP 2222 di sana juga. Jangan hapus aturan port 22 dahulu.

4. Restart layanan SSH:

   ```bash
   sudo systemctl restart ssh
   sudo systemctl status ssh
   ```

5. Dari terminal client kedua, uji port baru:

   ```bash
   ssh -p 2222 nama-pengguna@alamat-ip-server
   ```

   Pastikan berhasil login sebelum menutup sesi lama. Setelah port baru teruji dan aturan akses sudah benar, aturan port lama 22 boleh dihapus jika memang tidak lagi diperlukan.

Jika koneksi baru gagal, gunakan sesi lama atau console pemulihan untuk memeriksa `sudo journalctl -u ssh -n 50` dan konfigurasi. Nonaktifkan atau perbaiki file `60-custom-port.conf`, validasi kembali dengan `sudo sshd -t`, lalu restart layanan. Mengganti port dapat mengurangi pemindaian otomatis biasa, tetapi **bukan pengganti** autentikasi kuat, pembaruan keamanan, firewall, atau VPN.

### Transfer file dengan SCP

Menyalin file dari client ke server:

```bash
scp ./catatan.txt nama-pengguna@alamat-ip-server:/home/nama-pengguna/
```

Jika SSH server menggunakan port khusus, gunakan opsi `-P` huruf besar pada `scp`:

```bash
scp -P 2222 ./catatan.txt nama-pengguna@alamat-ip-server:/home/nama-pengguna/
```

Menyalin file dari server ke client:

```bash
scp nama-pengguna@alamat-ip-server:/home/nama-pengguna/catatan.txt ./
```

SCP menggunakan SSH untuk mengenkripsi transfer. Pastikan path tujuan dan akun penerima sudah benar.

### Menggunakan kunci SSH

Kunci SSH dapat digunakan sebagai alternatif kata sandi. Buat pasangan kunci di client:

```bash
ssh-keygen -t ed25519
```

Simpan private key dengan aman dan jangan membagikannya. Pasang public key ke akun server:

```bash
ssh-copy-id nama-pengguna@alamat-ip-server
```

Kemudian uji koneksi:

```bash
ssh nama-pengguna@alamat-ip-server
```

## 3. Telnet

Telnet menyediakan sesi terminal melalui jaringan, tetapi **tidak mengenkripsi** kata sandi maupun data. Informasi yang dikirim dapat dibaca pihak lain yang dapat memantau lalu lintas jaringan. Karena itu, jangan gunakan Telnet untuk login atau mengirim data sensitif. Gunakan SSH sebagai gantinya.

### Menguji port layanan dengan Telnet

Pada lab atau jaringan tepercaya, client Telnet kadang digunakan untuk menguji apakah port TCP dapat dijangkau. Pengujian ini tidak membuktikan bahwa layanan aman atau berfungsi sepenuhnya.

Pasang client jika belum tersedia:

```bash
sudo apt update
sudo apt install telnet
```

Coba menghubungkan client ke port layanan yang memang kamu kelola, misalnya port SSH:

```bash
telnet alamat-ip-server 22
```

Jika koneksi TCP berhasil, server SSH biasanya segera menampilkan banner protokol, lalu menutup sesi atau menunggu protokol SSH yang benar. Telnet bukan client SSH; jangan masukkan kata sandi melalui koneksi Telnet. Keluar dengan `Ctrl+]`, lalu ketik `quit`.

Jangan memasang atau mengaktifkan server Telnet untuk administrasi server. Jika harus mengelola perangkat lama yang hanya mendukung Telnet, batasi pada jaringan lab terisolasi dan jangan gunakan kredensial yang dipakai di sistem lain.

## 4. RDP (Remote Desktop Protocol)

RDP menyediakan desktop grafis jarak jauh. Di Ubuntu Server, salah satu implementasi server RDP yang dapat digunakan adalah **xrdp**. Server harus memiliki lingkungan desktop yang terpasang dan dikonfigurasi; memasang xrdp saja belum tentu menyediakan desktop.

### Memasang dan menjalankan xrdp

```bash
sudo apt update
sudo apt install xrdp
sudo systemctl enable --now xrdp
sudo systemctl status xrdp
```

Jika belum ada desktop grafis, pilih dan pasang lingkungan desktop yang sesuai dengan kebutuhan dan kapasitas server terlebih dahulu. Langkah ini dapat menggunakan sumber daya penyimpanan dan memori tambahan.

### Membatasi akses firewall

Jangan membuka RDP ke seluruh internet. Jika UFW digunakan, izinkan hanya subnet tepercaya. Contoh berikut mengizinkan jaringan lokal `192.168.1.0/24`; sesuaikan dengan jaringanmu:

```bash
sudo ufw allow from 192.168.1.0/24 to any port 3389 proto tcp
sudo ufw status
```

Jangan aktifkan firewall secara membabi buta pada server remote: pastikan aturan SSH yang diperlukan sudah ada agar koneksi administrasi tidak terputus.

### Terhubung ke server RDP

Di Windows, buka **Remote Desktop Connection** (`mstsc`), masukkan alamat IP server, lalu masuk dengan akun Ubuntu yang diizinkan. Di Linux, aplikasi seperti Remmina dapat digunakan. Nama aplikasi client di sistem lain dapat berbeda.

## 5. VNC (Virtual Network Computing)

VNC menyediakan akses ke desktop grafis menggunakan server dan viewer. Implementasi dan konfigurasi sesi desktop bergantung pada distribusi serta lingkungan desktop yang digunakan. VNC tidak selalu mengenkripsi seluruh sesi; gunakan konfigurasi keamanan yang didukung server dan utamakan tunnel SSH atau VPN.

### Contoh server TigerVNC

Ubuntu Server memerlukan lingkungan desktop yang cocok sebelum sesi VNC dapat digunakan. Salah satu pilihan paket pada Ubuntu adalah TigerVNC:

```bash
sudo apt update
sudo apt install tigervnc-standalone-server tigervnc-viewer
```

Pasang dan konfigurasi lingkungan desktop yang sesuai terlebih dahulu. Nama paket desktop dan konfigurasi sesi dapat berbeda menurut versi serta kebutuhan server. Setelah itu, atur kata sandi VNC untuk pengguna yang menjalankan sesi:

```bash
vncpasswd
```

Mulai sesi display `:1`, yang biasanya menggunakan port TCP `5901`:

```bash
vncserver :1
vncserver -list
```

Hentikan sesi display tersebut:

```bash
vncserver -kill :1
```

Perintah dan kebutuhan konfigurasi sesi dapat berbeda menurut paket TigerVNC dan versi Ubuntu. Periksa bantuan paket yang terpasang (`vncserver --help`) dan dokumentasi distribusi. Jangan menganggap hanya menjalankan `vncserver` sudah menghasilkan desktop yang aman atau sesuai.

### Menghubungkan VNC melalui tunnel SSH

Jangan mengekspos port VNC langsung ke internet. Dari client, buat tunnel SSH:

```bash
ssh -L 5901:localhost:5901 nama-pengguna@alamat-ip-server
```

Biarkan koneksi SSH tetap terbuka. Di aplikasi VNC Viewer pada client, sambungkan ke:

```text
localhost:5901
```

Jika memakai display lain, sesuaikan port lokal dan port server. VNC tetap memerlukan autentikasi dan konfigurasi yang aman pada server.

Jika viewer terpasang di client, koneksi ke tunnel lokal dapat dilakukan dengan:

```bash
vncviewer localhost:5901
```

## 6. Contoh Sesi SSH

Output berikut ilustratif; alamat IP, fingerprint, dan banner dapat berbeda:

```console
ademulyana@client:~$ ssh ademulyana@192.168.1.50
The authenticity of host '192.168.1.50' can't be established.
ED25519 key fingerprint is SHA256:contoh-fingerprint.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
ademulyana@192.168.1.50's password:
Welcome to Ubuntu Server
ademulyana@ademulyana-server:~$
```

## Kebiasaan Aman

- Utamakan SSH untuk administrasi terminal dan gunakan autentikasi kunci bila sesuai.
- Jangan gunakan Telnet untuk login atau mengirim informasi sensitif.
- Gunakan RDP/VNC hanya bila desktop grafis memang diperlukan.
- Batasi akses RDP/VNC ke jaringan tepercaya atau melalui VPN; jangan membuka port tersebut ke publik tanpa kontrol keamanan.
- Gunakan kata sandi unik dan kuat, perbarui sistem, dan nonaktifkan layanan yang tidak diperlukan.
- Pastikan aturan firewall tidak memutus akses administrasi yang sedang digunakan.

## Ringkasan

- SSH menyediakan akses terminal dan transfer file yang terenkripsi.
- Telnet tidak mengenkripsi lalu lintas dan sebaiknya hanya dipakai untuk pengujian terbatas atau sistem legacy dalam lab terisolasi.
- RDP dan VNC menyediakan akses desktop grafis; konfigurasi keamanan dan kebutuhan desktop bergantung pada server yang digunakan.
- Batasi akses remote ke sistem dan jaringan yang berwenang serta siapkan perlindungan firewall/VPN.

## Selanjutnya

Lanjutkan ke [Automating Tasks: Cron Job](../09-automating-tasks-cron-job/README.md) untuk mempelajari penjadwalan tugas otomatis.
