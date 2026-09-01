# IoT Room Controller - Complete README.md

```markdown
# 🏠 IoT Room Controller

[![Platform](https://img.shields.io/badge/Platform-ESP32S3-blue.svg)](https://www.espressif.com/en/products/socs/esp32-s3)
[![Framework](https://img.shields.io/badge/Framework-Arduino-red.svg)](https://www.arduino.cc/)
[![MQTT](https://img.shields.io/badge/MQTT-EMQX-brightgreen.svg)](https://www.emqx.com/)
[![Telegram](https://img.shields.io/badge/Telegram-Bot-26A5E4.svg)](https://core.telegram.org/bots)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/Version-2.0-blue.svg)](https://github.com/yourusername/iot-room-controller)

**A complete IoT Room Controller for ESP32-S3 with MQTT control, Telegram bot integration, and OTA firmware updates.**

---

## 📋 Table of Contents

- [Features](#-features)
- [Hardware Requirements](#-hardware-requirements)
- [Quick Start](#-quick-start)
- [Control Methods](#-control-methods)
- [OTA Updates](#-ota-updates)
- [MQTT Topics](#-mqtt-topics)
- [Hardware Wiring](#-hardware-wiring)
- [Configuration](#-configuration)
- [Troubleshooting](#-troubleshooting)
- [Libraries](#-libraries)

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 🎯 **WiFiManager** | Easy WiFi setup with captive portal |
| 📨 **MQTT Control** | Full control via MQTT broker |
| 🤖 **Telegram Bot** | Remote control and status monitoring |
| 🔄 **OTA Updates** | Over-the-air firmware updates with progress |
| 💾 **State Persistence** | Saves relay state in flash memory |
| 🏨 **Check-In/Out** | Room management with notifications |
| 🔄 **Reset Button** | Reset WiFi by holding BOOT button 5s |
| 📊 **Real-time Status** | MQTT updates and LED indicator |
| ⏰ **Cambodia Time** | UTC+7 timezone support |

---

## 🔧 Hardware Requirements

### Components
| Component | Specification | Purpose |
|-----------|---------------|---------|
| Microcontroller | ESP32-S3 (DevKitC-1) | Main controller |
| Relay Module | 5V Relay (GPIO9) | Room power control |
| LED | Any 3.3V LED (GPIO2) | WiFi status indicator |
| Button | Tactile switch (GPIO0) | WiFi reset |

### Pin Mapping
| Pin | Function | Description |
|-----|----------|-------------|
| GPIO9 | RELAY_PIN | Relay control output |
| GPIO2 | WIFI_LED_PIN | WiFi status LED |
| GPIO0 | RESET_BUTTON_PIN | Reset WiFi (hold 5s) |

---

## 🚀 Quick Start

### 1. Clone & Install
```bash
git clone https://github.com/yourusername/iot-room-controller.git
cd iot-room-controller
```

### 2. Configure Telegram
Edit `bot.h`:
```cpp
#define BOT_TOKEN "YOUR_BOT_TOKEN_HERE"
#define CHAT_ID   "YOUR_CHAT_ID_HERE"
```

### 3. Upload Firmware
```bash
# PlatformIO
pio run -t upload

# Arduino IDE: Select ESP32S3 Dev Module → Upload
```

### 4. First Time WiFi Setup
1. Device creates AP: **"Smart CB"** (password: **"password"**)
2. Connect to AP from your phone/PC
3. Captive portal opens automatically
4. Enter your WiFi credentials
5. Device connects and saves settings

---

## 🎮 Control Methods

### 1. MQTT Control

#### Basic Commands
```bash
# Turn ON/OFF
mosquitto_pub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/relay1" -m "ON"
mosquitto_pub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/relay1" -m "OFF"

# Check-In/Out (with Telegram notification)
mosquitto_pub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/room" -m "CHECK-IN"
mosquitto_pub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/room" -m "CHECK-OUT"

# Get Status
mosquitto_pub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/relay1/get" -m ""

