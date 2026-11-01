# backup-device

A standalone, offline backup for [SATI](https://github.com/aiontrust/ai-dev-assistant): a
handheld deck (a Raspberry Pi in a case, with a screen, keyboard, microphone, speaker and a
battery) that keeps working when the home PC, the internet or the power is down.

It is designed here, separately, and joins SATI later. Until then it borrows SATI's API
shapes and data format so the two can sync without conversion.

**What it should do with no internet**
- Talk: an assistant that listens and speaks using a small local model.
- Remember: memories, notes and lessons from SATI's library, carried with it.
- Look things up: offline Wikipedia, manuals and maps.
- Optionally reach people: text over a LoRa mesh radio (Meshtastic), no phone network needed.

## Where things are

| | |
|---|---|
| [docs/DESIGN.md](docs/DESIGN.md) | What it is, the hardware, the software stack and the phases |
| [hardware/BOM.md](hardware/BOM.md) | Parts list with rough prices |
| [docs/INTEGRATION.md](docs/INTEGRATION.md) | The contract with SATI: API, data and sync, for later |
| `software/` | The deck's own code (starts in phase 1) |

## Status

Design. No hardware bought, no code yet.
