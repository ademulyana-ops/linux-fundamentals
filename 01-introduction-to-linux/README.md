# Introduction to Linux

Linux adalah sistem operasi yang banyak digunakan pada server, komputer pribadi, perangkat seluler, perangkat jaringan, dan sistem tertanam. Linux dikenal fleksibel, stabil, dan dapat digunakan maupun dikembangkan secara bebas.

Materi ini memperkenalkan konsep dasar Linux sebelum masuk ke instalasi dan penggunaan perintah terminal.

## Tujuan Pembelajaran

Setelah mempelajari bagian ini, kamu diharapkan dapat:

- Menjelaskan apa itu Linux dan kernel.
- Memahami hubungan antara kernel, distribusi, dan aplikasi.
- Mengenali beberapa distribusi Linux yang umum.
- Menyebutkan contoh penggunaan serta kelebihan Linux.
- Memahami perbedaan dasar antara antarmuka grafis dan terminal.

## Apa Itu Linux?

Secara teknis, **Linux adalah kernel**: bagian inti sistem operasi yang menghubungkan perangkat keras dengan program. Kernel mengatur hal-hal seperti penggunaan prosesor, memori, perangkat penyimpanan, dan komunikasi dengan perangkat lain.

Dalam penggunaan sehari-hari, kata **Linux** biasanya merujuk pada sistem operasi lengkap yang menggunakan kernel Linux. Sistem lengkap ini terdiri dari kernel, utilitas sistem, pustaka, aplikasi, dan alat pengelolaan perangkat lunak. Banyak distribusi juga menyertakan perangkat lunak dari proyek GNU, sehingga istilah **GNU/Linux** kadang digunakan untuk menyebutnya.

Gambaran sederhananya:

```text
Pengguna
   ↓
Aplikasi dan shell
   ↓
Utilitas sistem dan pustaka
   ↓
Kernel Linux
   ↓
Perangkat keras
```

## Kernel, Distribusi, dan Desktop

### Kernel

Kernel adalah inti yang berkomunikasi dengan perangkat keras dan menyediakan layanan dasar bagi program. Kernel Linux dikembangkan secara terbuka dan digunakan oleh banyak sistem operasi.

### Distribusi Linux

**Distribusi** (sering disebut *distro*) adalah sistem operasi yang mengemas kernel Linux bersama perangkat lunak dan alat pengelolaan tertentu. Setiap distro dapat memiliki tampilan, jadwal rilis, dan cara pengelolaan paket yang berbeda.

Beberapa distribusi yang umum:

| Distribusi | Gambaran umum |
|---|---|
| Ubuntu | Populer untuk pemula, desktop, dan server; berbasis Debian. |
| Debian | Dikenal stabil dan memiliki ekosistem paket yang luas. |
| Fedora | Menghadirkan teknologi Linux yang relatif baru dan didukung komunitas Fedora. |
| Linux Mint | Berbasis Ubuntu dan dirancang agar nyaman bagi pengguna desktop baru. |
| Arch Linux | Memberi pengguna kontrol tinggi dan biasanya memerlukan konfigurasi lebih mandiri. |
| Rocky Linux | Distribusi yang berfokus pada penggunaan server dan kompatibilitas ekosistem Enterprise Linux. |

Semua distro tersebut menggunakan kernel Linux, tetapi dapat berbeda dalam pilihan perangkat lunak, tampilan, dan alat administrasinya.

### Lingkungan Desktop

Pada komputer desktop, distro dapat menyediakan **lingkungan desktop**, yaitu tampilan grafis yang berisi jendela, panel, menu, dan aplikasi. Contohnya GNOME, KDE Plasma, dan Xfce. Sebagian instalasi server tidak menyertakan desktop grafis agar lebih ringan dan dikelola melalui terminal.

## Terminal dan Antarmuka Grafis

Linux dapat digunakan melalui antarmuka grafis (*Graphical User Interface* atau GUI) maupun terminal. Terminal menerima perintah teks melalui sebuah program yang disebut **shell**. Bash adalah salah satu shell yang umum digunakan.

