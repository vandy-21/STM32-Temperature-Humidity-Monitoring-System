# STM32-Temperature-Humidity-Monitoring-System
A simple real-time embedded project that reads temperature and humidity using an STM32 microcontroller and sends the data to a PC through UART for live monitoring.
# STM32 Temperature & Humidity Monitoring System

> Real-time embedded sensor system using STM32F103, DHT11, and UART serial output — written in Embedded C.

---

##  Project Overview

This project implements a real-time **temperature and humidity monitoring system** on an STM32F103C8T6 (Blue Pill) microcontroller. The DHT11 sensor is interfaced using a custom **bit-bang single-wire driver** with microsecond-accurate timing via the ARM DWT cycle counter. Readings are transmitted every 2 seconds over **UART at 9600 baud** for serial monitoring and data logging.

---

##  Hardware Used

| Component | Details |
|-----------|---------|
| Microcontroller | STM32F103C8T6 (Blue Pill) |
| Sensor | DHT11 Temperature & Humidity |
| Communication | USART1 — PA9 (TX), PA10 (RX) |
| Data Pin | PA1 (DHT11 single-wire) |
| Status LED | PC13 (onboard Blue Pill LED) |
| Debug Tool | USB-to-TTL Serial Adapter |
| Power | 3.3V / 5V via USB |

---

##  Software & Tools

- **Language:** Embedded C (C99)
- **IDE:** STM32CubeIDE / Keil µVision
- **HAL:** STM32 HAL (Hardware Abstraction Layer)
- **Simulation:** Proteus 8
- **Version Control:** Git / GitHub
- **Serial Monitor:** PuTTY / Tera Term (9600 baud, 8N1)

---

##  Features

-  **Custom DHT11 bit-bang driver** — no external libraries
-  **DWT-based microsecond delay** — accurate timing at 72 MHz
-  **UART serial output** — formatted real-time data logging
-  **CRC/checksum validation** — data integrity on every read
-  **Status classification** — HIGH / NORMAL / LOW thresholds
-  **GPIO interrupt-ready architecture** — expandable design
-  **Non-blocking main loop** — 2-second polling interval

---

##  Project Structure

```
stm32-temp-humidity-monitor/
│
├── Core/
│   ├── Inc/
│   │   ├── main.h          # Project-wide includes
│   │   ├── dht11.h         # DHT11 driver header
│   │   └── uart.h          # UART utility header
│   │
│   └── Src/
│       ├── main.c          # Application entry point & main loop
│       ├── dht11.c         # DHT11 single-wire bit-bang driver
│       └── uart.c          # USART1 transmit utility
│
├── Docs/
│   └── circuit_diagram.md  # Wiring guide & pin connections
│
└── README.md
```

---

##  Circuit / Wiring Diagram

```
STM32F103 (Blue Pill)           DHT11 Sensor
─────────────────────           ────────────
PA1  ──────────────────────────  DATA  (pin 2)
3.3V ──────────────────────────  VCC   (pin 1)
GND  ──────────────────────────  GND   (pin 4)
                                 (4.7kΩ pull-up between DATA and VCC)

STM32F103 (Blue Pill)           USB-to-TTL Adapter
─────────────────────           ──────────────────
PA9  (TX) ──────────────────── RX
PA10 (RX) ──────────────────── TX
GND  ───────────────────────── GND
```

---

##  UART Output Format

Connect a serial terminal (9600 baud, 8N1) to see live output:

```
========================================
  STM32 Temp & Humidity Monitor v1.0
  Author : Vandana Vangireddy
  Sensor : DHT11   |  UART: 9600 baud
========================================

[DATA] Temp: 28 C (NORMAL) | Humidity: 65 % (NORMAL)
[DATA] Temp: 28 C (NORMAL) | Humidity: 66 % (NORMAL)
[DATA] Temp: 36 C (HIGH)   | Humidity: 82 % (HIGH)
[ERROR] DHT11 read failed – check wiring.
```

---

##  How the DHT11 Driver Works

The DHT11 uses a **single-wire half-duplex protocol**:

1. **Start signal** — Host pulls bus LOW for 20 ms, then HIGH for 30 µs
2. **Sensor ACK** — Sensor responds with ~80 µs LOW + ~80 µs HIGH
3. **40-bit data frame** — Each bit: ~50 µs LOW then HIGH duration:
   - `26–28 µs HIGH` → bit **0**
   - `~70 µs HIGH`   → bit **1**
4. **Frame layout:** `[Hum_int][Hum_dec][Tmp_int][Tmp_dec][CRC]`
5. **CRC check:** `byte0 + byte1 + byte2 + byte3 == byte4`

Timing is achieved using the **ARM DWT cycle counter** for µs-accurate delays without blocking SysTick.

---

##  How to Build & Flash

1. Clone this repo:
   ```bash
   git clone https://github.com/vandy-21/stm32-temp-humidity-monitor.git
   ```

2. Open in **STM32CubeIDE** → Import existing project

3. Add STM32F1 HAL drivers (via CubeMX or manually)

4. Build → Flash via ST-Link programmer

5. Open serial terminal at **9600 baud** on the ST-Link virtual COM port

---

##  Key Technical Concepts Demonstrated

| Concept | Implementation |
|---------|---------------|
| Bit-bang protocol | Custom DHT11 single-wire driver |
| Microsecond timing | ARM DWT cycle counter |
| UART communication | USART1 at 9600 baud, 8N1 |
| GPIO control | Input/Output mode switching |
| Data validation | CRC checksum verification |
| Interrupt-ready design | Non-blocking polling architecture |
| Firmware structure | Layered driver + application model |

