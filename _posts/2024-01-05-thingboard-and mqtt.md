---
title: "IOT - ThingBoard or Mosquitto?"
date: 2025-01-05 00:00:00 +0700
categories: [IOT, server]
tags: [IOT, server, proxmox, thingsboard, mosquitto]
---

### **Best Practices for Installing ThingsBoard on Proxmox VE and Using It with an Existing MQTT Broker**

#### **Overview**
ThingsBoard is an open-source IoT platform that supports data collection, visualization, and device management. It has a built-in MQTT broker, but it can also integrate with external MQTT brokers like Mosquitto.

Here’s a step-by-step guide to install ThingsBoard on a Proxmox VE LXC container and configure it to work with an existing MQTT broker.

---

### **Step 1: Prepare Proxmox VE Environment**

1. **Create an LXC Container**:
   - Allocate resources for the container (e.g., 2 vCPUs, 4GB RAM, and 20GB storage).
   - Use a **Debian 11** or **Ubuntu 22.04** template for compatibility.

2. **Access the Container**:
   - SSH into the container or use the Proxmox console.

---

### **Step 2: Install ThingsBoard in the LXC Container**

1. **Update the System**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install Java** (ThingsBoard requires Java):
   ```bash
   sudo apt install openjdk-11-jdk -y
   ```

3. **Download and Install ThingsBoard**:
   - Download the ThingsBoard installation package:
     ```bash
     wget https://github.com/thingsboard/thingsboard/releases/download/v3.5.1/thingsboard-3.5.1.deb
     ```
   - Install ThingsBoard:
     ```bash
     sudo dpkg -i thingsboard-3.5.1.deb
     ```
   - Install any missing dependencies:
     ```bash
     sudo apt --fix-broken install
     ```

4. **Configure ThingsBoard Database**:
   ThingsBoard requires either **PostgreSQL** or **TimescaleDB**. Use PostgreSQL for simplicity:
   ```bash
   sudo apt install postgresql postgresql-contrib -y
   ```

   - Configure the database:
     ```bash
     sudo -u postgres psql
     CREATE DATABASE thingsboard;
     CREATE USER tb_user WITH PASSWORD 'tb_password';
     GRANT ALL PRIVILEGES ON DATABASE thingsboard TO tb_user;
     \q
     ```

   - Update ThingsBoard configuration:
     ```bash
     sudo nano /etc/thingsboard/conf/thingsboard.yml
     ```
     Update the database section:
     ```yaml
     database:
       type: postgresql
       host: 127.0.0.1
       port: 5432
       name: thingsboard
       username: tb_user
       password: tb_password
     ```

5. **Start ThingsBoard**:
   ```bash
   sudo service thingsboard start
   ```

---

### **Step 3: Configure ThingsBoard to Use an External MQTT Broker**

1. **Edit ThingsBoard MQTT Configuration**:
   - Open the configuration file:
     ```bash
     sudo nano /etc/thingsboard/conf/thingsboard.yml
     ```
   - Locate the MQTT integration section and update it to point to your Mosquitto broker:
     ```yaml
     mqtt:
       enabled: true
       host: YOUR_MQTT_BROKER_IP
       port: 1883
       security:
         enabled: false
     ```

2. **Restart ThingsBoard**:
   ```bash
   sudo service thingsboard restart
   ```

---

### **Step 4: Configure ThingsBoard Devices**

1. **Create a Device in ThingsBoard**:
   - Access ThingsBoard Web UI:  
     Open your browser and navigate to `http://<LXC_IP>:8080`. Log in with the default credentials:
     - Username: `tenant@thingsboard.org`
     - Password: `tenant`

   - Go to **Devices** > **Add Device** and create a new device.

2. **Get the Device Access Token**:
   - After creating the device, click on it to retrieve the **Access Token**.

3. **Modify ESP32 MQTT Code**:
   Update the ESP32 code to publish data using the ThingsBoard token:
   ```cpp
   const char* mqtt_topic = "v1/devices/me/telemetry";
   const char* access_token = "YOUR_DEVICE_ACCESS_TOKEN";

   client.connect("ESP32", access_token, NULL);
   ```

---

### **Step 5: Visualize Data in ThingsBoard**

1. **Create a Dashboard**:
   - Go to **Dashboards** in ThingsBoard.
   - Add widgets to visualize temperature, humidity, or other telemetry data.

2. **Test the Setup**:
   - Publish test data from ESP32 to ThingsBoard via Mosquitto.
   - Verify that the data appears in the ThingsBoard dashboard.

---

### **Step 6: Security Best Practices**

1. **Secure the MQTT Broker**:
   - Enable authentication and TLS for Mosquitto to secure communication.

