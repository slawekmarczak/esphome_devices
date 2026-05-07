# Starting A New Device Project

Use this repo for production ESPHome device projects. Use the sibling
`~/esphome_agent` repo for compile, flash, reset, logs, and testing.

## Create The Device

```bash
DEVICE=my-device-name
cp -r ~/esphome_agent/templates/esphome_devices/devices/example-device \
  ~/esphome_devices/devices/$DEVICE
```

Then edit:

```text
devices/<device-name>/PROJECT.md
devices/<device-name>/device.yaml
devices/<device-name>/hardware.md
devices/<device-name>/README.md
devices/<device-name>/test-log.md
```

## First Agent Prompt

```text
Pracuj nad devices/<device-name>.
Przeczytaj AGENTS.md oraz devices/<device-name>/PROJECT.md.
Działaj autonomicznie przez flashpoint/MCP flow z ~/esphome_agent.
Nie instaluj lokalnego PlatformIO ani lokalnego środowiska ESPHome.
```

## Validation Flow

The MCP path for the YAML is:

```text
/projects/devices/<device-name>/device.yaml
```

The agent should compile, flash, reset, read logs, and check API/web through
`~/esphome_agent` MCP endpoints. It should not build locally in this repo.

