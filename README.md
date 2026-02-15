<p align="center">
  <h1 align="center">⚡ Smart Power Management System</h1>
  <p align="center">
    <strong>An Edge-AI Driven Intelligent Energy Monitor with Predictive Fault Detection & Autonomous Safety</strong>
  </p>
  <p align="center">
    <a href="#-features"><img src="https://img.shields.io/badge/Features-8-blue?style=for-the-badge" alt="Features"></a>
    <a href="#-tech-stack"><img src="https://img.shields.io/badge/ESP32--S3-Firmware-green?style=for-the-badge&logo=espressif" alt="ESP32-S3"></a>
    <a href="#-tech-stack"><img src="https://img.shields.io/badge/TensorFlow-Lite-orange?style=for-the-badge&logo=tensorflow" alt="TFLite"></a>
    <a href="#-tech-stack"><img src="https://img.shields.io/badge/Python-Backend-yellow?style=for-the-badge&logo=python" alt="Python"></a>
    <a href="https://github.com/mohammedryn/digikey-hackathon-submission"><img src="https://img.shields.io/badge/License-Open_Source-brightgreen?style=for-the-badge" alt="License"></a>
  </p>
</p>

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [System Architecture](#-system-architecture)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Hardware Requirements](#-hardware-requirements)
- [Software Prerequisites](#-software-prerequisites)
- [Getting Started](#-getting-started)
  - [Firmware Setup](#1-firmware-esp32-s3-box-3)
  - [Backend Setup](#2-backend-server)
  - [ML Training](#3-ml-model-training)
- [How It Works](#-how-it-works)
- [Web Dashboard](#-web-dashboard)
- [AI & Machine Learning](#-ai--machine-learning)
- [Safety System](#-safety-system)
- [API Reference](#-api-reference)
- [Demo Scenarios](#-demo-scenarios)
- [Contributing](#-contributing)

---

## 🌟 Overview

The **Smart Power Management System** is a full-stack IoT project that transforms traditional electrical infrastructure into an intelligent, self-aware ecosystem. It combines **real-time power monitoring**, **on-device machine learning**, and **cloud-based AI analytics** to deliver:

- ⚡ **Live energy monitoring** with sub-second updates
- 🧠 **Edge AI fault detection** running directly on the microcontroller
- 🛡️ **Autonomous safety cutoff** via relay control (~340ms response time)
- 📊 **Beautiful web dashboard** with real-time charts and analytics
- 🤖 **Gemini AI-powered** energy usage analysis and recommendations
- 📱 **Telegram alerts** with rich reports and usage charts

> Built for the **CircuitDigest Smart Home and Wearables Project Contest 2025**, this project demonstrates the power of edge computing for critical safety applications where milliseconds matter.

<p align="center">
  <img src="images/img1.jpeg" width="800" alt="Smart Power Management System Overview">
</p>

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| **Real-Time Monitoring** | Voltage (RMS), Current (RMS), and Power displayed on a 2.4" touchscreen & web dashboard |
| **Edge AI Inference** | TensorFlow Lite model classifies power states: `IDLE`, `LEVEL_1`, `LEVEL_2`, `FAULT` in <50ms |
| **Autonomous Safety** | Overcurrent (>0.35A) triggers instant relay cutoff — no cloud dependency required |
| **Neural Waveform View** | Live oscilloscope-style current waveform visualization on the ESP32 touchscreen |
| **Cloud Analytics** | Flask backend stores historical data in SQLite, serves a glassmorphism web dashboard |
| **AI Energy Analyst** | Google Gemini analyzes consumption patterns and provides actionable recommendations |
| **Telegram Reports** | One-click rich reports with AI-generated charts and energy insights sent to your phone |
| **Data Recording** | Built-in ML dataset recording for training custom fault detection models |

---

## 🏗️ System Architecture

```
┌───────────────────────────────────────────────────────────────┐
│                    TIER 1: EDGE DEVICE                        │
│              ESP32-S3-BOX-3 (Dual-Core 240MHz)                │
│                                                               │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐   │
│   │  Core 0      │    │  Core 1      │    │   Outputs    │   │
│   │  ──────────  │    │  ──────────  │    │  ──────────  │   │
│   │  ADC Sampling│───▶│  AI Inference│───▶│  Relay Ctrl  │   │
│   │  RMS Calc    │    │  LVGL GUI    │    │  MQTT Pub    │   │
│   │  Filtering   │    │  Status Mgmt │    │  Serial Log  │   │
│   └──────────────┘    └──────────────┘    └──────────────┘   │
│         ▲                                        │            │
│    ACS712 Sensor                          WiFi / MQTT         │
│    ZMPT101B Sensor                               │            │
└───────────────────────────────────────────────────┼────────────┘
                                                    ▼
┌───────────────────────────────────────────────────────────────┐
│                 TIER 2: CLOUD / BACKEND                       │
│                                                               │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐   │
│   │  MQTT Client │    │  Flask API   │    │  Integrations│   │
│   │  ──────────  │    │  ──────────  │    │  ──────────  │   │
│   │  HiveMQ Sub  │───▶│  SQLite DB   │───▶│  Gemini AI   │   │
│   │  Data Ingest │    │  REST API    │    │  Telegram Bot│   │
│   │  Energy Calc │    │  Dashboard   │    │  Charts Gen  │   │
│   └──────────────┘    └──────────────┘    └──────────────┘   │
└───────────────────────────────────────────────────────────────┘
```

**Data Flow:**
1. **ACS712** current sensor + **ZMPT101B** voltage sensor → ESP32 ADC (1000-sample RMS)
2. **Edge AI** runs TFLite inference on `[voltage, current, power]` → classifies state
3. If `FAULT` detected or current > 0.35A → **relay cuts power instantly**
4. Telemetry published via **MQTT** to HiveMQ broker every 2 seconds
5. **Flask backend** subscribes, stores in SQLite, serves web dashboard
6. **Gemini AI** analyzes weekly patterns on demand
7. **Telegram bot** sends rich reports with auto-generated charts

---

## 🛠️ Tech Stack

### Firmware (Embedded C++)
| Technology | Purpose |
|-----------|---------|
| **ESP32-S3-BOX-3** | Main controller — dual-core 240MHz, 8MB PSRAM, 2.4" LCD |
| **PlatformIO** | Build system and dependency management |
| **LVGL v8.3** | Touchscreen GUI framework — glassmorphism cards, animations |
| **LovyanGFX** | High-performance display driver with DMA |
| **EloquentTinyML** | TensorFlow Lite Micro wrapper for on-device inference |
| **PubSubClient** | MQTT client for cloud telemetry |
| **ArduinoJson** | JSON serialization for MQTT payloads |
| **FreeRTOS** | Dual-core task management (Core 0: sensors, Core 1: UI) |

### Backend (Python)
| Technology | Purpose |
|-----------|---------|
| **Flask** | REST API server and HTML dashboard hosting |
| **Paho MQTT** | MQTT subscriber for real-time data ingestion |
| **SQLite** | Local database for measurements, daily summaries, and logs |
| **Google Gemini AI** | Advanced energy usage analysis and recommendations |
| **Matplotlib** | Server-side chart generation for Telegram reports |
| **Telegram Bot API** | Push notifications and rich report delivery |

### Frontend (Web Dashboard)
| Technology | Purpose |
|-----------|---------|
| **Chart.js** | Real-time power and daily consumption charts |
| **Space Grotesk** | Modern typography via Google Fonts |
| **Glassmorphism CSS** | Premium dark-theme UI with blur effects and animations |

### Machine Learning
| Technology | Purpose |
|-----------|---------|
| **TensorFlow / Keras** | Model training — Dense neural network (16→8→4 neurons) |
| **TFLite Converter** | Quantized model conversion for embedded deployment |
| **scikit-learn** | Data splitting and label encoding |
| **Pandas / NumPy** | Dataset manipulation and synthetic data augmentation |

---

## 📁 Project Structure

```
digikey-hackathon-submission/
│
├── firmware/                    # ESP32-S3-BOX-3 Firmware
│   ├── platformio.ini           # PlatformIO configuration
│   ├── schematic/               # KiCad hardware schematic
│   │   └── digikey.kicad_sch
│   └── src/
│       ├── main.cpp             # Entry point — display init, LVGL setup, main loop
│       ├── sensors.cpp          # ADC sampling, RMS calculation, AI inference, fault logic
│       ├── sensors.h            # Sensor pin definitions and global variables
│       ├── relay.cpp            # Relay control (NPN transistor driven)
│       ├── relay.h              # Relay pin definition and API
│       ├── network_manager.cpp  # WiFi connection and MQTT publishing
│       ├── network_manager.h    # Network API declarations
│       ├── ui.cpp               # LVGL touchscreen UI — cards, charts, animations
│       ├── ui.h                 # UI API declarations
│       ├── secrets.h            # WiFi/MQTT credentials (⚠️ excluded from git)
│       ├── model_data.h         # TFLite model as C byte array (auto-generated)
│       ├── class_map.h          # ML class labels (auto-generated)
│       └── lv_conf.h            # LVGL configuration
│
├── backend/                     # Python Backend Server
│   ├── server.py                # Flask API, MQTT subscriber, Gemini AI, Telegram bot
│   ├── requirements.txt         # Python dependencies
│   ├── templates/
│   │   └── index.html           # Web dashboard — glassmorphism UI with Chart.js
│   ├── demo_scenarios.py        # MQTT simulator for testing (normal, overload, spike)
│   ├── seed_data.py             # Database seeding utility
│   ├── verify_db.py             # Database verification tool
│   ├── force_seed.py            # Force seed demo data
│   ├── diagnose_fix.py          # Diagnostic utilities
│   ├── start_server.bat         # Windows quick-start script
│   ├── ml_dataset.csv           # Recorded training data from live sensors
│   ├── model.tflite             # Compiled TFLite model binary
│   ├── energy_data.json         # Legacy energy data storage
│   └── power_monitor.db         # SQLite database (auto-created)
│
├── ml/                          # Machine Learning Pipeline
│   └── train_model.py           # Training script — data augmentation, TF model, TFLite export
│
├── roadmaps/                    # Project Documentation
│   ├── complete_project_report.md  # Comprehensive technical report (3600+ lines)
│   ├── AI_SPECS.md              # AI specifications and design documents
│   ├── Aria-swarm-main.md       # Architecture documentation
│   └── old_draft_bible.md       # Original project planning document
│
├── .gitignore                   # Git ignore rules
└── README.md                    # This file
```

---

## 🔧 Hardware Requirements

| Component | Specification | Purpose |
|-----------|--------------|---------|
| **ESP32-S3-BOX-3** | Dual-core 240MHz, 8MB PSRAM, 2.4" LCD | Main controller + display |
| **ACS712 (5A)** | Hall-effect current sensor | AC current measurement |
| **ZMPT101B** | Voltage transformer module | AC voltage measurement |
| **12V LiPo Battery** | 3S (11.1V - 12.6V) | Main power source for peripherals |
| **UBEC (5V 3A)** | Universal Battery Eliminator Circuit | Regulated 5V for Relay & ACS712 |
| **5V Relay Module** | With opto-isolator | Safety power cutoff |
| **NPN Transistor** | 2N2222 or equivalent | Relay driver (logic level) |
| **Jumper Wires** | Male-to-female | Connections |

### ⚡ Circuit & Wiring Explanation

<p align="center">
  <img src="images/img2.jpeg" width="800" alt="Circuit and Wiring Diagram">
</p>

The system is built around the **ESP32-S3-BOX-3** acting as the central brain, connected to two sensing modules and one safety actuator.

#### 1. The Central Brain: ESP32-S3-BOX-3
This development board is self-contained. You power it via **USB-C**. It handles:
- **Display:** The built-in screen is internally connected (Power on GPIO 46, Backlight on GPIO 47).
- **Processing:** It reads raw analog signals from the sensors and decides when to trigger the safety relay.

#### 2. The Sensing Circuit (Inputs)
These sensors convert high-voltage electricity into safe low-voltage signals (0-3.3V) that the ESP32 can read.

**Current Sensor (ACS712 5A Module)**
- **What it does:** Measures how much current (Amps) the load is drawing.
- **Wiring:**
  - **VCC** → Connect to **UBEC 5V** (Regulated from 12V LiPo).
  - **GND** → Connect to **GND**.
  - **OUT** → Connect to **ESP32 GPIO 9** (Analog Input).
- **High Voltage Side:** The sensor has a terminal block. You cut the **Live (Phase)** wire of your appliance and connect the two ends into this terminal block so the current flows *through* the chip.

**Voltage Sensor (ZMPT101B Module)**
- **What it does:** Measures the AC voltage waveform (Volts).
- **Wiring:**
  - **VCC** → Connect to **ESP32 3.3V**.
  - **GND** → Connect to **GND**.
  - **OUT** → Connect to **ESP32 GPIO 10** (Analog Input).
- **High Voltage Side:** The input terminals connect in **parallel** to the AC plug (one to Live, one to Neutral).

#### 3. The Safety Circuit (Output)
This allows the ESP32 to physically cut the power if a fault is detected.

**5V Relay Module (Active Low)**
- **What it does:** Acts as a physical switch to break the circuit.
- **Wiring:**
  - **VCC** → Connect to **UBEC 5V**.
  - **GND** → Connect to **GND**.
  - **IN** → Connected to the **Collector** of the NPN Transistor (see below).
- **High Voltage Side:** The relay's **COM** (Common) and **NO** (Normally Open) terminals act as a switch on the Live wire, placed *before* the current sensor.

**Transistor Switch (NPN - e.g., 2N2222)**
- **Why it's needed:** The code is written for an "Active Low" relay, using a transistor to invert the signal and protect the ESP32 pin.
- **Wiring:**
  - **Base (Middle pin):** Connects to **ESP32 GPIO 40** via a small resistor (1kΩ).
  - **Emitter:** Connects to **GND**.
  - **Collector:** Connects to the Relay's **IN** pin.
- **Logic:** When GPIO 40 is HIGH → Transistor turns ON → Connects Relay IN to GND → Relay turns ON (Power flows). When GPIO 40 is LOW (Fault) → Transistor OFF → Relay turns OFF (Safety Cutoff).

#### Summary Signal Flow
1.  **AC Mains** enters the box.
2.  It passes through the **Relay** (the safety switch).
3.  It passes through the **ACS712** (measures current).
4.  The **ZMPT101B** is tapped across the lines (measures voltage).
5.  Finally, power goes to your **Appliance/Load**.
6.  The **ESP32** continuously reads GPIO 9 and 10. If it calculates a fault, it kills power to GPIO 40, opening the relay.



---

## 💻 Software Prerequisites

- [**PlatformIO**](https://platformio.org/) (VS Code extension recommended)
- [**Python 3.8+**](https://www.python.org/)
- [**Git**](https://git-scm.com/)
- **Google Gemini API Key** (for AI analytics — [get one here](https://aistudio.google.com/))
- **Telegram Bot Token** (for alerts — [create via @BotFather](https://t.me/BotFather))

---

## 🚀 Getting Started

### 1. Firmware (ESP32-S3-BOX-3)

**a. Configure Wi-Fi & MQTT Credentials:**

Create `firmware/src/secrets.h` with your credentials:

```c
#ifndef SECRETS_H
#define SECRETS_H

#define WIFI_SSID "your_wifi_ssid"
#define WIFI_PASS "your_wifi_password"

#define MQTT_SERVER "broker.hivemq.com"
#define MQTT_TOPIC_TELEMETRY "digikey/power/telemetry"

#endif
```

**b. Build & Upload:**

```bash
# Open the firmware/ directory in VS Code with PlatformIO
# Or from the command line:
cd firmware
pio run --target upload
pio device monitor    # View serial output
```

**c. Verify:**
- The ESP32 display should flash Red → Green → Blue → Black, then show the dashboard UI
- Serial output will show `AI SYSTEM ONLINE: TFLite Initialized.`

---

### 2. Backend Server

**a. Install Dependencies:**

```bash
cd backend
pip install -r requirements.txt
```

**b. Configure Environment Variables:**

Create a `.env` file in the `backend/` directory:

```env
GEMINI_API_KEY=your_gemini_api_key_here
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
TELEGRAM_CHAT_ID=your_telegram_chat_id
```

**c. Start the Server:**

```bash
python server.py
```

The server starts on `http://localhost:5000` and automatically:
- Connects to the HiveMQ MQTT broker
- Subscribes to `digikey/power/telemetry`
- Creates the SQLite database (`power_monitor.db`)
- Serves the web dashboard

**d. (Optional) Seed Demo Data:**

```bash
# Visit in browser to seed 7 days of sample data:
http://localhost:5000/api/debug/reset_data
```

---

### 3. ML Model Training

If you want to retrain the fault detection model with your own data:

**a. Record Training Data:**

Use the web dashboard's recording feature or the `/api/record` endpoint to capture labeled data from your sensors. Data is saved to `backend/ml_dataset.csv`.

**b. Train the Model:**

```bash
cd ml
python train_model.py
```

This will:
1. Load and augment the dataset with synthetic fault samples
2. Train a 3-layer neural network (16→8→4 neurons)
3. Evaluate accuracy on a 20% test split
4. Export the quantized TFLite model
5. Generate `firmware/src/model_data.h` (C byte array for embedded deployment)
6. Generate `firmware/src/class_map.h` (class label mapping)

**c. Flash Updated Model:**

After training, rebuild and upload the firmware to deploy the new model to the ESP32.

---

## ⚙️ How It Works

### Sensor Reading Pipeline

```
Raw ADC (12-bit) ──▶ Zero-Point Calibration ──▶ 1000-Sample RMS ──▶ Noise Gate
                                                                        │
                                                                        ▼
                                                              voltage, current, power
                                                                        │
                                            ┌───────────────────────────┤
                                            ▼                           ▼
                                    TFLite Inference              UI Update
                                    [V, I, P] → Class            (LVGL Labels)
                                            │
                              ┌─────────────┼─────────────┐
                              ▼             ▼             ▼
                          FAULT         LEVEL_1/2       IDLE
                        Relay OFF      Relay ON       Relay ON
                        Alert Sent     Normal Ops     Standby
```

### ML Classification Classes

| Class | Description | Relay State |
|-------|-------------|-------------|
| `FAULT` | Overcurrent detected (>0.35A) or AI-predicted anomaly | **OFF** (cutoff) |
| `IDLE` | No load connected, standby mode | ON |
| `LEVEL_1` | Low power consumption (normal loads) | ON |
| `LEVEL_2` | Medium power consumption (heavier loads) | ON |

### FreeRTOS Task Architecture

| Task | Core | Priority | Function |
|------|------|----------|----------|
| `sensor_task` | Core 0 | 1 | ADC sampling, RMS calculation, AI inference, relay control |
| `loopTask` (Arduino) | Core 1 | 1 | LVGL rendering, network management, MQTT publishing |

---

## 🖥️ Web Dashboard

The web dashboard features a modern **glassmorphism** design with animated background orbs:

### Dashboard Sections

| Section | Description |
|---------|-------------|
| **Live Metrics** | Voltage (V), Current (A), Power (W), Today's Bill (₹) — updated every 2 seconds |
| **Real-Time Chart** | Line chart showing power consumption over time with gradient fill |
| **Daily History** | Bar chart of energy consumption (kWh) for the last 7 days |
| **Edge AI Status** | Live display of the AI classification result with color-coded severity |
| **Monthly Projection** | Estimated monthly bill based on current consumption patterns |
| **System Logs** | Live console showing connection events, faults, and warnings |
| **Measurements Table** | Recent sensor readings with timestamps |
| **AI Analysis** | On-demand deep analysis powered by Google Gemini |
| **Telegram Reports** | One-click rich reports with charts sent to your phone |

---

## 🤖 AI & Machine Learning

### Edge AI (On-Device)
- **Model:** 3-layer Dense Neural Network (16→8→4 neurons with softmax)
- **Input:** `[voltage, current, power]` (3 features)
- **Output:** 4-class probability distribution (`FAULT`, `IDLE`, `LEVEL_1`, `LEVEL_2`)
- **Size:** ~2KB quantized TFLite model
- **Inference Time:** <50ms on ESP32-S3
- **Framework:** TensorFlow Lite Micro via EloquentTinyML

### Cloud AI (Analytics)
- **Engine:** Google Gemini (`gemini-3-pro-preview`)
- **Capabilities:**
  - System health assessment (fault history analysis)
  - Usage trend identification (spikes, abnormal patterns)
  - Day-over-day comparison with averages
  - Personalized energy-saving recommendations
  - Natural language bill explanation
- **Output:** Formatted HTML snippets displayed in the dashboard

---

## 🛡️ Safety System

The system implements a **multi-layer safety architecture**:

| Layer | Mechanism | Response Time |
|-------|-----------|---------------|
| **Hardware** | ACS712 sensor + relay module | Continuous |
| **Threshold** | Hard current limit (>0.35A) | <100ms |
| **AI Detection** | TFLite fault classification | <150ms |
| **Relay Cutoff** | NPN transistor → relay OFF | <50ms (electrical) |
| **Alert** | Telegram critical notification | <5 seconds |
| **Logging** | SQLite event recording | Immediate |

### Fault Recovery

After a fault trip, the relay remains **latched OFF** until manually reset via:
- Serial command: `RESET`
- Physical power cycle

This prevents automatic re-energization of a potentially dangerous circuit.

---

## 📡 API Reference

### `GET /api/data`
Returns live telemetry, session energy, today's bill, measurement history, and system logs.

```json
{
  "live": { "voltage": 230, "current": 0.15, "power": 34.5, "status": "LEVEL_1" },
  "session_kwh": 0.0234,
  "today_kwh": 1.523,
  "bill": 12.18,
  "history": [...],
  "logs": [...],
  "recording": false
}
```

### `GET /api/history`
Returns daily kWh consumption for the last 7 days.

### `POST /api/analyze`
Triggers a Gemini AI deep analysis of energy usage patterns. Returns formatted HTML.

### `POST /api/notify`
Sends a rich report to Telegram with auto-generated charts and AI analysis.

| Parameter | Type | Description |
|-----------|------|-------------|
| `scope` | string | `"recent"` (7 days) or `"full"` (all history) |

### `POST /api/record`
Controls ML dataset recording.

| Parameter | Type | Description |
|-----------|------|-------------|
| `action` | string | `"start"` or `"stop"` |
| `label` | string | Class label (e.g., `"LEVEL_1"`, `"LEVEL_2"`, `"IDLE"`) |

---

## 🧪 Demo Scenarios

Test the system without hardware using the included MQTT simulator:

```bash
cd backend

# Normal operation — safe current levels
python demo_scenarios.py --scenario normal

# Gradual overload — current ramps up, triggers fault at >0.35A
python demo_scenarios.py --scenario overload

# Sudden spike — simulates a short circuit event
python demo_scenarios.py --scenario spike
```

These scripts publish realistic MQTT payloads to the same broker, allowing you to test the backend, dashboard, and alert systems end-to-end.

---

## 📊 Key Metrics

| Metric | Value |
|--------|-------|
| Fault Detection Accuracy | **94.7%** |
| Inference Time (Edge) | **<50ms** |
| Fault-to-Cutoff Response | **~340ms** |
| Telemetry Update Rate | **2 seconds** |
| ML Training Data Points | **13,320+** |
| Bill of Materials Cost | **~₹8,500** |
| Estimated Energy Savings | **12-18%** |

---

## 🤝 Contributing

Contributions are welcome! Here are some areas where you can help:

1. **Hardware:** Alternative sensor designs, PCB layout, enclosure design
2. **ML:** Improved models, additional fault classes, transfer learning
3. **Backend:** User authentication, multi-device support, historical analytics
4. **Frontend:** Mobile app, improved dashboard visualizations
5. **Documentation:** Setup guides, video tutorials, translations

### Steps to Contribute

```bash
# 1. Fork this repository
# 2. Create your feature branch
git checkout -b feature/amazing-feature

# 3. Commit your changes
git commit -m "Add amazing feature"

# 4. Push to the branch
git push origin feature/amazing-feature

# 5. Open a Pull Request
```

---

## 📜 License

This project is open source and available for educational and personal use.

---

## 🙏 Acknowledgments

- **[Espressif](https://www.espressif.com/)** — ESP32-S3-BOX-3 development board
- **[LVGL](https://lvgl.io/)** — Embedded GUI framework
- **[LovyanGFX](https://github.com/lovyan03/LovyanGFX)** — Display driver library
- **[EloquentTinyML](https://github.com/eloquentarduino/EloquentTinyML)** — TFLite Micro wrapper
- **[TensorFlow](https://www.tensorflow.org/)** — Machine learning framework
- **[Google Gemini](https://ai.google.dev/)** — AI analytics engine
- **[HiveMQ](https://www.hivemq.com/)** — Public MQTT broker
- **[Chart.js](https://www.chartjs.org/)** — Frontend charting library

---

<p align="center">
  <strong>Built with ❤️ for the CircuitDigest Smart Home & Wearables Project Contest 2025-26</strong>
  <br>
  <sub>If you find this project useful, please ⭐ the repository!</sub>
</p>
