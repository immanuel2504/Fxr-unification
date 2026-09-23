# `PUT /cloud/config` schema comparison

The tree shows the request branches reviewed in the workbook.

```text
FXR 60/90                                                        FX9600
─────────                                                        ──────

PUT /cloud/config                                                PUT /cloud/config
│                                                                │
├─ xml (string)                                                  ├─ xml (string)
├─ GPIO-LED                                                      ├─ GPIO-LED
│  ├─ GPI_1_H (array)                                            │  ├─ GPI_1_H (array)
│  ├─ GPI_1_L (array)                                            │  ├─ GPI_1_L (array)
│  ├─ GPI_2_H (array)                                            │  ├─ GPI_2_H (array)
│  ├─ GPI_2_L (array)                                            │  ├─ GPI_2_L (array)
│  └─ GPIDebounce — supported; missing from FXR schema           │  └─ GPIDebounce
│                                                                  ├─ 1 (number, default: 50)
│                                                                  ├─ 2 (number, default: 50)
│                                                                  ├─ 3 (number, default: 50)
│                                                                  └─ 4 (number, default: 50)
└─ READER-GATEWAY                                                └─ READER-GATEWAY
   └─ endpointConfig                                                └─ endpointConfig
      └─ data                                                          └─ data
         └─ event                                                       └─ event
            └─ connections[]                                              └─ connections[]
               └─ options                                                    └─ options (protocol-specific)
                  ├─ additional                                               ├─ basicAuthentication
                  └─ security                                                 ├─ additional
                                                                               │  ├─ retain
                                                                               │  └─ alpnProtocolNames (AWS)
                                                                               └─ security
```

## Key differences

- Both request schemas already contain `xml`; no additional XML field is needed.
- Both currently declare only `GPI_1_H`, `GPI_1_L`, `GPI_2_H`, and `GPI_2_L`. Four physical GPI ports do not by themselves prove support for `GPI_3_*` and `GPI_4_*` action keys.
- FX9600 documents `GPIDebounce` for ports 1–4. Kamali's feedback says the same field is supported on FXR, but the FXR schema still needs to expose it.
- FX9600 documents protocol-specific `basicAuthentication`, `additional.retain`, and AWS `alpnProtocolNames` branches.
- Kamali's feedback says those connection options are supported by FXR firmware, but their exact nesting and requiredness still need to be added to the FXR schema.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 21.
