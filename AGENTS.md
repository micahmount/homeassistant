# AGENTS.md - Home Assistant Stack

## Directory Structure

```
/home/micah/code/homeassistant/
├── server/                        # Server-side components
│   ├── ha/                        # Home Assistant config
│   │   └── config/                # HA config (mounted to /config in container)
│   └── mosquitto/                 # MQTT broker
├── esphome/                       # ESPHome configs (laptop use)
└── compose.yml                    # Unified compose for server services
```

## Docker Commands

```bash
# Server: start all services
docker compose up -d

# Restart HA (to reload HACS changes)
docker compose restart homeassistant

# View HA logs
docker compose logs -f homeassistant
```

## Lint

Run inside container:

```bash
docker compose exec homeassistant ruff check server/ha/config/custom_components/hacs/
```

## Notes for Agents

- This is a Home Assistant custom component running inside the HA event loop
- HACS source is in `server/ha/config/custom_components/hacs/`
- Changes require HA restart to take effect
- Follow HA Developer Guidelines: https://developers.home-assistant.io/