Terminal berguna untuk mengelola file, memasang perangkat lunak, memeriksa sistem, dan mengotomatisasi pekerjaan. Perintah dasar akan dibahas pada bagian [Basic Commands](../03-basic-commands/README.md).

## Di Mana Linux Digunakan?

Linux digunakan dalam berbagai lingkungan, antara lain:

- **Server dan cloud** — menjalankan situs web, aplikasi, basis data, dan layanan jaringan.
- **Komputer desktop dan laptop** — untuk belajar, bekerja, pemrograman, dan penggunaan sehari-hari.
- **Perangkat seluler** — Android menggunakan kernel Linux, dengan lapisan perangkat lunak dan ekosistemnya sendiri.
- **Perangkat jaringan dan sistem tertanam** — misalnya router, perangkat IoT, dan komputer kecil.
- **Komputasi berkinerja tinggi** — banyak sistem superkomputer menggunakan Linux.

## Kelebihan Linux

- **Terbuka dan fleksibel** — kode sumber Linux dapat dipelajari dan dikembangkan sesuai lisensinya.
- **Banyak pilihan** — pengguna dapat memilih distro dan lingkungan desktop sesuai kebutuhan.
- **Stabil dan efisien** — Linux banyak digunakan untuk sistem yang perlu berjalan dalam waktu lama.
- **Kuat untuk administrasi dan otomatisasi** — terminal dan skrip membantu mengelola tugas berulang.
- **Komunitas dan dokumentasi luas** — banyak panduan dan forum tersedia untuk membantu pengguna.
- **Pilihan perangkat lunak beragam** — aplikasi umumnya dipasang melalui repositori distribusi atau sumber resmi lainnya.

Pengalaman menggunakan Linux tetap bergantung pada distro, perangkat keras, aplikasi, dan konfigurasi. Tidak semua aplikasi atau perangkat memiliki dukungan yang sama di setiap distro.

## Linux dan Sistem Operasi Lain

Linux, Windows, dan macOS sama-sama menyediakan lingkungan untuk menjalankan aplikasi dan mengelola perangkat keras. Perbedaannya antara lain pada desain, ekosistem perangkat lunak, lisensi, dan cara administrasi.

Pada Linux, pengguna sering berinteraksi dengan repositori perangkat lunak dan terminal. Namun, banyak distro juga menyediakan aplikasi grafis untuk tugas sehari-hari. Memilih sistem operasi yang tepat bergantung pada kebutuhan dan aplikasi yang digunakan.

## Istilah Penting

| Istilah | Arti |
|---|---|
| Kernel | Inti sistem operasi yang mengelola perangkat keras dan menyediakan layanan bagi program. |
| Distro | Sistem operasi yang menggabungkan kernel Linux dengan perangkat lunak dan alat tertentu. |
| Shell | Program yang membaca dan menjalankan perintah pengguna. |
| Terminal | Aplikasi untuk berinteraksi dengan shell menggunakan teks. |
| GUI | Antarmuka grafis yang menggunakan elemen seperti jendela, ikon, dan menu. |
| Repository | Kumpulan paket perangkat lunak yang dikelola dan disediakan untuk suatu distro. |

## Ringkasan

- Linux secara teknis adalah kernel, sedangkan distro menyediakan sistem lengkap yang menggunakannya.
- Distro berbeda dalam perangkat lunak, tampilan, dan cara pengelolaan, tetapi sama-sama menggunakan kernel Linux.
- Linux digunakan mulai dari komputer pribadi hingga server, perangkat jaringan, dan sistem tertanam.
- Terminal adalah salah satu cara penting untuk menggunakan dan mengelola Linux, tetapi bukan satu-satunya.

## Selanjutnya

Lanjutkan ke [Installation](../02-installation/README.md) untuk mempelajari pilihan dan persiapan instalasi Linux.
