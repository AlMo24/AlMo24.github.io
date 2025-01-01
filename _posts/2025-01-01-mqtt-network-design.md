---
title: "IOT - Network Design Planning"
date: 2025-01-01 01:00:00 +0700
categories: [IOT, network-planning]
tags: [IOT, server, proxmox, security, network]
---

For a broader area network with multiple ESP32 devices and multiple Wi-Fi access points, the best practice is to implement a **seamless roaming Wi-Fi network**. This ensures that ESP32 devices can connect to the closest access point without hardcoding multiple SSIDs in the code.

Here’s how you can achieve this:

---

### **1. Use a Unified Wi-Fi Network with a Single SSID**

#### **Set Up Multiple Access Points (APs):**
1. **Deploy Access Points Strategically**:
   - Place multiple Wi-Fi access points across the coverage area.
   - Ensure they have overlapping coverage to allow smooth transitions (roaming) between APs.

2. **Configure a Single SSID and Password**:
   - Use the same SSID, password, and security type (e.g., WPA2) on all access points.
   - This allows ESP32 devices to connect automatically to the strongest signal without needing to switch manually.

3. **Ensure Unique Channels**:
   - Assign non-overlapping Wi-Fi channels to each access point to minimize interference (e.g., channels 1, 6, and 11 for 2.4 GHz Wi-Fi).

4. **Enable Fast Roaming (802.11r)**:
   - If your access points support 802.11r (fast roaming), enable it to reduce latency during handovers between APs.
   - Fast roaming is critical for applications where real-time connectivity is essential.

#### **Use a Managed Mesh Wi-Fi System**:
- Consider using a mesh Wi-Fi system like **Ubiquiti UniFi**, **TP-Link Omada**, or **Google Nest Wi-Fi**. These systems simplify management and ensure seamless roaming for connected devices.

---

### **2. Implement Wi-Fi Auto-Reconnect in ESP32**

Even with a single SSID network, ESP32 devices may occasionally disconnect or need to switch access points. Implement auto-reconnect logic to handle such cases dynamically:

#### **Dynamic Wi-Fi Connection Logic in ESP32 Code**:
```cpp
#include <WiFi.h>

// Wi-Fi credentials
const char* ssid = "Your_Single_SSID";
const char* password = "Your_Password";

void connectToWiFi() {
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to Wi-Fi...");
  }
  Serial.println("Connected to Wi-Fi!");
  Serial.print("IP Address: ");
  Serial.println(WiFi.localIP());
}

void setup() {
  Serial.begin(115200);
  connectToWiFi();
}

void loop() {
  if (WiFi.status() != WL_CONNECTED) {
    Serial.println("Disconnected! Reconnecting...");
    connectToWiFi();
  }
  delay(10000);
}
```
- The ESP32 will attempt to reconnect automatically if it loses connection to the Wi-Fi network.

---

### **3. Optional: Use mDNS for Broker Discovery**

To avoid hardcoding the MQTT broker’s IP address, you can use **mDNS (Multicast DNS)** for dynamic discovery. This allows ESP32 devices to resolve the broker’s hostname instead of relying on a static IP.

#### **Setup mDNS on MQTT Broker**:
1. Install `avahi-daemon` on the MQTT broker (if using Linux):
   ```bash
   sudo apt install avahi-daemon
   ```
2. Add an mDNS entry for the broker:
   - For example, set the hostname as `mqtt-broker.local`.

#### **Update ESP32 Code for mDNS**:
- Use the mDNS hostname instead of the broker’s IP:
  ```cpp
  const char* mqtt_server = "mqtt-broker.local";
  ```

---

### **4. Optimize Network for IoT**

#### **Set Static IPs for ESP32 Devices**:
- Use DHCP reservations in your router or AP to assign fixed IPs to ESP32 devices. This avoids IP conflicts and simplifies device management.

#### **Segment IoT Traffic Using VLANs**:
- Create a separate VLAN for IoT devices to isolate their traffic from other network traffic.
- Configure the VLAN on your managed switches and access points.

#### **Enable QoS (Quality of Service)**:
- Prioritize MQTT traffic on your network to ensure timely delivery of messages, especially if your network is shared with other devices.

---

### **5. Use an Enterprise-Grade Controller**

