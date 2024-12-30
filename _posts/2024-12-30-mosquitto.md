---
title: "Welcome to my homelab! - Micro Datacenter 3"
date: 2024-12-30 00:00:00 +0700
categories: [homelab, server]
tags: [homelab, server, proxmox.lxc, vm, mosquitto]
---

**Step-by-Step Guide: Setting Up MQTT Broker on Proxmox VE**

This guide provides a detailed walkthrough to set up an MQTT broker (Mosquitto) on Proxmox Virtual Environment (Proxmox VE).

---

### **Step 1: Prepare Proxmox VE**

1. **Create a Virtual Machine (VM) or LXC Container**:
   - Open the Proxmox web interface.
   - Navigate to **Datacenter > Node > Create VM** or **Create CT**.
   - Allocate resources such as CPU, RAM, and storage based on your requirements:
     - **Recommended specs for MQTT broker**: 1 vCPU, 512MB RAM, and 5GB storage.

2. **Select an Operating System**:
   - Use Ubuntu Server or Debian (preferred for lightweight installations).
   - Download the ISO and upload it to Proxmox via **Storage > ISO Images > Upload**.
   - Install the OS on the VM.

---

### **Step 2: Install Mosquitto MQTT Broker**

1. **Update System Packages**:
   After logging into your VM or container via SSH, run:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install Mosquitto**:
   Install the Mosquitto broker and client tools:
   ```bash
   sudo apt install mosquitto mosquitto-clients -y
   ```

3. **Enable and Start Mosquitto**:
   Ensure Mosquitto starts automatically:
   ```bash
   sudo systemctl enable mosquitto
   sudo systemctl start mosquitto
   ```

4. **Verify Installation**:
   Check the Mosquitto status:
   ```bash
   sudo systemctl status mosquitto
   ```
   If it shows **active (running)**, the broker is installed and running.

---

### **Step 3: Configure Mosquitto**

1. **Modify Configuration File**:
   Edit the Mosquitto configuration file:
   ```bash
   sudo nano /etc/mosquitto/mosquitto.conf
   ```
   Add the following lines:
   ```conf
   listener 1883
   allow_anonymous false
   password_file /etc/mosquitto/passwd
   ```
   - **listener 1883**: Defines the port for MQTT connections.
   - **allow_anonymous false**: Disables anonymous access.
   - **password_file**: Points to the file storing user credentials.

2. **Create a Username and Password**:
   Generate credentials for your MQTT broker:
   ```bash
   sudo mosquitto_passwd -c /etc/mosquitto/passwd your_username
   ```
   Enter a secure password when prompted.

3. **Restart Mosquitto**:
   Apply the configuration changes:
   ```bash
   sudo systemctl restart mosquitto
   ```

---

### **Step 4: Test the MQTT Broker**

1. **Test Locally Using Mosquitto Clients**:
   - **Subscribe to a topic**:
     ```bash
     mosquitto_sub -h localhost -t "test" -u your_username -P your_password
     ```
   - **Publish a message**:
     Open another terminal and run:
     ```bash
     mosquitto_pub -h localhost -t "test" -m "Hello, MQTT!" -u your_username -P your_password
     ```
   - You should see the message "Hello, MQTT!" in the subscriber terminal.

2. **Allow Remote Access (Optional)**:
   - Open port 1883 in your Proxmox or VM/container firewall:
     ```bash
     sudo ufw allow 1883
     ```
   - Test connectivity from a remote client by replacing `localhost` with your server’s IP.

---

### **Step 5: Secure the Broker with SSL/TLS (Optional)**

1. **Install OpenSSL**:
   ```bash
   sudo apt install openssl -y
   ```

2. **Generate Certificates**:
   ```bash
   openssl genrsa -out mosquitto.key 2048
   openssl req -new -key mosquitto.key -out mosquitto.csr
   openssl x509 -req -in mosquitto.csr -signkey mosquitto.key -out mosquitto.crt -days 365
   ```

3. **Configure Mosquitto for SSL**:
   Edit `/etc/mosquitto/mosquitto.conf` and add:
   ```conf
   listener 8883
   cafile /path/to/ca.crt
   certfile /path/to/mosquitto.crt
   keyfile /path/to/mosquitto.key
   ```
   Restart Mosquitto:
   ```bash
   sudo systemctl restart mosquitto
   ```

4. **Test SSL Connection**:
   Use a client that supports TLS, such as MQTT Explorer or a custom script.

---

### **Step 6: Integrate with IoT Platforms (Optional)**

1. **Node-RED Integration**:
   Install Node-RED on the same or another VM/container, and configure an MQTT node to connect to your broker.

2. **Home Assistant**:
   Use MQTT to connect smart home devices to Home Assistant by providing broker details in its configuration.

3. **Cloud Integration**:
   Forward MQTT messages to cloud services like AWS IoT or Google Cloud IoT for advanced analytics.

---

### **Step 7: Monitor and Maintain the Broker**

1. **Logs**:
   Check broker logs for troubleshooting:
   ```bash
   sudo journalctl -u mosquitto
   ```

2. **Update Regularly**:
   Ensure the broker and system packages are kept up-to-date:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

3. **Backup Configuration**:
   Periodically back up the `/etc/mosquitto` directory and any SSL certificates.

---

### **Conclusion**
By following these steps, you’ll have a fully functional MQTT broker running on Proxmox VE, ready for IoT applications. Adjust the configuration as needed for your specific use case, and consider implementing additional security measures for production environments.

