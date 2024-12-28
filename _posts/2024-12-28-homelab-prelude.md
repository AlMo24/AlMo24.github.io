---
title: "Welcome to my homelab! - Prelude"
date: 2024-12-28 00:00:00 +0700
categories: [homelab]
tags: [homelab, why]
---
Halo..

Sebelum lanjut lagi, rasanya perlu ada semacam penjelasan background: kenapa harus homelab?  
Saya jelaskan ya..

### **Pentingnya Membangun Home Lab Komputer**

Home lab komputer adalah lingkungan IT pribadi yang digunakan untuk eksperimen, belajar, dan mengembangkan keterampilan teknologi. Home lab sangat penting bagi profesional atau penggemar di bidang seperti jaringan, administrasi sistem, keamanan siber, IoT, atau pengembangan perangkat lunak. Berikut alasan mengapa home lab diperlukan:

#### **1. Pengalaman Praktis**
   - Memberikan pembelajaran praktis yang melampaui konsep teoretis. Hands-on experience is always the best.
   - Memungkinkan eksperimen dengan konfigurasi, protokol, dan alat tanpa risiko terhadap sistem nyata. Anggaplah latihan sebelum benar-benar handling system buat production.

#### **2. Pengembangan Keterampilan**
   - Memperkuat keahlian dalam mengatur sistem, memecahkan masalah, dan mengimplementasikan teknologi seperti virtualisasi, komputasi awan, atau platform IoT.
   - Mempersiapkan testbed untuk sertifikasi seperti CCNA, CCNP, Azure, AWS, atau CompTIA, atau pun lainnya.

#### **3. Membangun Portofolio**
   - Menunjukkan kemampuan kepada calon pemberi kerja dengan proyek atau jaringan pribadi.

#### **4. Fleksibilitas dan Hemat Biaya**
   - Mengurangi kebutuhan menggunakan lab pelatihan eksternal.
   - Memberikan akses ke sumber daya untuk latihan kapan saja.

---

### **Praktik Terbaik dalam Membangun Home Lab**

#### **1. Tentukan Tujuan Anda**
   - Identifikasi tujuan Anda (misalnya, belajar IoT, virtualisasi, komputasi awan, atau jaringan).
   - Rencanakan sumber daya yang dibutuhkan (hardware, software, dan alat).

#### **2. Mulai dari yang Sederhana**
   - Mulailah dengan setup sederhana dan tingkatkan sesuai kebutuhan. Start small guys..
   - Fokus pada komponen penting seperti komputer yang baik, software virtualisasi, dan dasar-dasar jaringan.

#### **3. Gunakan Virtualisasi**
   - Manfaatkan hypervisor seperti VMware Workstation, VirtualBox, atau Proxmox untuk mensimulasikan beberapa sistem dalam satu mesin.
   - Menghemat ruang, daya, dan biaya.

#### **4. Optimalkan untuk Skalabilitas**
   - Rencanakan untuk upgrade agar mendukung teknologi baru.
   - Pilih hardware modular yang memungkinkan penggantian atau perluasan komponen.

#### **5. Manfaatkan Open Source**
   - Gunakan alat gratis atau open-source seperti Linux, OpenStack, Kubernetes, atau FreeNAS untuk eksperimen.
   - Mengurangi biaya lisensi software.

#### **6. Dokumentasikan dan Uji**
   - Buat dokumentasi untuk konfigurasi, setup, dan eksperimen Anda.
   - Uji skenario berbeda secara rutin untuk mengasah keterampilan pemecahan masalah.

---

### **Setup Biaya Terendah dengan Performa Maksimal**

#### **1. Rekomendasi Hardware**
   - **Komputer Utama**:
     - **CPU**: Pilih CPU dengan dukungan virtualisasi (misalnya, Intel Core i5/i7 atau AMD Ryzen 5/7), bekas pun tidak masalah.
     - **RAM**: Minimal 16 GB, idealnya dapat di-upgrade hingga 32 GB untuk menjalankan beberapa virtual machine (VM).
     - **Penyimpanan**: Gunakan SSD untuk kecepatan (minimal 500 GB) dan HDD tambahan untuk kebutuhan penyimpanan besar.
     - **Motherboard**: Pastikan kompatibel untuk upgrade di masa depan. Tapi mulai dengan yang ada/ sesuai budget pun OK.

   - **Peralatan Jaringan**:
     - Router atau switch sederhana (managed switch untuk lab jaringan).
     - UTP kabel dan RJ45 dan perlatan crimping nya.
     - Jika ada rak khusus tentu akan baik, kalau tidak ada tidak masalah, tapi pastikan aman dari gangguan fisik.
     - Raspberry Pi atau Arduino semacamnya beserta interface nya untuk proyek IoT.

   - **Cadangan Daya**: UPS (Uninterruptible Power Supply) untuk mencegah gangguan (optional).

#### **2. Manfaatkan Pasar Bekas**
   - Beli server refurbished (misalnya, Dell PowerEdge atau HP ProLiant) untuk pembelajaran tingkat enterprise.
   - Pertimbangkan perangkat jaringan bekas seperti router dan switch Cisco.

#### **3. Integrasi Cloud**
   - Gunakan layanan cloud gratis seperti AWS, Azure, atau Google Cloud untuk setup hybrid.
   - Gabungkan sumber daya lokal dengan instance cloud untuk lingkungan fleksibel dengan biaya rendah.

#### **4. Pilihan Software**
   - **Sistem Operasi**: Distribusi Linux (Ubuntu, CentOS, atau Debian).
   - **Virtualisasi**: VirtualBox (gratis) atau Proxmox (open source).
   - **Simulasi Jaringan**: GNS3, Cisco Packet Tracer, atau EVE-NG.

#### **5. Komponen DIY**
   - Gunakan komputer lama sebagai server.
   - Bangun perangkat IoT menggunakan komponen murah (misalnya, mikrokontroler ESP32).

#### **6. Efisiensi Energi**
   - Gunakan hardware hemat energi untuk mengurangi konsumsi daya.
   - Pilih server kompak atau mini-PC seperti Intel NUC untuk setup hemat daya.

Ingat, WITH GREAT POWER, COMES GREAT POWERBILL!

---

### **Contoh Setup Hemat Biaya**

- **Hardware**:
  - PC bekas (Rp 4.500.000) dengan i5 Gen 4, 16 GB RAM, dan SSD.
  - Managed switch (Rp 250.000 – Rp 1.500.000, refurbished).
  - Raspberry Pi 4 (Rp 800.000) untuk proyek IoT.

- **Software**:
  - VirtualBox (gratis).
  - Distribusi Linux (gratis).
  - GNS3 untuk simulasi jaringan (gratis).

- **Cloud**:
  - Layanan AWS free tier untuk pengalaman server jarak jauh.

- **Estimasi Biaya**: ~Rp 6.500.000 – Rp 8.000.000 untuk investasi awal.

Dengan perencanaan yang hati-hati dan bertahap, Anda dapat membangun home lab yang fleksibel untuk mengasah keterampilan dan mendukung karier, khususnya di bidang IoT engineering.


Salam..