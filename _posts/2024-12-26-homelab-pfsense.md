---
title: "Welcome to my homelab! - The Begining"
date: 2024-12-27 00:00:00 +0700
categories: [homelab, router]
tags: [homelab, firewall, router, pfsense]
---

Halo..
Ini adalah catatan instalasi firewall router di homelab saya.

Berawal dari speed ISP FTTH rumah yang naik-turun, akhirnya saya coba cek RX level di router dengan OPM. Ternyata normal, sekitar -7dBm, masih di threshold SLA mereka.
Oke, coba pantau traffic di wireshark. Dan voila, banyak traffic ke domain yang tidak perlu, dan tampaknya setting firewall router ISP di "Strict" tidak mampu menyelesaikan masalah. Baik, saya perlu firewall sendiri.

Mengadopsi next gen firewall tentu bukan pilihan yang tepat buat traffic rumahan. Apalagi menilik harganya. Ndak cocok, walaupun pasti keren sekali! Jadilah saya pakai alternatifnya. Ada beberapa hardware ex thin client dan laptop/PC tua di gudang. Mari disalah gunakan..

Saya memilih salah satu solusi dengan pfSense dari Netgate karena tersedia versi free dengan fitur yang saya nilai sangat cukup buat kebutuhan saya. Sebuah laptop tua "buntung" AMD E-450 dengan RAM DDR3l 2GB saya pilih buat hardwarenya. Buntung disini karena disamping layar yang rusak, batt yang mati, pun keyboard dan touchpad-nya juga tidak berfungsi. Tapi bukan masalah, pfSense bisa tetap kita akses baik via ssh atau pun web. Hanya CPU ini tidak support instruksi kriptografi AES-NI yang diperlukan buat VPN nantinya. Tapi untuk trial, masih oke lah. 



