# ESPHome Modbus EM24 Powermeter

ESPHome-Konfiguration für den Carlo Gavazzi EM24 Energiezähler über Modbus RTU (RS485).
Liest alle verfügbaren Messwerte aus und stellt sie in Home Assistant bereit.

![ESPHome](https://img.shields.io/badge/ESPHome-2024.x-blue)
![ESP32-C3](https://img.shields.io/badge/ESP32-C3-green)
![Modbus RTU](https://img.shields.io/badge/Modbus-RTU-orange)

---

## Hardware

| Komponente | Details |
|------------|---------|
| Mikrocontroller | ESP32-C3 DevKitM-1 |
| Framework | Arduino |
| Energiezähler | Carlo Gavazzi EM24 (3-phasig) |
| Protokoll | Modbus RTU über RS485 |
| Baudrate | 9600, 8N1 |

**Verkabelung (ESP32-C3 ↔ RS485-Modul):**

| ESP32-C3 | RS485-Modul | EM24 |
|----------|-------------|------|
| GPIO4 (TX) | DI | — |
| GPIO3 (RX) | RO | — |
| GND | GND | — |
| — | A+ | A |
| — | B- | B |

> Die Modbus-Adresse des EM24 ist werkseitig auf `0x01` gesetzt.

---

## Messwerte

### Spannungen
| Sensor | Einheit | Register |
|--------|---------|---------|
| `voltage_l1_n` | V | 0x0000 |
| `voltage_l2_n` | V | 0x0002 |
| `voltage_l3_n` | V | 0x0004 |
| `voltage_l1_l2` | V | 0x0006 |
| `voltage_l2_l3` | V | 0x0008 |
| `voltage_l3_l1` | V | 0x000A |

### Ströme
| Sensor | Einheit | Register |
|--------|---------|---------|
| `current_l1` | A | 0x000C |
| `current_l2` | A | 0x000E |
| `current_l3` | A | 0x0010 |

### Wirkleistung (Active Power)
| Sensor | Einheit | Register |
|--------|---------|---------|
| `power_l1` | W | 0x0012 |
| `power_l2` | W | 0x0014 |
| `power_l3` | W | 0x0016 |
| `power_total` | W | 0x0028 |

### Scheinleistung (Apparent Power)
| Sensor | Einheit | Register |
|--------|---------|---------|
| `apparent_power_l1` | VA | 0x0018 |
| `apparent_power_l2` | VA | 0x001A |
| `apparent_power_l3` | VA | 0x001C |
| `apparent_power_total` | VA | 0x002A |

### Blindleistung (Reactive Power)
| Sensor | Einheit | Register |
|--------|---------|---------|
| `reactive_power_l1` | VAr | 0x001E |
| `reactive_power_l2` | VAr | 0x0020 |
| `reactive_power_l3` | VAr | 0x0022 |
| `reactive_power_total` | VAr | 0x002C |

### Leistungsfaktor (Power Factor)
| Sensor | Einheit | Register |
|--------|---------|---------|
| `power_factor_l1` | — | 0x0024 |
| `power_factor_l2` | — | 0x0025 |
| `power_factor_l3` | — | 0x0026 |
| `power_factor_total` | — | 0x002E |

### Frequenz & Energie
| Sensor | Einheit | Register |
|--------|---------|---------|
| `frequency` | Hz | 0x0037 |
| `energy_import` | kWh | 0x003C |
| `energy_export` | kWh | 0x003E |
| `reactive_energy_q1` | kVArh | 0x0040 |
| `reactive_energy_q2` | kVArh | 0x0042 |
| `reactive_energy_q3` | kVArh | 0x0044 |
| `reactive_energy_q4` | kVArh | 0x0046 |

---

## Installation

**1. Repository klonen:**
```bash
git clone https://github.com/dagra68/esphome-em24-powermeter.git
cd esphome-em24-powermeter
```

**2. `secrets.yaml` anlegen** (nicht im Repository enthalten):
```yaml
wifi_ssid: "DeinNetzwerk"
wifi_password: "DeinPasswort"
api_encryption_key: "DeinAPIKey"
ota_password: "DeinOTAPasswort"
```

API-Key und OTA-Passwort generieren:
```bash
esphome generate-secret
```

**3. Kompilieren und flashen:**
```bash
esphome run em24-powermeter.yaml
```

---

## Home Assistant

Nach dem Flash erscheinen alle Sensoren automatisch als Entitäten in Home Assistant (native API, Port 6053).

Empfohlene Verwendung im Energie-Dashboard:
- `energy_import` → Netzbezug
- `energy_export` → Einspeisung
- `power_total` → Aktuelle Gesamtleistung

---

## Hinweise

- Registeradressen gelten für **EM24-DIN AV5** — bei anderen Modellvarianten mit dem Datenblatt abgleichen
- Negative Leistungswerte bedeuten Einspeisung ins Netz
- Update-Intervall: 10 Sekunden (anpassbar in `modbus_controller`)
