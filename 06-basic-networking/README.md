# Basic Networking

Bab ini membahas pemeriksaan jaringan dasar pada Ubuntu Server serta dua cara mengelola koneksi: **Netplan** dan **NMTUI**. Netplan mengatur konfigurasi jaringan melalui file YAML, sedangkan NMTUI adalah antarmuka teks untuk mengelola koneksi menggunakan NetworkManager.

> **Peringatan:** Perubahan jaringan dapat memutus koneksi SSH. Sebelum mengubah konfigurasi server remote, siapkan akses console/out-of-band atau mekanisme pemulihan. Periksa nama interface dan konfigurasi yang sudah ada sebelum mengedit.

## Tujuan Pembelajaran

Setelah mempelajari bagian ini, kamu diharapkan dapat:

- Memeriksa alamat IP, interface, routing, dan DNS.
- Memahami perbedaan Netplan dan NMTUI.
- Mengatur konfigurasi DHCP atau IP statis dengan Netplan.
- Menggunakan NMTUI jika NetworkManager tersedia dan mengelola interface tersebut.

## 1. Dasar Jaringan dan Pemeriksaan

### Melihat interface dan alamat IP: `ip a`

```bash
ip a
```

`ip a` adalah bentuk singkat dari `ip address`. Perintah ini menampilkan interface jaringan dan alamat IP yang sedang aktif. Nama interface dapat berupa `enp0s3`, `ens18`, `eno1`, atau nama lain; jangan mengasumsikan namanya selalu `eth0`.

Perintah ini **tidak menampilkan atau mengedit file konfigurasi YAML**. Ia menunjukkan kondisi jaringan yang sedang berjalan. Pada Ubuntu, konfigurasi Netplan disimpan terpisah sebagai file YAML di `/etc/netplan/`; Netplan menerapkan konfigurasi tersebut, lalu `ip a` dapat digunakan untuk memeriksa alamat IP hasilnya.

Untuk ringkasan alamat:

```bash
ip -brief address
```

### Memeriksa routing: `ip route`

```bash
ip route
```

Menampilkan tabel routing. Baris yang dimulai dengan `default via` menunjukkan gateway default yang digunakan untuk mencapai jaringan lain.

### Memeriksa DNS

```bash
resolvectl status
```

Menampilkan informasi DNS yang dikelola oleh `systemd-resolved`, jika layanan tersebut digunakan. `/etc/resolv.conf` sering berupa tautan yang dikelola sistem; hindari mengeditnya langsung tanpa memahami pengelolaan DNS pada server.

### Menguji koneksi

```bash
ping -c 4 1.1.1.1
ping -c 4 ubuntu.com
```

Perintah pertama menguji konektivitas IP ke tujuan, sedangkan yang kedua juga bergantung pada resolusi DNS. Sebagian jaringan memblokir ICMP, jadi kegagalan `ping` saja belum membuktikan bahwa seluruh koneksi jaringan mati.

## 2. Netplan

### Apa itu Netplan?

Netplan adalah alat konfigurasi jaringan deklaratif yang digunakan Ubuntu. Konfigurasi disimpan dalam file YAML di `/etc/netplan/`, kemudian Netplan meneruskannya ke *renderer* jaringan:

- `systemd-networkd` biasanya digunakan pada Ubuntu Server.
- `NetworkManager` biasanya digunakan pada desktop dan dapat dipilih jika NetworkManager dipasang serta dikonfigurasi.

Netplan bukan layanan jaringan itu sendiri; ia membuat konfigurasi untuk renderer yang dipilih. Sebaiknya gunakan satu pengelola konfigurasi yang sesuai dengan renderer aktif dan jangan mengubah interface yang sama melalui dua pengelola berbeda.

### Memeriksa interface dan konfigurasi saat ini

```bash
ip -brief address
ip route
sudo netplan status
sudo netplan get
```

Perintah `netplan status` dan sebagian fiturnya bergantung pada versi Netplan. Jika perintah tidak tersedia pada versi sistem, gunakan `ip a`, `ip route`, dan baca file yang ada di `/etc/netplan/`.

Untuk melihat nama file konfigurasi:

```bash
ls -l /etc/netplan/
```

