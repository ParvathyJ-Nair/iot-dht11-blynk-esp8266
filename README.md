# iot-dht11-blynk-esp8266
 Real-time temperature and humidity monitoring with NodeMCU ESP8266, DHT11 sensor, and Blynk IoT cloud dashboard.
# 🌡️ IoT-Based Humidity & Temperature Monitor

> Real-time environmental monitoring using **NodeMCU ESP8266** + **DHT11** sensor, streamed live to the **Blynk IoT** cloud dashboard from anywhere in the world.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Hardware Requirements](#hardware-requirements)
- [Software Requirements](#software-requirements)
- [Circuit Diagram & Wiring](#circuit-diagram--wiring)
- [Blynk Dashboard Setup](#blynk-dashboard-setup)
- [Project Configuration](#project-configuration)
- [Installation & Flashing](#installation--flashing)
- [Virtual Pin Mapping](#virtual-pin-mapping)
- [Serial Monitor Output](#serial-monitor-output)
- [How It Works](#how-it-works)
- [Customization](#customization)
- [Troubleshooting](#troubleshooting)
- [Future Improvements](#future-improvements)
- [License](#license)

---

## Overview

This project turns a low-cost **NodeMCU ESP8266** microcontroller and a **DHT11** temperature/humidity sensor into a fully connected IoT device. Sensor readings are pushed every **2 seconds** to the **Blynk IoT cloud platform**, where you can monitor live data, view historical charts, and receive high-temperature alerts — all from the free Blynk mobile app or web dashboard.

---

## Features

- 📡 **Wi-Fi Connected** — Connects to any 2.4 GHz network via ESP8266
- 🌡️ **Temperature Monitoring** — Reads ambient temperature in °C
- 💧 **Humidity Monitoring** — Reads relative humidity in %
- 🔴 **High-Temperature Alert** — Virtual LED triggers when temperature exceeds 35 °C
- 📊 **Live Dashboard** — Real-time gauges and historical SuperCharts on Blynk
- 🔁 **Non-Blocking Timer** — Uses `BlynkTimer` so the main loop stays responsive
- 🛠️ **Error Handling** — Gracefully skips and logs failed sensor reads
- 🖥️ **Serial Debug Output** — Human-readable logs via USB Serial Monitor

---

## Hardware Requirements

| Component | Quantity | Notes |
|---|---|---|
| NodeMCU ESP8266 (v2 or v3) | 1 | Any ESP8266-based board works |
| DHT11 Temperature & Humidity Sensor | 1 | Can substitute DHT22 for higher precision |
| 10 kΩ Resistor | 1 | Pull-up resistor on DHT11 DATA line |
| Breadboard | 1 | For prototyping |
| Jumper Wires | 4 | Male-to-male |
| USB Cable (Micro-USB) | 1 | For power and flashing |

---

## Software Requirements

| Tool / Library | Version | Purpose |
|---|---|---|
| [Arduino IDE](https://www.arduino.cc/en/software) | 1.8.x or 2.x | Code editor and uploader |
| ESP8266 Board Package | ≥ 3.0.0 | NodeMCU board support |
| [Blynk Library](https://github.com/blynkkk/blynk-library) | ≥ 1.3.0 | Blynk IoT connectivity |
| [DHT Sensor Library](https://github.com/adafruit/DHT-sensor-library) | ≥ 1.4.0 | DHT11/DHT22 driver |
| [Adafruit Unified Sensor](https://github.com/adafruit/Adafruit_Sensor) | ≥ 1.1.0 | Required by DHT library |
| [Blynk IoT Account](https://blynk.cloud) | Free tier | Cloud dashboard |

---

## Circuit Diagram & Wiring

```
NodeMCU ESP8266          DHT11 Sensor
─────────────────        ────────────
  3.3V (or VIN) ───────► VCC  (Pin 1)
  D4   (GPIO2)  ───────► DATA (Pin 2)  ←── 10kΩ pull-up to VCC
  GND           ───────► GND  (Pin 4)
```

### Schematic (ASCII)

```
3.3V ────┬──────────────────► VCC  [DHT11]
         │
        [10kΩ]
         │
D4  ─────┴──────────────────► DATA [DHT11]

GND ────────────────────────► GND  [DHT11]
```

> **⚠️ Important:** Always use the pull-up resistor on the DATA line. Without it, the sensor may give intermittent or failed readings (`NaN`).

---

## Blynk Dashboard Setup

### Step 1 — Create a Blynk Account
1. Go to [blynk.cloud](https://blynk.cloud) and sign up for a free account.

### Step 2 — Create a New Template
1. Click **"+ New Template"**
2. Set the name: `IoT Based Humidity Temperature Sensor`
3. Hardware: `ESP8266`
4. Connection Type: `WiFi`
5. Save the template and note down the **Template ID**.

### Step 3 — Create a Device
1. From your template, click **"Create Device"**
2. Name your device (e.g., `Living Room Sensor`)
3. Copy the **Auth Token** — you will paste this into the code.

### Step 4 — Add Dashboard Widgets

Go to **Dashboard → Edit** and add the following widgets:

| Widget | Virtual Pin | Label | Settings |
|---|---|---|---|
| Gauge | V0 | Temperature | Range: -10 to 60 °C |
| Gauge | V1 | Humidity | Range: 0 to 100 % |
| LED | V2 | Heat Alert | Color: Red |
| SuperChart | V0 + V1 | History | Time range: 1 hour |

### Step 5 — (Optional) Set Up Notifications
In the Blynk app, you can configure **Automations** to send push notifications when V2 (heat alert) turns on.

---

## Project Configuration

Open `main.ino` and fill in your credentials:

```cpp
// ── Blynk credentials ──────────────────────────────────────
#define BLYNK_TEMPLATE_ID   "TMPLxxxxxxxx"   // From Blynk dashboard
#define BLYNK_TEMPLATE_NAME "IoT Based Humidity Temperature Sensor"
#define BLYNK_AUTH_TOKEN    "xxxxxxxxxxxx"   // From your device page

// ── Wi-Fi credentials ──────────────────────────────────────
char ssid[] = "YourWiFiName";
char pass[] = "YourWiFiPassword";
```

> **Security tip:** Never commit real credentials to a public repository. Use `secrets.h` or environment-level config files and add them to `.gitignore`.

---

## Installation & Flashing

### 1. Install ESP8266 Board Support
In Arduino IDE → Preferences → Additional Board Manager URLs, add:
```
http://arduino.esp8266.com/stable/package_esp8266com_index.json
```
Then go to **Tools → Board → Board Manager** and install **esp8266 by ESP8266 Community**.

### 2. Install Required Libraries
In **Sketch → Include Library → Manage Libraries**, search for and install:
- `Blynk`
- `DHT sensor library` by Adafruit
- `Adafruit Unified Sensor`

### 3. Select Board & Port
- **Board:** `NodeMCU 1.0 (ESP-12E Module)`
- **Upload Speed:** `115200`
- **Port:** Your COM port (Windows) or `/dev/ttyUSB0` (Linux/macOS)

### 4. Upload the Sketch
1. Open `main.ino` in Arduino IDE
2. Fill in your credentials (see above)
3. Click **Upload (→)**
4. Open **Serial Monitor** at `9600 baud` to watch the boot logs

---

## Virtual Pin Mapping

| Virtual Pin | Data | Widget | Range |
|---|---|---|---|
| **V0** | Temperature (°C) | Gauge, SuperChart | -10 to 60 |
| **V1** | Humidity (%) | Gauge, SuperChart | 0 to 100 |
| **V2** | Heat Alert (0 / 255) | LED widget | 0 = OFF, 255 = ON |

---

## Serial Monitor Output

After a successful boot and Wi-Fi connection, you should see:

```
[BOOT] IoT Temperature & Humidity Monitor starting...
[DHT11] Sensor initialized.
[BLYNK] Connected to Blynk cloud.
[TIMER] Sensor polling every 2 seconds.
[DHT11] Humidity: 62.00 %   Temperature: 28.40 °C
[DHT11] Humidity: 61.00 %   Temperature: 28.50 °C
[DHT11] Humidity: 62.00 %   Temperature: 36.10 °C
[ALERT] High temperature detected!
```

If you see `[ERROR] Failed to read from DHT sensor!`, check your wiring and pull-up resistor.

---

## How It Works

```
┌─────────────┐    reads every    ┌──────────┐
│  DHT11      │ ◄──── 2 sec ───── │ ESP8266  │
│  Sensor     │ ────► t, h ──────► (firmware)│
└─────────────┘                   └────┬─────┘
                                        │  Wi-Fi
                                        ▼
                               ┌─────────────────┐
                               │  Blynk Cloud    │
                               │  V0: Temp       │
                               │  V1: Humidity   │
                               │  V2: Alert LED  │
                               └────────┬────────┘
                                        │
                              ┌─────────▼────────┐
                              │  Blynk App /     │
                              │  Web Dashboard   │
                              └──────────────────┘
```

1. **`setup()`** initializes Serial, the DHT11 sensor, and establishes a Blynk connection over Wi-Fi.
2. **`BlynkTimer`** fires `sendSensor()` every **2000 ms** without blocking the main loop.
3. **`sendSensor()`** reads float values from the DHT11. If either reading is `NaN` (a failed read), it logs the error and returns early.
4. Valid readings are sent to Blynk via `Blynk.virtualWrite()`.
5. A simple threshold check on temperature controls the virtual **alert LED** on V2.
6. **`loop()`** calls `Blynk.run()` (keeps the connection alive and handles server messages) and `timer.run()` (fires scheduled callbacks).

---

## Customization

### Change the alert threshold
```cpp
#define TEMP_ALERT_THRESHOLD 35.0   // Adjust to any value in °C
```

### Change the polling interval
```cpp
timer.setInterval(2000L, sendSensor);  // 2000L = 2 seconds; change to 5000L for 5 sec
```

### Use a DHT22 sensor instead
DHT22 has higher accuracy (±0.5 °C vs ±2 °C for DHT11) and a wider range:
```cpp
#define DHTTYPE DHT22   // Change from DHT11 to DHT22
```

### Report temperature in Fahrenheit
```cpp
float t = dht.readTemperature(true);  // Pass 'true' for °F
```

### Add more sensors
You can add additional `virtualWrite` calls in `sendSensor()` to push extra data (e.g., heat index, dew point) to V3, V4, etc., and add corresponding widgets on the Blynk dashboard.

---

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| `Failed to read from DHT sensor!` | Missing pull-up resistor or loose wire | Add 10kΩ resistor between DATA and VCC; check wiring |
| Device never connects to Wi-Fi | Wrong SSID/password or 5 GHz network | ESP8266 only supports 2.4 GHz; verify credentials |
| Blynk shows offline | Wrong Auth Token or Template ID | Double-check values copied from Blynk dashboard |
| Upload fails | Wrong board or port selected | Select `NodeMCU 1.0` and correct COM port |
| Readings stuck at 0 or 85 °C | DHT11 initializing | Wait 2–3 seconds after power-on; add `delay(2000)` before first read |
| Serial Monitor shows garbage | Wrong baud rate | Set Serial Monitor to `9600 baud` |

---

## Future Improvements

- [ ] Add **OLED display** (SSD1306) for local readout without a phone
- [ ] Use **DHT22** for higher accuracy measurements
- [ ] Implement **deep sleep** between readings for battery-powered deployments
- [ ] Store readings in **EEPROM** or **LittleFS** for offline data logging
- [ ] Add **Blynk Automations** for email/SMS alerts on threshold breach
- [ ] Expand to multiple sensors on different GPIO pins
- [ ] Add an **NTP time-stamp** to each reading
- [ ] Build a **web server fallback** (ESPAsyncWebServer) for LAN-only access

---

## License

```
MIT License

Copyright (c) 2025 <Your Name>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
```

---

> Made with ❤️ and a soldering iron. If this helped you, give the repo a ⭐!