2. **Secure ThingsBoard**:
   - Use HTTPS for the ThingsBoard Web UI.
   - Set strong passwords for database and ThingsBoard accounts.

3. **Monitor the System**:
   - Use Proxmox tools to monitor the performance of the LXC container.
   - Set up logging and alerts in ThingsBoard for unusual activities.

---

### **Conclusion**

By using an external MQTT broker with ThingsBoard, you can manage IoT data efficiently while leveraging the robust visualization and device management features of ThingsBoard. This architecture also provides flexibility and scalability for future expansion.  

But which is better, standalone Thingsboard or combo with Mosquitto?  
  
  
### **ThingsBoard with External MQTT Broker (e.g., Mosquitto) vs. Fully Integrated ThingsBoard**

Both approaches have their advantages, and the choice depends on your specific requirements, including scalability, reliability, security, and ease of maintenance. Here's a detailed comparison:

---

### **1. ThingsBoard with an External MQTT Broker (Mosquitto)**

#### **Advantages:**

1. **Scalability:**
   - Mosquitto is lightweight and optimized for high-throughput MQTT messaging.
   - You can handle a large number of devices and topics independently of ThingsBoard's internal processing.

2. **Decoupling:**
   - Separation of responsibilities ensures that ThingsBoard handles only visualization, device management, and analytics, while Mosquitto focuses on messaging.
   - Makes it easier to scale or replace individual components if needed.

3. **Flexibility:**
   - Mosquitto can integrate with other systems or applications beyond ThingsBoard.
   - Allows for advanced configurations like bridging multiple brokers, QoS settings, and session persistence.

4. **Reliability:**
   - Mosquitto’s simplicity and stability reduce potential points of failure.
   - If ThingsBoard encounters an issue, the MQTT broker can still buffer messages (if configured with persistence).

5. **Enhanced Debugging and Monitoring:**
   - Tools like **MQTT Explorer** or **mosquitto_sub** allow for detailed inspection of MQTT traffic.

---

#### **Disadvantages:**

1. **Complexity:**
   - Requires additional setup and configuration for Mosquitto.
   - More components to manage (e.g., maintaining broker security, monitoring two systems).

2. **Latency:**
   - Slight increase in message latency due to routing through the external broker.

---

### **2. Fully Integrated ThingsBoard (Without Mosquitto)**

#### **Advantages:**

1. **Simpler Architecture:**
   - All-in-one solution reduces complexity and the number of components to manage.
   - Easier to deploy and maintain for smaller projects or less experienced users.

2. **Lower Latency:**
   - Direct communication between devices and ThingsBoard minimizes latency.

3. **Tighter Integration:**
   - Built-in MQTT broker is optimized for ThingsBoard’s internal data flow and features.
   - Easier to configure device attributes, telemetry, and RPC (Remote Procedure Calls).

4. **Reduced Resource Usage:**
   - Avoids the need to allocate additional system resources for an external broker.

---

#### **Disadvantages:**

1. **Limited Scalability:**
   - The built-in broker may struggle to handle a high volume of simultaneous connections or messages compared to Mosquitto.
   - Resource-intensive if you have thousands of devices or high-frequency data.

2. **Single Point of Failure:**
   - If ThingsBoard goes down, MQTT messaging also stops, as the built-in broker is tied to the platform.

3. **Less Flexibility:**
   - Difficult to use the built-in broker with other systems outside ThingsBoard.
   - Advanced MQTT features like message bridging or persistent sessions may be limited.

4. **Performance Overhead:**
   - Combined workloads (data storage, analytics, and MQTT) may affect performance under heavy load.

---

### **When to Choose Each Approach**

| **Criteria**                | **ThingsBoard + Mosquitto**               | **Fully Integrated ThingsBoard**       |
|-----------------------------|-------------------------------------------|----------------------------------------|
| **Project Scale**           | Large-scale projects with many devices    | Small to medium-sized projects         |
| **Scalability Needs**       | High                                      | Moderate                               |
| **System Flexibility**      | High                                      | Low                                    |
| **Ease of Setup**           | Moderate complexity                       | Simple setup                           |
| **Fault Tolerance**         | Higher (separate components)              | Lower (single point of failure)        |
| **Performance**             | Optimized for high-throughput             | Sufficient for small-scale use cases   |

---

### **Conclusion:**

- **For large-scale or complex IoT deployments**, using an external MQTT broker like Mosquitto in tandem with ThingsBoard is the better choice due to its scalability, flexibility, and fault tolerance.
  
- **For smaller projects or simpler deployments**, the fully integrated ThingsBoard solution is sufficient and easier to manage.

---

### **How to Decide?**
- Start with **fully integrated ThingsBoard** if you're prototyping or managing a small number of devices.
- Transition to **ThingsBoard + Mosquitto** as your system grows or requires advanced MQTT features.