# backup-device

An offline backup deck for SATI (`aiontrust/ai-dev-assistant`), designed here and joined to
SATI later. Read `docs/DESIGN.md` for what it is and `docs/INTEGRATION.md` for the contract
with SATI.

Rules:
- Keep to `docs/INTEGRATION.md`: SATI's API paths and fields, SATI's table and column names.
  If something has to differ, change that page in the same commit and say why.
- Offline first: nothing in the deck's normal use may need the internet or a cloud key.
- No secrets in git: no keys, `.env`, or credentials. No large files either (models,
  `.zim`, map tiles): those are downloaded onto the device by scripts.
- Target Raspberry Pi 5 (arm64, Raspberry Pi OS). Keep it light: plain Python services under
  systemd, no Docker unless there's a reason.
- Write docs in plain language for the owner, like SATI's docs.
- Commit or push only when asked; work on a branch, not `main`.
