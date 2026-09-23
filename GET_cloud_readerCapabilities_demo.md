# `GET /cloud/readerCapabilities` schema comparison

The tree shows the response fields reviewed in the workbook.

```text
FXR 60/90                                                        FX9600
─────────                                                        ──────

GET /cloud/readerCapabilities                                   GET /cloud/readerCapabilities
│                                                                │
└─ capabilities                                                  └─ capabilities
   ├─ antennas[]                                                    ├─ antennas[]
   │  └─ type: external | internal                                  │  └─ type: EXTERNAL | INTERNAL
   ├─ apiSupported                                                  ├─ apiSupported
   │  └─ versions[] — array                                         │  └─ versions — object
   │     └─ item                                                     │     ├─ documentation (string)
   │        ├─ documentation (string)                                │     └─ version: v1
   │        └─ version (string)                                      ├─ appLedColors[]
   ├─ appLEDColors[]                                                 │  └─ RED | GREEN | AMBER
   ├─ networkInterfaces[]                                            ├─ networkInterfaces[]
   │  ├─ type: ETHERNET | WIFI | BLUETOOTH | WAN                     │  └─ type: ETHERNET | WIFI | BLUETOOTH
   │  ├─ supportedSim[] — WAN                                        ├─ maxAppLEDs (integer)
   │  │  └─ Physical SIM | Embedded SIM                              ├─ numGPIs (integer)
   │  └─ NetworkType[] — WAN                                         └─ numGPOs (integer)
   │     └─ AUTO | LTE | NR5G
   ├─ stackLED — FXR only
   ├─ gen2xFeaturesSupported[] — FXR only
   │  └─ FASTID | TAGFOCUS | TAGQUIETING | PROTECTEDMODE
   ├─ maxAppLEDs (number)
   ├─ numGPIs (number)
   └─ numGPOs (number)
```

## Key differences

- FXR60/90 uses lowercase antenna values (`external`, `internal`); FX9600 uses uppercase values.
- FXR60/90 models `apiSupported.versions` as an array; FX9600 models it as one object.
- The LED-color field is `appLEDColors` on FXR60/90 and `appLedColors` on FX9600.
- FXR60/90 adds `WAN`, SIM information, network types, `stackLED`, and `gen2xFeaturesSupported`.
- A `BLUETOOTH` network-interface capability must not be interpreted as BLE-scanning support.
- FX9600 documents count fields as integers; FXR60/90 currently documents them as numbers.
- The workbook says the FX9600 document matches firmware, while Kamali's paired review was not supplied.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 29.