For large-scale deployments, consider using a centralized network controller:
- Examples: **Ubiquiti UniFi Controller**, **TP-Link Omada Controller**, or **Cisco Meraki Dashboard**.
- These controllers allow you to:
  - Manage all APs from a single interface.
  - Monitor network performance.
  - Optimize roaming and coverage.

---

### **6. Test and Monitor the Network**

1. **Conduct Site Surveys**:
   - Use tools like **NetSpot** or **Ekahau** to analyze coverage and identify weak spots or interference.

2. **Monitor Device Connectivity**:
   - Use network monitoring tools (e.g., **Zabbix**, **Nagios**) to track ESP32 connections and troubleshoot issues.

3. **Test Roaming**:
   - Move ESP32 devices across the coverage area to ensure seamless transitions between APs.

---

By using a single SSID with seamless roaming, dynamic Wi-Fi handling in ESP32 code, and advanced network configurations like VLANs and QoS, you can create a robust and scalable IoT network for a broader area.  
  
### **Description of Advanced Network Configuration for IoT Networks**

To ensure a robust, scalable, and secure IoT network that can handle multiple ESP32 devices across a broader area, advanced network configurations such as VLANs, QoS, and centralized management are essential. Below is an in-depth explanation of these configurations:

---

### **1. VLAN (Virtual Local Area Network)**

#### **What is VLAN?**
A VLAN is a logical subdivision of a network that segments devices into separate groups, even if they share the same physical network infrastructure. This improves security, traffic management, and network efficiency.

#### **Why Use VLANs for IoT?**
- **Traffic Segregation**: Keep IoT devices on a separate VLAN to prevent them from interacting with sensitive parts of the network.
- **Security**: Restrict IoT devices from accessing the internet or other VLANs unless explicitly allowed.
- **Scalability**: Simplify management by grouping IoT devices logically, regardless of their physical location.

#### **Steps to Configure VLANs**:
1. **Enable VLAN Support on Network Devices**:
   - Use a managed switch, router, and access points that support VLANs.

2. **Plan VLAN IDs**:
   - Assign a unique VLAN ID for IoT devices (e.g., VLAN 20).

3. **Configure VLAN on the Router/Firewall**:
   - Set up VLAN interfaces on your router.
   - Example (using OpenWRT):
     ```bash
     config interface 'IoT'
         option ifname 'eth0.20'
         option proto 'static'
         option ipaddr '192.168.20.1'
         option netmask '255.255.255.0'
     ```
   - Assign a unique IP subnet for the VLAN (e.g., `192.168.20.0/24`).

4. **Configure VLAN on Switches**:
   - Define the VLAN ID on the managed switch.
   - Assign ports as **tagged** (for trunk connections) or **untagged** (for end devices like APs).

5. **Set Up VLANs on Access Points**:
   - Map the SSID for IoT devices to the IoT VLAN.
   - Example:
     - SSID: `IoT_WiFi`
     - VLAN ID: `20`

6. **Restrict Traffic with Firewall Rules**:
   - Block IoT VLAN from accessing the main network or other VLANs:
     ```bash
     iptables -A FORWARD -i IoT -d 192.168.0.0/24 -j DROP
     iptables -A FORWARD -i IoT -o eth0 -j ACCEPT
     ```

---

### **2. QoS (Quality of Service)**

#### **What is QoS?**
QoS prioritizes network traffic to ensure critical applications receive the required bandwidth and low latency.

#### **Why Use QoS for IoT?**
- **Prioritize MQTT Traffic**: Ensure MQTT messages are delivered reliably and in real-time, even during network congestion.
- **Prevent Bandwidth Hogging**: Limit the bandwidth of non-critical traffic to avoid disruption.

#### **Steps to Configure QoS**:
1. **Identify Critical Traffic**:
   - Classify MQTT traffic by port (e.g., port `1883` for MQTT and `8883` for secure MQTT).

2. **Enable QoS on the Router**:
   - Use router settings to create traffic rules.
   - Example (OpenWRT):
     ```bash
     config class 'MQTT'
         option target 'priority'
         option ports '1883,8883'
     ```

3. **Set Bandwidth Limits**:
   - Reserve bandwidth for IoT traffic:
     - Example: Allocate 10 Mbps for MQTT traffic.

