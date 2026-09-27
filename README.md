# NFC-tags

Tap an NFC sticker on my water bottle to log 2000 ml in the US app, without opening anything.

## How it works

The phone app **Automate** (LlamaLab, free) waits for the sticker, then sends a background request to the US app:

```
POST https://foreverours.vercel.app/api/tap/water
Authorization: Bearer <WATER_TAP_TOKEN>
```

The server logs 2000 ml. **The one-minute repeat filter is temporarily disabled for NFC range testing: every successful tap adds another 2000 ml.** Restore `REPEAT_WINDOW_MS` to `60_000` in the US app's `app/api/tap/water/route.ts` after testing, and restore the repeat-filter test. The key lives in Vercel as `WATER_TAP_TOKEN` and never in this repo.

## Phone setup (Automate flow)

The configured flow is **US Water - 2000 ml**:

1. **Flow beginning** → **Failure catch** → **NFC tag scanned** (Automate type, content output variable `tag`).
2. **Expression true**: `tag = "us-water"`. NO loops back to NFC; YES continues.
3. **HTTP request**: URL above, method `POST`, request headers `{"Authorization": "Bearer <key>"}`. Save the response to text variable `response`, and the status code to `status`.
4. **Toast show**: `status = 200 ? jsonDecode(response)["message"] : "Water not logged (HTTP " ++ status ++ "). Try again."`
5. Loop back to **Failure catch**. Its FAIL path shows “Water tap failed. Check internet, wait a minute and tap again.” before looping back.
6. Start the flow once. Enable Automate's **Run on system startup** and exempt it from battery optimization.

Write the sticker once using **Write tag** inside the NFC scanned block, with Tag content `"us-water"`. The secret stays in the phone flow, not on the sticker. Do not publish or commit a configured flow export: it contains the secret.

Keep the phone unlocked when tapping; NFC scanning while locked is not guaranteed. Internet access is required. A failed request is reported, not queued for a later water entry.

## Stickers

The bottle is a stainless steel Yeti Rambler, so plain NFC stickers only work on the plastic lid. On the metal body, use **anti-metal** NTAG215 tags (waterproof epoxy/PET).
