# Parts list

Rough prices in USD, late 2026; check before buying. "Core" is what phase 1 needs.

| Part | Example | Core | Price |
|---|---|---|---|
| Raspberry Pi 5, 16 GB | Official | yes | $120 |
| Active cooler | Official Pi 5 Active Cooler | yes | $5 |
| M.2 HAT for NVMe | Official M.2 HAT+ or Pimoroni NVMe Base | yes | $12-15 |
| NVMe SSD, 256-512 GB | Any 2230/2242 PCIe drive | yes | $30-50 |
| microSD card, 32 GB | For first boot and recovery | yes | $8 |
| Touchscreen, 7-8" | Official Touch Display 2 (7") or an HDMI 8" panel | yes | $60-90 |
| USB microphone | Small omnidirectional | yes | $10-20 |
| Speaker | Small USB-powered | yes | $10-15 |
| Push-to-talk button | Arcade or tactile button on the GPIO | yes | $3 |
| Power bank | USB-C PD, 20,000 mAh+, 5 V 5 A (27 W+) | yes | $40-60 |
| RTC battery | Official Pi 5 RTC battery | yes | $5 |
| Compact keyboard | Mini USB/Bluetooth with trackpad | yes | $20-35 |
| Case | Rugged hard case to start; printed later | yes | $25-40 |
| Cables | Short USB-C, micro-HDMI to HDMI, USB-A | yes | $15 |
| Meshtastic node | Heltec V3 or RAK WisBlock, with antenna | no | $25-40 |
| Second Meshtastic node | To test the radio (or for a phone) | no | $25-40 |

**Core total: about $365-480.** With the radio pair: about $415-560.

Notes:
- The Pi 5 asks the power supply for 5 A. A bank that offers only 3 A works, but the Pi
  limits USB power; check the bank lists 5 V 5 A.
- 16 GB matters: a 4B model (~3 GB), whisper, Piper, the embedding model and the OS fit with
  room to spare. 8 GB works only with smaller models.
- Buy the SSD in a size that leaves room for full Wikipedia with pictures (~110 GB) if you
  want it; the text-only version is ~50 GB.
