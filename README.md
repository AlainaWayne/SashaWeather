# SashaWeather: ESP32-S2 Climate Sensor Integration with Home Assistant

## Description
This project aims to integrate various environmental sensors with Home Assistant using an ESP32-S2 microcontroller. Currently, it includes a BME280 sensor for temperature, humidity, and pressure readings, with plans to add 5V hall sensors for anemometer, wind vane, and rain sensor functionalities via I2C converters.

## Project Structure
```
/esp32_s2_climate_sensors
|-- /src
|   |-- main.py
|   |-- bme280_spi.py
|-- /docs
|   |-- README.md
|-- /config
|   |-- sashaweather.yaml
|-- platformio.ini
|-- LICENSE
|-- .gitignore
|-- secrets.yaml
```

## Initial Setup
1. Clone this repository:
```bash
git clone <repository_url>
cd esp32_s2_climate_sensors
```

2. Open the project in [PlatformIO](https://platformio.org/):
```bash
code .
```

3. Configure the `platformio.ini` file with the ESP32-S2 details:
```ini
[env:esp32s2]
platform = espressif32
board = esp32dev
framework = arduino
lib_deps =
    adafruit/Adafruit BME280 Library @ ^2.1.2
    adafruit/Adafruit Unified Sensor @ ^1.1.4
    esphome/esphome @ ^2025.1.0
```

4. Create a `secrets.yaml` file to store sensitive information:
```yaml
wifi_ssid: "YourWiFiSSID"
wifi_password: "YourWiFiPassword"
api_encryption_key: "YourEncryptionKey"
ota_password: "YourOTAPassword"
fallback_hotspot_ssid: "FallbackHotspotSSID"
fallback_hotspot_password: "FallbackHotspotPassword"
```

5. Add the `secrets.yaml` file to the `.gitignore` to avoid committing secrets:
```
# Ignore secrets
secrets.yaml
```

6. Update `/config/sashaweather.yaml` to reference the secrets file:
```yaml
esphome:
  name: sashaweather
  friendly_name: SashaWeather

esp32:
  board: esp32-s2-saola-1
  framework:
    type: arduino

logger:

api:
  encryption:
    key: !secret api_encryption_key

ota:
  platform: esphome
  password: !secret ota_password

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

  manual_ip:
    static_ip: 10.0.0.21
    gateway: 10.0.0.1
    subnet: 255.255.255.0

  ap:
    ssid: !secret fallback_hotspot_ssid
    password: !secret fallback_hotspot_password

captive_portal:
```

7. Connect the ESP32-S2 to your development environment and upload the initial code:
```bash
pio run --target upload
```

## Current Functionality
- **BME280 Sensor**: Communicates over SPI to provide temperature, humidity, and pressure data.
- **ESPHome Integration**: Connects the ESP32-S2 to Home Assistant via Wi-Fi, allowing seamless integration of climate data.

## Adding Hall Sensors
1. Connect the hall sensors to the ESP32-S2 using I2C converters to allow 5V sensors.
2. Update the wiring diagram in `/docs/wiring_diagram.png`.
3. Modify `main.py` to include support for additional sensors.

## Files Overview
### `/src/main.py`
```python
from machine import Pin, SPI
from bme280_spi import BME280_SPI
from utime import sleep

def main():
    spi = SPI(1, baudrate=1000000, sck=Pin(16), mosi=Pin(14), miso=Pin(15))
    bme = BME280_SPI(spi, cs=Pin(13))

    while True:
        try:
            t, p, h = bme.read()
            print(f"Temperature: {t:.2f} °C, Pressure: {p:.2f} hPa, Humidity: {h:.2f} %RH")
            sleep(1)
        except Exception as e:
            print(f"Error: {e}")
            break

if __name__ == "__main__":
    main()
```

### `/src/bme280_spi.py`
```python
from machine import Pin, SPI
from utime import sleep_ms
from struct import unpack

DEFAULT_ADDRESS = 0xEC >> 1  # Default I2C address for BME280

class BME280_base:
    """
    A class to operate a combined humidity and pressure sensor: BME280
    """
    ...
```
*See full implementation in the repository.*
