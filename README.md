# Home Assistant with HACS

Docker Compose based Home Assistant setup with HACS, ESPHome, and Mosquitto.

## Quick Start

```bash
docker compose up -d
```

Access Home Assistant at <http://localhost:8123>

## Services

- **Home Assistant**: <http://localhost:8123>
- **ESPHome**: <http://localhost:6052>
- **Mosquitto MQTT**: localhost:1883

## Configuration

- HA config: `ha/config/`
- ESPHome configs: `esphome/`
- MQTT config: `mosquitto/config/`

## Useful Commands

```bash
# Restart HA to reload HACS
docker compose restart homeassistant

# View logs
docker compose logs -f homeassistant
```

## Notes

- HACS auto-installs on first HA startup
- See `AGENTS.md` for development guidelines
