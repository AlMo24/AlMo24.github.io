---
title: "Welcome to my homelab! - Micro Datacenter 2"
date: 2024-12-29 00:00:00 +0700
categories: [homelab, virtualization]
tags: [homelab, virtualization, proxmox.lxc, vm]
---

Halo..

Akhirnya PC untuk belajar sudah terinstall dan siap dipakai. Saya menjalankan 2 buah PC untuk menguji clustering di Proxmox dengan config berikut:
1. PC CPU i7 4790 (4C/8T), RAM 32GB, SSD 512GB
2. PC CPU i5 4570 (4C/4T), RAM 16GB, SSD 120GB dan 4 HDD total 8TB config tanpa RAID dijalankan dalam NAS

Sebetulnya Proxmox menyarankan untuk menjalankan minimal 3 PC untuk clustering, tapi PC 1 lagi dengan CPU AMD FX8150 RAM 32GB membutuhkan terlalu banyak daya dan sepertinya tidak cocok buat run 24/7. Kenapa tidak pakai RAID? Soalnya selain merk dan speednya beda-beda, tidak ada data yang penting-penting banget di dalamnya. Sudah ada storage tersendiri buat penyimpanan data anggota keluarga di rumah. Tapi mungkin nanti akan dicoba.

Jadi apa yang dijalankan di dalamnya? 
  
### **Instance yang Dapat Dijalankan di Proxmox VE untuk Belajar dan Persiapan Sertifikasi Jaringan**

Proxmox VE memungkinkan Anda menjalankan berbagai jenis instance yang mendukung pembelajaran jaringan dan persiapan sertifikasi seperti **CCNA, CCNP, CompTIA Network+, atau JNCIA**. Berikut adalah jenis instance yang relevan dan penjelasannya:

---

### **1. Virtual Machine (VM)**
#### **Deskripsi:**
   - VM adalah instance yang memungkinkan Anda menjalankan sistem operasi lengkap seperti Windows atau Linux.
   - Digunakan untuk menguji software atau simulasi jaringan kompleks.

#### **Manfaat untuk Belajar Jaringan:**
   - Menyediakan lingkungan server dan client yang dapat digunakan untuk konfigurasi jaringan.
   - Simulasi jaringan berbasis OS seperti server DHCP, DNS, atau server web.
   - Menguji protokol seperti TCP/IP, FTP, atau SSH.

#### **Contoh Penggunaan:**
   - **Linux Server (Ubuntu/Debian)**: Untuk mengkonfigurasi layanan jaringan seperti Apache (web server) atau BIND (DNS server).
   - **Windows Server**: Untuk belajar Active Directory, DHCP, dan konfigurasi lainnya.

---

### **2. LXC Container**
#### **Deskripsi:**
   - LXC adalah container ringan yang memungkinkan Anda menjalankan aplikasi atau layanan tertentu tanpa memerlukan OS lengkap.
   - Lebih efisien dibandingkan VM dalam penggunaan sumber daya.

#### **Manfaat untuk Belajar Jaringan:**
   - Ideal untuk menjalankan aplikasi jaringan ringan seperti router berbasis software, server web, atau aplikasi monitoring jaringan.
   - Memungkinkan pengujian cepat tanpa overhead sumber daya yang besar.

#### **Contoh Penggunaan:**
   - **Nginx/Apache**: Untuk belajar tentang konfigurasi server HTTP.
   - **Monitoring Tools (Nagios/Zabbix)**: Untuk mengawasi performa jaringan.

---

### **3. Simulasi dan Emulasi Jaringan**
#### **Deskripsi:**
   - Anda dapat menginstal perangkat lunak simulasi jaringan di VM atau container untuk mengemulasi perangkat jaringan seperti router, switch, dan firewall.

#### **Manfaat untuk Belajar Jaringan:**
   - Memungkinkan Anda membuat topologi jaringan virtual tanpa membeli perangkat keras fisik.
   - Sangat relevan untuk belajar protokol routing (OSPF, BGP) atau segmentasi jaringan (VLAN).

#### **Contoh Perangkat Lunak:**
   - **GNS3**:
     - Perangkat lunak simulasi jaringan yang mendukung router dan switch virtual.
     - Memungkinkan integrasi dengan perangkat nyata.
   - **EVE-NG**:
     - Emulator jaringan yang sering digunakan untuk pelatihan CCNA atau CCNP.
   - **Cisco Packet Tracer**:
     - Simulator dari Cisco yang ringan dan ideal untuk belajar dasar-dasar jaringan.

