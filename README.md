# Firmware_Smart-IoT-Device
# IoT Room Controller - Complete Single File

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

- [✨ Features](#-features)
- [🔧 Hardware Requirements](#-hardware-requirements)
- [📦 Software Requirements](#-software-requirements)
- [🚀 Quick Start](#-quick-start)
- [🏗️ Architecture](#️-architecture)
- [🎮 Control Methods](#-control-methods)
  - [1. MQTT Control](#1-mqtt-control)
  - [2. Telegram Bot Control](#2-telegram-bot-control)
  - [3. Hardware Control](#3-hardware-control)
  - [4. WiFiManager Setup](#4-wifimanager-setup)
- [🔄 OTA Updates](#-ota-updates)
- [📡 MQTT Topics Reference](#-mqtt-topics-reference)
- [🔌 Hardware Wiring](#-hardware-wiring)
- [⚙️ Configuration](#️-configuration)
- [🔍 Troubleshooting](#-troubleshooting)
- [📚 Libraries & Dependencies](#-libraries--dependencies)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)
- [👤 Author](#-author)

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| 🎯 **WiFiManager** | Easy WiFi setup with captive portal - no hardcoded credentials |
| 📨 **MQTT Control** | Full control via MQTT broker (EMQX) |
| 🤖 **Telegram Bot** | Remote control and real-time status monitoring |
| 🔄 **OTA Updates** | Over-the-air firmware updates triggered via MQTT with progress tracking |
| 💾 **State Persistence** | Saves relay state in flash memory (Preferences) |
| 🏨 **Check-In/Out** | Room management with automatic Telegram notifications |
| 🔄 **Reset Button** | Reset WiFi settings by holding BOOT button 5 seconds |
| 📊 **Real-time Status** | MQTT status updates, LED indicator, and signal monitoring |
| ⏰ **Cambodia Time** | Local timezone support (UTC+7) for timestamps |
| 📦 **Version Tracking** | Firmware version reporting via MQTT and Telegram |

---

## 🔧 Hardware Requirements

### Components List

| Component | Specification | Purpose |
|-----------|---------------|---------|
| Microcontroller | ESP32-S3 (DevKitC-1) | Main controller |
| Relay Module | 5V Relay (GPIO9) | Room power control |
| LED | Any 3.3V LED (GPIO2) | WiFi status indicator |
| Button | Tactile switch (GPIO0) | WiFi reset (BOOT button) |
| Power Supply | USB-C 5V/2A | Device power |

### Pin Mapping

```
┌─────────────────────────────────────────────────────────────┐
│                     ESP32-S3 Pinout                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐    │
│  │   GPIO 9    │    │   GPIO 2    │    │   GPIO 0    │    │
│  │  (Relay)    │    │   (LED)     │    │  (Button)   │    │
│  └──────┬──────┘    └──────┬──────┘    └──────┬──────┘    │
│         │                  │                  │            │
│  ┌──────▼──────────────────▼──────────────────▼──────┐    │
│  │                 ESP32-S3 Chip                     │    │
│  └──────┬──────────────────┬──────────────────┬──────┘    │
│         │                  │                  │            │
│  ┌──────▼──────┐    ┌──────▼──────┐    ┌──────▼──────┐    │
│  │  3.3V Power │    │    GND      │    │   USB-C     │    │
│  └─────────────┘    └─────────────┘    └─────────────┘    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 📦 Software Requirements

### Development Environment

| Tool | Version | Purpose |
|------|---------|---------|
| PlatformIO | Latest | Recommended development |
| Arduino IDE | 2.x+ | Alternative development |
| ESP32 Core | 3.3.10+ | ESP32-S3 support |
| Python | 3.x | Build tools |

### Required Libraries

```json
{
  "dependencies": [
    {
      "name": "WiFiManager",
      "version": "^2.0.17",
      "author": "tzapu"
    },
    {
      "name": "PubSubClient",
      "version": "^2.8",
      "author": "knolleary"
    },
    {
      "name": "ArduinoJson",
      "version": "^6.21.3",
      "author": "bblanchon"
    },
    {
      "name": "UniversalTelegramBot",
      "version": "^2.1.0",
      "author": "witnessmenow"
    }
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
build_flags = 
    -DFIRMWARE_VERSION=\"v2.0\"
lib_deps = 
    tzapu/WiFiManager@^2.0.17
    knolleary/PubSubClient@^2.8
    bblanchon/ArduinoJson@^6.21.3
    UniversalTelegramBot/UniversalTelegramBot@^2.1.0
```

---

## 🚀 Quick Start

### Step 1: Clone & Install

```bash
# Clone repository
git clone https://github.com/yourusername/iot-room-controller.git
cd iot-room-controller

# Install dependencies (PlatformIO)
pio lib install

# Or use Arduino IDE: Install libraries via Library Manager
```

### Step 2: Configure

```cpp
// Edit bot.h to set your Telegram credentials
#define BOT_TOKEN "YOUR_BOT_TOKEN_HERE"
#define CHAT_ID   "YOUR_CHAT_ID_HERE"
```

### Step 3: Upload Firmware

```bash
# PlatformIO
pio run -t upload

# Arduino IDE: Select board ESP32S3 Dev Module, click Upload
```

### Step 4: First Time WiFi Setup

```
┌─────────────────────────────────────────────────────────────┐
│              First Time WiFi Setup Flow                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. Device creates AP: "Smart CB" (password: "password")   │
│                        ↓                                   │
│  2. Connect to "Smart CB" from your phone/PC               │
│                        ↓                                   │
│  3. Captive portal opens automatically                     │
│                        ↓                                   │
│  4. Enter your WiFi SSID and Password                      │
│                        ↓                                   │
│  5. Device connects and saves credentials                  │
│                        ↓                                   │
│  6. Device connects to MQTT broker                         │
│                        ↓                                   │
│  7. System ready! Monitor Serial for confirmation          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🏗️ Architecture

### System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         IoT Room Controller                        │
│                           ESP32-S3                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────┐      ┌──────────┐      ┌──────────┐                │
│  │  Relay   │      │   LED    │      │  Button  │                │
│  │  (GPIO9) │      │  (GPIO2) │      │  (GPIO0) │                │
│  └────┬─────┘      └────┬─────┘      └────┬─────┘                │
│       │                 │                 │                       │
│  ┌────▼─────────────────▼─────────────────▼─────┐                │
│  │              Main Controller                 │                │
│  │          (setup() / loop())                  │                │
│  └────┬─────────────────┬─────────────────┬─────┘                │
│       │                 │                 │                       │
│  ┌────▼─────┐      ┌────▼─────┐      ┌────▼─────┐               │
│  │  WiFi    │      │   MQTT   │      │ Telegram │               │
│  │ Manager  │      │  Client  │      │   Bot    │               │
│  └──────────┘      └──────────┘      └──────────┘               │
│       │                 │                 │                       │
│  ┌────▼─────────────────▼─────────────────▼─────┐                │
│  │              External Services               │                │
│  ├───────────────────────────────────────────────┤                │
│  │  WiFi AP  │  EMQX Broker  │ Telegram API  │                │
│  └───────────────────────────────────────────────┘                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Code Structure

```
Smart_IoT_R1_V1-OTA/
├── Smart_IoT_R1_V1-OTA.ino    # Main application
├── bot.h                       # Telegram bot implementation
└── platformio.ini              # Build configuration
```

### Data Flow

```
┌──────────────────────────────────────────────────────────────────────┐
│                           Data Flow Diagram                         │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  User Input                                                         │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐                        │
│  │  MQTT   │    │Telegram │    │ Button  │                        │
│  │ Publish │    │ Command │    │  Press  │                        │
│  └────┬────┘    └────┬────┘    └────┬────┘                        │
│       │              │              │                              │
│       ▼              ▼              ▼                              │
│  ┌─────────────────────────────────────────────────┐              │
│  │           ESP32-S3 Processing                   │              │
│  │  • Parse Command                               │              │
│  │  • Check Authorization                         │              │
│  │  • Execute Action                              │              │
│  │  • Update State                                │              │
│  └─────────────────┬───────────────────────────────┘              │
│                    │                                               │
│                    ▼                                               │
│  Output                                                           │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐                      │
│  │  Relay  │    │  LED    │    │  MQTT   │                      │
│  │Control  │    │Update   │    │ Publish │                      │
│  └─────────┘    └─────────┘    └─────────┘                      │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 🎮 Control Methods

### 1. MQTT Control

#### Control Topics

| Action | Topic | Payload | Description |
|--------|-------|---------|-------------|
| Turn Room ON | `/IoT/EC-IoT-MNSEC0001/relay1` | `ON` | Turns relay ON |
| Turn Room OFF | `/IoT/EC-IoT-MNSEC0001/relay1` | `OFF` | Turns relay OFF |
| Get Status | `/IoT/EC-IoT-MNSEC0001/relay1/get` | *(any)* | Returns current status |
| Check-In | `/IoT/EC-IoT-MNSEC0001/room` | `CHECK-IN` | ON + Telegram notification |
| Check-Out | `/IoT/EC-IoT-MNSEC0001/room` | `CHECK-OUT` | OFF + Telegram notification |
| OTA Update | `/IoT/EC-IoT-MNSEC0001/ota/update` | JSON | Start firmware update |
| Get OTA Status | `/IoT/EC-IoT-MNSEC0001/ota/get` | *(any)* | Get OTA status |

#### Example MQTT Commands

```bash
# Using mosquitto_pub (install via: sudo apt-get install mosquitto-clients)

# Turn room ON
mosquitto_pub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/relay1" \
  -m "ON"

# Turn room OFF
mosquitto_pub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/relay1" \
  -m "OFF"

# Check-In (ON + Telegram notification)
mosquitto_pub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/room" \
  -m "CHECK-IN"

# Check-Out (OFF + Telegram notification)
mosquitto_pub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/room" \
  -m "CHECK-OUT"

# Get current status
mosquitto_pub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/relay1/get" \
  -m ""

# Subscribe to status updates
mosquitto_sub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/relay1/status"

# Subscribe to all messages
mosquitto_sub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/#"
```

#### MQTT Response Format

```json
// Status response (on /relay1/status)
{
  "state": "ON",
  "device": "EC-IoT-MNSEC0001",
  "timestamp": "2026-09-01 14:30:25"
}
```

---

### 2. Telegram Bot Control

#### Available Commands

| Command | Description | Example Response |
|---------|-------------|------------------|
| `/status` | Get full device status | Shows relay state, WiFi, signal, firmware version |

#### Example Telegram Interaction

```text
User: /status
Bot:  🏠 *SMART IoT*
      📟 *EC-IoT-MNSEC0001*
      
      🔌 Room Status: ON
      📡 WiFi: Online
      📶 Signal: -45 dBm
      📦 FW: v2.0
      🕐 2026-09-01 14:30:25
```

#### Telegram Bot Setup

1. **Create Bot**: Open Telegram, search @BotFather
2. **Commands**: Send `/newbot` and follow prompts
3. **Get Token**: BotFather provides the token
4. **Get Chat ID**: Use @userinfobot or send message and check updates
5. **Update Code**:

```cpp
// bot.h
#define BOT_TOKEN "1234567890:ABCdefGHIjklMNOpqrsTUVwxyz"
#define CHAT_ID   "123456789"
```

---

### 3. Hardware Control

#### Reset WiFi Credentials

```
┌─────────────────────────────────────────────────────────────┐
│            WiFi Reset Procedure                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  1. Press and HOLD BOOT button (GPIO0)                    │
│                        ↓                                   │
│  2. Keep holding for 5 seconds                            │
│                        ↓                                   │
│  3. LED blinks 5 times (confirmation)                     │
│                        ↓                                   │
│  4. WiFi credentials cleared                              │
│                        ↓                                   │
│  5. Device restarts automatically                         │
│                        ↓                                   │
│  6. Device enters AP mode: "Smart CB"                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### 4. WiFiManager Setup

#### First Time Setup Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    WiFiManager Setup                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Step 1: Device boots                                      │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ No saved credentials found                          │   │
│  │ Creates AP: "Smart CB" (password: "password")      │   │
│  └─────────────────────────────────────────────────────┘   │
│                        ↓                                   │
│  Step 2: User connects                                    │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Connect to "Smart CB" from phone/PC                │   │
│  │ Captive portal opens automatically                  │   │
│  └─────────────────────────────────────────────────────┘   │
│                        ↓                                   │
│  Step 3: Enter credentials                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Select your WiFi network                            │   │
│  │ Enter password                                      │   │
│  │ Click "Save"                                        │   │
│  └─────────────────────────────────────────────────────┘   │
│                        ↓                                   │
│  Step 4: Connection                                      │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Device attempts connection                          │   │
│  │ Saves credentials to flash                         │   │
│  │ Connects to WiFi & MQTT                            │   │
│  │ System ready!                                       │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

#### WiFiManager Customization

```cpp
// Custom AP settings
wm.setTitle("UDAYA Technology");
wm.setConnectRetries(5);
wm.setEnableConfigPortal(!wm.getWiFiIsSaved());

// Custom AP name and password
wm.autoConnect("Smart CB", "password");
```

---

## 🔄 OTA Updates

### Overview

OTA (Over-The-Air) updates allow you to update firmware without physical access to the device.

### OTA Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                         OTA Update Flow                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────────────────────────────────────────────┐      │
│  │  Step 1: Trigger Update                                 │      │
│  │  Publish JSON to: /IoT/EC-IoT-MNSEC0001/ota/update     │      │
│  └─────────────────────────────────────────────────────────┘      │
│                              ↓                                    │
│  ┌─────────────────────────────────────────────────────────┐      │
│  │  Step 2: Device Receives                                │      │
│  │  • Parses JSON command                                 │      │
│  │  • Validates parameters                                │      │
│  │  • Checks WiFi connectivity                            │      │
│  └─────────────────────────────────────────────────────────┘      │
│                              ↓                                    │
│  ┌─────────────────────────────────────────────────────────┐      │
│  │  Step 3: Download Firmware                             │      │
│  │  • HTTP GET request to firmware URL                   │      │
│  │  • Streams data to flash                               │      │
│  │  • Publishes progress (every 5%)                      │      │
│  └─────────────────────────────────────────────────────────┘      │
│                              ↓                                    │
│  ┌─────────────────────────────────────────────────────────┐      │
│  │  Step 4: Verify & Flash                                │      │
│  │  • Checks firmware integrity                           │      │
│  │  • Writes to flash memory                              │      │
│  │  • Verifies write success                              │      │
│  └─────────────────────────────────────────────────────────┘      │
│                              ↓                                    │
│  ┌─────────────────────────────────────────────────────────┐      │
│  │  Step 5: Reboot                                       │      │
│  │  • Publishes success status                           │      │
│  │  • Sends Telegram notification                        │      │
│  │  • Delays 1s for message delivery                     │      │
│  │  • ESP32 restarts                                     │      │
│  └─────────────────────────────────────────────────────────┘      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### OTA Topics

| Topic | Direction | Type | Description |
|-------|-----------|------|-------------|
| `/IoT/EC-IoT-MNSEC0001/ota/update` | Publish | Command | Start OTA update |
| `/IoT/EC-IoT-MNSEC0001/ota/status` | Subscribe | Response | OTA status (retained) |
| `/IoT/EC-IoT-MNSEC0001/ota/progress` | Subscribe | Response | Progress updates (0-100%) |
| `/IoT/EC-IoT-MNSEC0001/ota/get` | Publish | Command | Request current status |

### OTA Command Format

**Start Update:**
```json
{
  "ota_update": {
    "url": "https://raw.githubusercontent.com/CHHUNLONGKH/Firmware_GSM/main/GSM_4G_V2_R4.ino.bin",
    "version": "v2.0"
  }
}
```

**OTA Status Response:**
```json
{
  "device": "EC-IoT-MNSEC0001",
  "timestamp": "2026-09-01 14:30:25",
  "status": "OTA started for version v2.0",
  "success": true,
  "version": "v2.0"
}
```

**OTA Progress Update:**
```json
{
  "device": "EC-IoT-MNSEC0001",
  "progress": 45,
  "timestamp": "2026-09-01 14:30:45"
}
```

### OTA Example Commands

```bash
# Start OTA Update
mosquitto_pub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/ota/update" \
  -m '{"ota_update":{"url":"https://raw.githubusercontent.com/CHHUNLONGKH/Firmware_GSM/main/GSM_4G_V2_R4.ino.bin","version":"v2.0"}}'

# Monitor OTA Progress
mosquitto_sub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/ota/progress"

# Subscribe to all OTA topics
mosquitto_sub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/ota/#"

# Get OTA Status
mosquitto_pub -h broker.emqx.io \
  -t "/IoT/EC-IoT-MNSEC0001/ota/get" \
  -m ""
```

### OTA Security Features

| Feature | Description |
|---------|-------------|
| ⏱️ Timeout Protection | 2-minute timeout prevents hanging |
| 🚫 Concurrent Protection | Prevents multiple simultaneous OTA attempts |
| 📊 Progress Tracking | Real-time updates every 5% |
| 📝 Error Reporting | Detailed failure messages with error codes |
| 📱 Telegram Notifications | Success/failure alerts via Telegram |
| ✅ Integrity Check | Verifies firmware before flashing |

---

## 📡 MQTT Topics Reference

### Complete Topic List

| Topic | Type | Retained | Description |
|-------|------|----------|-------------|
| `/IoT/EC-IoT-MNSEC0001/relay1` | Control | No | Set relay ON/OFF |
| `/IoT/EC-IoT-MNSEC0001/relay1/status` | Status | Yes | Current relay state |
| `/IoT/EC-IoT-MNSEC0001/relay1/get` | Control | No | Request status |
| `/IoT/EC-IoT-MNSEC0001/room` | Control | No | CHECK-IN / CHECK-OUT |
| `/IoT/EC-IoT-MNSEC0001/ota/update` | Control | No | Start OTA update |
| `/IoT/EC-IoT-MNSEC0001/ota/status` | Status | Yes | OTA status |
| `/IoT/EC-IoT-MNSEC0001/ota/progress` | Status | No | OTA progress updates |
| `/IoT/EC-IoT-MNSEC0001/ota/get` | Control | No | Request OTA status |

### MQTT Configuration

```cpp
const char* MQTT_BROKER = "broker.emqx.io";  // Public EMQX broker
const int   MQTT_PORT   = 1883;              // Default MQTT port
const char* MQTT_USER   = "chhunlong";       // Your username
const char* MQTT_PASS   = "1234";            // Your password
```

### Payload Examples

| Topic | Payload | Description |
|-------|---------|-------------|
| `/relay1` | `ON` | Turn ON |
| `/relay1` | `OFF` | Turn OFF |
| `/relay1/status` | `ON` | Current state (retained) |
| `/room` | `CHECK-IN` | Check-in command |
| `/room` | `CHECK-OUT` | Check-out command |
| `/ota/update` | `{"ota_update":{...}}` | OTA command |

---

## 🔌 Hardware Wiring

### Detailed Wiring Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                      Hardware Wiring Diagram                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ESP32-S3              Relay Module          LED           Button  │
│  ┌─────────┐         ┌────────────┐      ┌──────┐      ┌────────┐ │
│  │         │         │            │      │      │      │        │ │
│  │  GPIO9  ├─────────► IN         │      │      │      │        │ │
│  │         │         │   Relay    │      │      │      │        │ │
│  │         │         │   Module   │      │      │      │        │ │
│  │  GPIO2  ├───────────────────────────►  │  LED │      │        │ │
│  │         │         │            │      │      │      │        │ │
│  │  GPIO0  ├───────────────────────────────────────────► Button │ │
│  │         │         │            │      │      │      │        │ │
│  │  3.3V   ├─────────► VCC        ├──────► VCC  ├──────► VCC    │ │
│  │         │         │            │      │      │      │        │ │
│  │  GND    ├─────────► GND        ├──────► GND  ├──────► GND    │ │
│  │         │         │            │      │      │      │        │ │
│  └─────────┘         └────────────┘      └──────┘      └────────┘ │
│                                                                     │
│  Note:                                                                 │
│  • GPIO0 has internal pull-up, connect button to GND                 │
│  • LED needs current limiting resistor (220Ω-330Ω)                   │
│  • Relay module may need external 5V if not powered by ESP32         │
│  • BOOT button is GPIO0 on most ESP32-S3 boards                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Component Specifications

| Component | Specification | Notes |
|-----------|---------------|-------|
| Relay Module | 5V DC, 10A 250VAC | Opto-isolated recommended |
| LED | 3.3V, 20mA | Use 220Ω resistor |
| Button | Tactile switch | Normally open |
| Power Supply | 5V 2A USB-C | Ensure adequate current |

### Connection Checklist

- [ ] GPIO9 → Relay IN
- [ ] GPIO2 → LED Anode (with resistor)
- [ ] GPIO0 → Button (to GND)
- [ ] 3.3V → Relay VCC, LED VCC, Button VCC
- [ ] GND → All components ground

---

## ⚙️ Configuration

### WiFi Configuration

| Parameter | Value | Description |
|-----------|-------|-------------|
| AP Name | `Smart CB` | Access Point SSID |
| AP Password | `password` | Access Point password |
| WiFi Mode | STA | Station mode |
| Auto Reconnect | Enabled | Auto-reconnect on disconnect |
| Connection Timeout | 20s | Max connection attempt time |

### MQTT Configuration

| Parameter | Value | Description |
|-----------|-------|-------------|
| Broker | `broker.emqx.io` | MQTT broker address |
| Port | `1883` | MQTT port (non-SSL) |
| Username | `chhunlong` | MQTT username |
| Password | `1234` | MQTT password |
| Keep Alive | 30s | MQTT keep-alive interval |
| Socket Timeout | 2s | Socket timeout |

### Telegram Configuration

| Parameter | Value | Description |
|-----------|-------|-------------|
| Bot Token | `YOUR_BOT_TOKEN` | Telegram bot token |
| Chat ID | `YOUR_CHAT_ID` | Target chat ID |
| Send Interval | 3s | Rate limiting interval |

### OTA Configuration

| Parameter | Value | Description |
|-----------|-------|-------------|
| Timeout | 120s | OTA update timeout |
| Progress Report | 5% | Progress update interval |
| HTTP Timeout | 30s | HTTP download timeout |
| Max Firmware Size | Flash size | Depends on partition |

---

## 🔍 Troubleshooting

### Common Issues & Solutions

#### 1. WiFi Connection Failed

| Symptom | Cause | Solution |
|---------|-------|----------|
| Device in AP mode constantly | Wrong credentials | Hold BOOT 5s → Reconfigure |
| No WiFi LED blinking | WiFi disconnected | Check network availability |
| Slow connection | Weak signal | Move closer to router |

**Code Debug:**
```cpp
Serial.print("WiFi Status: ");
Serial.println(WiFi.status());
Serial.print("IP: ");
Serial.println(WiFi.localIP());
Serial.print("RSSI: ");
Serial.println(WiFi.RSSI());
```

#### 2. MQTT Connection Failed

| Error Code | Meaning | Solution |
|------------|---------|----------|
| -1 | Network failure | Check WiFi |
| -2 | Connection timeout | Check broker address |
| -3 | Connection lost | Network issue |
| -4 | Malformed packet | Restart device |
| -5 | Protocol mismatch | Check MQTT version |
| -6 | Server not found | Verify broker IP |
| -7 | Connection refused | Check credentials |

#### 3. OTA Update Fails

| Issue | Cause | Solution |
|-------|-------|----------|
| "HTTP GET failed" | URL not accessible | Verify URL is correct and accessible |
| "OTA begin failed" | Insufficient flash | Check partition size |
| "Incomplete data" | Network interruption | Retry with better connection |
| "Timeout" | Slow download | Increase timeout or use faster network |

#### 4. Telegram Bot Not Responding

| Symptom | Cause | Solution |
|---------|-------|----------|
| No response | Wrong token | Verify BOT_TOKEN |
| "Chat not found" | Wrong chat ID | Get correct chat ID |
| Rate limited | Too many messages | Wait 3s between messages |
| No internet | WiFi down | Check WiFi status |

#### 5. Compilation Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `getCambodiaTime` redefinition | Duplicate function | Remove from main .ino |
| `BOT_H` not found | Missing header | Verify bot.h exists |
| Library not found | Missing dependency | Install required library |
| `DEVICE_ID` undefined | Missing define | Add DEVICE_ID definition |

### LED Status Indicators

| Pattern | Meaning | Action |
|---------|---------|--------|
| OFF | WiFi disconnected | Check WiFi |
| Slow Blink (1s) | WiFi connected | Normal operation |
| Fast Blink | OTA in progress | Wait for completion |
| 5 Blinks | WiFi reset | Reconfigure WiFi |
| Solid ON | System error | Restart device |

### Debug Output Examples

**Normal Startup:**
```
==============================
 EC IoT ROOM CONTROLLER v2.0
==============================
Relay state: OFF
Starting WiFiManager...
WiFi credentials found. Attempting to connect...
WiFi connected successfully!
IP: 192.168.1.100
RSSI: -45 dBm
MQTT connecting...CONNECTED
MQTT subscribed.
Telegram initialized.
System ready.
```

**OTA Update:**
```
OTA Update requested:
  URL: https://example.com/firmware.bin
  Version: v2.0
Starting OTA update from URL: https://example.com/firmware.bin
Firmware size: 524288 bytes
Starting OTA write...
OTA Progress: 5%
OTA Progress: 10%
...
OTA Progress: 100%
OTA write completed: 524288 bytes
OTA done! Rebooting for version v2.0
```

### Reset Procedures

| Procedure | Method | Result |
|-----------|--------|--------|
| WiFi Reset | Hold BOOT 5s | Clears credentials |
| Soft Reset | `ESP.restart()` | Reboot device |
| Hard Reset | Power cycle | Full restart |
| Factory Reset | Re-flash firmware | Fresh install |

---

## 📚 Libraries & Dependencies

### Core Libraries

| Library | Version | Purpose | License |
|---------|---------|---------|---------|
| WiFiManager | ^2.0.17 | WiFi configuration | MIT |
| PubSubClient | ^2.8 | MQTT communication | MIT |
| ArduinoJson | ^6.21.3 | JSON parsing | MIT |
| UniversalTelegramBot | ^2.1.0 | Telegram bot | MIT |

### Installation Commands

**PlatformIO:**
```bash
pio lib install tzapu/WiFiManager
pio lib install knolleary/PubSubClient
pio lib install bblanchon/ArduinoJson
pio lib install UniversalTelegramBot/UniversalTelegramBot
```

**Arduino IDE:**
```arduino
// Library Manager:
WiFiManager by tzapu
PubSubClient by Nick O'Leary
ArduinoJson by Benoit Blanchon
UniversalTelegramBot by Brian Lough
```

**Manual Installation:**
```bash
git clone https://github.com/tzapu/WiFiManager.git
git clone https://github.com/knolleary/pubsubclient.git
git clone https://github.com/bblanchon/ArduinoJson.git
git clone https://github.com/witnessmenow/Universal-Arduino-Telegram-Bot.git
```

---

## 🤝 Contributing

We welcome contributions! Please follow these steps:

### How to Contribute

1. **Fork the repository**
   ```bash
   git clone https://github.com/yourusername/iot-room-controller.git
   cd iot-room-controller
   ```

2. **Create a feature branch**
   ```bash
   git checkout -b feature/AmazingFeature
   ```

3. **Make your changes**
