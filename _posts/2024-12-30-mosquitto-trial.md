---
title: "IOT - Mosquitto Trial"
date: 2024-12-30 01:00:00 +0700
categories: [IOT, server]
tags: [IOT, server, proxmox, mosquitto]
---

**Wiring and Script for ESP32 with DHT22 Sensors to Publish to Mosquitto Server**

### **Hardware Setup**

#### **Components Required:**
- 2 x ESP32 Devkit-C boards or any type available
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

