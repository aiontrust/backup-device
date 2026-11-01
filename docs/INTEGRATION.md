# Integration with SATI

Not built yet. This is the contract both sides keep to now, so joining later is a sync and
not a rewrite. Taken from SATI's code as of November 2026
(`backend/app/memory/models.py`, `backend/app/routes/`); if SATI changes these, update this
page.

## 1. Same API shapes

The deck's service uses the same paths and fields as SATI's backend, under `/api/v1`. A
client written for one works with the other by changing the address. The deck implements
only what it needs:

| Path | SATI | Deck |
|---|---|---|
| `POST /api/v1/assistant/chat` | yes | phase 1 |
| `GET/POST /api/v1/conversations`, `GET /api/v1/conversations/{id}`, `/latest` | yes | phase 2 |
| `GET/POST /api/v1/memories`, `DELETE /api/v1/memories/{id}` | yes | phase 2 |
| `GET/POST /api/v1/library`, `/library/search`, `/library/shelves/{shelf}`, `/library/{id}` | yes | phase 2 |
| `GET /api/v1/reference/search` (Kiwix) | no | phase 3, deck only |
| `GET/POST /api/v1/mesh/messages` | no | phase 4, deck only |
| `/sync/...` | no | phase 5, both |

**Chat request** (SATI's `ChatRequest`):
```json
{"messages": [{"role": "user", "content": "..."}], "conversation_id": 12}
```
`hud` and `ide` are SATI-only and ignored by the deck.

**Chat response** (SATI's registry result):
```json
{"reply": "...", "provider": "ollama", "label": "Ollama", "model": "qwen3:4b",
 "fallback_from": null, "skipped": {}, "actions": [], "tools": [],
 "conversation_id": 12, "memory_error": null}
```

## 2. Same data

The deck's SQLite uses SATI's table and column names, so a row means the same on both:

- `conversations` (id, title, created_at, updated_at)
- `messages` (id, conversation_id, role, content, provider, model, created_at). `role` is
  `user`, `assistant` or `tools`.
- `saved_memories` (id, text, source, created_at). `source` is `chatgpt`, `you` or `sati`;
  the deck adds `deck`.
- `library_docs` (id, shelf, kind, title, source, created_at). `kind` is `note`, `lesson`
  or `doc`.
- `library_chunks` (id, doc_id, position, text, embedding, embedding_model). The embedding
  is text (`"[0.12,-0.5,...]"`); `embedding_model` must match to compare. Both sides use
  `nomic-embed-text` v1.5.

**One addition on the deck, needed for sync:** SATI's ids are plain integers, so the same
number can mean different rows on the two machines. Every deck row also gets:
- `uid`: a UUID made when the row is created, never reused.
- `sati_id`: SATI's id once synced, else empty.
- `origin`: `sati` (copied from the home PC) or `deck` (made here).

SATI will need a `uid` column too in phase 5. That's a small migration on SATI's side and
the only change this plan asks of SATI.

## 3. Sync (phase 5)

When the deck can reach the home PC (home Wi-Fi, or the same tunnel the phone uses):

1. **Pull.** The deck asks for everything changed since its last sync: memories, library
   entries and recent conversations. SATI's copy wins; the deck replaces its copies.
2. **Push.** The deck sends what it made while away (`origin = deck`, no `sati_id` yet):
   new memories, notes, lessons and conversations. SATI adds them, marked as from the deck,
   and returns their new ids.
3. **Never deleted by sync.** A delete on one side is not copied automatically at first;
   the owner sees a list of differences instead. Safer while it's new.

Authentication: a SATI device key (`devices add backup-deck` in the HUD terminal), sent as
`Authorization: Bearer <key>`. SATI already treats such a device as remote, so the shell,
running code and managing keys stay off-limits to it. `devices revoke` cuts it off.

Until phase 5, a one-way copy is enough: export memories and the library from SATI (a
script reading its API) and load them onto the deck by USB.

## What stays apart

- The deck never holds SATI's `.env`, the Anthropic key, the Neon password or the Google
  key.
- SATI never depends on the deck being there.
