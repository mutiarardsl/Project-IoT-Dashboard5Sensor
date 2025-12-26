# 🌐 ESP32 IoT Multi-Sensor Monitoring & Control System

[![ESP32](https://img.shields.io/badge/ESP32-Compatible-green.svg)](https://www.espressif.com/en/products/socs/esp32)
[![MQTT](https://img.shields.io/badge/MQTT-Protocol-blue.svg)](https://mqtt.org/)
[![Node-RED](https://img.shields.io/badge/Node--RED-Dashboard-red.svg)](https://nodered.org/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Sistem IoT berbasis ESP32 dengan monitoring 5 sensor dan kontrol 3 aktuator menggunakan protokol MQTT dan Node-RED Dashboard. Project ini menyediakan 3 versi implementasi dengan tingkat kompleksitas yang berbeda.

---

## 📋 Daftar Isi

- [Overview](#-overview)
- [Hardware Requirements](#-hardware-requirements)
- [Software Requirements](#-software-requirements)
- [Fitur Utama](#-fitur-utama)
- [Perbandingan 3 Versi](#-perbandingan-3-versi)
- [Instalasi](#-instalasi)
- [Konfigurasi](#-konfigurasi)
- [Penggunaan](#-penggunaan)
- [Arsitektur Sistem](#-arsitektur-sistem)
- [MQTT Topics](#-mqtt-topics)
- [Troubleshooting](#-troubleshooting)
- [Kontributor](#-kontributor)
- [Lisensi](#-lisensi)

---

## 🎯 Overview

Project ini mengimplementasikan sistem IoT lengkap untuk:
- **Monitoring lingkungan** secara real-time (suhu, kelembaban, cahaya, jarak, gerakan)
- **Kontrol aktuator** remote melalui dashboard web
- **Automation intelligent** berdasarkan kondisi sensor
- **Data visualization** dengan gauge dan grafik time-series

**Use Cases:**
- 🏠 Smart Home Automation
- 🔐 Security & Surveillance System
- 🌡️ Environmental Monitoring
- ⚡ Energy Management

---

## 🔧 Hardware Requirements

### Komponen Utama:
- **1x ESP32 Development Board** (ESP32-WROOM-32 atau compatible)
- **5x Sensor:**
  - DHT11/DHT22 (Temperature & Humidity)
  - HC-SR04 (Ultrasonic Distance)
  - LDR Module (Light Sensor)
  - PIR Sensor (Motion Detection)
  - *(Optional)* Potentiometer
- **3x Aktuator:**
  - LED (atau LED Module)
  - Buzzer (Active/Passive)
  - Relay Module (5V)
- Breadboard & Jumper Wires
- USB Cable (untuk programming)
- Power Supply (5V/1A minimum)

### Wiring Diagram:

```
ESP32 Pin Connections:
┌─────────────────────────────────────┐
│ Sensor       │ ESP32 Pin            │
├─────────────────────────────────────┤
│ DHT11/22     │ GPIO 15 (Data)       │
│ Ultrasonic   │ GPIO 5 (Trig)        │
│              │ GPIO 18 (Echo)       │
│ LDR          │ GPIO 34 (ADC)        │
│ PIR          │ GPIO 14              │
├─────────────────────────────────────┤
│ LED          │ GPIO 25              │
│ Buzzer       │ GPIO 13              │
│ Relay        │ GPIO 23              │
└─────────────────────────────────────┘
```

---

## 💻 Software Requirements

### Arduino IDE Setup:
```
- Arduino IDE 2.x
- ESP32 Board Support (via Board Manager)
- Libraries:
  - WiFi (built-in)
  - PubSubClient (MQTT)
  - DHT sensor library by Adafruit
  - Adafruit Unified Sensor
```

### Node-RED Setup:
```bash
# Install Node-RED
npm install -g node-red

# Install Dashboard
npm install node-red-dashboard

# Run Node-RED
node-red
```

### MQTT Broker:
- **HiveMQ Public Broker** (broker.hivemq.com:1883)
- *Alternative:* Mosquitto (local installation)

---

## ✨ Fitur Utama

### 🔍 Monitoring Real-time
- ✅ 5 sensor data update setiap 2 detik
- ✅ Dashboard web dengan gauge & grafik
- ✅ Status aktuator real-time
- ✅ Serial Monitor untuk debugging

### 🎛️ Remote Control
- ✅ Kontrol aktuator dari dashboard
- ✅ Two-way communication (ESP32 ↔ Dashboard)
- ✅ Status feedback confirmation

### 🤖 Intelligent Automation
- ✅ Smart Lighting (auto LED saat gelap)
- ✅ Security Alarm (motion detection)
- ✅ Auto Cooling (temperature control)
- ✅ Proximity Alert (ultrasonic warning)

### 🔀 Hybrid Control (Version 3)
- ✅ Manual override kapan saja
- ✅ Auto-release setelah timeout
- ✅ Per-actuator mode control

---

## 📊 Perbandingan 3 Versi

### **Version 1: Basic Sensor Reading** 
📁 `5sensor.ino`

**Karakteristik:**
- ✅ Pure manual control dari dashboard
- ✅ Tidak ada automation
- ✅ Cocok untuk pemula atau testing hardware

**Kapan Menggunakan:**
- Learning IoT basics
- Testing sensor & aktuator
- Development fase awal
- Butuh full manual control

**Kelebihan:**
- Simple & mudah dipahami
- Tidak ada automation conflict
- Full control dari user

**Kekurangan:**
- Tidak ada automation
- Perlu manual monitoring terus-menerus

---

### **Version 2: Automation Only** 
📁 `5SensorAktuatorOtomatis.ino`

**Karakteristik:**
- ✅ 4 automation scenarios aktif
- ✅ Mode switching (AUTO/MANUAL global)
- ✅ Aktuator bereaksi otomatis dari sensor

**Kapan Menggunakan:**
- Production deployment
- Smart home automation
- Minimal manual intervention
- Reliable autonomous operation

**Automation Scenarios:**
1. **Smart Lighting** - LED ON saat cahaya < 30%
2. **Security Alarm** - Buzzer + LED ON saat motion detected
3. **Auto Cooling** - Relay ON saat suhu > 25°C
4. **Proximity Alert** - Buzzer ON saat jarak < 20cm

**Kelebihan:**
- Autonomous & intelligent
- Hands-free operation
- Energy efficient (smart lighting)

**Kekurangan:**
- Mode switching all-or-nothing (semua AUTO atau semua MANUAL)
- Tidak bisa mix automation & manual

---

### **Version 3: Hybrid Control** ⭐ **RECOMMENDED**
📁 `5SensorAktuatorOtomatisManual.ino`

**Karakteristik:**
- ✅ Automation + Manual control coexist
- ✅ Per-actuator manual override
- ✅ Auto-release setelah 30 detik
- ✅ Best of both worlds

**Kapan Menggunakan:**
- Production deployment (preferred)
- Butuh flexibility
- User bisa intervene kapan saja
- Automation tetap jalan

**Hybrid Logic:**
```
┌─────────────────────────────────────┐
│  LED: AUTO → Manual Override        │
│  Buzzer: AUTO (tetap automation)    │
│  Relay: AUTO (tetap automation)     │
│                                     │
│  Setelah 30s:                       │
│  LED: Kembali AUTO otomatis         │
└─────────────────────────────────────┘
```

**Kelebihan:**
- ✅ Automation untuk convenience
- ✅ Manual override untuk flexibility
- ✅ No conflict antar mode
- ✅ Auto-release prevent forgotten manual state

**Kekurangan:**
- Slightly more complex code
- Perlu pemahaman hybrid logic

---

## 🚀 Instalasi

### 1. Clone Repository

```bash
git clone https://github.com/yourusername/esp32-iot-project.git
cd esp32-iot-project
```

### 2. Install Arduino Libraries

**Via Arduino Library Manager:**
```
Sketch → Include Library → Manage Libraries
```

Install:
- `DHT sensor library` by Adafruit
- `Adafruit Unified Sensor`
- `PubSubClient`

### 3. Setup ESP32 Board

**Add Board Manager URL:**
```
File → Preferences → Additional Board Manager URLs:
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
```

**Install ESP32 Board:**
```
Tools → Board → Boards Manager → Search "ESP32" → Install
```

### 4. Pilih Versi yang Sesuai

```
Version 1 (Basic):     5sensor.ino
Version 2 (Auto Only): 5SensorAktuatorOtomatis.ino
Version 3 (Hybrid):    5SensorAktuatorOtomatisManual.ino ⭐
```

---

## ⚙️ Konfigurasi

### 1. WiFi Configuration

Edit di kode baris ~6-7:
```cpp
const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";
```

### 2. MQTT Configuration

Edit di kode baris ~9-11:
```cpp
const char* mqtt_server = "broker.hivemq.com";
const int mqtt_port = 1883;
String clientId = "ESP32_YourName_"; // Ganti dengan nama unik
```

### 3. MQTT Topics

Edit di kode baris ~72-86:
```cpp
const char* TOPIC_TEMP = "YourGroup/esp32/temperature";
const char* TOPIC_HUMID = "YourGroup/esp32/humidity";
// ... ganti "YourGroup" dengan identifier unik Anda
```

⚠️ **PENTING:** Gunakan topic unik untuk avoid conflict dengan user lain di public broker!

### 4. Pin Configuration

Sesuaikan pin dengan board Anda (baris ~14-26):
```cpp
#define DHTPIN 15           // Ubah sesuai wiring
#define LDR_PIN 34          
#define PIR_PIN 14          
#define LED_RED_PIN 25      
// ...
```

### 5. Automation Thresholds (Version 2 & 3)

Edit threshold di baris ~38-40:
```cpp
#define LIGHT_THRESHOLD 30       // Cahaya < 30% = gelap
#define TEMP_THRESHOLD 25.0      // Suhu > 25°C = panas
#define DISTANCE_THRESHOLD 20.0  // Jarak < 20cm = dekat
```

### 6. LDR Calibration

Edit di baris ~46:
```cpp
#define LDR_INVERT true  // Set false jika mapping terbalik
```

**Cara Test:**
1. Upload code
2. Buka Serial Monitor (115200 baud)
3. Lihat "LDR Average Raw Value"
4. Tutup LDR → Lihat nilai naik/turun?
   - Nilai turun saat ditutup → `LDR_INVERT false`
   - Nilai naik saat ditutup → `LDR_INVERT true`

---

## 📱 Penggunaan

### Upload ke ESP32

1. Connect ESP32 via USB
2. **Tools → Board:** "ESP32 Dev Module"
3. **Tools → Port:** Select COM port
4. **Upload** (→ button)
5. Open **Serial Monitor** (Ctrl+Shift+M)
6. Set baud rate: **115200**

### Expected Serial Output

```
╔═══════════════════════════════════════╗
║   ESP32 IoT - System Starting         ║
╚═══════════════════════════════════════╝

🔧 Initializing Sensors...
  ✓ DHT Sensor
  ✓ Ultrasonic Sensor
  ✓ LDR Sensor
  ✓ PIR Sensor

🔧 Initializing Actuators...
  ✓ LED
  ✓ Buzzer
  ✓ Relay

📡 Connecting WiFi..... ✓
   IP: 192.168.1.xxx

🔄 MQTT... ✓
  ✓ Subscribed to control topics

╔═══════════════════════════════════════╗
║      System Ready! Monitoring...      ║
╚═══════════════════════════════════════╝
```

### Setup Node-RED Dashboard

1. **Start Node-RED:**
```bash
node-red
```

2. **Open Flow Editor:**
```
http://localhost:1880
```

3. **Import Flow:**
   - Menu (☰) → Import
   - Paste JSON flow (lihat `/node-red-flow/` directory)
   - **Deploy**

4. **Access Dashboard:**
```
http://localhost:1880/ui
```

### Dashboard Features

**Tab 1: Sensor Monitoring**
- 📊 Gauge untuk instant values
- 📈 Chart untuk historical data
- 🔄 Auto-update setiap 2 detik

**Tab 2: Actuator Control**
- 🔘 Toggle switches untuk ON/OFF
- 📍 Status indicators
- 🔄 Real-time feedback

**Tab 3: System Status (Version 3)**
- 🤖 Mode indicators (AUTO/MANUAL)
- ⏱️ Countdown timers
- 📊 System health

---

## 🏗️ Arsitektur Sistem

```
┌─────────────────────────────────────────────┐
│         PRESENTATION LAYER                  │
│    Node-RED Dashboard (Web Interface)       │
└──────────────────┬──────────────────────────┘
                   │ HTTP
┌──────────────────▼──────────────────────────┐
│         APPLICATION LAYER                   │
│    Node-RED Flow Processing                 │
└──────────────────┬──────────────────────────┘
                   │ MQTT Protocol
┌──────────────────▼──────────────────────────┐
│      COMMUNICATION LAYER                    │
│    HiveMQ MQTT Broker (Public)              │
└──────────────────┬──────────────────────────┘
                   │ MQTT Protocol
┌──────────────────▼──────────────────────────┐
│           DEVICE LAYER                      │
│  ESP32 + Sensors + Actuators                │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐     │
│  │ DHT11   │  │ LDR     │  │ PIR     │     │
│  │ HC-SR04 │  │ LED     │  │ Buzzer  │     │
│  │         │  │ Relay   │  │         │     │
│  └─────────┘  └─────────┘  └─────────┘     │
└─────────────────────────────────────────────┘
```

### Data Flow

```
Sensor Reading:
ESP32 → Read Sensors → Publish MQTT → Node-RED → Display Dashboard

Manual Control:
Dashboard → Click Switch → MQTT Publish → ESP32 → Actuator ON/OFF

Automation (V2/V3):
ESP32 → Evaluate Conditions → Auto Control → Publish Status → Dashboard Update
```

---

## 📡 MQTT Topics

### Sensor Data (ESP32 → Dashboard)

| Topic | Description | Data Type | Example |
|-------|-------------|-----------|---------|
| `Klp2/esp32/temperature` | Suhu (°C) | Float | `28.50` |
| `Klp2/esp32/humidity` | Kelembaban (%) | Float | `65.30` |
| `Klp2/esp32/distance` | Jarak (cm) | Float | `120.45` |
| `Klp2/esp32/light` | Cahaya (%) | Integer | `75` |
| `Klp2/esp32/motion` | Gerakan | Boolean | `0` / `1` |

### Actuator Control (Dashboard → ESP32)

| Topic | Description | Payload | Response |
|-------|-------------|---------|----------|
| `Klp2/esp32/led/control` | LED ON/OFF | `ON` / `OFF` | Immediate |
| `Klp2/esp32/buzzer/control` | Buzzer ON/OFF | `ON` / `OFF` | Immediate |
| `Klp2/esp32/relay/control` | Relay ON/OFF | `ON` / `OFF` | Immediate |

### Status Feedback (ESP32 → Dashboard)

| Topic | Description | Payload |
|-------|-------------|---------|
| `Klp2/esp32/led/status` | LED state | `ON` / `OFF` |
| `Klp2/esp32/buzzer/status` | Buzzer state | `ON` / `OFF` |
| `Klp2/esp32/relay/status` | Relay state | `ON` / `OFF` |

### Mode Control (Version 3 Only)

| Topic | Description | Payload |
|-------|-------------|---------|
| `Klp2/esp32/led/mode` | LED mode | `AUTO` / `MANUAL` |
| `Klp2/esp32/buzzer/mode` | Buzzer mode | `AUTO` / `MANUAL` |
| `Klp2/esp32/relay/mode` | Relay mode | `AUTO` / `MANUAL` |

---

## 🐛 Troubleshooting

### ESP32 Tidak Connect WiFi

**Problem:** Serial Monitor menunjukkan titik-titik terus tanpa connect

**Solutions:**
1. Cek SSID dan password benar (case-sensitive)
2. Pastikan WiFi 2.4GHz (ESP32 tidak support 5GHz)
3. Cek signal strength WiFi
4. Reset ESP32 (tombol EN/RST)

```cpp
// Debug: Print WiFi info
Serial.println(ssid);
Serial.println(password);
```

---

### MQTT Connection Failed

**Problem:** `✗ Failed, rc=-2` atau `rc=-4`

**Solutions:**
1. Cek koneksi internet
2. Test MQTT broker:
   ```bash
   mosquitto_sub -h broker.hivemq.com -t "#" -v
   ```
3. Ganti broker alternatif:
   ```cpp
   const char* mqtt_server = "broker.emqx.io";
   ```
4. Cek firewall tidak block port 1883

**MQTT Error Codes:**
- `rc=-2`: Network connection failed
- `rc=-4`: Connection timeout
- `rc=5`: Connection refused (authentication)

---

### Sensor Tidak Terbaca

**Problem:** Sensor return `-999`, `-1`, atau `NaN`

**Solutions:**

**DHT Sensor:**
1. Cek wiring: VCC → 3.3V, GND → GND, Data → GPIO 15
2. Coba delay lebih lama: `delay(2000)` sebelum read
3. Cek library version up-to-date
4. Test dengan contoh code library DHT

**Ultrasonic:**
1. Cek wiring: VCC → 5V (bukan 3.3V!)
2. Test pin echo dengan multimeter
3. Pastikan tidak ada obstacle di depan sensor

**LDR:**
1. Cek nilai Raw di Serial Monitor
2. Adjust mapping atau `LDR_INVERT`
3. Test dengan cover/uncover sensor

**PIR:**
1. Warm-up 30-60 detik setelah power on
2. Adjust sensitivity potensiometer di module
3. Cek jarak deteksi (max 7 meter)

---

### Dashboard Tidak Update

**Problem:** Dashboard tidak menampilkan data atau frozen

**Solutions:**
1. Refresh browser (Ctrl+F5)
2. Cek Node-RED flow sudah deploy
3. Verify MQTT topics match:
   ```
   ESP32 publish: "Klp2/esp32/temperature"
   Node-RED subscribe: "Klp2/esp32/temperature"
   ```
4. Check Node-RED console untuk errors:
   ```bash
   # Terminal running node-red
   # Look for connection errors
   ```

---

### Aktuator Tidak Merespon

**Problem:** Klik switch di dashboard tapi hardware tidak berubah

**Solutions:**
1. Cek Serial Monitor muncul "📩 [topic] message"
2. Verify callback function running
3. Test manual digitalWrite:
   ```cpp
   digitalWrite(LED_RED_PIN, HIGH);
   delay(1000);
   digitalWrite(LED_RED_PIN, LOW);
   ```
4. Cek wiring aktuator
5. Test aktuator dengan voltmeter

---

### Automation Tidak Jalan (V2/V3)

**Problem:** Aktuator tidak bereaksi meski kondisi terpenuhi

**Solutions:**
1. Cek mode: Harus AUTO (bukan MANUAL)
   ```
   Serial Monitor: [AUTO Mode]
   ```
2. Cek threshold values sesuai:
   ```cpp
   Serial.printf("Light: %d%% (Threshold: %d%%)\n", 
                 lightLevel, LIGHT_THRESHOLD);
   ```
3. Test dengan threshold lebih rendah
4. Disable automation yang conflict:
   ```cpp
   #define AUTO_LIGHTING false  // Test one by one
   ```

---

### LDR Mapping Terbalik (V2/V3)

**Problem:** LED nyala saat terang, mati saat gelap

**Solution:**
```cpp
// Di kode baris ~46
#define LDR_INVERT true  // Toggle antara true/false
```

**How to Test:**
1. Upload code
2. Open Serial Monitor
3. Look for: `LDR Average Raw Value: XXXX`
4. Cover LDR dengan tangan
5. Jika nilai RAW naik → `LDR_INVERT true`
6. Jika nilai RAW turun → `LDR_INVERT false`

---

### PIR Kedip-kedip Terus (V2/V3)

**Problem:** Buzzer/LED nyala-mati setiap 2-3 detik

**Solutions:**
1. Increase debounce time:
   ```cpp
   #define PIR_DEBOUNCE_TIME 5000  // 5 seconds
   ```
2. Adjust PIR sensitivity (potensiometer di module)
3. Pindahkan PIR dari sumber panas/gerakan
4. Wait PIR warm-up (30-60 seconds after power on)

---

### Version 3: Manual Override Tidak Release

**Problem:** Manual control tidak kembali AUTO setelah 30 detik

**Solutions:**
1. Cek `checkManualTimeout()` dipanggil di loop
2. Verify timer logic di Serial Monitor:
   ```
   Auto-release in 30 seconds...
   ```
3. Adjust timeout duration:
   ```cpp
   #define MANUAL_TIMEOUT 10000  // 10 seconds for testing
   ```

---

## 📚 Resources

### Documentation
- [ESP32 Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32_datasheet_en.pdf)
- [MQTT Protocol Specs](https://mqtt.org/mqtt-specification/)
- [Node-RED Docs](https://nodered.org/docs/)
- [DHT Sensor Guide](https://learn.adafruit.com/dht)

### Tutorials
- [ESP32 Getting Started](https://randomnerdtutorials.com/getting-started-with-esp32/)
- [MQTT Basics](https://www.hivemq.com/mqtt-essentials/)
- [Node-RED Dashboard Tutorial](https://flows.nodered.org/node/node-red-dashboard)

### Tools
- [MQTT Explorer](http://mqtt-explorer.com/) - MQTT client for testing
- [Arduino Serial Monitor](https://docs.arduino.cc/software/ide-v2/tutorials/ide-v2-serial-monitor/)
- [Fritzing](https://fritzing.org/) - Circuit design tool

---

## 👥 Kontributor

- Anggota 1 - Lutfiah Nailil Izzah
- Anggota 2 - Acik Imtia Chana
- Anggota 3 - Sylvasisca Andini
- Anggota 4 - Hanifa Syifa Syafitri
- Anggota 5 - Muhammad Tony Putra
- Anggota 6 - Poeti Jelita

---

## 📄 Lisensi

MIT License - see [LICENSE](LICENSE) file for details

---

## 🙏 Acknowledgments

- ESP32 community untuk resources
- Adafruit untuk sensor libraries
- Node-RED team untuk awesome platform

---

## 📞 Support

Jika ada pertanyaan atau issue:
1. **Check Troubleshooting** section di atas
2. **Open GitHub Issue** dengan detail:
   - Versi code yang digunakan
   - Serial Monitor output
   - Error message lengkap
   - Hardware setup
3. **Email:** mutiararosidas07@gmail.com

---

## 🔄 Changelog

### v3.0.0 (Latest) - Hybrid Control
- ✨ Added per-actuator manual override
- ✨ Auto-release mechanism (30s timeout)
- ✨ Mode indicators in dashboard
- 🐛 Fixed PIR debounce issues
- 🐛 Fixed LDR mapping problems

### v2.0.0 - Automation
- ✨ Added 4 automation scenarios
- ✨ Smart lighting, security, cooling, proximity
- ✨ Mode switching (AUTO/MANUAL)
- 🐛 Improved sensor reading stability

### v1.0.0 - Basic
- ✨ Initial release
- ✨ Basic sensor reading
- ✨ Manual control from dashboard
- ✨ MQTT integration
