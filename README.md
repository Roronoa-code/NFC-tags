# NFC-tags

Tap an NFC sticker on my water bottle to log 1000 ml in the US app, without opening anything.

## How it works

The phone app **Automate** (LlamaLab, free) waits for the sticker, then sends a background request to the US app:

```
POST https://foreverours.vercel.app/api/tap/water
Authorization: Bearer <WATER_TAP_TOKEN>
```

The server logs 1000 ml and ignores a repeat within a minute. The key lives in Vercel as `WATER_TAP_TOKEN` and never in this repo.

## Phone setup (Automate flow)

1. **Flow beginning** → **NFC tag scanned**
2. → **HTTP request**: URL above, method `POST`, request headers `{"Authorization": "Bearer <key>"}`
3. → **Toast show** (optional)
4. → loop back to **NFC tag scanned**
5. Start the flow once; it keeps waiting in the background.

Write the sticker once with Automate's **NFC tag write** block, NDEF type "Automate".

## Stickers

The bottle is a stainless steel Yeti Rambler, so plain NFC stickers only work on the plastic lid. On the metal body, use **anti-metal** NTAG215 tags (waterproof epoxy/PET).
