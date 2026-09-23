# `PUT /cloud/cloudConfig` schema comparison

The tree shows the branches discussed in the workbook. `*` indicates a required field.

```text
FXR 60/90                                                        FX9600
─────────                                                        ──────

PUT /cloud/cloudConfig                                          PUT /cloud/cloudConfig
│                                                                │
└─ endpointConfig*                                               └─ endpointConfig
   ├─ data                                                          └─ data
   │  └─ event                                                        └─ event
   │     └─ connections[]                                               └─ connections[]
   │        ├─ options                                                    ├─ options (protocol-specific)
   │        │  ├─ additional                                              │  ├─ basicAuthentication
   │        │  └─ security                                                │  ├─ additional
   │        └─ additionalOptions                                          │  │  ├─ retain
   │           └─ dataAck — not documented in FX9600                      │  │  └─ alpnProtocolNames (AWS)
   │              ├─ enable (boolean)                                     │  └─ security
   │              ├─ responseTopic (string)                               └─ additionalOptions
   │              └─ correlationFieldName (string)
   ├─ control
   │  └─ commandResponse
   │     └─ enableLocalRest — remove from schema
   └─ management
      └─ commandResponse
         └─ enableLocalRest — remove from schema
```

## Key differences

- FXR60/90 documents `additionalOptions.dataAck`; the FX9600 schema does not currently document its `enable`, `responseTopic`, or `correlationFieldName` fields.
- FX9600 documents protocol-specific `basicAuthentication`, `additional.retain`, and AWS `alpnProtocolNames` branches.
- Kamali's workbook feedback says the FX9600 option fields are supported by FXR firmware, but the FXR schema still needs those fields documented in their proper protocol branches.
- `enableLocalRest` appears in the FXR schema under both control and management command responses; the workbook says it is not needed and should be removed from the schema.
- The exact FX9600 firmware fields needed for full FXR90 parity are still not identified at leaf level.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 19.