4. **Apply Traffic Shaping**:
   - Ensure low latency for MQTT packets:
     ```bash
     tc qdisc add dev eth0 root handle 1: htb default 12
     tc class add dev eth0 parent 1: classid 1:1 htb rate 10mbit
     tc filter add dev eth0 protocol ip parent 1:0 prio 1 u32 match ip dport 1883 0xffff flowid 1:1
     ```

5. **Enable QoS on Switches**:
   - Prioritize traffic using IEEE 802.1p tags (part of VLAN configuration).

---

### **3. Centralized Management**

#### **Why Centralized Management?**
- Simplifies configuration and monitoring of multiple access points, VLANs, and devices.
- Ensures consistent policies across the network.

#### **Options for Centralized Management**:
1. **Ubiquiti UniFi Controller**:
   - Manage UniFi access points, switches, and routers.
   - Features:
     - Centralized dashboard.
     - Seamless roaming.
     - VLAN and QoS management.
   - Example Setup:
     - Install UniFi Controller on a server or cloud VM.
     - Configure all UniFi devices to communicate with the controller.

2. **TP-Link Omada Controller**:
   - Manage TP-Link Omada devices.
   - Features:
     - User-friendly interface.
     - Bandwidth monitoring.
     - VLAN and traffic shaping support.

3. **Cisco Meraki Dashboard**:
   - Cloud-based management for Cisco Meraki devices.
   - Best for enterprise-grade deployments.
   - Features:
     - Real-time analytics.
     - Automated optimizations.
     - Scalable architecture.

---

### **4. Redundant Network Design**

#### **Why Redundancy?**
- Ensures high availability and reliability.
- Prevents single points of failure in the network.

#### **Best Practices for Redundant IoT Networks**:
1. **Deploy Backup MQTT Brokers**:
   - Use clustering or secondary brokers to ensure availability.

2. **Implement Failover Access Points**:
   - Configure multiple APs with overlapping coverage.

3. **Dual-WAN Setup**:
   - Use dual-WAN routers to switch between ISPs in case of failure.

---

### **5. Monitoring and Troubleshooting**

#### **Why Monitoring is Critical?**
- Detects connectivity issues and optimizes network performance.
- Ensures the reliability of IoT systems.

#### **Monitoring Tools**:
1. **Network Monitoring**:
   - Use tools like **Zabbix**, **Nagios**, or **PRTG Network Monitor** to track device uptime, signal strength, and traffic.

2. **Wi-Fi Analysis**:
   - Use **NetSpot** or **Ekahau** to analyze signal strength and interference.

3. **Log Analysis**:
   - Monitor logs from access points, switches, and MQTT brokers for errors.

---

By implementing these advanced configurations, your IoT network will be more scalable, secure, and reliable, capable of supporting a wide area and/ or large number of ESP32 devices in a distributed environment.  



Each Access Point (AP) in your IoT network can connect to the main network using different methods, depending on your infrastructure, network scale, and desired reliability. Here are the options, along with their advantages and challenges:

---

### **1. Ethernet Backhaul**
**Description:** The APs are connected to the main network via wired Ethernet connections.

#### **Advantages:**
- **Reliable Connection:** Wired connections offer high-speed and stable communication, ideal for high-density networks.
- **Low Latency:** Eliminates the latency issues commonly found in wireless connections.
- **Full Bandwidth:** Dedicated bandwidth per AP without interference from other wireless devices.
- **Supports Power over Ethernet (PoE):** Simplifies deployment by powering APs via Ethernet cables.

#### **Challenges:**
- **Cabling Requirements:** Requires laying Ethernet cables to all AP locations, which can be labor-intensive and costly in large or distributed areas.
- **Infrastructure Dependency:** Needs switches that support VLANs and sufficient ports.

#### **Best Practices:**
- Use **Cat5e or Cat6 cables** for gigabit connections.
- Deploy **managed switches** to support VLAN and QoS configurations.
- Use **PoE injectors or switches** to avoid separate power lines for APs.

---

### **2. Wireless Mesh Backhaul**
**Description:** The APs communicate wirelessly with each other, forming a mesh network. One or more APs (called root nodes) are wired to the main network, while others relay data through the mesh.

#### **Advantages:**
- **Flexibility:** No need for extensive wiring; APs can be deployed in hard-to-reach areas.
- **Scalability:** Additional APs can be added easily to expand the network.
- **Redundancy:** Self-healing in case of AP failure; traffic automatically reroutes.

