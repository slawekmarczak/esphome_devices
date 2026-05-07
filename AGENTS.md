# ESPHome Devices — Agent Instructions

## Role

You work on production ESPHome device configurations.

This repository is for device projects only. The build, flash, test, MCP,
Raspberry Pi flashpoint, and ESPHome LXC pipeline lives in the sibling tooling
repository:

```text
~/esphome_agent
```

## Hard Rules

- Do not install or initialize local PlatformIO.
- Do not create a local ESPHome/PlatformIO build environment in this repository.
- Do not run `pio`, `platformio`, or local `esphome compile` unless the user
  explicitly asks for a local fallback.
- Use the existing MCP/build pipeline from `~/esphome_agent` for compile,
  flash, reset, UART logs, HTTP/web checks, and Native API checks.
- Keep real secrets out of Git. Use `.example` files and substitutions.

## Repository Split

- `~/esphome_devices` contains production ESPHome device projects.
- `~/esphome_agent` contains the tooling pipeline, MCP server, flashpoint
  setup, reusable module knowledge, and infrastructure tests.
- Reusable hardware knowledge goes to `~/esphome_agent/Modules`.
- Device-specific behavior, requirements, wiring decisions, and test logs stay
  under `devices/<device-name>/`.

## Required Project Layout

Each production device project uses:

```text
devices/<device-name>/
  PROJECT.md
  device.yaml
  README.md
  hardware.md
  test-log.md
```

`<device-name>` must be an ASCII lowercase slug with hyphens.

## Required Start Procedure

When starting or resuming work on a device:

1. Read this `AGENTS.md`.
2. Read `devices/<device-name>/PROJECT.md`.
3. Preserve the `Raw User Input` section exactly unless the user explicitly asks
   to edit it.
4. Update the `Agent-Maintained Spec` section before changing firmware.
5. Use the MCP pipeline from `~/esphome_agent` for validation.
6. Record meaningful test results in `devices/<device-name>/test-log.md`.
7. Commit and push after a validated, working change.

## PROJECT.md Contract

Every `PROJECT.md` has two top-level sections:

- `Raw User Input`: append-only notes from the user. Do not rewrite or delete.
- `Agent-Maintained Spec`: clean, current, structured project specification
  maintained by the agent.

If raw input conflicts with the maintained spec, keep the raw input intact and
capture the conflict under `Open Questions` or `Decisions`.

## Using the Existing MCP Pipeline

The MCP server runs from `~/esphome_agent` and exposes HTTP helper endpoints on
`http://localhost:8090`.

Typical commands from `~/esphome_agent`:

```bash
curl -sS -X POST http://localhost:8090/tools/esphome_compile \
  -H 'Content-Type: application/json' \
  -d '{"yaml_path":"/projects/devices/<device-name>/device.yaml"}'

curl -sS -X POST http://localhost:8090/tools/esphome_flash \
  -H 'Content-Type: application/json' \
  -d '{"yaml_path":"/projects/devices/<device-name>/device.yaml","method":"ota"}'

curl -sS -X POST http://localhost:8090/tools/device_reset

curl -sS -X POST http://localhost:8090/tools/read_uart_logs \
  -H 'Content-Type: application/json' \
  -d '{"duration_sec":30}'

curl -sS http://localhost:8090/tools/get_device_status
```

If `/projects/...` is unavailable, use the mount/path documented in
`~/esphome_agent/.env` and `docker-compose.yml`, or update the tooling repo
generically so this repository is mounted read-only into MCP/orchestrator.

Expected tooling configuration:

```env
PROJECTS_HOST_PATH=/home/agent/esphome_devices
```

The MCP container mounts that host path at `/projects`, so
`devices/<device-name>/device.yaml` is addressed as:

```text
/projects/devices/<device-name>/device.yaml
```

## Autonomy

Act autonomously within the existing pipeline:

- edit project YAML/docs
- compile through MCP/build server
- flash through OTA or UART when appropriate
- reset via flashpoint
- read UART logs
- check HTTP/web server and Native API
- update project documentation
- commit and push validated changes

Ask the user only when:

- physical wiring must change
- a product decision is missing
- requirements conflict
- real secrets are needed
- the same technical fix fails 3 times

## Module Knowledge

If a project reveals reusable hardware behavior, update the relevant file in:

```text
~/esphome_agent/Modules
```

Examples:

- pinout corrections
- boot/flash wiring requirements
- IO0/EN/JTAG/UART caveats
- PHY, clock, power, reset, or strapping behavior
- module-specific ESPHome YAML requirements
