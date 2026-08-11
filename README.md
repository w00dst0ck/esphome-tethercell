# ESPHome Tethercell

[PLACEHOLDER: Tethercell device image]

An ESPHome integration for controlling and monitoring **Tethercell batteries over Bluetooth Low Energy (BLE)**.

The project was created to give unused Tethercell batteries a second life by integrating them into an **ESPHome and Home Assistant** environment. An ESP32 with BLE support connects directly to the Tethercell, handles authentication, controls its internal FET, and reads the battery voltage.

The integration is provided as a reusable ESPHome package, making it possible to control one or multiple Tethercells from a single ESP32.

## Features

* 🔵 Bluetooth Low Energy communication
* 🔐 Tethercell PIN authentication
* 🔌 Remote control of the internal FET
* 🔋 Battery voltage monitoring
* 🏠 Native ESPHome integration
* 🏡 Home Assistant integration through ESPHome
* 🔁 Support for multiple Tethercells from a single ESP32
* 📦 Reusable ESPHome package

## How It Works

The Tethercell is a battery adapter that can remotely control the power output of a connected battery.

This project uses an ESP32 as a BLE client. The ESP32 connects to the Tethercell using its Bluetooth MAC address and authenticates using the configured Tethercell PIN.

Once connected and authorized, the ESP32 can communicate with the Tethercell's BLE service.

The current implementation provides two main functions:

1. **FET control** – turn the Tethercell's battery output on or off.
2. **Battery voltage monitoring** – read and expose the current battery voltage.

The resulting entities are automatically available through ESPHome and can therefore be used in Home Assistant dashboards and automations.

## Supported Functions

### FET Control

The Tethercell's internal FET can be controlled using an ESPHome switch.

**ON**

Enables the Tethercell's battery output.

**OFF**

Disables the Tethercell's battery output.

This makes it possible to use a Tethercell as a remotely controlled power source and to integrate it into Home Assistant automations.

### Battery Voltage

The Tethercell battery voltage is exposed as an ESPHome sensor.

The voltage is read over BLE and converted to volts using the same conversion formula used by the original Tethercell Node.js implementation.

The sensor currently updates every **5 minutes**.

Example:

```text
Tethercell 1 Battery voltage: 1.42 V
```

## Requirements

You need:

* An **ESP32** with Bluetooth Low Energy support
* One or more **Tethercell** devices
* The Bluetooth MAC address of each Tethercell
* The PIN of each Tethercell
* ESPHome
* Home Assistant (optional, but recommended)

The ESP32 must be within Bluetooth range of the Tethercell.

## ESPHome Configuration

The integration is designed to be included as an ESPHome package.

First, enable the ESP32 BLE tracker:

```yaml
esp32_ble_tracker:
```

Then include the Tethercell package:

```yaml
packages:
  tethercell1:
    url: https://github.com/w00dst0ck/esphome-tethercell
    files:
      - path: esphome_tethercell_generic.yaml
        vars:
          tethercell_number: 1
          tethercell_mac: 88:33:14:49:XX:XX
          tethercell_pin: 00000000
    ref: main
    refresh: 1d
```

Replace the following values with the information from your Tethercell:

| Parameter           | Description                                      |
| ------------------- | ------------------------------------------------ |
| `tethercell_number` | A unique number used to identify the Tethercell  |
| `tethercell_mac`    | The Bluetooth MAC address of the Tethercell      |
| `tethercell_pin`    | The PIN used to authenticate with the Tethercell |

### Multiple Tethercells

Multiple Tethercells can be controlled by including the package multiple times.

Each instance needs its own unique `tethercell_number`, MAC address, and PIN.

Example:

```yaml
esp32_ble_tracker:

packages:
  tethercell1:
    url: https://github.com/w00dst0ck/esphome-tethercell
    files:
      - path: esphome_tethercell_generic.yaml
        vars:
          tethercell_number: 1
          tethercell_mac: 88:33:14:49:XX:XX
          tethercell_pin: 00000000
    ref: main
    refresh: 1d

  tethercell2:
    url: https://github.com/w00dst0ck/esphome-tethercell
    files:
      - path: esphome_tethercell_generic.yaml
        vars:
          tethercell_number: 2
          tethercell_mac: 88:33:14:49:XX:XX
          tethercell_pin: 00000000
    ref: main
    refresh: 1d
```

## Home Assistant

When the ESPHome device is connected to Home Assistant, each configured Tethercell exposes its entities automatically.

For each Tethercell, the current implementation provides:

| Entity                         | Type   | Description                         |
| ------------------------------ | ------ | ----------------------------------- |
| `Tethercell X FET`             | Switch | Turns the battery output on or off  |
| `Tethercell X Battery voltage` | Sensor | Reports the current battery voltage |

This allows Tethercells to be used directly in Home Assistant dashboards, scripts, and automations.