---

### **4. Firewall dan IDS/IPS**
#### **Deskripsi:**
   - Anda dapat menginstal software firewall seperti **pfSense** atau **OPNsense** untuk belajar konfigurasi keamanan jaringan.
   - Software IDS/IPS (Intrusion Detection/Prevention Systems) seperti Snort juga dapat dijalankan.

#### **Manfaat untuk Belajar Jaringan:**
   - Belajar tentang keamanan jaringan, seperti konfigurasi firewall rules, NAT, VPN, atau VLAN.
   - Memahami cara melindungi jaringan dari ancaman dan serangan.

#### **Contoh Penggunaan:**
   - **pfSense**: Sebagai router/firewall untuk mengelola traffic jaringan.
   - **Snort**: Untuk memonitor dan menganalisis paket jaringan.

---

### **5. Server Virtual untuk Aplikasi Jaringan**
#### **Deskripsi:**
   - Menjalankan berbagai server yang dibutuhkan untuk simulasi jaringan lengkap.

#### **Manfaat untuk Belajar Jaringan:**
   - Meningkatkan pemahaman tentang bagaimana server bekerja dalam jaringan.
   - Menyiapkan lingkungan jaringan seperti di dunia nyata.

#### **Contoh Server yang Dijalankan:**
   - **DHCP Server**: Untuk belajar tentang pengaturan IP otomatis.
   - **DNS Server**: Untuk memahami resolusi nama domain.
   - **Web Server (Apache/Nginx)**: Untuk mempelajari pengaturan layanan HTTP/HTTPS.
   - **FTP Server**: Untuk belajar transfer file di jaringan.

---

### **6. Tools Monitoring dan Analisis Jaringan**
#### **Deskripsi:**
   - Instance yang menjalankan alat monitoring jaringan membantu dalam analisis dan troubleshooting.

#### **Manfaat untuk Belajar Jaringan:**
   - Memantau performa jaringan secara real-time.
   - Menganalisis paket jaringan untuk memahami protokol.

#### **Contoh Perangkat Lunak:**
   - **Wireshark**: Untuk menganalisis paket jaringan.
   - **Nagios/Zabbix**: Untuk monitoring performa jaringan.
   - **NetFlow Tools**: Untuk menganalisis lalu lintas jaringan.

---

### **7. Router dan Switch Virtual**
#### **Deskripsi:**
   - Menjalankan OS perangkat jaringan virtual seperti Cisco IOS, JunOS, atau MikroTik di VM.

#### **Manfaat untuk Belajar Jaringan:**
   - Memungkinkan simulasi perangkat jaringan untuk belajar routing, switching, atau konfigurasi jaringan kompleks.
   - Memberikan pengalaman serupa seperti bekerja dengan perangkat fisik.

#### **Contoh Penggunaan:**
   - **Cisco IOS** di GNS3 atau EVE-NG: Untuk belajar routing (OSPF, BGP, EIGRP) dan konfigurasi switch.
   - **MikroTik RouterOS**: Untuk belajar konfigurasi router.

---

### **Rekomendasi Setup Proxmox VE untuk Belajar Jaringan**

#### **Hardware Minimal**:
   - **Prosesor**: Intel i5 atau AMD Ryzen 5 (dengan dukungan virtualisasi).  
   - **RAM**: 16 GB (minimal), 32 GB lebih baik.  
   - **Storage**: SSD untuk kecepatan + HDD untuk penyimpanan data besar.  
   - **NIC**: Gigabit Ethernet (lebih baik jika memiliki 2 NIC untuk simulasi jaringan yang lebih realistis).

#### **Topologi Awal**:
   - 1 VM sebagai Router Virtual (pfSense atau Cisco IOS).  
   - 2-3 VM untuk Client dan Server (Windows/Linux).  
   - Integrasi dengan alat simulasi seperti GNS3 atau EVE-NG.



Dengan Proxmox VE, Anda dapat menciptakan lingkungan belajar yang kaya dan realistis untuk mendukung persiapan sertifikasi jaringan secara efektif.
  
  
  Salam..