# Subscribe to Status
mosquitto_sub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/relay1/status"
```

#### Response Format
```json
{
  "state": "ON",
  "device": "EC-IoT-MNSEC0001",
  "timestamp": "2026-09-01 14:30:25"
}
```

### 2. Telegram Bot Control
| Command | Description |
|---------|-------------|
| `/status` | Get device status (relay, WiFi, signal, version) |

### 3. Hardware Control
| Action | Method |
|--------|--------|
| Reset WiFi | Hold BOOT button 5 seconds |

### 4. WiFiManager Setup
| Step | Action |
|------|--------|
| 1 | Connect to "Smart CB" |
| 2 | Enter WiFi credentials |
| 3 | Device auto-connects |

---

## 🔄 OTA Updates

### OTA Topics
| Topic | Direction | Description |
|-------|-----------|-------------|
| `/IoT/EC-IoT-MNSEC0001/ota/update` | Publish | Start OTA update |
| `/IoT/EC-IoT-MNSEC0001/ota/status` | Subscribe | OTA status updates |
| `/IoT/EC-IoT-MNSEC0001/ota/progress` | Subscribe | Progress updates (0-100%) |
| `/IoT/EC-IoT-MNSEC0001/ota/get` | Publish | Request OTA status |

### OTA Command Format
```json
{
  "ota_update": {
    "url": "https://example.com/firmware.bin",
    "version": "v2.0"
  }
}
```

### OTA Example
```bash
# Start OTA Update
mosquitto_pub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/ota/update" -m '{"ota_update":{"url":"https://example.com/firmware.bin","version":"v2.0"}}'

# Monitor Progress
mosquitto_sub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/ota/progress"

# Subscribe to all OTA topics
mosquitto_sub -h broker.emqx.io -t "/IoT/EC-IoT-MNSEC0001/ota/#"
```

### OTA Flow
```
User → MQTT Publish (ota/update)
         ↓
    ESP32 Downloads Firmware
         ↓
    Progress Updates (every 5%)
         ↓
    Flash & Verify
         ↓
    Reboot (success) or Error (fail)
```

### OTA Security Features
- ⏱️ 2-minute timeout protection
- 🚫 Prevents multiple simultaneous updates
- 📊 Real-time progress tracking
- 📝 Detailed error reporting
- 📱 Telegram notifications

---

## 📡 MQTT Topics

### Complete Topic List
| Topic | Payload | Description |
|-------|---------|-------------|
| `/IoT/EC-IoT-MNSEC0001/relay1` | `ON` / `OFF` | Control relay |
| `/IoT/EC-IoT-MNSEC0001/relay1/status` | `ON` / `OFF` | Current state (retained) |
| `/IoT/EC-IoT-MNSEC0001/relay1/get` | *(any)* | Request status |
| `/IoT/EC-IoT-MNSEC0001/room` | `CHECK-IN` / `CHECK-OUT` | Room control |
| `/IoT/EC-IoT-MNSEC0001/ota/update` | JSON | Start OTA |
| `/IoT/EC-IoT-MNSEC0001/ota/status` | JSON | OTA status (retained) |
| `/IoT/EC-IoT-MNSEC0001/ota/progress` | JSON | Progress updates |
| `/IoT/EC-IoT-MNSEC0001/ota/get` | *(any)* | Get OTA status |

### MQTT Configuration
```cpp
const char* MQTT_BROKER = "broker.emqx.io";
const int   MQTT_PORT   = 1883;
const char* MQTT_USER   = "chhunlong";
const char* MQTT_PASS   = "1234";
```

---

## 🔌 Hardware Wiring

```
ESP32-S3          Relay Module          LED           Button
┌─────────┐      ┌────────────┐      ┌──────┐      ┌────────┐
│         │      │            │      │      │      │        │
│  GPIO9  ├──────► IN         │      │      │      │        │
│         │      │   Relay    │      │      │      │        │
│         │      │            │      │      │      │        │
│  GPIO2  ├───────────────────────►  │  LED │      │        │
│         │      │            │      │      │      │        │
│         │      │            │      │      │      │        │
│  GPIO0  ├───────────────────────────────────────► Button │
│         │      │            │      │      │      │        │
│  3.3V   ├──────► VCC        ├──────► VCC  ├──────► VCC    │
│         │      │            │      │      │      │        │
│  GND    ├──────► GND        ├──────► GND  ├──────► GND    │
│         │      │            │      │      │      │        │
└─────────┘      └────────────┘      └──────┘      └────────┘
```

### Connection Checklist
- [ ] GPIO9 → Relay IN
- [ ] GPIO2 → LED Anode (with 220Ω resistor)
- [ ] GPIO0 → Button (to GND)
- [ ] 3.3V → All VCC
- [ ] GND → All ground

---

## ⚙️ Configuration

### WiFi
| Parameter | Value |
|-----------|-------|
| AP Name | `Smart CB` |
| AP Password | `password` |
| WiFi Mode | STA |

### MQTT
| Parameter | Value |
|-----------|-------|
| Broker | `broker.emqx.io` |
| Port | `1883` |
| Username | `chhunlong` |
| Password | `1234` |

### Telegram
```cpp
#define BOT_TOKEN "YOUR_BOT_TOKEN"
#define CHAT_ID   "YOUR_CHAT_ID"
```

### OTA
| Parameter | Value |
|-----------|-------|
| Timeout | 120 seconds |
| Progress Report | Every 5% |

---

## 🔍 Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| WiFi Connection Failed | Hold BOOT 5s → Reconfigure |
| MQTT Connection Failed | Check broker/credentials |
| OTA Update Fails | Verify URL accessible |
| Telegram Bot Not Responding | Check token/chat ID |
| Compilation Error | Remove duplicate `getCambodiaTime()` |

### LED Status Indicators
| LED State | Meaning |
|-----------|---------|
| OFF | WiFi disconnected |
| Blinking (1s) | WiFi connected |
| Fast Blink | OTA in progress |
| 5 Blinks | WiFi reset |

### Reset Procedures
| Issue | Procedure |
|-------|-----------|
| WiFi Credentials | Hold BOOT 5s |
| Full Reset | Re-upload firmware |
| Hardware Reset | Power cycle |

---

## 📚 Libraries & Dependencies

### Required Libraries
```json
{
  "dependencies": [
    {"name": "WiFiManager", "version": "^2.0.17"},
    {"name": "PubSubClient", "version": "^2.8"},
    {"name": "ArduinoJson", "version": "^6.21.3"},
    {"name": "UniversalTelegramBot", "version": "^2.1.0"}
  ]
}
```

### PlatformIO Configuration
```ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
monitor_speed = 115200
upload_speed = 921600
build_flags = -DFIRMWARE_VERSION=\"v2.0\"
lib_deps = 
    tzapu/WiFiManager@^2.0.17
    knolleary/PubSubClient@^2.8
    bblanchon/ArduinoJson@^6.21.3
    UniversalTelegramBot/UniversalTelegramBot@^2.1.0
