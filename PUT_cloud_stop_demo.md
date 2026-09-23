# `PUT /cloud/stop` schema comparison

```text
FXR 60/90                                                     FX9600
─────────                                                     ──────

PUT /cloud/stop                                               PUT /cloud/stop
│                                                             │
└─ scanType (array) — not supported in FX9600                 └─ no request body
   └─ allowed values:
      ├─ ble
      └─ rfid
```

## Key difference

- FXR60/90 supports the optional `scanType` array for selecting which scan type to stop.
- FX9600 does not accept a request payload for `PUT /cloud/stop`.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 16. The workbook's proposed action is to retain `scanType` for FXR60/90 and document FX9600 `/cloud/stop` without a request body.
