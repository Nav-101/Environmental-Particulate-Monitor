# Environmental Particulate Monitor
> **Autonomous PM2.5/PM10 Data Logging Node with Dual-Power Redundancy**

## 📋 Overview
This project is a standalone environmental monitoring station designed for long-term particulate matter (PM) data acquisition. Built around the **Sensirion SPS30** and **ESP32**, the system prioritizes local data reliability and power robustness, ensuring consistent logging to a microSD card even in environments without network connectivity.

---

## 🛠️ System Specifications
| Feature | Implementation |
| :--- | :--- |
| **MCU** | ESP32 (Cortex-M7 equivalent control logic) |
| **PM Sensor** | Sensirion SPS30 (MCERT certified laser scattering) |
| **Storage** | microSD via SPI (FAT32 / CSV Format) |
| **Timekeeping** | I2C Real-Time Clock (RTC) with Battery Backup |
| **User Interface** | Debounced Mode-Select Button + LED State Indicators |
| **Power Front-End** | Dual-Input (USB 5V / DC Jack) with Source Isolation |

---

## 📐 Hardware Architecture

### 1. Power Design (Source Isolation & Regulation)
The power stage is engineered to handle the high current bursts required by the ESP32 and the microSD "Write" cycles.
* **Input OR-ing:** Implemented a source-isolation strategy to prevent back-feeding between the USB and DC Jack inputs.
* **Transient Protection:** The front-end is hardened against hot-plug transients and basic industrial power surges.
* **Thermal Management:** Wide copper pours and optimized trace widths ensure the 3.3V LDO/Regulator handles bursty loads without thermal throttling.

![Power Front-End](./media/pwrfront.png)

### 2. Sensor Integration (Sensirion SPS30)
* **Signal Integrity:** Short-path I2C/Serial routing with local decoupling capacitors to ensure stable digital communication.
* **Airflow Logic:** The PCB layout and connector placement were designed with enclosure integration in mind, ensuring the SPS30 has unobstructed airflow for accurate PM1.0, PM2.5, and PM10 measurements.

![Sensor Interface](./media/sps_foot.png)

### 3. Data Integrity & Logging
To prevent data loss in the field, the system employs a robust logging architecture:
* **RTC Timestamping:** Every data point is indexed with a hardware-backed timestamp, making the dataset meaningful for time-series analysis without requiring NTP/Wi-Fi.
* **SD Reliability:** Implemented a file-write strategy that minimizes the "Open-Write-Close" window to reduce the risk of CSV corruption during unexpected power-loss events.

---

## 🚀 Technical Design Decisions
* **Standalone Operation:** Purposefully excluded Wi-Fi dependencies to maximize battery life and data security in remote deployments.
* **UI Determinism:** Used a hardware-debounced push button and a simple LED-state machine to cycle sampling frequencies, providing a foolproof interface for field technicians.
* **Grounding Strategy:** Separated high-current digital return paths (SD/MCU) from the sensor's power return to minimize electrical noise coupling into the acquisition logic.

---

## 🧪 Validation & Performance
* **Power Stability:** Verified clean transition between USB and DC power sources under full sensor load.
* **Logging Durability:** Successfully validated 48-hour continuous logging stress tests with zero dropped samples.
* **Timestamp Accuracy:** Confirmed RTC drift is within specified tolerances against a reference GPS-disciplined clock.

---

## 📈 Future Improvements
* **Power Profiling:** Integration of an INA-family current monitor to profile power consumption across different sampling modes.
* **Enhanced Protection:** Adding high-level ESD protection to the external DC Jack for deployment in harsher environments.
* **Sleep Optimization:** Implementing Deep-Sleep cycles to enable multi-month operation on a single Li-Po battery.