```

### Installation Commands
```bash
# PlatformIO
pio lib install

# Arduino IDE: Use Library Manager
# Install: WiFiManager, PubSubClient, ArduinoJson, UniversalTelegramBot
```

---

## 📝 Changelog

### v2.0 (2026-09-01)
- ✅ Added OTA update support via MQTT
- ✅ Added progress tracking
- ✅ Added Telegram notifications for OTA
- ✅ Improved error handling
- ✅ Added firmware version tracking

### v1.0 (2026-08-01)
- ✅ Initial release
- ✅ MQTT control
- ✅ Telegram bot integration
- ✅ WiFiManager support
- ✅ State persistence

---

## 📁 Project Structure
```
Smart_IoT_R1_V1-OTA/
├── Smart_IoT_R1_V1-OTA.ino    # Main application
├── bot.h                       # Telegram bot
├── platformio.ini              # Build config
└── README.md                   # Documentation
```

---

## 🤝 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open Pull Request

---

## 📄 License

This project is licensed under the MIT License - see LICENSE file for details.

---

## 👤 Author

**Your Name**
- GitHub: [@yourusername](https://github.com/yourusername)
- Telegram: [@yourtelegram](https://t.me/yourtelegram)

---

## 🙏 Acknowledgments

- [EMQX](https://www.emqx.com/) for MQTT broker
- [Telegram](https://telegram.org/) for bot platform
- [Espressif](https://www.espressif.com/) for ESP32-S3
- All open-source library contributors

---

## 📊 Quick Reference

### Commands Cheat Sheet
```bash
# ON/OFF
ON/OFF → /IoT/EC-IoT-MNSEC0001/relay1

# Check-In/Out
CHECK-IN/CHECK-OUT → /IoT/EC-IoT-MNSEC0001/room

# OTA Update
{"ota_update":{"url":"URL","version":"v2.0"}} → /ota/update

# Status
/status → Telegram Bot
```

### First Time Setup
1. Upload firmware
2. Connect to **"Smart CB"** (password: **"password"**)
3. Enter WiFi credentials
4. Control via MQTT or Telegram

---

**Made with ❤️ for IoT**
```
