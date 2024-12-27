---
title: "Welcome to my homelab! - Iklan??"
date: 2024-12-28 00:00:00 +0700
categories: [homelab, DNS]
tags: [homelab, firewall, adblocker, DNS, proxmox]
---
Halo..

Setelah beberapa lama deploy pfsense, speed internet rumah sudah membaik. Tapi masih ada yang mengganjal.  
Pernah kepikiran ndak, berapa banyak packet yang mondar-mandir hanya buat nampilkan iklan di browser atau app di handphone? Ternyata buanyak! Dan tentu ini jadi beban tersendiri ke total throughput ISP ke rumah yang sebetulnya tidak perlu.
Saya membutuhkan _ad-blocker_, syukur-syukur yang bisa pula memperkuat firewall saya.   
Dan saya menemukan _pi-hole_.

"Lha? Ini kan buat Raspbian nya Raspberry Pi?"  
Yha, tapi Raspbian pun adalah turunan dari Armbian, salah satu distro Linux untuk prosesor ARM. Jadi bisa dengan mudah dijalankan di mesin virtual.

So, sekalian saya prepare buat bangun virtualization server homelab saya, _ad-blocker_ ini jadi VM server pertama yang saya install demi lebih mengirit traffic packet yang membebani throughput ke depan..

![Logo pi-hole](https://upload.wikimedia.org/wikipedia/commons/0/00/Pi-hole_Logo.png)

 **Panduan Lengkap Pi-hole: Solusi Pemblokiran Iklan di Jaringan Rumah dengan Mesin Virtual**

Dalam dunia yang dipenuhi oleh iklan online, kebutuhan akan pengalaman internet yang lebih bersih semakin meningkat. Salah satu solusi yang efektif dan populer adalah Pi-hole, sebuah perangkat lunak open-source yang bertindak sebagai DNS sinkhole untuk memblokir iklan dan pelacak di tingkat jaringan. Artikel ini akan membahas apa itu Pi-hole, cara penggunaannya, serta panduan untuk mengatur Pi-hole di lingkungan mesin virtual untuk lab rumah.

### Apa itu Pi-hole?

Pi-hole adalah aplikasi berbasis Linux yang berfungsi sebagai pemblokir iklan dan pelacak dengan cara bekerja sebagai server DNS. Semua permintaan DNS dari perangkat dalam jaringan akan melewati Pi-hole terlebih dahulu, sehingga domain yang masuk daftar hitam dapat diblokir sebelum mencapai perangkat Anda.

#### Fitur Utama Pi-hole:
- **Pemblokiran Iklan di Seluruh Jaringan:** Tidak hanya untuk browser, tetapi juga untuk aplikasi di perangkat Anda.
- **Meningkatkan Kecepatan Internet:** Dengan mengurangi jumlah permintaan iklan dan pelacak.
- **Monitoring dan Statistik Jaringan:** Memberikan laporan rinci tentang penggunaan DNS.
- **Kompatibilitas Tinggi:** Dapat digunakan bersama perangkat lain seperti router dan server VPN.

### Keuntungan Menggunakan Pi-hole di Mesin Virtual

Deploying Pi-hole di mesin virtual (VM) memiliki beberapa kelebihan, terutama untuk pengguna yang memiliki lab rumah:

1. **Efisiensi Biaya:** Tidak memerlukan perangkat keras tambahan seperti Raspberry Pi.
2. **Fleksibilitas:** Mesin virtual mudah dikelola, diubah, atau dihapus tanpa memengaruhi perangkat keras.
3. **Eksperimen Aman:** Cocok untuk pengguna yang ingin bereksperimen tanpa risiko merusak perangkat keras utama.
4. **Skalabilitas:** Memungkinkan integrasi dengan layanan lain di lab rumah.

### Langkah-Langkah Instalasi Pi-hole di Mesin Virtual

#### 1. **Persiapan Lingkungan Mesin Virtual**
- **Platform Virtualisasi:** Gunakan software seperti VirtualBox, VMware, atau Proxmox.
- **Sistem Operasi Guest:** Pi-hole mendukung berbagai distro Linux seperti Debian, Ubuntu, atau Fedora.
- **Spesifikasi Minimum VM:**
  - **CPU:** 1 core
  - **RAM:** 512 MB
  - **Penyimpanan:** 2 GB (lebih baik menggunakan SSD)
  - **Jaringan:** Kartu jaringan bridge untuk akses langsung ke jaringan rumah.

#### 2. **Instalasi Sistem Operasi Linux**
- Unduh ISO Linux dari situs resmi distro yang dipilih.
- Buat VM baru dengan spesifikasi di atas.
- Pasang dan konfigurasi sistem operasi Linux di dalam VM.

#### 3. **Instalasi Pi-hole**
1. **Perbarui Sistem Operasi:**
   ```
   sudo apt update && sudo apt upgrade -y
   ```
2. **Unduh dan Jalankan Skrip Instalasi Pi-hole:**
   ```
   curl -sSL https://install.pi-hole.net | bash
   ```
3. **Ikuti Wizard Instalasi:**
   - Pilih jaringan yang sesuai (kartu jaringan VM).
   - Tentukan upstream DNS server (seperti Google DNS atau Cloudflare).
   - Pilih daftar blokir (blocklist) bawaan atau tambahkan daftar khusus Anda.

#### 4. **Konfigurasi dan Integrasi Jaringan**
- **Atur DNS Gateway di Router:** Arahkan semua perangkat dalam jaringan untuk menggunakan IP VM sebagai DNS utama.
- **Tambahkan Daftar Blokir:** Tambahkan lebih banyak blocklist melalui antarmuka web Pi-hole.
- **Monitor Statistik:** Akses laporan dan grafik penggunaan DNS dari dashboard web Pi-hole.

### Tips Penggunaan Pi-hole di Mesin Virtual

1. **Snapshot VM:** Sebelum melakukan perubahan besar, buat snapshot VM untuk rollback cepat jika terjadi kesalahan.
2. **Optimasi Performa:** Batasi resource VM agar tidak mengganggu layanan lain di host.
3. **Pemantauan Berkala:** Periksa log untuk memantau aktivitas DNS yang mencurigakan.
4. **Integrasi dengan VPN:** Gabungkan dengan OpenVPN atau WireGuard untuk perlindungan tambahan saat di luar jaringan rumah.

### Kesimpulan

Pi-hole adalah solusi efektif untuk memblokir iklan dan meningkatkan pengalaman internet di jaringan rumah. Dengan memanfaatkan mesin virtual, Anda dapat menikmati fleksibilitas dan efisiensi biaya tanpa memerlukan perangkat keras tambahan. Pi-hole di lab rumah Anda tidak hanya membersihkan jaringan dari iklan, tetapi juga memberikan wawasan yang berguna tentang aktivitas DNS. Terbukti bisa dilihat di _dashboard_ banyaknya request yang terblokir sangat banyak! Jadi yuk, nikmati internet yang lebih cepat, bersih, dan bebas gangguan!

Salam..