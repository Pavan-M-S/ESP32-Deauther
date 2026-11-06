# ESP32 Wi-Fi Deauther

[![Platform](https://img.shields.io/badge/Platform-PlatformIO-orange.svg)](https://platformio.org/)
[![Hardware](https://img.shields.io/badge/Hardware-ESP32--WROOM--32-blue.svg)](https://www.espressif.com/)
[![Framework](https://img.shields.io/badge/Framework-Arduino-green.svg)](https://www.arduino.cc/)
[![License](https://img.shields.io/badge/License-GPL%20v3-red.svg)](https://www.gnu.org/licenses/gpl-3.0)

An 802.11 management frame manipulation and deauthentication penetration testing utility developed for the ESP32 microcontroller using PlatformIO and the Arduino framework. The system creates a standalone Access Point with an embedded web-based control panel, enabling authorized security researchers and network administrators to audit 2.4GHz Wi-Fi networks against 802.11 deauthentication attacks.

> [!CAUTION]
> **DISCLAIMER & LEGAL NOTICE**: This tool is designed exclusively for educational, research, and authorized security auditing purposes on equipment you own or have explicit written permission to test. Transmitting unauthorized deauthentication frames against third-party networks violates wireless telecommunication regulations and local laws. The author assumes no liability for misuse.

---

## Features

- **SoftAP Captive Portal**: Hosts a standalone Wi-Fi Access Point (`ESP32-Deauther` / `esp32wroom32`) hosting an embedded HTTP control interface on port `80` (default IP: `192.168.4.1`).
- **Network Reconnaissance (`/rescan`)**: Performs synchronous 2.4GHz 802.11b/g/n scans across channels 1–13, displaying BSSID, SSID, RSSI, channel, and encryption scheme (Open, WEP, WPA, WPA2).
- **Targeted Deauthentication (`/deauth`)**: Puts the ESP32 Wi-Fi interface into promiscuous packet-sniffing mode to capture connected client MAC addresses for a selected target AP and injects raw IEEE 802.11 deauth frames (`esp_wifi_80211_tx`).
- **All-Channel Broadcast Sweep (`/deauth_all`)**: Systematically cycles through channels 1 to 13, broadcasting deauthentication frames across all detected networks.
- **Configurable Reason Codes**: Supports standard IEEE 802.11 reason codes (1 through 24) to test client reconnection handling.
- **Visual Status Feedback**: Blinks the built-in LED (GPIO 2) during active packet injection.

---

## Hardware & Pinout

| Function / Component | ESP32 GPIO | Description |
| :--- | :--- | :--- |
| **Status LED** | `GPIO 2` | Built-in Blue LED (flashes during frame transmission) |
| **Wi-Fi Radio** | Internal RF | 2.4 GHz 802.11b/g/n internal transceiver |
| **UART Serial** | `GPIO 1` (TX), `GPIO 3` (RX) | Debug logging at 115200 baud |

---

## PlatformIO Configuration

From [`platformio.ini`](platformio.ini):

```ini
[env:esp32doit-devkit-v1]
platform = espressif32
board = esp32doit-devkit-v1
framework = arduino
build_flags = -Wl,-z,muldefs
monitor_speed = 115200
upload_speed = 921600
```

> [!NOTE]
> The build flag `-Wl,-z,muldefs` allows overriding internal ESP-IDF Wi-Fi symbol definitions required for low-level frame transmission (`ieee80211_raw_frame_sanity_check`).

---

## Getting Started

### 1. Build and Flash
Clone or open this repository in VS Code with PlatformIO installed:

```bash
# Build firmware
pio run

# Flash to connected ESP32
pio run --target upload

# Monitor serial diagnostics
pio device monitor
```

### 2. Operation Workflow
1. Power on the ESP32.
2. Connect your computer or smartphone to the Wi-Fi network:
   - **SSID**: `ESP32-Deauther`
   - **Password**: `esp32wroom32`
3. Open a browser and navigate to `http://192.168.4.1`.
4. Click **Scan Networks** to populate nearby access points.
5. Select a target network ID, select an IEEE 802.11 reason code, and press **Launch Deauth-Attack**.
6. To stop an active attack, click **Stop Deauth-Attack**.

---

## Project Structure

```
ESP32-Deauther/
├── include/
│   ├── deauth.h          # Deauthentication routine headers & packet structures
│   ├── definitions.h     # Network credentials, channel & pin configurations
│   ├── types.h           # IEEE 802.11 frame struct definitions
│   └── web_interface.h   # WebServer route definitions & HTML templates
├── src/
│   ├── deauth.cpp        # Promiscuous sniffer callback & raw frame transmission
│   ├── general.cpp       # LED signaling and diagnostic utilities
│   ├── main.cpp          # System setup, AP init, and event loop
│   └── web_interface.cpp # HTTP request handlers and dynamic UI generation
└── platformio.ini         # PlatformIO build configuration
```

---

## License

This project is licensed under the **GNU General Public License v3.0 (GPL-3.0)**.