For example, a Tethercell could be switched on only when another condition is met, or its battery voltage could be monitored to determine when the battery needs to be replaced.

## Bluetooth Communication

The ESPHome implementation communicates directly with the Tethercell using its BLE service.

The following UUIDs are currently defined by the integration:

| UUID                                   | Purpose                           |
| -------------------------------------- | --------------------------------- |
| `5EC0FFF0-3CF2-A682-E211-2AF96EFDF667` | Tethercell service                |
| `5EC0FFF1-3CF2-A682-E211-2AF96EFDF667` | Family                            |
| `5EC0FFF2-3CF2-A682-E211-2AF96EFDF667` | FET state                         |
| `5EC0FFF3-3CF2-A682-E211-2AF96EFDF667` | Battery voltage                   |
| `5EC0FFF4-3CF2-A682-E211-2AF96EFDF667` | Timers                            |
| `5EC0FFF5-3CF2-A682-E211-2AF96EFDF667` | Timer access index                |
| `5EC0FFF6-3CF2-A682-E211-2AF96EFDF667` | Most recent battery voltage index |
| `5EC0FFF7-3CF2-A682-E211-2AF96EFDF667` | Battery voltage history           |
| `5EC0FFF8-3CF2-A682-E211-2AF96EFDF667` | Password                          |
| `5EC0FFF9-3CF2-A682-E211-2AF96EFDF667` | Device name                       |
| `5EC0FFFA-3CF2-A682-E211-2AF96EFDF667` | UTC time                          |
| `5EC0FFFB-3CF2-A682-E211-2AF96EFDF667` | Advertising period                |
| `5EC0FFFC-3CF2-A682-E211-2AF96EFDF667` | Authorization                     |

Not all of these characteristics are currently exposed as ESPHome entities. They are included in the configuration because they are part of the Tethercell BLE interface and may be useful for future development.

## Authentication

The Tethercell requires authentication before its service can be used.

The configured PIN is sent to the authorization characteristic when the ESP32 connects to the Tethercell. Authentication is also performed again before changing the FET state.

The PIN is configured as part of the ESPHome package:

```yaml
vars:
  tethercell_pin: 00000000
```

**Do not commit your personal Tethercell PINs to a public repository.**

If you are sharing your ESPHome configuration, replace the actual PIN with a placeholder.

## Project Structure

The repository currently contains the reusable ESPHome configuration:

```text
esphome-tethercell/
├── esphome_tethercell_generic.yaml
├── README.md
└── LICENSE
```

The `esphome_tethercell_generic.yaml` file contains the BLE client configuration, authentication logic, battery voltage sensor, and FET switch.

## Credits & Attribution

This project builds on the excellent groundwork provided by **Sandeep Mistry** in the original [`node-tethercell`](https://github.com/sandeepmistry/node-tethercell) project.

The original project was a Node.js library for communicating with the Tethercell and provided important information about the Tethercell's Bluetooth Low Energy interface. It implements functionality for discovering and connecting to Tethercells, authenticating with a PIN, controlling the FET, and reading the battery voltage.

The BLE service and characteristic UUIDs used by this ESPHome integration are based on the UUID definitions from the original `node-tethercell` project. The battery voltage conversion and FET control behavior also follow the protocol information implemented there.

Many thanks to **Sandeep Mistry** for reverse-engineering and documenting the Tethercell BLE interface and for making this work available to the community.

Original project:

https://github.com/sandeepmistry/node-tethercell

The original `node-tethercell` project is released under the **MIT License**.

This ESPHome implementation is a separate project and is not affiliated with, sponsored by, or endorsed by Sandeep Mistry.

## Project Status

This project is currently focused on the basic functionality required to integrate Tethercells into ESPHome and Home Assistant.

### Currently implemented

* BLE connection
* PIN authentication
* FET ON/OFF control
* Battery voltage monitoring
* Multiple Tethercell support
* ESPHome package configuration

### Potential future improvements

The Tethercell exposes additional BLE characteristics that are not yet implemented as ESPHome entities.

Possible future improvements include:

* Reading and displaying the current FET state
* Device name support
* Timer support
* Battery voltage history
* Additional Tethercell configuration options
* Improved connection and error handling
* Further investigation of the Tethercell BLE protocol

Contributions, testing, and additional protocol information are welcome.

## Disclaimer

This project is provided **as-is** and is intended for experimentation, reuse, and integration with ESPHome.

The authors of this project are not responsible for damage resulting from the use or misuse of this software or hardware.

## License

This project is released under the **MIT License**.

See [`LICENSE`](LICENSE) for the full license text.

## Links

* **This project:** https://github.com/w00dst0ck/esphome-tethercell
* **Original node-tethercell project:** https://github.com/sandeepmistry/node-tethercell
* **ESPHome:** https://esphome.io/
* **Home Assistant:** https://www.home-assistant.io/

---

*Made to give old Tethercells a new life with ESPHome and Home Assistant.*