![Logo pfSense](https://cdn.brandfetch.io/idZVqCtze8/w/820/h/244/theme/dark/logo.png)

**Panduan Lengkap pfSense: Solusi Firewall dan Router Hemat Biaya dengan Perangkat Lama**

Dalam era digital seperti sekarang, keamanan jaringan menjadi prioritas utama, baik untuk individu maupun bisnis. Salah satu solusi populer dan hemat biaya adalah pfSense, perangkat lunak open-source yang berfungsi sebagai firewall dan router. Artikel ini akan membahas penggunaan pfSense, cara mengatur, dan bagaimana memanfaatkan perangkat keras bekas untuk implementasi yang murah namun efektif.

### Apa itu pfSense?

pfSense adalah distribusi perangkat lunak berbasis FreeBSD yang dirancang untuk menyediakan fitur firewall dan routing yang kuat dan fleksibel. Dengan antarmuka berbasis web yang intuitif, pfSense cocok untuk digunakan oleh pemula maupun profesional IT. Berikut adalah beberapa fitur unggulan pfSense:

- **Firewall dan NAT (Network Address Translation):** Mengamankan jaringan dari ancaman eksternal.
- **VPN (Virtual Private Network):** Mendukung OpenVPN, IPsec, dan lain-lain.
- **QoS (Quality of Service):** Mengelola bandwidth dengan lebih efisien.
- **Monitoring Jaringan:** Menyediakan laporan dan grafik penggunaan jaringan.
- **Ekstensi Modular:** Mendukung instalasi plugin tambahan seperti Snort untuk IDS/IPS atau Squid untuk proxy.

### Keuntungan Menggunakan pfSense

1. **Open Source dan Gratis**: Tidak memerlukan lisensi mahal.
2. **Skalabilitas**: Cocok untuk rumah, kantor kecil, hingga data center.
3. **Hemat Biaya**: Bisa dijalankan di perangkat keras lama atau bekas.
4. **Kustomisasi Tinggi**: Pengguna dapat mengatur sesuai kebutuhan spesifik jaringan mereka.

### Memanfaatkan Perangkat Lama untuk pfSense

Salah satu keunggulan pfSense adalah kemampuannya berjalan di perangkat keras dengan spesifikasi rendah. Ini memungkinkan Anda menggunakan komputer atau laptop bekas yang sudah tidak digunakan. Berikut adalah spesifikasi minimum yang direkomendasikan:

- **CPU**: Prosesor 1 GHz atau lebih.
- **RAM**: 1 GB (disarankan 2 GB atau lebih untuk fitur tambahan seperti VPN).
- **Penyimpanan**: Minimal 8 GB (SSD lebih baik untuk kinerja optimal).
- **Kartu Jaringan**: Minimal dua port Ethernet (bisa menggunakan NIC tambahan), atau bisa juga menggunakan routing VLAN.

### Langkah-Langkah Instalasi pfSense

#### 1. **Unduh dan Persiapkan Media Instalasi**

- Kunjungi situs resmi pfSense ([https://www.pfsense.org](https://www.pfsense.org)) untuk mengunduh file ISO.
- Gunakan perangkat lunak seperti Rufus atau Balena Etcher untuk membuat bootable USB dari file ISO tersebut.

#### 2. **Persiapkan Perangkat Keras**

- Pastikan perangkat keras Anda memiliki dua kartu jaringan (NIC), satu untuk koneksi WAN (internet) dan satu untuk LAN (jaringan lokal).
- Pasang perangkat tambahan seperti switch jika diperlukan.

#### 3. **Instalasi pfSense**

- Boot perangkat dari USB yang telah dibuat.
- Ikuti wizard instalasi yang akan memandu Anda melalui proses pengaturan awal, seperti partisi disk dan konfigurasi jaringan.

#### 4. **Konfigurasi Awal**

- Setelah instalasi selesai, akses antarmuka web pfSense melalui browser dengan alamat default `192.168.1.1`.
- Login dengan kredensial default (`admin`/`pfsense`) dan ikuti wizard konfigurasi awal untuk mengatur WAN, LAN, dan pengaturan lainnya.

### Mengenal Plugin Utama di pfSense

#### 1. **pfBlockerNG**

pfBlockerNG adalah plugin yang digunakan untuk memblokir lalu lintas berbahaya atau tidak diinginkan dari wilayah tertentu atau daftar IP/domain tertentu. Plugin ini sangat bermanfaat untuk:
- **Memblokir Ancaman:** Melindungi jaringan dari IP berbahaya secara global.
- **Filtering Konten:** Membatasi akses ke situs-situs tertentu berdasarkan kategori.
- **Efisiensi Bandwidth:** Mengurangi penggunaan bandwidth oleh lalu lintas yang tidak penting.

#### 2. **Squid**

Squid adalah plugin proxy caching yang memungkinkan kontrol akses internet yang lebih baik. Fitur utamanya meliputi:
- **Caching Konten:** Mengurangi waktu muat dan penggunaan bandwidth dengan menyimpan data situs yang sering diakses.
- **Kontrol Akses:** Membatasi akses ke situs atau layanan tertentu.
- **Pemantauan Penggunaan:** Memberikan laporan tentang penggunaan internet oleh pengguna.

#### 3. **Snort**

Snort adalah sistem deteksi dan pencegahan intrusi (IDS/IPS) yang kuat. Plugin ini membantu:
- **Mendeteksi Serangan:** Mengidentifikasi pola serangan jaringan dan mengambil tindakan pencegahan.
- **Mencegah Ancaman:** Memblokir lalu lintas yang mencurigakan secara otomatis.
- **Analisis Keamanan:** Memberikan wawasan mendalam tentang ancaman yang mencoba menargetkan jaringan Anda.

### Optimalisasi dan Penggunaan Lanjutan

Setelah pfSense berjalan, Anda dapat mulai memanfaatkan fitur-fiturnya:

- **Setup VPN**: Buat koneksi VPN untuk keamanan akses jarak jauh.
- **Monitoring Jaringan**: Pantau penggunaan bandwidth dan identifikasi potensi masalah.
- **Content Filtering**: Gunakan plugin seperti Squid untuk mengontrol akses internet.
- **Keamanan Tambahan**: Tambahkan Snort untuk mendeteksi dan mencegah ancaman jaringan.

### Kesimpulan

pfSense adalah solusi hemat biaya yang memungkinkan Anda membangun sistem keamanan jaringan yang handal dengan menggunakan perangkat keras bekas. Dengan sedikit usaha dan pemahaman, pfSense dapat menjadi andalan untuk mengelola jaringan rumah atau bisnis kecil. Jangan ragu untuk mencoba dan eksplorasi fitur-fiturnya, karena keamanan jaringan adalah investasi yang tidak ternilai harganya.




Salam..

