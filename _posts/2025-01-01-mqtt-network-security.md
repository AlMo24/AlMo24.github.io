---
title: "IOT - Network Security"
date: 2025-01-01 00:00:00 +0700
categories: [IOT, security]
tags: [IOT, server, proxmox, security, network]
---

This setup provides an isolated environment where ESP32 devices can securely communicate with the MQTT broker without any internet access. Bear in mind for best practices that every node can, and will be used by another entitiy for their own purposes and reasons. Remember the botnets that using under-secured network cameras.  

Creating an isolated network for ESP32 devices to communicate with the MQTT broker in Proxmox VE ensures secure and local-only communication. Below is a step-by-step guide:

---

### **Step-by-Step Guide to Create an Isolated Network in Proxmox VE**

---

#### **1. Create a Virtual Network in Proxmox VE**

1. **Access Proxmox Web Interface**:
   - Open the Proxmox VE web interface in your browser.

2. **Navigate to the Network Settings**:
   - Select the Proxmox node from the left panel.
   - Go to **System > Network**.

3. **Add a Linux Bridge**:
   - Click on **Create > Linux Bridge**.
   - Configure the bridge:
     - **Name**: For example, `vmbr1`.
     - **Autostart**: Check this option to enable the bridge on system startup.
     - Leave the IP and subnet fields empty to prevent internet access.
   - Click **Create**.

4. **Apply the Network Configuration**:
   - Click **Apply Configuration** to activate the new bridge.

---

#### **2. Configure the MQTT Broker Container or VM**

1. **Attach the MQTT Broker to the Isolated Bridge**:
   - Select the MQTT broker VM or LXC container from the Proxmox web interface.
   - Go to **Hardware > Network Device**.
   - Edit the existing network interface or add a new one:
     - **Bridge**: Select `vmbr1` (the isolated bridge created earlier).

2. **Assign a Static IP to the MQTT Broker**:
   - Access the MQTT broker's console via Proxmox or SSH.
   - Edit the network configuration file:
     - For Debian/Ubuntu:
       ```bash
       sudo nano /etc/network/interfaces
       ```
       Add the following lines for `vmbr1`:
       ```plaintext
       auto eth0
       iface eth0 inet static
           address 192.168.50.1
           netmask 255.255.255.0
       ```
   - Restart networking services:
     ```bash
     sudo systemctl restart networking
     ```

---

#### **3. Connect ESP32 Devices to the Isolated Network**

1. **Set Up a Wi-Fi Access Point**:
   - Use a Wi-Fi router or an ESP32 device as a soft AP to create the isolated Wi-Fi network.
   - Configure the access point:
     - **SSID**: For example, `ESP32_MQTT_Network`.
     - **DHCP Range**: 192.168.50.2 to 192.168.50.254.
     - Ensure the AP does not provide internet access.

2. **Connect ESP32 Devices to the Wi-Fi Network**:
   - In the ESP32 code (Arduino IDE), configure the Wi-Fi credentials:
     ```cpp
     const char* ssid = "ESP32_MQTT_Network";
     const char* password = "YourPassword";
     ```

3. **Update MQTT Broker IP in ESP32 Code**:
   - Use the static IP of the MQTT broker (e.g., `192.168.50.1`) as the server address in your ESP32 scripts:
     ```cpp
     const char* mqtt_server = "192.168.50.1";
     ```

---

#### **4. Test Communication in the Isolated Network**

1. **Ping the MQTT Broker from an ESP32 Device**:
   - Run a basic sketch on the ESP32 to ensure it can connect to `192.168.50.1`.

2. **Subscribe to MQTT Topics**:
   - Use a test topic (e.g., `test/topic`) to confirm successful message delivery:
     - Publish from the ESP32.
     - Subscribe using tools like `mosquitto_sub` or MQTT Explorer from within the same network.

---

#### **5. Ensure Isolation**

1. **Disable NAT (Internet Access)**:
   - Ensure the bridge (`vmbr1`) is not configured with a gateway or NAT.

2. **Restrict Traffic to the Broker Only**:
   - If needed, set up firewall rules to block all external access to `vmbr1`:
     - Navigate to **Datacenter > Firewall** in Proxmox.
     - Create rules for `vmbr1` allowing only local traffic (e.g., within `192.168.50.0/24`).

---

#### **Optional: Monitoring and Logging**
- Install monitoring tools like **Node-RED** or **Telegraf** in the MQTT broker container to visualize traffic or logs for troubleshooting.

