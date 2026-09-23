# `PUT /cloud/mode` schema comparison

The tree shows the fields reviewed in the workbook. `*` indicates a required field.

```text
FXR 60/90                                                        FX9600
─────────                                                        ──────

PUT /cloud/mode                                                  PUT /cloud/mode
│                                                                │
├─ type*                                                         ├─ type*
│  ├─ SIMPLE                                                     │  ├─ SIMPLE
│  ├─ INVENTORY                                                  │  ├─ INVENTORY
│  ├─ PORTAL                                                     │  ├─ PORTAL
│  ├─ CONVEYOR                                                   │  ├─ CONVEYOR
│  └─ CUSTOM                                                     │  └─ CUSTOM
├─ inventoryProtocol — not documented in FX9600                  ├─ DIRECTIONALITY — ATR7000 only, not FX9600
│  └─ mode*                                                      ├─ beams — ATR7000 only, not FX9600
│     ├─ GEN2X                                                   ├─ accesses[]
│     ├─ GEN2                                                    │  └─ READ.config.wordCount (integer)
│     └─ HYBRID                                                  └─ tagMetaData[]
├─ accesses[]                                                       ├─ common string values
│  └─ READ.config.wordCount (integer)                                ├─ RESERVED — FX9600 schema only
└─ tagMetaData[]                                                     └─ object form
   ├─ common string values                                             ├─ userDefined (string)
   ├─ READERLOCATION — FXR schema only                                 └─ antennaPortNames (array)
   └─ object form
      ├─ userDefined (string)
      ├─ antennaNames (array)
      └─ gpsCoordinates (object)
```

## Key differences

- FXR60/90 defines `inventoryProtocol.mode` with `GEN2X`, `GEN2`, and `HYBRID`; FX9600 support is not established in the workbook.
- `DIRECTIONALITY` and `beams` appear in the multi-model Starfish schema but are described as ATR7000-only, so they must not be presented as FX9600 features.
- Both request schemas use `wordCount`; the reviewed comparison does not support renaming it to `wordCounter`.
- FXR60/90 metadata adds `READERLOCATION`, `antennaNames`, and `gpsCoordinates`.
- The FX9600 schema adds `RESERVED` and uses `antennaPortNames` in the metadata object form.
- Kamali's PUT `/cloud/mode` review was not supplied, and the available Starfish comment discusses a response rather than request support.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 24.
