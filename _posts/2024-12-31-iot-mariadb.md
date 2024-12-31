---
title: "IOT - MariaDB"
date: 2024-12-31 00:00:00 +0700
categories: [IOT, sql]
tags: [IOT, server, proxmox, mariadb, sql]
---
Halo..



### **MariaDB: Penjelasan, Keamanan, dan Praktik Terbaik di Proxmox VE**

#### **Apa itu MariaDB?**
MariaDB adalah sistem manajemen basis data relasional (RDBMS) yang berasal dari MySQL. MariaDB terkenal dengan performa tinggi, fitur keamanan yang diperluas, dan kompatibilitas penuh dengan MySQL, sehingga banyak digunakan untuk aplikasi web, IoT, dan analitik data.

#### **Keamanan dengan `mysql_secure_installation`**
Setelah MariaDB diinstal, skrip `mysql_secure_installation` digunakan untuk meningkatkan keamanan. Langkah-langkahnya:

1. **Jalankan Skrip Keamanan:**
   ```bash
   sudo mysql_secure_installation
   ```
2. **Konfigurasi Skrip:**
   - **Password Root:** Atur kata sandi root untuk MariaDB.
   - **Remove Anonymous Users:** Hapus pengguna anonim untuk mencegah akses tidak sah.
   - **Disable Remote Root Login:** Nonaktifkan login root dari jarak jauh untuk keamanan tambahan.
   - **Remove Test Database:** Hapus basis data uji yang tidak diperlukan.
   - **Reload Privilege Tables:** Muat ulang tabel hak akses untuk menerapkan perubahan.

#### **Praktik Terbaik untuk Fungsi dan Keamanan MariaDB**

1. **Gunakan Kontainer LXC dengan Isolasi yang Kuat:**
   - Alokasikan sumber daya yang memadai di Proxmox VE untuk kontainer MariaDB.
   - Gunakan sistem operasi yang ringan seperti Debian untuk kinerja optimal.

2. **Perbarui Secara Berkala:**
   - Pastikan MariaDB selalu diperbarui untuk mendapatkan patch keamanan terbaru.
     ```bash
     sudo apt update && sudo apt upgrade -y
     ```

3. **Gunakan Firewall:**
   - Batasi akses ke port MariaDB (default: 3306) hanya untuk server yang membutuhkan koneksi.
     ```bash
     sudo ufw allow from <IP_KLIEN> to any port 3306
     ```

4. **Enkripsi Koneksi:**
   - Gunakan SSL/TLS untuk mengenkripsi koneksi antara MariaDB dan klien.

5. **Buat Pengguna dengan Hak Akses Minimal:**
   - Hindari penggunaan root untuk operasi sehari-hari. Buat pengguna dengan hak akses spesifik:
     ```sql
     CREATE USER 'iot_user'@'%' IDENTIFIED BY 'secure_password';
     GRANT INSERT, SELECT ON mqtt_data.* TO 'iot_user'@'%';
     ```

6. **Monitor Aktivitas MariaDB:**
   - Gunakan log audit untuk memantau aktivitas database:
     ```bash
     sudo apt install mariadb-plugin-audit
     ```

7. **Backup Rutin:**
   - Lakukan pencadangan data secara rutin menggunakan alat seperti `mysqldump` atau `mariabackup`:
     ```bash
     mysqldump -u root -p mqtt_data > backup.sql
     ```

Dengan langkah-langkah ini, MariaDB di Proxmox VE dapat diimplementasikan dengan aman dan andal untuk mendukung kebutuhan IoT Anda.

