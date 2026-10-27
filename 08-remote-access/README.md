# Remote Access

Bab ini khusus membahas cara mengakses **Ubuntu Server dari Ubuntu client** melalui jaringan. Metode yang dibahas adalah SSH, Telnet, RDP, dan VNC.

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
| SSH | Terminal/command line | TCP 22 | Ya | Administrasi Ubuntu Server dan transfer file |
| Telnet | Terminal/teks | TCP 23 | Tidak | Pengujian konektivitas TCP terbatas di lab; bukan untuk administrasi Ubuntu |
| RDP | Desktop grafis | TCP/UDP 3389 | Bergantung konfigurasi | Mengakses desktop grafis Ubuntu |
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

Pastikan Ubuntu client memiliki program SSH client. Biasanya sudah tersedia; jika belum:

```bash
sudo apt update
sudo apt install openssh-client
```

Cari alamat IP server dengan `ip a`, lalu dari terminal Ubuntu client jalankan:

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

   Ganti `192.168.1.25` dengan alamat IP Ubuntu client tepercaya. Jika ada firewall jaringan tambahan, izinkan TCP 2222 di sana juga. Jangan hapus aturan port 22 dahulu.

4. Restart layanan SSH:

   ```bash
   sudo systemctl restart ssh
   sudo systemctl status ssh
   ```

5. Dari terminal Ubuntu client kedua, uji port baru:

   ```bash
   ssh -p 2222 nama-pengguna@alamat-ip-server
   ```

   Pastikan berhasil login sebelum menutup sesi lama. Setelah port baru teruji dan aturan akses sudah benar, aturan port lama 22 boleh dihapus jika memang tidak lagi diperlukan.

Jika koneksi baru gagal, gunakan sesi lama atau console pemulihan untuk memeriksa `sudo journalctl -u ssh -n 50` dan konfigurasi. Nonaktifkan atau perbaiki file `60-custom-port.conf`, validasi kembali dengan `sudo sshd -t`, lalu restart layanan. Mengganti port dapat mengurangi pemindaian otomatis biasa, tetapi **bukan pengganti** autentikasi kuat, pembaruan keamanan, firewall, atau VPN.

### Transfer file dengan SCP

Jalankan `scp` dari terminal Ubuntu client untuk menyalin file ke server:

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

Autentikasi kunci SSH memungkinkan login tanpa mengetik **kata sandi akun server** setiap kali terhubung. Buat pasangan kunci di komputer client:

```bash
ssh-keygen -t ed25519
```

Ikuti petunjuk untuk lokasi penyimpanan. Gunakan passphrase untuk melindungi private key jika perangkat client hilang atau diakses orang lain. Passphrase tersebut melindungi file kunci di client dan berbeda dari kata sandi akun server. **Jangan pernah membagikan atau mengirim private key**; hanya public key (`.pub`) yang dipasang ke server.

Pasang public key ke akun server:

```bash
ssh-copy-id nama-pengguna@alamat-ip-server
```

Jika SSH server menggunakan port khusus, misalnya `2222`:

```bash
ssh-copy-id -p 2222 nama-pengguna@alamat-ip-server
```

`ssh-copy-id` akan meminta autentikasi server yang masih tersedia, lalu menambahkan public key ke `~/.ssh/authorized_keys` pada akun tujuan. Setelah selesai, uji login menggunakan kunci saja:

```bash
ssh -o PreferredAuthentications=publickey -o PasswordAuthentication=no nama-pengguna@alamat-ip-server
```

Dengan port khusus:

```bash
ssh -p 2222 -o PreferredAuthentications=publickey -o PasswordAuthentication=no nama-pengguna@alamat-ip-server
```

Jika login berhasil, koneksi menggunakan kunci publik. Jika private key dilindungi passphrase, client mungkin tetap meminta passphrase tersebut; gunakan `ssh-agent` untuk mengingat passphrase selama sesi client. Jangan menonaktifkan autentikasi kata sandi server sebelum login dengan kunci berhasil diuji dan kamu memiliki akses console pemulihan.

Contoh sesi terminal di Ubuntu client (output ilustratif):

