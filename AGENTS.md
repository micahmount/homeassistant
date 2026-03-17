# AGENTS.md - Home Assistant HACS Custom Component

## Project Overview

This is the **HACS (Home Assistant Community Store)** custom component -
a Docker Compose based Home Assistant project.

## Directory Structure

```text
/home/micah/code/homeassistant/
├── ha/config/                          # Home Assistant config
│   └── custom_components/hacs/         # HACS custom component
├── esphome/                            # ESPHome configurations
└── mosquitto/                          # MQTT broker config
```

## Build/Lint/Test Commands

### Docker Compose

```bash
# Start all containers
docker compose up -d

# Stop all containers
docker compose down

# Restart Home Assistant (to reload HACS changes)
docker compose restart homeassistant

# View HACS logs
docker compose logs -f homeassistant | grep HACS

# Shell into Home Assistant container
docker compose exec homeassistant /bin/bash
```

### Linting

```bash
# Using ruff (recommended for HA projects)
ruff check custom_components/hacs/

# Format code
ruff format custom_components/hacs/
```

### Testing

No unit tests currently exist. When added:

```bash
# Run all tests
pytest tests/ -v

# Run a single test file
pytest tests/test_file.py -v

# Run a single test function
pytest tests/test_file.py::test_function_name -v

# Run tests matching a pattern
pytest -k "test_pattern" -v
```

## Code Style Guidelines

### General Conventions

1. **Always use `from __future__ import annotations`** at the top of every
   Python file
2. **Use `TYPE_CHECKING`** block for imports that cause circular dependencies
3. **Maximum line length**: 88 characters (Black-compatible)
4. **Use 4 spaces for indentation**

### Import Organization

```python
from __future__ import annotations

import asyncio
from collections.abc import Awaitable, Callable
from dataclasses import dataclass
from datetime import timedelta
import os
from typing import TYPE_CHECKING, Any

# Third-party imports
from aiogithubapi import AIOGitHubAPIException
from aiohttp.client import ClientSession
from awesomeversion import AwesomeVersion
from homeassistant.components.frontend import async_remove_panel
from homeassistant.config_entries import ConfigEntry
from homeassistant.const import Platform
from homeassistant.core import HomeAssistant

# Local application imports
from .base import HacsBase
from .const import DOMAIN, HACS_SYSTEM_ID
from .enums import HacsCategory, HacsStage
from .exceptions import HacsException
from .utils.logger import LOGGER
```

### Type Hints

- Use Python 3.10+ union syntax: `str | None` (not `Optional[str]`)
- Use `dict[str, Any]` (not `Dict[str, Any]`)
- Use `TypedDict` for structured dictionaries
- Use `TYPE_CHECKING` to avoid circular imports

```python
from typing import TYPE_CHECKING, TypedDict

if TYPE_CHECKING:
    from .base import HacsBase

class MyDict(TypedDict):
    key: str
    value: int
```

### Naming Conventions

- **Classes**: `PascalCase` (e.g., `HacsBase`, `HacsDataClient`)
- **Functions/variables**: `snake_case` (e.g., `async_initialize`)
- **Constants**: `UPPER_SNAKE_CASE` (e.g., `DOMAIN`, `DEFAULT_CONCURRENT_TASKS`)
- **Enums**: Use `StrEnum` (inherits from `str` and `Enum`)

### Error Handling

1. Use custom exceptions inheriting from `HacsException`:

   ```python
   class HacsException(Exception): pass
   class HacsNotModifiedException(HacsException): pass
   ```

2. Use exception chaining with `from`:

   ```python
   except TimeoutError:
       raise HacsException("Timeout of 60s reached") from None
   except Exception as exception:
       raise HacsException(f"Error: {exception}") from exception
   ```

3. Use logging for expected errors:

   ```python
   LOGGER.info("Got invalid data for %s (%s)", repo_name, exception)
   ```

### Async/Await Patterns

- Use `async def` for all I/O operations
- Use `asyncio.Semaphore` for concurrency control
- Use `@concurrent` decorator for rate limiting

### Logging

Use the module-level logger pattern:

```python
from .utils.logger import LOGGER
LOGGER.info("Starting HACS version %s", version)
```

### Home Assistant Specific Patterns

1. **Platform enum**: `from homeassistant.const import Platform`
2. **ConfigFlow**:
  `async def async_setup_entry(hass: HomeAssistant, entry: ConfigEntry) -> bool`
3. **Issue registry**:
  `from homeassistant.helpers.issue_registry import IssueSeverity, async_create_issue`
4. **Dispatch signals**: `from homeassistant.helpers.dispatcher import async_dispatcher_send`

### Validation

Use `voluptuous` for configuration and data validation:

```python
import voluptuous as vol
VALIDATE_SCHEMA = vol.Schema({vol.Required("name"): str})
```

### Common Dependencies

- `aiogithubapi` - GitHub API client
- `aiohttp` - Async HTTP client
- `awesomeversion` - Version parsing
- `voluptuous` - Data validation
- `homeassistant` - HA core
- `attr` - Class definitions

## Notes for Agents

- This is a Home Assistant custom component running inside the HA event loop
- Changes require Home Assistant restart to take effect
- Follow Home Assistant Developer Guidelines: <https://developers.home-assistant.io/>
- Use `LOGGER` from `utils/logger.py` for logging
