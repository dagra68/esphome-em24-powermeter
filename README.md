# ESPHome Modbus EM24 Powermeter

ESPHome configuration for the Carlo Gavazzi EM24 energy meter via Modbus RTU (RS485).
Reads all available measurements and exposes them to Home Assistant.

![ESPHome](https://img.shields.io/badge/ESPHome-2024.x-blue)
![ESP32-C3](https://img.shields.io/badge/ESP32-C3-green)
![Modbus RTU](https://img.shields.io/badge/Modbus-RTU-orange)

---

## Hardware

| Component | Details |
|-----------|---------|
| Microcontroller | ESP32-C3 DevKitM-1 |
| Framework | Arduino |
| Energy meter | Carlo Gavazzi EM24 (3-phase) |
| Protocol | Modbus RTU over RS485 |
| Baud rate | 9600, 8N1 |

**Wiring (ESP32-C3 ↔ RS485 module):**

| ESP32-C3 | RS485 module | EM24 |
|----------|--------------|------|
| GPIO4 (TX) | DI | — |
| GPIO3 (RX) | RO | — |
| GND | GND | — |
| — | A+ | A |
| — | B- | B |

> The Modbus address of the EM24 is factory-set to `0x01`.

---

## Measurements

**32 sensors total** — all exposed as Home Assistant entities via native API.

| Sensor | Unit | Description |
|--------|------|-------------|
| `voltage_l1_n` | V | Phase L1 to neutral |
| `voltage_l2_n` | V | Phase L2 to neutral |
| `voltage_l3_n` | V | Phase L3 to neutral |
| `voltage_l1_l2` | V | Line voltage L1–L2 |
| `voltage_l2_l3` | V | Line voltage L2–L3 |
| `voltage_l3_l1` | V | Line voltage L3–L1 |
| `current_l1` | A | Phase L1 current |
| `current_l2` | A | Phase L2 current |
| `current_l3` | A | Phase L3 current |
| `power_l1` | W | Active power L1 |
| `power_l2` | W | Active power L2 |
| `power_l3` | W | Active power L3 |
| `power_total` | W | Total active power |
| `apparent_power_l1` | VA | Apparent power L1 |
| `apparent_power_l2` | VA | Apparent power L2 |
| `apparent_power_l3` | VA | Apparent power L3 |
| `apparent_power_total` | VA | Total apparent power |
| `reactive_power_l1` | VAr | Reactive power L1 |
| `reactive_power_l2` | VAr | Reactive power L2 |
| `reactive_power_l3` | VAr | Reactive power L3 |
| `reactive_power_total` | VAr | Total reactive power |
| `power_factor_l1` | — | Power factor L1 |
| `power_factor_l2` | — | Power factor L2 |
| `power_factor_l3` | — | Power factor L3 |
| `power_factor_total` | — | Total power factor |
| `frequency` | Hz | Grid frequency |
| `energy_import` | kWh | Active energy import (grid consumption) |
| `energy_export` | kWh | Active energy export (grid feed-in) |
| `reactive_energy_q1` | kVArh | Reactive energy Q1 (inductive import) |
| `reactive_energy_q2` | kVArh | Reactive energy Q2 (capacitive import) |
| `reactive_energy_q3` | kVArh | Reactive energy Q3 (inductive export) |
| `reactive_energy_q4` | kVArh | Reactive energy Q4 (capacitive export) |

---

### Voltages
| Sensor | Unit | Register |
|--------|------|----------|
| `voltage_l1_n` | V | 0x0000 |
| `voltage_l2_n` | V | 0x0002 |
| `voltage_l3_n` | V | 0x0004 |
| `voltage_l1_l2` | V | 0x0006 |
| `voltage_l2_l3` | V | 0x0008 |
| `voltage_l3_l1` | V | 0x000A |

### Currents
| Sensor | Unit | Register |
|--------|------|----------|
| `current_l1` | A | 0x000C |
| `current_l2` | A | 0x000E |
| `current_l3` | A | 0x0010 |

### Active Power
| Sensor | Unit | Register |
|--------|------|----------|
| `power_l1` | W | 0x0012 |
| `power_l2` | W | 0x0014 |
| `power_l3` | W | 0x0016 |
| `power_total` | W | 0x0028 |

### Apparent Power
| Sensor | Unit | Register |
|--------|------|----------|
| `apparent_power_l1` | VA | 0x0018 |
| `apparent_power_l2` | VA | 0x001A |
| `apparent_power_l3` | VA | 0x001C |
| `apparent_power_total` | VA | 0x002A |

### Reactive Power
| Sensor | Unit | Register |
|--------|------|----------|
| `reactive_power_l1` | VAr | 0x001E |
| `reactive_power_l2` | VAr | 0x0020 |
| `reactive_power_l3` | VAr | 0x0022 |
| `reactive_power_total` | VAr | 0x002C |

### Power Factor
| Sensor | Unit | Register |
|--------|------|----------|
| `power_factor_l1` | — | 0x0024 |
| `power_factor_l2` | — | 0x0025 |
| `power_factor_l3` | — | 0x0026 |
| `power_factor_total` | — | 0x002E |

### Frequency & Energy
| Sensor | Unit | Register |
|--------|------|----------|
| `frequency` | Hz | 0x0037 |
| `energy_import` | kWh | 0x003C |
| `energy_export` | kWh | 0x003E |
| `reactive_energy_q1` | kVArh | 0x0040 |
| `reactive_energy_q2` | kVArh | 0x0042 |
| `reactive_energy_q3` | kVArh | 0x0044 |
| `reactive_energy_q4` | kVArh | 0x0046 |

---

## Installation

**1. Clone the repository:**
```bash
git clone https://github.com/dagra68/esphome-em24-powermeter.git
cd esphome-em24-powermeter
```

**2. Create `secrets.yaml`** (not included in the repository):
```yaml
wifi_ssid: "YourNetwork"
wifi_password: "YourPassword"
api_encryption_key: "YourAPIKey"
ota_password: "YourOTAPassword"
```

Generate API key and OTA password:
```bash
esphome generate-secret
```

**3. Compile and flash:**
```bash
esphome run em24-powermeter.yaml
```

---

## Home Assistant

After flashing, all sensors appear automatically as entities in Home Assistant (native API, port 6053).

Recommended use in the Energy dashboard:
- `energy_import` → Grid consumption
- `energy_export` → Grid feed-in
- `power_total` → Current total power

---

## Notes

- Register addresses apply to **EM24-DIN AV5** — verify against the datasheet for other model variants
- Negative power values indicate feed-in to the grid
- Update interval: 10 seconds (adjustable in `modbus_controller`)
