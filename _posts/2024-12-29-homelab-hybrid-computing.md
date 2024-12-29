---
title: "Hybrid Computing"
date: 2024-12-29 00:00:00 +0700
categories: [homelab, virtualization]
tags: [homelab, virtualization, proxmox.lxc, vm]
---
  
  Halo..

Setelah install, setup, dan config semua instances di mikro datacenter (ciye) on premise kita selesai, rasanya ada timbul penasaran bagaimana kalau resource yang ada ini dipadu dengan resource dari layanan cloud seperti AWS. Apakah onggokan dua PC tua ini bisa bekerja baik? Seperti apa sih hybrid computing itu?  

Not: Untuk ini baru saya start coba lakukan. Page ini adalah sementara hal yang bisa saya konpilasi untuk memulai.

### **Membangun Hybrid Computing antara Proxmox VE Cluster dan AWS**

Hybrid computing menggabungkan sumber daya lokal (on-premises) seperti cluster Proxmox VE dengan layanan cloud publik seperti AWS. Dengan pendekatan ini, Anda dapat memanfaatkan keunggulan kedua infrastruktur: kontrol penuh dari Proxmox VE di lokal dan skalabilitas serta fleksibilitas AWS. Berikut adalah panduan langkah demi langkah untuk memulai hybrid computing antara Proxmox VE dan AWS.

---

### **1. Memahami Konsep Hybrid Computing**
Hybrid computing memungkinkan Anda menjalankan workload secara bersamaan di cluster lokal (Proxmox VE) dan cloud (AWS). Beberapa skenario umum penggunaan hybrid computing adalah:

- **Disaster Recovery (DR):** Backup atau replikasi data dari cluster lokal ke AWS.  
- **Load Balancing:** Mengalihkan beban kerja yang tinggi dari Proxmox VE ke AWS untuk menghindari overload.  
- **Hybrid Applications:** Menjalankan sebagian aplikasi di lokal dan sebagian di cloud, misalnya database lokal di Proxmox dan frontend di AWS.  
- **Testing dan Development:** Menyediakan lingkungan pengujian cloud sementara yang terintegrasi dengan lokal.  

---

### **2. Persiapan Infrastruktur Proxmox VE**
Untuk memulai, Anda perlu memastikan bahwa cluster Proxmox VE Anda siap untuk diintegrasikan dengan AWS. Berikut adalah langkah-langkah persiapannya:

#### **a. Konfigurasi Jaringan**
- Pastikan Proxmox VE terhubung ke internet melalui jaringan yang stabil.
- Konfigurasi alamat IP statis untuk node Proxmox VE agar mudah diakses dari luar.
- Jika perlu, gunakan firewall untuk melindungi node dari akses tidak sah.

#### **b. Instalasi Alat Pendukung**
- Gunakan Proxmox VE versi terbaru untuk memastikan kompatibilitas fitur (Versi terkini di v8.3.2).  
- Instal modul atau tool tambahan seperti `rclone` untuk integrasi penyimpanan atau `terraform` untuk orkestrasi hybrid.

#### **c. Virtual Machines atau LXC Containers**
- Siapkan VM atau container yang akan digunakan untuk hybrid workload, misalnya server aplikasi atau database.

---

### **3. Persiapan Akun AWS**
AWS menyediakan berbagai layanan yang mendukung hybrid computing. Langkah berikut memastikan akun AWS Anda siap:

#### **a. Buat Akun dan Konfigurasi IAM**
- Buat akun AWS dan aktifkan layanan seperti **EC2**, **S3**, dan **VPC**.  
- Gunakan IAM (Identity and Access Management) untuk membuat user dengan akses terbatas hanya untuk integrasi hybrid.  
- Generate key pair IAM untuk digunakan pada Proxmox VE.

#### **b. Buat Virtual Private Cloud (VPC)**
- Konfigurasi VPC untuk memastikan bahwa workload di AWS dapat berkomunikasi dengan infrastruktur lokal.  
- Buat subnet, route table, dan security group yang memungkinkan koneksi aman antara Proxmox VE dan AWS.

