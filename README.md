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
| Server (homelab) | HA + ESPHome + Mosquitto | Running HA, dashboard access |
| Laptop | ESPHome only | Flashing devices via USB |

## Quick Start

### Server (Homelab)

```bash
docker compose up -d homeassistant esphome mosquitto
```

### Laptop (Flashing)

```bash
docker compose up -d esphome
```

## Services

| Service | URL | Description |
| ------- | --- | ----------- |
| Home Assistant | <http://localhost:8123> | Main HA UI |
| ESPHome | <http://localhost:6052> | Device flashing dashboard |
| Mosquitto | localhost:1883 | MQTT broker |

## ESPHome

Store device configs in `esphome/` (version controlled).

**Flashing workflow:**

1. On **server**: ensure ESPHome container is running
2. On **laptop**: `docker compose up -d esphome`
3. Edit configs in `esphome/` locally (sync to server)
4. Open ESPHome dashboard at server's IP (e.g., `http://192.168.1.x:6052`)
5. Click the device → **Install** → select connected USB device
6. ESP32 connects to your WiFi and auto-discovers in Home Assistant

## Configuration

- HA config: `ha/config/`
- ESPHome configs: `esphome/`
- MQTT config: `mosquitto/config/`

## Useful Commands

```bash
# Server: start all services
docker compose up -d homeassistant esphome mosquitto

# Laptop: start ESPHome only
docker compose up -d esphome

# Restart Home Assistant (to reload HACS changes)
docker compose restart homeassistant

# View logs
docker compose logs -f homeassistant
docker compose logs -f esphome
```

## Notes

- HACS auto-installs on first HA startup
- See `AGENTS.md` for development guidelines
