---
title: "IOT - Mosquitto Trial"
date: 2024-12-30 01:00:00 +0700
categories: [IOT, server]
tags: [IOT, server, proxmox, mosquitto]
---

**Wiring and Script for ESP32 with DHT22 Sensors to Publish to Mosquitto Server**

### **Hardware Setup**

#### **Components Required:**
- 2 x ESP32 Devkit-C boards
- 2 x DHT22 temperature and humidity sensors
- Jumper wires
- Breadboards

#### **Wiring for Each Device:**
1. **DHT22 Pinout:**
   - **VCC (Pin 1)**: Connect to **3.3V** on ESP32.
   - **Data (Pin 2)**: Connect to **GPIO4** on ESP32 (can be changed in the code).
   - **GND (Pin 4)**: Connect to **GND** on ESP32.

2. **Connections for ESP32 (Device 1 and Device 2):**
   - Connect each DHT22 sensor to its respective ESP32 board as per the pinout above.
   - Ensure both ESP32 boards are powered via USB or an external power supply.

---

### **Arduino IDE Setup**

#### **Install Required Libraries:**
1. Open Arduino IDE.
2. Go to **Sketch > Include Library > Manage Libraries**.
3. Search for and install the following libraries:
   - **Adafruit Unified Sensor**
   - **DHT sensor library**
   - **PubSubClient** (for MQTT)

---

### **Script for Each ESP32 Device**

#### **Device 1 Script:**
```cpp
#include <WiFi.h>
#include <PubSubClient.h>
#include "DHT.h"

#define DHTPIN 4          // GPIO connected to the DHT22 data pin
#define DHTTYPE DHT22     // DHT 22 (AM2302)

DHT dht(DHTPIN, DHTTYPE);

// WiFi credentials
const char* ssid = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";

// MQTT broker details
const char* mqtt_server = "YOUR_MQTT_BROKER_IP";
const char* mqtt_topic = "device1/temperature_humidity";

WiFiClient espClient;
PubSubClient client(espClient);

void setup() {
  Serial.begin(115200);
  dht.begin();

  // Connect to WiFi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi connected");

  client.setServer(mqtt_server, 1883);
}

void reconnect() {
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    if (client.connect("ESP32_Device1")) {
      Serial.println("connected");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      delay(5000);
    }
  }
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  // Read temperature and humidity
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  // Prepare payload
  char payload[50];
  snprintf(payload, sizeof(payload), "{\"temperature\": %.2f, \"humidity\": %.2f}", t, h);

  // Publish data
  client.publish(mqtt_topic, payload);
  Serial.println(payload);

  delay(5000);  // Delay for 5 seconds
}
```

#### **Device 2 Script:**
Change only the MQTT topic and client ID:
```cpp
const char* mqtt_topic = "device2/temperature_humidity";
...
if (client.connect("ESP32_Device2")) {
...
```
Use the same wiring and setup process for Device 2.

---

### **Testing**
1. Upload the respective scripts to each ESP32 device via Arduino IDE.
2. Ensure the Mosquitto broker is running.
3. Use an MQTT client (e.g., MQTT Explorer or mosquitto_sub) to subscribe to:
   - `device1/temperature_humidity`
   - `device2/temperature_humidity`
4. Verify that temperature and humidity data from both devices are being published to the respective topics.

---

### **Setting Up a Database Service on Proxmox VE to Store Data**

#### **Step 1: Install a Database Service on Proxmox VE**

1. **Create a VM or LXC Container for the Database**:
   - Follow the process in Proxmox to create a VM or LXC container.
   - Allocate resources (1 vCPU, 1GB RAM, 10GB storage).
   - Install Ubuntu Server or Debian OS.

2. **Install MySQL or MariaDB**:
   - SSH into the VM or container.
   - Update packages:
     ```bash
     sudo apt update && sudo apt upgrade -y
     ```
   - Install the database service:
     ```bash
     sudo apt install mariadb-server -y
     ```

3. **Secure the Installation**:
   - Run the security script:
     ```bash
     sudo mysql_secure_installation
     ```
   - Follow the prompts to set a root password and secure the installation.

4. **Create a Database and Table**:
   - Access MySQL:
     ```bash
     sudo mysql -u root -p
     ```
   - Create a database and table:
     ```sql
     CREATE DATABASE mqtt_data;
     USE mqtt_data;

     CREATE TABLE sensor_readings (
       id INT AUTO_INCREMENT PRIMARY KEY,
       date DATE NOT NULL,
       time TIME NOT NULL,
       source_device VARCHAR(50),
       temperature FLOAT,
       humidity FLOAT
     );
     ```
   - Exit MySQL:
     ```sql
     EXIT;
     ```

---

#### **Step 2: Write Data to the Database from MQTT**

1. **Install Python and Required Libraries**:
   - Install Python and pip:
     ```bash
     sudo apt install python3 python3-pip -y
     ```
   - Install libraries for MQTT and MySQL:
     ```bash
     pip3 install paho-mqtt mysql-connector-python
     ```

2. **Create a Python Script to Subscribe and Write Data**:
   - Save the following script as `mqtt_to_db.py`:
     ```python
     import paho.mqtt.client as mqtt
     import mysql.connector
     from datetime import datetime

     # MQTT settings
     MQTT_BROKER = "YOUR_MQTT_BROKER_IP"
     MQTT_TOPIC = ["device1/temperature_humidity", "device2/temperature_humidity"]

     # Database settings
     db = mysql.connector.connect(
         host="localhost",
         user="YOUR_DB_USER",
         password="YOUR_DB_PASSWORD",
         database="mqtt_data"
     )
     cursor = db.cursor()

     def on_connect(client, userdata, flags, rc):
         print(f"Connected with result code {rc}")
         for topic in MQTT_TOPIC:
             client.subscribe(topic)

     def on_message(client, userdata, msg):
         payload = msg.payload.decode()
         print(f"Received `{payload}` from `{msg.topic}` topic")

         # Parse data
         data = eval(payload)
         temperature = data["temperature"]
         humidity = data["humidity"]
         source_device = msg.topic.split("/")[0]
         
         # Get current date and time
         now = datetime.now()
         date = now.strftime("%Y-%m-%d")
         time = now.strftime("%H:%M:%S")

         # Insert data into the database
         cursor.execute(
             "INSERT INTO sensor_readings (date, time, source_device, temperature, humidity) VALUES (%s, %s, %s, %s, %s)",
             (date, time, source_device, temperature, humidity)
         )
         db.commit()

     client = mqtt.Client()
     client.on_connect = on_connect
     client.on_message = on_message

     client.connect(MQTT_BROKER, 1883, 60)

     client.loop_forever()
     ```

3. **Run the Script**:
   - Execute the script to start subscribing and writing data:
     ```bash
     python3 mqtt_to_db.py
     ```

---

### **Testing the Integration**
1. Ensure MQTT broker and database services are running.
2. Use MQTT clients to publish test messages to the topics.
3. Verify that the data is written into the database by querying the table:
   ```sql
   SELECT * FROM mqtt_data.sensor_readings;
   ```

This setup ensures data from the ESP32 devices is securely stored in a database for further analysis.

