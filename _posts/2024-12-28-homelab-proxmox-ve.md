---
title: "Welcome to my homelab! - Micro Datacenter 1"
date: 2024-12-28 00:00:00 +0700
categories: [homelab, virtualization]
tags: [homelab, virtualization, proxmox]
---

Halo..

Disini saya akan jelaskan tentang Proxmox VM.
Saya memilih ini dibanding misalnya VMWare, karena selain saya sudah pernah deploy VMWare sebelunya, saya ingin mencoba sesuatu yang lain. Ternyata Proxmox menarik untuk diexplore karena selain lebih sesuai dengan keperluan, juga dukungan komunitas yang besar, dan hardware nya lebih sesuai buat tujuan saya.

![logo-proxmox](https://www.proxmox.com/images/proxmox/logos/mediakit-proxmox-server-solutions-logos-light.svg)

### **Proxmox VE: Deskripsi dan Keunggulan**

**Proxmox VE (Virtual Environment)** adalah platform open-source berbasis Linux yang digunakan untuk manajemen virtualisasi. Proxmox VE mengintegrasikan **KVM (Kernel-based Virtual Machine)** untuk virtualisasi penuh dan **LXC (Linux Containers)** untuk virtualisasi berbasis kontainer, memberikan fleksibilitas untuk menjalankan berbagai sistem operasi atau aplikasi secara efisien di lingkungan yang terisolasi.

#### **Keunggulan Menggunakan Proxmox VE**

1. **Gratis dan Open-Source**  
   - Proxmox VE dapat diunduh dan digunakan tanpa biaya lisensi.  
   - Tersedia komunitas besar yang aktif mendukung dan menyediakan dokumentasi.

2. **Virtualisasi Ganda (KVM & LXC)**  
   - Kombinasi KVM untuk virtualisasi penuh dan LXC untuk kontainer membuatnya fleksibel untuk berbagai kebutuhan.

3. **Manajemen Web yang Mudah**  
   - Proxmox memiliki antarmuka berbasis web yang intuitif, memungkinkan pengguna mengelola virtual machine (VM), kontainer, jaringan, dan penyimpanan dari satu tempat.

4. **Cluster dan High Availability (HA)**  
   - Mendukung clustering untuk mengelola beberapa server Proxmox dalam satu konsol.  
   - Fitur HA memastikan VM tetap berjalan meskipun terjadi kegagalan hardware.

5. **Dukungan Backup dan Restore**  
   - Proxmox memiliki fitur backup dan restore yang kuat, termasuk integrasi dengan alat seperti vzdump dan Proxmox Backup Server.

6. **Dukungan ZFS**  
   - Sistem file ZFS memungkinkan snapshot cepat, replikasi, dan perlindungan data yang kuat.

7. **Ringan dan Efisien**  
   - Dibangun di atas Debian Linux, Proxmox relatif ringan dibandingkan dengan platform lain seperti VMware atau Hyper-V.

8. **Integrasi dengan Cloud**  
   - Mendukung solusi hybrid dengan integrasi penyedia cloud untuk pengujian atau pengembangan skala besar.

---

### **Praktik Terbaik dan Terjangkau untuk Implementasi Proxmox VE di Home Lab**

#### **1. Pilih Hardware yang Memadai**
   - **CPU**: Gunakan prosesor multi core/ thread dengan dukungan virtualisasi (Intel VT-x/AMD-V). Contoh: Intel Core i5/i7 atau AMD Ryzen 5/7.  
   - **RAM**: Minimal 16 GB, idealnya 32 GB keatas untuk menjalankan beberapa VM secara bersamaan.  
   - **Storage**:
     - **SSD (Solid State Drive)** untuk performa cepat dalam menjalankan VM.
     - **HDD tambahan** untuk penyimpanan data besar.  
   - **Motherboard**: Pastikan kompatibel dengan prosesor dan mendukung upgrade RAM atau storage.  
   - **NIC (Network Interface Card)**: Minimal satu port Gigabit Ethernet, lebih baik jika memiliki NIC tambahan untuk segmentasi jaringan.

#### **2. Instalasi Proxmox VE**
   - **Langkah Awal**:
     - Unduh ISO Proxmox VE dari situs resmi.
     - Instal pada hardware server atau PC yang sudah disiapkan.  
   - **Konfigurasi Dasar**:
     - Siapkan jaringan lokal untuk akses melalui antarmuka web Proxmox.  
     - Konfigurasi penyimpanan (ZFS untuk performa optimal).

#### **3. Virtualisasi Berbiaya Rendah**
   - **VM untuk Beragam Sistem Operasi**:
     - Gunakan Proxmox untuk menjalankan beberapa VM dengan OS seperti Windows, Linux, atau distribusi IoT (misalnya Ubuntu Server atau Debian).  
   - **LXC Containers untuk Aplikasi Ringan**:
     - Jalankan aplikasi ringan seperti server web atau database di dalam kontainer untuk efisiensi sumber daya.

#### **4. Manfaatkan Perangkat Bekas**
   - Gunakan PC atau server bekas (misalnya, Dell PowerEdge atau HP ProLiant) yang tersedia di pasar lokal dengan harga mulai dari Rp 3.000.000 – Rp 5.000.000.

#### **5. Integrasi Penyimpanan Jaringan**
   - Pasang NAS (Network-Attached Storage) untuk penyimpanan bersama atau gunakan server lama sebagai penyimpanan jaringan dengan FreeNAS atau OpenMediaVault.

#### **6. Backup dan Snapshot**
   - Gunakan fitur bawaan Proxmox untuk membuat snapshot sebelum eksperimen.  
   - Konfigurasikan backup otomatis untuk memastikan data aman.

#### **7. Optimalkan Energi**
   - Pilih hardware hemat daya (seperti Intel NUC atau Raspberry Pi untuk simulasi ringan).  
   - Gunakan fitur sleep/wake di BIOS untuk menghemat listrik saat tidak digunakan.

---

### **Contoh Setup Proxmox VE Home Lab Berbiaya Rendah**

- **Hardware**:
  - PC Bekas dengan Intel i5, 16 GB RAM, dan SSD 500 GB tanpa monitor: ~Rp 2.500.000.  
  - HDD tambahan 1 TB: ~Rp 800.000.  
  - Router sederhana untuk segmentasi jaringan: ~Rp 500.000.

- **Software**:
  - Proxmox VE (gratis).  
  - OS untuk VM (gunakan distribusi Linux gratis seperti Ubuntu atau Debian).

- **Tambahan**:
  - Raspberry Pi 4 (~Rp 800.000) untuk eksperimen IoT.  
  - Switch jaringan murah (~Rp 300.000) jika menggunakan lebih dari satu perangkat.

- **Estimasi Biaya Total**: ~Rp 3.500.000 – Rp 7.500.000.

---

Dengan Proxmox VE, Anda dapat mengelola lingkungan home lab secara efisien tanpa biaya tinggi. Ini adalah solusi yang fleksibel dan ideal untuk belajar, bereksperimen, dan mengembangkan keterampilan IT.
