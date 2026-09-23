# `GET /cloud/config` schema comparison

The tree shows the response branches reviewed in the workbook.

```text
FXR 60/90                                                        FX9600
─────────                                                        ──────

GET /cloud/config                                                GET /cloud/config
│                                                                │
└─ 200 response                                                  └─ 200 response
   ├─ xml (string)                                                  ├─ xml (string)
   ├─ GPIO-LED                                                      ├─ GPIO-LED
   │  └─ GPIDebounce — supported; missing from FXR schema           │  └─ GPIDebounce
   │                                                                  ├─ 1 (number, default: 50)
   │                                                                  ├─ 2 (number, default: 50)
   │                                                                  ├─ 3 (number, default: 50)
   │                                                                  └─ 4 (number, default: 50)
   └─ READER-GATEWAY                                                └─ READER-GATEWAY
      └─ endpointConfig                                                └─ endpointConfig
         ├─ data                                                          └─ data
         │  └─ event                                                         └─ event
         │     └─ connections[]                                                └─ connections[]
         │        ├─ options                                                      └─ options (protocol-specific)
         │        │  ├─ additional                                                  ├─ basicAuthentication
         │        │  └─ security                                                    ├─ additional
         │        └─ additionalOptions                                              │  ├─ retain
         │           └─ dataAck — not documented in FX9600                          │  └─ alpnProtocolNames (AWS)
         └─ management                                                              └─ security
            ├─ commandResponse
            │  └─ enableLocalRest — remove from FXR schema
            └─ event.connections[].additionalOptions.dataAck
```

## Key differences

- Both schemas already contain top-level `xml`; no additional XML field is needed.
- FX9600 documents `GPIO-LED.GPIDebounce` for ports 1–4. Kamali's feedback says the same functionality is supported on FXR, but it is missing from the FXR schema.
- FXR60/90 documents `additionalOptions.dataAck`; the FX9600 response schema does not currently document it.
- FX9600 documents protocol-specific `basicAuthentication`, `additional.retain`, and AWS `alpnProtocolNames`; the workbook says these supported fields must also be represented correctly in the FXR schema.
- `enableLocalRest` should be removed from the FXR response schema while retaining `dataAck`.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 20.