#### **Challenges:**
- **Reduced Bandwidth:** Wireless backhaul uses part of the available bandwidth, reducing throughput for client devices.
- **Higher Latency:** Each hop in the mesh adds latency.
- **Interference Risks:** Performance can degrade in environments with high RF interference.

#### **Best Practices:**
- Use **dual-band or tri-band APs** where one band is dedicated to backhaul.
- Limit the number of hops (preferably 2-3) to minimize latency.
- Place APs with clear line-of-sight for better performance.

---

### **3. Powerline Networking**
**Description:** APs connect to the main network using the building's electrical wiring via Powerline adapters.

#### **Advantages:**
- **No Additional Cabling:** Uses existing electrical wiring, reducing deployment costs.
- **Convenient for Existing Buildings:** Ideal for retrofitting networks in older buildings.

#### **Challenges:**
- **Variable Performance:** Signal quality depends on the building's electrical wiring condition.
- **Limited Bandwidth:** Speeds are generally lower than Ethernet.
- **Susceptible to Interference:** Devices like microwaves or power tools can cause disruptions.

#### **Best Practices:**
- Use **AV2-compliant Powerline adapters** for better speed and reliability.
- Avoid connecting through surge protectors or power strips.
- Test for electrical noise before deployment.

---

### **4. Fiber Backhaul**
**Description:** APs are connected via fiber-optic cables to the main network, providing high-speed connections over long distances.

#### **Advantages:**
- **High Performance:** Supports extremely high speeds and low latency.
- **Long Distance:** Maintains performance over kilometers without significant loss.
- **Future-Proof:** Ready for future high-bandwidth demands.

#### **Challenges:**
- **High Cost:** Expensive to deploy and maintain compared to Ethernet.
- **Complex Installation:** Requires specialized knowledge for termination and splicing.

#### **Best Practices:**
- Use fiber where long-distance or high-speed links are required (e.g., between buildings or campuses).
- Deploy **media converters** to connect fiber to Ethernet at endpoints.
- Use **single-mode fiber** for long-distance and **multi-mode fiber** for short-distance connections.

---

### **5. Wireless Point-to-Point (PtP) or Point-to-Multipoint (PtMP) Links**
**Description:** High-bandwidth wireless links are used to connect APs to the main network, typically with directional antennas.

#### **Advantages:**
- **Cost-Effective for Long Distances:** Avoids the need for laying cables in outdoor or hard-to-reach areas.
- **High Speed:** Modern PtP links can rival Ethernet performance.
- **Quick Deployment:** No need for trenching or physical cabling.

#### **Challenges:**
- **Line-of-Sight Requirement:** Obstructions like buildings or trees can disrupt the signal.
- **Weather Sensitivity:** Performance may degrade in adverse weather conditions.
- **Spectrum Licensing:** Some frequencies require licensing.

#### **Best Practices:**
- Use licensed frequencies for interference-free operation.
- Deploy **directional antennas** for PtP and **sector antennas** for PtMP setups.
- Conduct a site survey to ensure line-of-sight and optimal antenna placement.

---

### **6. Hybrid Backhaul**
**Description:** Combines multiple backhaul options, such as Ethernet for primary connections and wireless mesh or PtP for areas where cabling isn’t feasible.

#### **Advantages:**
- **Resilience:** Redundant links improve network reliability.
- **Flexibility:** Can adapt to diverse deployment environments.

#### **Challenges:**
- **Complex Configuration:** Requires careful planning to manage different backhaul types.
- **Cost:** Higher initial setup cost due to mixed technologies.

#### **Best Practices:**
- Prioritize Ethernet for critical areas and use wireless for remote or temporary setups.
- Implement load balancing to optimize network traffic across backhaul links.

---

### **Recommendation for IoT Networks**
For a large IoT network with multiple ESP32 devices:
- **Primary Backhaul:** Use Ethernet for access points close to the main network for reliability.
- **Secondary Backhaul:** Deploy a wireless mesh or PtP links for remote areas.
- **Segmentation:** Use VLANs to isolate IoT traffic.
- **Centralized Management:** Use controllers like UniFi or Omada to simplify AP configuration and roaming.

This hybrid approach ensures a reliable and scalable network, minimizing hardcoding of SSIDs in ESP32 devices while maintaining security and performance.