# Home Assistant Stack

Docker Compose based Home Assistant setup with three services:

- **Home Assistant** - Core home automation platform
- **ESPHome** - ESP32/ESP8266 device management
- **Mosquitto** - MQTT broker

## Two-Machine Setup

This repo is designed to run on both your **server** (homelab) and
**laptop** (for flashing).

| Machine | Services | Purpose |
| ------- | --------- | -------- |
| Server (homelab) | HA + Mosquitto + ESPHome | Running HA, dashboard access |
| Laptop | ESPHome only | Flashing devices via USB |

## Quick Start

### Server (Homelab)

```bash
# Start HA + Mosquitto (unified compose)
docker compose up -d

# Start ESPHome
cd esphome && docker compose up -d
```

### Laptop (Flashing)

```bash
cd esphome && docker compose up -d
```

## Services

| Service | URL | Description |
| ------- | --- | ----------- |
| Home Assistant | http://localhost:8123 | Main HA UI |
| ESPHome | http://localhost:6052 | Device flashing dashboard |
| Mosquitto | localhost:1883 | MQTT broker |

## ESPHome

Store device configs in `esphome/` (version controlled).

**Flashing workflow:**

1. On **server**: `docker compose -f esphome/compose.yml up -d`
2. On **laptop**: `docker compose -f esphome/compose.yml up -d`
3. Edit configs in `esphome/` locally (sync to server)
4. Open ESPHome dashboard at server's IP (e.g., `http://192.168.1.x:6052`)
5. Click the device → **Install** → select connected USB device
6. ESP32 connects to your WiFi and auto-discovers in Home Assistant

## Configuration

- HA config: `server/ha/config/`
- ESPHome configs: `esphome/`
- MQTT config: `server/mosquitto/config/`

## Useful Commands

```bash
# Server: start all services
docker compose up -d
cd esphome && docker compose up -d

# Laptop: start ESPHome only
cd esphome && docker compose up -d

# Restart Home Assistant (to reload HACS changes)
docker compose restart homeassistant

# View logs
docker compose logs -f homeassistant
cd esphome && docker compose logs -f esphome
```

## Notes

- HACS auto-installs on first HA startup
- See `AGENTS.md` for development guidelines