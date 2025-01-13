# SashaWeather: ESP32-S2 Climate Sensor Integration with Home Assistant

## Description
This project aims to integrate various environmental sensors with Home Assistant using an ESP32-S2 microcontroller. Currently, it includes a BME280 sensor for temperature, humidity, and pressure readings, with plans to add 5V hall sensors for anemometer, wind vane, and rain sensor functionalities via I2C converters.

## Project Structure
```
/esp32_s2_climate_sensors
|-- /src
|   |-- main.cpp
|-- /docs
|   |-- README.md
|-- /config
|   |-- home_assistant.yaml
|-- platformio.ini
|-- .gitignore
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

4. Connect the ESP32-S2 to your development environment and upload the initial code:
```bash
pio run --target upload
```

## Current Functionality
- **BME280 Sensor**: Communicates over SPI to provide temperature, humidity, and pressure data.

## Adding Hall Sensors
1. Connect the hall sensors to the ESP32-S2 using I2C converters to allow 5V sensors.
2. Update the wiring diagram in `/docs/wiring_diagram.png`.
3. Modify `main.cpp` to include support for additional sensors:

```cpp
#include <Wire.h>
#include <Adafruit_BME280.h>

Adafruit_BME280 bme;

void setup() {
    Serial.begin(115200);

    if (!bme.begin(0x76)) {
        Serial.println("Could not find a valid BME280 sensor!");
        while (1);
    }
}

void loop() {
    Serial.print("Temperature = ");
    Serial.print(bme.readTemperature());
    Serial.println(" *C");

    delay(2000);
}
```

4. Add necessary Home Assistant YAML configuration to `/config/home_assistant.yaml`.
```yaml
sensor:
  - platform: mqtt
    name: "Temperature"
    state_topic: "home/climate/temperature"
```
