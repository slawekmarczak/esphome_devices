# Device Name

## Raw User Input

Append user requirements here. This section is append-only unless the user
explicitly asks for edits.

## Agent-Maintained Spec

Maintain this section as the current project spec.

### Goal

TBD.

### Hardware

- Module/board: TBD.
- Power: TBD.
- Network: TBD.
- Peripherals: TBD.

### Pinout

| Signal | Module Pin | External Connection | Notes |
|--------|------------|---------------------|-------|
| TBD | TBD | TBD | TBD |

### Firmware Behavior

- TBD.

### ESPHome Entities

- TBD.

### Flashing And Update Path

- Compile through `~/esphome_agent`.
- Prefer OTA once networking works.
- Use UART through flashpoint for first flash or recovery.

### Validation Plan

- Compile via MCP.
- Flash via OTA or UART.
- Reset via MCP.
- Read UART logs.
- Check HTTP/web and Native API.
- Record results in `test-log.md`.

### Decisions

- TBD.

### Open Questions

- TBD.

