# ESP32 Weather Station for Home Assistant

A reliable, 3D-printed weather station powered by ESP32 and integrated with Home Assistant via ESPHome. **Field tested and validated for over 4 years** in real-world conditions.

![Weather Station](docs/images/main_photo.jpg)
<!-- TODO: Agregar foto principal del proyecto -->

## ⭐ Features

- 🌡️ Temperature monitoring (dual sensors)
- 💧 Humidity measurement (air and soil)
- 🌪️ Wind speed tracking
- 🌧️ Rainfall measurement
- 📊 Atmospheric pressure
- 🍃 Leaf wetness detection
- 🔋 Battery monitoring
- 💦 Irrigation control (2 zones)
- 🏠 Full Home Assistant integration via ESPHome
- 📡 Optional MQTT support
- ✅ **4+ years of continuous operation and refinement**

## 📋 Table of Contents

- [Hardware Requirements](#hardware-requirements)
- [Sensors](#sensors)
- [Wiring Diagram](#wiring-diagram)
- [3D Printed Parts](#3d-printed-parts)
- [Installation](#installation)
- [Configuration](#configuration)
- [Calibration](#calibration)
- [Home Assistant Integration](#home-assistant-integration)
- [Lessons Learned](#lessons-learned)
- [Contributing](#contributing)
- [License](#license)

## 🛠️ Hardware Requirements

### Main Components

| Component | Model/Type | Quantity | Notes |
|-----------|------------|----------|-------|
| Microcontroller | ESP32-DevKit | 1 | 
| Temperature/Humidity | DHT22 | 1 | 
| Pressure Sensor | BMP280 | 1 | I2C address: 0x76 |
| Soil Moisture | <!-- TODO: Modelo específico --> | 1 | Capacitive sensor |
| Anemometer | <!-- TODO: Modelo --> | 1 | 
| Rain Gauge | <!-- TODO: Modelo --> | 1 | Tipping bucket type |
| Leaf Wetness | <!-- TODO: Modelo --> | 1 | 
| Battery | <!-- TODO: Tipo y capacidad --> | 1 | Optional for backup |
| Relay Module | <!-- TODO: Modelo --> | 1 | For irrigation control |

<!-- TODO: Completar modelos específicos de sensores -->

### Additional Components

- Resistors for voltage divider (battery monitoring)
- Wiring and connectors
- Power supply (<!-- TODO: voltaje y amperaje -->)
- SD card (optional, for logging)
- <!-- TODO: Agregar otros componentes -->

### Tools Required

- 3D Printer
- Soldering iron
- Multimeter (for calibration)
- Wire strippers
- <!-- TODO: Otras herramientas -->

**Estimated Total Cost:** $<!-- TODO: costo aproximado --> USD

## 📡 Sensors

### Pin Configuration

| Sensor | GPIO Pin | Type | Update Interval |
|--------|----------|------|-----------------|
| DHT22 | 23 | Digital | 15 min |
| BMP280 | 21/22 | I2C | 15 min |
| Anemometer | 27 | Pulse Counter | 60 sec |
| Rain Gauge | 33 | Pulse Counter | 60 sec |
| Soil Moisture | 34 | ADC | 10 min |
| Leaf Wetness | 32 | ADC | 15 min |
| Battery Monitor | 35 | ADC | 10 min |
| Irrigation Zone 1 | 14 | Digital Output | - |
| Irrigation Zone 2 | 13 | Digital Output | - |

<!-- TODO: Verificar si hay algún pin adicional -->

## 🔌 Wiring Diagram

![Wiring Diagram](hardware/wiring.png)

<!-- TODO: Crear y agregar diagrama de conexiones -->

**Important Notes:**
- Use appropriate pull-up/pull-down resistors
- Ensure proper voltage levels (3.3V vs 5V)
- Add filtering capacitors near sensors to reduce noise
- <!-- TODO: Otras notas importantes de conexión -->

## 🖨️ 3D Printed Parts

All STL files are available in the [`hardware/3d_models/`](hardware/3d_models/) directory.

### Parts List

<!-- TODO: Listar cada pieza impresa -->
- Main housing
- Sensor mounts
- Radiation shield (for temperature accuracy)
- Rain gauge funnel
- <!-- TODO: Completar lista de piezas -->

### Print Settings

- **Layer Height:** 0.2mm
- **Infill:** 20-30%
- **Material:** PETG or ASA (outdoor resistant)
- **Supports:** <!-- TODO: indicar si/no y dónde -->
- **Notes:** <!-- TODO: consideraciones especiales -->

<!-- TODO: Agregar configuraciones específicas de impresión -->

## 📥 Installation

### 1. Hardware Assembly

1. Print all 3D parts
2. Solder components according to wiring diagram
3. Assemble weather station housing
4. Mount sensors in appropriate locations
5. Connect ESP32 to power supply

<!-- TODO: Expandir con pasos más detallados -->

### 2. ESPHome Setup

#### Prerequisites

- [ESPHome](https://esphome.io/) installed
- Home Assistant running (optional but recommended)
- USB cable for initial flash

#### Steps

1. Clone this repository:
```bash
git clone https://github.com/YOUR_USERNAME/weather-station-esp32.git
cd weather-station-esp32
```

2. Copy the secrets template:
```bash
cp secrets.yaml.example secrets.yaml
```

3. Edit `secrets.yaml` with your credentials:
```yaml
wifi_ssid: "Your_WiFi_SSID"
wifi_password: "Your_WiFi_Password"
# ... etc
```

4. Adjust `weather_station.yaml` settings:
   - Static IP address (or comment out for DHCP)
   - Device name
   - Calibration values (see [Calibration](#calibration) section)

5. Compile and upload:
```bash
esphome run weather_station.yaml
```

6. After first flash, use OTA updates for future changes

## ⚙️ Configuration

### Static IP vs DHCP

By default, the configuration uses a static IP. To use DHCP instead, comment out the `manual_ip` section:

```yaml
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  # manual_ip:
  #   static_ip: 192.168.1.XXX
  #   ...
```

### Update Intervals

Adjust sensor update intervals based on your needs:
- Battery monitoring: 10 minutes (default)
- Temperature/Humidity: 15 minutes (default)
- Wind/Rain: 60 seconds (real-time)

Lower intervals = more frequent updates but higher power consumption.

### MQTT (Optional)

To enable MQTT, uncomment the MQTT section in `weather_station.yaml` and add credentials to `secrets.yaml`.

## 🎯 Calibration

### Soil Moisture Sensor

**Important:** Calibration is sensor-specific and critical for accuracy.

**Current configuration (DECAGON 10HS):**
- Dry (in air): 0.40V = 0%
- Wet (in water): 1.12V = 100%

**To calibrate your sensor:**

1. Place sensor in air (completely dry):
```bash
# Check ESPHome logs for voltage reading
esphome logs weather_station.yaml
```

2. Place sensor in water (completely saturated):
   - Note the voltage reading

3. Update calibration in YAML:
```yaml
- calibrate_linear:
    - YOUR_DRY_VOLTAGE -> 0
    - YOUR_WET_VOLTAGE -> 100
```

<!-- TODO: Agregar fotos del proceso de calibración -->

### Wind Speed

The anemometer calibration depends on your specific model:
- Current factor: 0.0111156 × 3.6 (for km/h)
- Adjust based on manufacturer specifications

<!-- TODO: Documentar proceso de calibración del anemómetro -->

### Rain Gauge

- Current: 0.375mm per tip
- Adjust based on your rain gauge specifications (commonly 0.2794mm or 0.375mm)

### Battery Monitor

Voltage divider ratio: 7.3037037

**To calibrate:**
1. Measure actual battery voltage with multimeter
2. Compare with sensor reading
3. Adjust multiplier: `new_multiplier = (actual_voltage / sensor_reading) * current_multiplier`

## 🏠 Home Assistant Integration

### Automatic Discovery

Once ESPHome device is running, it will automatically appear in Home Assistant:

1. Go to **Settings** → **Devices & Services**
2. ESPHome integration should show your weather station
3. Click **Configure** and enter API encryption key

### Creating Dashboards

Example Lovelace card configuration:

```yaml
# TODO: Agregar ejemplo de tarjeta de Lovelace
```

### Automations

Example automation for irrigation based on soil moisture:

```yaml
# TODO: Agregar ejemplos de automatizaciones
```

## 📊 Lessons Learned (4+ Years of Operation)

### What Worked Well

<!-- TODO: Documentar experiencias positivas -->
- Sensor reliability
- Weather resistance of enclosure
- Battery life
- etc.

### Challenges Overcome

<!-- TODO: Documentar problemas resueltos -->
- Initial calibration issues
- Weather-related failures
- Improvements made over time
- etc.

### Recommendations

<!-- TODO: Consejos para quien replique el proyecto -->
- Use PETG or ASA for outdoor parts
- Add proper ventilation but protect from rain
- Regular maintenance schedule
- etc.

### Design Evolution

<!-- TODO: Cómo ha evolucionado el diseño en 4 años -->
- Version 1.0: Initial design
- Version 2.0: Improvements based on first year
- Current version: Refined after 4 years
- etc.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

<!-- TODO: Agregar guidelines de contribución si deseas -->

## 📝 TODO / Future Improvements

- [ ] <!-- TODO: Lista de mejoras futuras -->
- [ ] Add UV sensor
- [ ] Solar panel integration
- [ ] Wind direction sensor
- [ ] etc.

## 📄 License

<!-- TODO: Elegir una licencia -->
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

Alternatively, you can choose:
- GPL-3.0 (if you want derivatives to be open source)
- CC BY-SA 4.0 (for hardware/documentation)
- Apache 2.0

## 🙏 Acknowledgments

- ESPHome community
- Home Assistant community
- <!-- TODO: Agregar créditos si usaste referencias de otros proyectos -->

## 📧 Contact

<!-- TODO: Agregar información de contacto si deseas -->
- GitHub: [@YOUR_USERNAME](https://github.com/YOUR_USERNAME)
- Home Assistant Community: [Link to forum post]

---

**Note:** This project has been tested and refined over 4+ years of continuous outdoor operation. While the design is proven, always consider your local climate and conditions when replicating.

## 🖼️ Gallery

<!-- TODO: Agregar más fotos -->
![Assembly](docs/images/assembly.jpg)
![Installed](docs/images/installed.jpg)
![Dashboard](docs/images/dashboard.png)