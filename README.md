# 🐘 ElephantGuard AI — Real-Time Human-Elephant Conflict Prevention System

<div align="center">

![License](https://img.shields.io/badge/license-MIT-green)
![Python](https://img.shields.io/badge/Python-3.10+-blue)
![Platform](https://img.shields.io/badge/platform-Raspberry%20Pi%20%7C%20ESP32-orange)
![ML](https://img.shields.io/badge/ML-YOLOv7%20%7C%20TFLite-red)
![Status](https://img.shields.io/badge/status-Active-brightgreen)
![SIH](https://img.shields.io/badge/Smart%20India%20Hackathon-2025-purple)

**AI + IoT powered early-warning system to detect elephant movement in real time and prevent Human-Elephant Conflict (HEC) before it happens.**

[🌐 Live Demo](https://glitchhunters1.github.io/elephant-guard-ai) · [📋 Problem Statement PS-25167](#problem-statement) · [🚀 Quick Start](#quick-start) · [📸 Screenshots](#screenshots)

</div>

---

## 📌 Table of Contents

- [Problem Statement](#problem-statement)
- [Solution Overview](#solution-overview)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Features](#features)
- [Repository Structure](#repository-structure)
- [Quick Start](#quick-start)
- [Hardware Setup](#hardware-setup)
- [ML Model](#ml-model)
- [Dashboard](#dashboard)
- [Results & Impact](#results--impact)
- [Team](#team)
- [License](#license)

---

## 🎯 Problem Statement

**PS ID:** 25167 | **Category:** Hardware | **Event:** Smart India Hackathon 2025

Human-Elephant Conflict (HEC) is one of India's most critical wildlife conservation challenges:

| Metric | Figure | Source |
|--------|--------|--------|
| Human casualties (11 years) | ~595 deaths | India Today, 2024 |
| Deaths in last 5 years | ~303 | Economic Times, 2024 |
| Elephant deaths (3 years) | ~45 electrocuted | Hindustan Times, 2024 |
| Elephant mortality due to HEC | 66% | WTI Report |
| Hotspot districts | Surguja, Raigarh, Korba, Surajpur, Balrampur (N. Chhattisgarh) | — |

Existing solutions are **reactive** — barriers built after attacks, compensation paid after deaths. There is no scalable, affordable early-warning system that detects elephant approach *before* conflict erupts.

---

## 💡 Solution Overview

ElephantGuard AI is a **multi-sensor, edge-AI early warning system** that:

1. **Detects** elephant presence using thermal camera, Doppler radar, and PIR sensors
2. **Identifies** elephants vs. humans/animals using on-device YOLOv7 + TFLite ML model
3. **Transmits** alerts over LoRa radio (10–15 km, no internet needed)
4. **Alerts** villagers and forest officials via siren, LED strobe, SMS, and WhatsApp
5. **Learns** from each event to continuously improve detection accuracy

> ✅ Fully offline-capable · ✅ Solar-powered · ✅ Weatherproof (IP65) · ✅ Self-learning AI

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────┐
│                  FIELD NODE (ESP32)                  │
│                                                      │
│  PIR Sensor ──┐                                      │
│  Doppler Radar┼──► ESP32 MCU ──► YOLOv7/TFLite      │
│  MLX90640     ┘      │           (on-device ML)      │
│                      │                               │
│               LoRa SX1276 ──────────────────────┐   │
└──────────────────────────────────────────────────│───┘
                                                   │
                                           LoRa Radio
                                                   │
┌──────────────────────────────────────────────────│───┐
│              GATEWAY (Raspberry Pi 4/5)          │   │
│                                                  ▼   │
│  LoRa Receiver ──► MQTT/Mosquitto ──► FastAPI        │
│                         │                            │
│                  ┌──────┴──────┐                     │
│                  ▼             ▼                     │
│           TimescaleDB    Node-RED Dashboard          │
│                  │             │                     │
│                  └──────┬──────┘                     │
│                         ▼                            │
│              Twilio SMS / WhatsApp API               │
└──────────────────────────────────────────────────────┘
         │                    │
         ▼                    ▼
  📱 Villager SMS       🖥️ Forest Dept Dashboard
```

---

## 🛠️ Tech Stack

### Hardware
| Component | Purpose |
|-----------|---------|
| ESP32 | Edge MCU — sensor reading, ML inference, LoRa communication |
| Raspberry Pi 4/5 | Gateway, backend, dashboard host |
| SX1276/SX1278 (LoRa) | Long-range (10–15 km) low-power radio |
| MLX90640 Thermal Camera | Detect large warm objects (elephant heat signature) |
| 24 GHz Doppler Radar | Detect large, slow-moving objects |
| PIR Sensor | Wake-on-motion trigger |
| 12V Siren + Speaker | Acoustic deterrent |
| LED Strobe / Floodlight | Visual deterrent and village warning |
| Solar Panel + LiPo Battery | Off-grid continuous power |
| IP65 Enclosure + Pole Mount | Weatherproof outdoor deployment |

### Software
| Technology | Role |
|------------|------|
| YOLOv7 | Real-time elephant object detection |
| OpenCV | Computer vision preprocessing pipeline |
| TensorFlow Lite | On-device model inference (ESP32/RPi) |
| Edge Impulse | Model training and quantisation |
| Python 3.10+ | Gateway logic, backend, ML pipeline |
| C/C++ (Arduino/PlatformIO) | ESP32 firmware |
| MQTT / Mosquitto | Lightweight IoT messaging |
| FastAPI | REST API and backend |
| Node-RED | Dashboard and rules engine |
| PostgreSQL / TimescaleDB | Time-series event logging |
| Twilio / WhatsApp API | SMS and WhatsApp notifications |
| Git / GitHub | Version control |

---

## ✨ Features

- 🔍 **Real-time detection** — identifies elephants within seconds of entering sensor range
- 🧠 **Multi-class ML** — distinguishes elephants from humans, cattle, and vehicles
- 📡 **Offline-first** — fully operational without internet via LoRa radio
- ☀️ **Solar-powered** — 5+ day battery backup for cloudy periods
- 🚨 **Multi-channel alerts** — siren + LED + SMS + WhatsApp simultaneously
- 📊 **Live dashboard** — event logs with timestamp, location, confidence score
- 🔄 **Self-learning** — periodic model retraining on local data
- 🌧️ **Weatherproof** — IP65-rated enclosures for all-season forest deployment
- 📱 **Mobile-ready** — responsive dashboard accessible from any device

---

## 📁 Repository Structure

```
elephant-guard-ai/
│
├── firmware/                    # ESP32 embedded code
│   ├── src/
│   │   ├── main.cpp             # Main firmware loop
│   │   ├── sensors.cpp          # PIR, radar, thermal sensor drivers
│   │   ├── lora.cpp             # LoRa SX1276 communication
│   │   └── inference.cpp        # TFLite model inference
│   ├── include/
│   │   └── config.h             # Pin definitions, thresholds
│   └── platformio.ini           # PlatformIO build config
│
├── gateway/                     # Raspberry Pi gateway
│   ├── main.py                  # Entry point
│   ├── lora_receiver.py         # LoRa packet handler
│   ├── mqtt_broker.py           # MQTT message routing
│   ├── alert_dispatcher.py      # SMS/WhatsApp notifications
│   ├── db_logger.py             # TimescaleDB event logging
│   └── requirements.txt
│
├── ml/                          # Machine learning
│   ├── train/
│   │   ├── train_yolov7.py      # YOLOv7 training script
│   │   ├── dataset_prep.py      # Thermal image preprocessing
│   │   └── augment.py           # Data augmentation
│   ├── models/
│   │   ├── elephant_yolov7.pt   # Trained PyTorch weights
│   │   └── elephant_tflite.tflite  # Quantised edge model
│   ├── inference/
│   │   └── detect.py            # OpenCV + YOLOv7 inference pipeline
│   └── notebooks/
│       └── model_evaluation.ipynb
│
├── dashboard/                   # Web dashboard (Node-RED / FastAPI)
│   ├── app.py                   # FastAPI backend
│   ├── flows.json               # Node-RED flow export
│   ├── static/
│   │   └── index.html           # Frontend (this repo's landing page)
│   └── templates/
│
├── docs/                        # Documentation
│   ├── hardware_setup.md        # Wiring diagrams, BOM
│   ├── deployment_guide.md      # Field installation steps
│   ├── api_reference.md         # REST API docs
│   └── images/
│
├── tests/                       # Unit + integration tests
│   ├── test_sensors.py
│   ├── test_inference.py
│   └── test_alerts.py
│
├── scripts/                     # Utility scripts
│   ├── setup_rpi.sh             # One-command RPi setup
│   └── retrain_model.sh         # Trigger model retraining
│
├── .github/
│   └── workflows/
│       └── ci.yml               # GitHub Actions CI pipeline
│
├── .gitignore
├── LICENSE
└── README.md
```

---

## 🚀 Quick Start

### Prerequisites
- Raspberry Pi 4/5 running Raspberry Pi OS (64-bit)
- Python 3.10+
- PlatformIO (for ESP32 firmware)

### 1. Clone the repository
```bash
git clone https://github.com/GlitchHunters1/elephant-guard-ai.git
cd elephant-guard-ai
```

### 2. Set up the gateway (Raspberry Pi)
```bash
cd gateway
pip install -r requirements.txt
cp .env.example .env          # Add your Twilio credentials
python main.py
```

### 3. Flash the ESP32 firmware
```bash
cd firmware
pio run --target upload
```

### 4. Start the dashboard
```bash
cd dashboard
uvicorn app:app --host 0.0.0.0 --port 8000
# Open http://raspberrypi.local:8000
```

### 5. One-command RPi setup (automated)
```bash
chmod +x scripts/setup_rpi.sh
./scripts/setup_rpi.sh
```

---

## 🔧 Hardware Setup

Full wiring diagrams and bill of materials are in [`docs/hardware_setup.md`](docs/hardware_setup.md).

**Estimated BOM Cost per Node: ₹4,500–₹6,000**

| Component | Approx. Cost (INR) |
|-----------|-------------------|
| ESP32 Dev Board | ₹350 |
| MLX90640 Thermal Camera | ₹1,800 |
| SX1276 LoRa Module | ₹400 |
| 24 GHz Doppler Radar | ₹600 |
| PIR Sensor | ₹80 |
| Solar Panel (10W) + Battery | ₹900 |
| Siren + LED Strobe | ₹250 |
| IP65 Enclosure | ₹300 |
| Misc (wiring, mounts) | ₹200 |

---

## 🧠 ML Model

The detection model is based on **YOLOv7** fine-tuned on a custom dataset of thermal elephant images.

```
Model:         YOLOv7 (fine-tuned)
Input:         Thermal + visible-light images (640×640)
Classes:       elephant, human, animal_other, vehicle
mAP@0.5:       ~91.4%
Inference:     ~28ms on Raspberry Pi 5
Edge format:   TFLite INT8 quantised
Training data: 4,200+ labelled thermal images
```

To retrain the model on new local data:
```bash
cd ml
python train/train_yolov7.py --data dataset.yaml --weights elephant_yolov7.pt --epochs 50
```

---

## 📊 Dashboard

The live monitoring dashboard shows:
- 🗺️ Map of active sensor nodes
- 🚨 Real-time detection alerts with confidence score
- 📈 Event history and trend graphs
- 🔋 Node battery / solar status
- 📱 SMS delivery status per alert

---

## 📈 Results & Impact

| Benefit | Description |
|---------|-------------|
| 👥 Human Safety | Early alerts give villagers 5–15 minutes to evacuate before elephant arrival |
| 🐘 Wildlife Protection | Non-violent deterrence eliminates need for retaliatory killings |
| 🌿 Conservation | Maintains natural elephant corridors; zero harm to animals |
| 🤝 Community | Builds trust between forest-border communities and forest authorities |
| 💰 Economic | Prevents crop loss worth ₹2–5 lakh per incident annually per village |

---

## 👥 Team

**Team Name:** Glitch Hunters_1 | **Team ID:** 98915
**Event:** Smart India Hackathon 2025 | **Problem Statement:** PS-25167

---

## 📄 License

This project is licensed under the MIT License — see [`LICENSE`](LICENSE) for details.

---

## 🙏 Acknowledgements

- Ministry of Environment, Forest and Climate Change (MoEFCC), India
- Wildlife Trust of India (WTI) — elephant corridor research
- [YOLOv7](https://github.com/WongKinYiu/yolov7) by WongKinYiu
- Smart India Hackathon 2025 organizers

---

<div align="center">
  <sub>Built with ❤️ for wildlife conservation · Smart India Hackathon 2025</sub>
</div>
