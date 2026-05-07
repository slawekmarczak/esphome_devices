# ESPHome Devices

Production ESPHome device configurations.

The tooling pipeline lives in the sibling repository:

```text
~/esphome_agent
```

Agents working in this repository must read `AGENTS.md` first. They should use
the MCP/build/flash pipeline from `~/esphome_agent` and must not bootstrap a
local PlatformIO environment here.

This repository is the GitHub-backed device repo. The `origin` remote points to
`git@github.com:slawekmarczak/esphome_devices.git`.

## Layout

```text
common/
  packages/
  substitutions/
devices/
  <device-name>/
    PROJECT.md
    device.yaml
    README.md
    hardware.md
    test-log.md
secrets.example.yaml
```

## Device Projects

Each device has its own directory under `devices/`. The device directory keeps
all project-specific requirements, YAML, wiring, test notes, and user-facing
documentation.

Reusable module or board knowledge belongs in `~/esphome_agent/Modules`, not in
this repository.

## Templates

Copy new device projects from:

```text
templates/device-template/
```

The local template mirrors the expected device layout and keeps the project
structure visible inside this repository.
# esphome_devices
