# `PUT /cloud/start` schema comparison

```text
FXR 60/90                                                                        FX9600
─────────                                                                        ──────

PUT /cloud/start                                                                 PUT /cloud/start
│                                                                                │
├─ doNotPersistState (boolean)                                                   └─ doNotPersistState
├─ applyImpinjGen2X (boolean) — supported in FX9600 but not documented              └─ boolean, default: true
└─ scanType (array) — not supported in FX9600
   └─ allowed values:
      ├─ ble
      └─ rfid
```

## Key differences

- `applyImpinjGen2X` is present in the FX9600 payload but missing from its current schema documentation.
- `scanType` is supported by FXR60/90 and is not supported by FX9600.
- The documented FX9600 schema defines `doNotPersistState` with a default value of `true`.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 31. The workbook's proposed action is to add `applyImpinjGen2X` to the FX9600 schema/examples without adding `scanType`.