Nama file dapat berbeda, misalnya `50-cloud-init.yaml`. Periksa konfigurasi sebelum mengedit:

```bash
sudo cat /etc/netplan/50-cloud-init.yaml
```

Ganti nama file contoh tersebut dengan file yang benar pada server. Jika konfigurasi dibuat oleh cloud-init, perubahan manual dapat ditimpa oleh cloud-init; ikuti mekanisme konfigurasi dari penyedia server.

### Konfigurasi DHCP

DHCP meminta alamat IP, gateway, dan konfigurasi jaringan secara otomatis dari jaringan. Contoh berikut menggunakan interface `enp0s3`; sesuaikan dengan nama interface yang ditemukan melalui `ip -brief address`.

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: true
```

Simpan konfigurasi pada file YAML di `/etc/netplan/`. Indentasi YAML menggunakan spasi; jangan gunakan tab.

### Konfigurasi IP statis

Gunakan IP statis hanya jika alamat, prefix, gateway, dan DNS sudah ditentukan oleh administrator jaringan atau penyedia server.

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses:
        - 192.168.1.50/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

Ganti nilai contoh dengan konfigurasi jaringan yang benar. Alamat contoh dapat bertabrakan atau tidak cocok dengan jaringanmu jika disalin tanpa penyesuaian.

### Memvalidasi dan menerapkan konfigurasi

Sebelum menerapkan, batasi akses file konfigurasi karena dapat berisi rincian jaringan:

```bash
sudo chmod 600 /etc/netplan/01-netcfg.yaml
sudo netplan generate
```

Ganti `01-netcfg.yaml` dengan nama file yang kamu buat atau ubah. `netplan generate` memeriksa konfigurasi dan membuat konfigurasi untuk renderer tanpa menerapkannya.

Untuk menerapkan konfigurasi dengan kesempatan rollback otomatis jika konfirmasi tidak diberikan:

```bash
sudo netplan try
```

Periksa apakah koneksi dan akses SSH tetap berjalan, lalu konfirmasikan konfigurasi saat diminta. Jika tidak dikonfirmasi sebelum timeout, Netplan mencoba membatalkan perubahan. Tetap siapkan akses pemulihan karena rollback tidak menggantikan console jika terjadi masalah.

Jika bekerja melalui console lokal dan yakin dengan konfigurasi, terapkan dengan:

```bash
sudo netplan apply
```

Periksa hasilnya:

```bash
ip a
ip route
resolvectl status
```

### Contoh alur Netplan

Output berikut hanya ilustrasi; interface, alamat, dan hasil dapat berbeda:

```console
ademulyana@ademulyana-server:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 state UNKNOWN
    inet 127.0.0.1/8 scope host lo
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 state UP
    inet 192.168.1.50/24 brd 192.168.1.255 scope global enp0s3

ademulyana@ademulyana-server:~$ ls -l /etc/netplan/
-rw------- 1 root root 120 Oct 24 10:00 01-netcfg.yaml

ademulyana@ademulyana-server:~$ sudo netplan generate

ademulyana@ademulyana-server:~$ sudo netplan try
Do you want to keep these settings?
Press ENTER before the timeout to accept the new configuration

ademulyana@ademulyana-server:~$ ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 state UP
    inet 192.168.1.50/24 brd 192.168.1.255 scope global enp0s3