```console
ademulyana@ubuntu-client:~$ ssh-keygen -t ed25519
Enter file in which to save the key (/home/ademulyana/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Your public key has been saved in /home/ademulyana/.ssh/id_ed25519.pub

ademulyana@ubuntu-client:~$ ssh-copy-id -p 2222 ademulyana@192.168.1.50
Number of key(s) added: 1

ademulyana@ubuntu-client:~$ ssh -p 2222 -o PreferredAuthentications=publickey -o PasswordAuthentication=no ademulyana@192.168.1.50
Welcome to Ubuntu Server
ademulyana@ademulyana-server:~$
```

Jika gagal, periksa bahwa public key terpasang pada akun server yang benar, port dan alamat server tepat, serta permission direktori `.ssh` dan file `authorized_keys` sesuai. Pastikan akses alternatif masih tersedia sebelum mengubah konfigurasi autentikasi SSH.

## 3. Telnet

Telnet menyediakan sesi terminal melalui jaringan, tetapi **tidak mengenkripsi** kata sandi maupun data. Informasi yang dikirim dapat dibaca pihak lain yang dapat memantau lalu lintas jaringan. Karena itu, jangan gunakan Telnet untuk login atau mengirim data sensitif. Gunakan SSH sebagai gantinya.

### Menguji port layanan dengan Telnet

Pada Ubuntu client di lab atau jaringan tepercaya, client Telnet kadang digunakan untuk menguji apakah port TCP dapat dijangkau. Pengujian ini tidak membuktikan bahwa layanan aman atau berfungsi sepenuhnya.

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

Jangan memasang atau mengaktifkan server Telnet untuk administrasi Ubuntu. Pada Ubuntu, gunakan SSH untuk login jarak jauh; Telnet di bab ini hanya contoh client untuk menguji konektivitas TCP di jaringan lab.

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

### Terhubung dari Ubuntu client ke server RDP

Di Ubuntu client, pasang Remmina jika belum tersedia:

```bash
sudo apt update
sudo apt install remmina remmina-plugin-rdp
```

Buka Remmina dari menu aplikasi, pilih protokol **RDP**, masukkan alamat IP Ubuntu Server, lalu masuk dengan akun yang diizinkan. Pastikan firewall server hanya mengizinkan koneksi dari jaringan tepercaya.

## 5. VNC (Virtual Network Computing)

VNC menyediakan akses ke desktop grafis Ubuntu menggunakan server dan viewer. Konfigurasi sesi desktop bergantung pada lingkungan desktop yang dipasang. VNC tidak selalu mengenkripsi seluruh sesi; gunakan konfigurasi keamanan yang didukung server dan utamakan tunnel SSH atau VPN.

### Contoh server TigerVNC

Ubuntu Server memerlukan lingkungan desktop yang cocok sebelum sesi VNC dapat digunakan. Salah satu pilihan paket pada Ubuntu adalah TigerVNC:

```bash
sudo apt update
sudo apt install tigervnc-standalone-server
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

Perintah dan kebutuhan konfigurasi sesi dapat berbeda menurut paket TigerVNC dan versi Ubuntu. Periksa bantuan paket yang terpasang (`vncserver --help`) dan dokumentasi Ubuntu untuk paket tersebut. Jangan menganggap hanya menjalankan `vncserver` sudah menghasilkan desktop yang aman atau sesuai.

Pada Ubuntu client, pasang aplikasi viewer jika belum tersedia:

```bash
sudo apt update
sudo apt install tigervnc-viewer
```

### Menghubungkan dari Ubuntu client melalui tunnel SSH

Jangan mengekspos port VNC langsung ke internet. Dari terminal Ubuntu client, buat tunnel SSH:

```bash
ssh -L 5901:localhost:5901 nama-pengguna@alamat-ip-server
```

Biarkan koneksi SSH tetap terbuka. Sambungkan viewer VNC pada Ubuntu client ke:

```text
localhost:5901
```

Jika memakai display lain, sesuaikan port lokal dan port server. VNC tetap memerlukan autentikasi dan konfigurasi yang aman pada server.

Jika viewer terpasang di client, koneksi ke tunnel lokal dapat dilakukan dengan:

```bash
vncviewer localhost:5901
```

## 6. Contoh Sesi SSH

Output berikut ilustratif; alamat IP, fingerprint, dan banner dapat berbeda. Perintah dijalankan di terminal Ubuntu client:

```console
ademulyana@ubuntu-client:~$ ssh ademulyana@192.168.1.50
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