#### **c. Siapkan EC2 Instances**
- Deploy instance EC2 yang akan digunakan untuk menjalankan bagian dari hybrid workload, misalnya web server atau application server.  
- Pastikan Anda memilih instance dengan spesifikasi yang sesuai dan aktifkan Elastic IP jika memerlukan IP tetap.

---

### **4. Integrasi Proxmox VE dengan AWS**
Setelah infrastruktur dasar di kedua sisi siap, langkah berikutnya adalah menghubungkan Proxmox VE dengan AWS.

#### **a. Gunakan Storage Hybrid dengan S3**
- Instal `rclone` di node Proxmox VE untuk mengakses penyimpanan S3.  
- Konfigurasi bucket S3 sebagai storage backend untuk backup atau penyimpanan data dari VM di Proxmox VE.  
- Tambahkan bucket S3 ke cluster Proxmox sebagai penyimpanan eksternal.

#### **b. Konfigurasi VPN atau Direct Connect**
- **VPN (Virtual Private Network):** Hubungkan cluster Proxmox VE dengan AWS VPC menggunakan OpenVPN atau IPSec VPN.  
  - Setup VPN di Proxmox VE menggunakan LXC container atau VM.  
  - Konfigurasikan endpoint VPN di AWS untuk menghubungkan kedua infrastruktur.  
- **AWS Direct Connect:** Untuk koneksi dengan latensi rendah dan bandwidth tinggi, gunakan AWS Direct Connect (layanan berbayar).

#### **c. Gunakan Terraform untuk Orkestrasi**
- **Terraform** adalah alat yang berguna untuk mengelola infrastruktur hybrid.  
- Buat skrip Terraform untuk mendefinisikan instance AWS dan konfigurasi jaringan yang diperlukan untuk hybrid workload.  
- Jalankan Terraform dari node Proxmox untuk mengotomatisasi deployment AWS.

---

### **5. Implementasi Hybrid Workload**
Setelah koneksi terintegrasi, Anda dapat mulai menjalankan workload hybrid. Berikut adalah contoh implementasi:

#### **a. Backup dan Disaster Recovery**
- Gunakan Proxmox Backup Server untuk menyimpan salinan VM di AWS S3 sebagai cadangan.  
- Konfigurasikan replikasi otomatis sehingga jika cluster lokal gagal, VM dapat dipulihkan di AWS.

#### **b. Load Balancing**
- Deploy server load balancer di AWS (misalnya ELB) yang mendistribusikan lalu lintas antara Proxmox VE dan AWS.  
- Gunakan Proxmox VE untuk bagian backend (database) dan AWS untuk bagian frontend (web server).

#### **c. Aplikasi Terdistribusi**
- Jalankan database lokal di Proxmox VE untuk efisiensi dan kecepatan.  
- Hosting frontend atau microservices di AWS untuk skala global.

---

### **6. Monitoring dan Keamanan**
#### **Monitoring**
- Gunakan alat seperti Zabbix atau Prometheus untuk memonitor performa dan konektivitas antara Proxmox VE dan AWS.  
- Pastikan metrik seperti latency, penggunaan resource, dan uptime terus dipantau.

#### **Keamanan**
- Gunakan enkripsi untuk semua koneksi antar Proxmox VE dan AWS.  
- Konfigurasi firewall untuk membatasi akses hanya pada alamat IP yang dikenal.  
- Aktifkan MFA (Multi-Factor Authentication) untuk akses AWS.

---

Memulai komputasi hybrid antara cluster Proxmox VE dan layanan AWS memerlukan pemahaman tentang biaya yang terkait dengan layanan AWS yang akan digunakan. Berikut adalah estimasi biaya untuk layanan AWS yang umum digunakan dalam skenario ini:

### 1. **Amazon EC2 (Elastic Compute Cloud)**
Amazon EC2 menyediakan instance virtual untuk menjalankan aplikasi Anda. Biaya bervariasi berdasarkan jenis instance, wilayah, dan waktu penggunaan.