ademulyana@ademulyana-server:~$ ip route
default via 192.168.1.1 dev enp0s3
192.168.1.0/24 dev enp0s3 proto kernel scope link
```

Perintah yang berhasil sering kali tidak menampilkan output. Verifikasi konfigurasi menggunakan perintah pemeriksaan dan uji koneksi yang relevan.

## 3. NMTUI dan NetworkManager

### Apa itu NMTUI?

NMTUI (*NetworkManager Text User Interface*) adalah antarmuka berbasis teks untuk mengatur koneksi yang dikelola oleh NetworkManager. NMTUI bukan pengganti umum untuk semua konfigurasi Netplan:

- Jika interface menggunakan renderer `NetworkManager`, koneksi dapat dikelola dengan NMTUI.
- Jika interface menggunakan `systemd-networkd` (umum pada Ubuntu Server), NMTUI tidak mengelola interface tersebut.
- Pada konfigurasi Netplan, renderer `NetworkManager` harus dipilih untuk interface yang ingin dikelola oleh NetworkManager.

Periksa ketersediaan perintah dan layanan:

```bash
command -v nmtui
systemctl is-active NetworkManager
```

Jika `nmtui` tidak ditemukan atau NetworkManager tidak aktif, jangan memasang atau mengganti renderer pada server remote tanpa memahami dampaknya. Perubahan tersebut dapat mengganggu jaringan yang sedang digunakan.

### Membuka NMTUI

```bash
sudo nmtui
```

Di menu NMTUI, gunakan tombol panah untuk memilih:

- **Edit a connection** — mengubah koneksi yang sudah ada atau membuat koneksi baru.
- **Activate a connection** — mengaktifkan atau menonaktifkan koneksi.
- **Set system hostname** — mengubah hostname sistem.

Pada **Edit a connection**, pilih interface/koneksi, atur IPv4/IPv6 menjadi otomatis (DHCP) atau manual, isi alamat, gateway, dan DNS sesuai jaringan, lalu simpan. Kembali ke menu utama dan pilih **Activate a connection** untuk mengaktifkan koneksi yang diperlukan.

Tekan `Esc` untuk kembali atau keluar dari menu. Setelah perubahan, verifikasi menggunakan `ip address`, `ip route`, dan `resolvectl status`.

### Contoh sesi NMTUI

Menu dan nama koneksi dapat berbeda menurut versi dan konfigurasi:

```console
ademulyana@ademulyana-server:~$ sudo nmtui

┌──────────────────── NetworkManager TUI ────────────────────┐
│ Please select an option                                    │
│                                                            │
│ Edit a connection                                          │
│ Activate a connection                                      │
│ Set system hostname                                        │
│                                                            │
│                    <OK>        <Quit>                      │
└────────────────────────────────────────────────────────────┘
```

Tampilan ini adalah ilustrasi. Opsi yang tersedia bergantung pada versi NetworkManager dan koneksi yang terdeteksi.

## 4. Memilih Netplan atau NMTUI

| Kebutuhan | Pilihan yang sesuai |
|---|---|
| Mengelola konfigurasi Ubuntu Server melalui file YAML dan renderer `networkd` | Netplan |
| Mengatur koneksi secara interaktif pada sistem yang dikelola NetworkManager | NMTUI |
| Server remote tanpa akses console cadangan | Hindari perubahan jaringan langsung; siapkan pemulihan terlebih dahulu |

Jangan mengatur interface yang sama melalui Netplan/networkd dan NetworkManager secara bersamaan. Periksa konfigurasi yang aktif terlebih dahulu.

## Kebiasaan Aman

- Catat konfigurasi awal dan siapkan akses console sebelum mengubah jaringan server remote.
- Pastikan nama interface, alamat, prefix, gateway, dan DNS sudah benar.
- Gunakan `sudo netplan generate` untuk memvalidasi konfigurasi dan `sudo netplan try` untuk mencoba perubahan dengan mekanisme konfirmasi.
- Simpan salinan konfigurasi sebelum mengedit file di `/etc/netplan/`.
- Jangan menyalin IP statis contoh tanpa memastikan alamat tersebut sesuai dan tidak digunakan perangkat lain.
- Jangan mengubah permission, renderer, atau layanan jaringan tanpa memahami cara pemulihannya.

## Ringkasan

- `ip address`, `ip route`, dan `resolvectl status` membantu memeriksa jaringan.
- Netplan mengelola konfigurasi YAML dan meneruskannya ke `systemd-networkd` atau `NetworkManager`.
- NMTUI adalah antarmuka teks untuk koneksi yang dikelola NetworkManager.
- Gunakan `netplan generate` untuk validasi, `netplan try` untuk mencoba perubahan, dan `netplan apply` untuk menerapkannya secara langsung.
- Perubahan jaringan remote berisiko memutus SSH; selalu siapkan akses pemulihan.

## Selanjutnya

Lanjutkan ke [Package Management](../07-package-management/README.md) untuk mempelajari pengelolaan paket perangkat lunak di Ubuntu Server.
