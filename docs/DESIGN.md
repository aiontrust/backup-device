# Design

## What it is

A self-contained deck that can stand in for SATI when SATI can't be reached. SATI's own
plan ([REMOTE.md](https://github.com/aiontrust/ai-dev-assistant/blob/main/docs/REMOTE.md))
treats the phone and glasses as remotes for the home PC; this is different: it has its own
brain, smaller, and needs nothing else to work.

It is a **backup**, not a second SATI. When it can reach the home PC it brings itself up to
date and hands back what it learned; the home PC stays the one that's right.

### Goals

1. Works with every radio off: no Wi-Fi, no internet, no cloud keys.
2. Voice in and out, like SATI.
3. Carries SATI's memories and library, read-only at first.
4. Runs a full day on battery for occasional use; a few hours of continuous talking.
5. Light enough to stay in a bag: about 1 kg or less.
6. Holds no secrets worth stealing: no API keys on the device, the disk encrypted.

### Not goals (for now)

- Matching Claude. A Pi runs a 3-4B model: good for notes, facts and simple questions,
  weak at code.
- The IDE, the sandboxed shell or the HUD. The deck has its own simple screen.
- Being reachable from outside. It answers only its own screen and keyboard.

## Hardware

Full list and prices in [../hardware/BOM.md](../hardware/BOM.md). The choices:

| Part | Choice | Why |
|---|---|---|
| Computer | Raspberry Pi 5, 16 GB | Enough memory for a 4B model plus speech; the most support and spare parts |
| Storage | 256-512 GB NVMe SSD on an M.2 HAT | Models, Wikipedia (~100 GB with pictures) and maps; far faster and tougher than a microSD |
| Cooling | Active cooler | The model keeps the CPU busy; without it the Pi slows down |
| Screen | 7-8" HDMI or DSI touchscreen | Readable, fits a small case |
| Keyboard | Compact USB or Bluetooth, or built into the case | Typing when talking isn't possible |
| Audio | USB microphone and a small USB or 3.5 mm speaker | Simple, no drivers |
| Power | USB-C PD power bank, 20,000 mAh+, 27 W+ (5 V 5 A) | The Pi 5 needs 5 A for full USB power; a bank is replaceable and airline-safe |
| Clock | RTC battery for the Pi 5 | Keeps the time with no internet, so sync order is right |
| Radio (optional) | Meshtastic LoRa board (e.g. a Heltec or RAK node) over USB | Off-grid text messaging |
| Case | A rugged hard case, or 3D-printed | Starts as a box; a custom case once the parts are settled |

Open questions:
- Screen size against battery life (a bigger screen draws more).
- Built-in keyboard (a clamshell like a laptop) or separate.
- Whether to add a small always-on display for status (battery, sync age).

## Software stack

Everything runs on the Pi, offline. Raspberry Pi OS (64-bit, Bookworm or newer).

| Job | Choice | Notes |
|---|---|---|
| Model | Ollama with a 3-4B model (Llama 3.2 3B, Qwen 3 4B, Gemma 3 4B) | Tested in phase 1; pick on speed and answer quality |
| Listening | whisper.cpp, `base.en` or `small.en` | Runs on the CPU; `base.en` is near real time on a Pi 5 |
| Speaking | Piper | Fast, natural-sounding, many voices |
| Wake / push to talk | A button first; a wake word later (openWakeWord) | A button is reliable and saves battery |
| Data | SQLite, one file | Same tables as SATI (see [INTEGRATION.md](INTEGRATION.md)) |
| Search by meaning | nomic-embed-text through Ollama | The same model SATI's library uses, so vectors carry over |
| Reference | Kiwix (`.zim` files: Wikipedia, iFixit, medical, survival guides) | Read through kiwix-serve's local API |
| Maps | Offline tiles (PMTiles or OsmAnd data) | Phase 3 |
| Radio | Meshtastic Python library | Phase 4, optional |
| App | One small Python service plus a full-screen page in the browser | Shaped like SATI's API so it can later point at SATI |

Not Docker on the deck at first: one service and Ollama run directly with systemd. It's
lighter on the Pi and starts faster. This can change if it makes integration easier.

## Phases

Each phase ends with something that works on its own.

1. **Talks offline.** The Pi set up (a script in `software/`), Ollama with a chosen model,
   push-to-talk voice in and out, and a plain chat screen. Measure: seconds from finishing
   speaking to the start of the answer, and battery hours.
2. **Remembers.** SQLite with SATI's tables; memories and library entries loaded from an
   export of SATI; the assistant uses them like SATI does. The library searched by meaning.
3. **Knows things.** Kiwix and maps, and assistant tools to search them.
4. **Off-grid messaging (optional).** Meshtastic: read and send messages, and ask the
   assistant to.
5. **Joins SATI.** Sync with the home PC (see [INTEGRATION.md](INTEGRATION.md)). Done in
   both repos.

## Security

- No API keys or cloud credentials on the deck, ever. It doesn't need them.
- Full-disk encryption (LUKS) with a passphrase at boot. If it's lost, the data is safe.
- Wi-Fi and Bluetooth off by default; turned on only to sync or for the keyboard.
- The service listens on `127.0.0.1` only.
- When it syncs with SATI (phase 5) it uses a SATI device key, so `devices revoke` on the
  home PC cuts it off.