- **Biaya On-Demand Instance:**
  - Misalnya, untuk instance tipe t4g.small di wilayah Asia Pasifik (Singapura), biaya sekitar $0,0336 per jam. 
  - Jika digunakan selama 24 jam penuh dalam sebulan:
    - $0,0336/jam × 24 jam/hari × 30 hari = $24,19 per bulan.

### 2. **Amazon S3 (Simple Storage Service)**
Amazon S3 digunakan untuk penyimpanan objek. Biaya dihitung berdasarkan volume data yang disimpan dan permintaan yang dilakukan.

- **Biaya Penyimpanan Standar:**
  - $0,023 per GB untuk 50 TB pertama per bulan. 
  - Misalnya, untuk 100 GB data:
    - 100 GB × $0,023/GB = $2,30 per bulan.

### 3. **Amazon VPC (Virtual Private Cloud)**
Membuat VPC tidak dikenakan biaya tambahan. Namun, layanan terkait seperti NAT Gateway memiliki biaya tersendiri.

- **Biaya NAT Gateway:**
  - $0,045 per jam. 
  - $0,045 per GB data yang diproses.
  - Jika NAT Gateway aktif selama 24 jam dalam sehari:
    - $0,045/jam × 24 jam = $1,08 per hari.
  - Untuk 10 GB data yang diproses:
    - 10 GB × $0,045/GB = $0,45.

### 4. **Biaya Transfer Data**
Transfer data masuk (inbound) ke AWS umumnya gratis. 
Namun, transfer data keluar (outbound) dari AWS dikenakan biaya.

- **Biaya Transfer Data Keluar:**
  - 100 GB pertama per bulan gratis. 
  - Setelah itu, biaya sekitar $0,09 per GB untuk wilayah AS, dan $0,12 per GB untuk wilayah Asia Pasifik. 
  - Misalnya, untuk 50 GB data keluar setelah kuota gratis:
    - 50 GB × $0,12/GB = $6,00.

### 5. **Estimasi Total Biaya Bulanan**
Berikut adalah estimasi total biaya bulanan berdasarkan contoh di atas:

- **Amazon EC2:**
  - $24,19 per bulan.
- **Amazon S3:**
  - $2,30 per bulan.
- **NAT Gateway:**
  - $1,08 per hari × 30 hari = $32,40 per bulan.
  - $0,45 untuk 10 GB data yang diproses.
- **Transfer Data Keluar:**
  - $6,00 untuk 50 GB.

**Total Estimasi Biaya Bulanan:**
- $24,19 + $2,30 + $32,40 + $0,45 + $6,00 = $65,34.

### 6. **Konversi ke Rupiah (IDR)**
Dengan asumsi kurs $1 = Rp15.000:

- $65,34 × Rp15.000 = Rp980.100 per bulan (iya oke tau ini gak murah sih T_T)  


Buat belajar kita mulai dengan stay di free tier dulu saja.

**Catatan Penting:**

- Biaya di atas adalah estimasi dan dapat berbeda berdasarkan penggunaan aktual, jenis instance, wilayah, dan layanan tambahan yang digunakan.
- Disarankan untuk menggunakan [AWS Pricing Calculator](https://calculator.aws/) untuk mendapatkan estimasi biaya yang lebih akurat sesuai dengan kebutuhan spesifik Anda. 
- Selalu periksa halaman harga resmi AWS untuk informasi terbaru dan detail lebih lanjut.

Dengan memahami struktur biaya ini, Anda dapat merencanakan dan mengelola anggaran untuk memulai komputasi hybrid antara Proxmox VE dan AWS secara lebih efektif. 


### **Kesimpulan**
Membangun hybrid computing antara Proxmox VE dan AWS memerlukan perencanaan matang, namun memberikan fleksibilitas dan efisiensi tinggi dalam pengelolaan workload. Dengan memanfaatkan fitur Proxmox VE dan layanan AWS seperti S3, EC2, dan VPC, Anda dapat menciptakan infrastruktur hybrid yang scalable, aman, dan andal. Integrasi ini sangat bermanfaat untuk berbagai kebutuhan, termasuk backup, disaster recovery, pengembangan aplikasi, hingga hosting hybrid.

Salam..