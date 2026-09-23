# `GET /cloud/supportedStandardList` schema comparison

```text
FXR 60/90                                                        FX9600
─────────                                                        ──────

GET /cloud/supportedStandardList                                GET /cloud/supportedStandardList
│                                                                │
├─ request                                                       ├─ request body (optional)
│  └─ query: region (string, optional)                            │  └─ region (string)
│                                                                │     └─ documented enum: Argentina
└─ 200 response                                                  └─ 200 response
   └─ SupportedStandards[]                                          └─ SupportedStandards[]
      ├─ StandardName (string)                                         ├─ StandardName (string, default: UNDEFINED)
      ├─ channeldata (array of strings)                                ├─ channelData (array of numbers)
      ├─ isChannelSelectable ("true" | "false") — FXR only            ├─ isHoppingConfigurable (boolean)
      ├─ isHoppingConfigurable ("true" | "false")                     └─ isLBTConfigurable (boolean)
      └─ isLBTConfigurable ("true" | "false")
```

## Key differences

- FXR60/90 sends `region` as an optional query parameter; FX9600 documents it in an optional JSON request body.
- FX9600 currently constrains `region` to `Argentina`; the workbook questions whether this is an incorrect example-as-enum restriction.
- FXR60/90 uses lowercase `channeldata` with string items; FX9600 uses camel-case `channelData` with numeric items.
- FXR60/90 represents configurability flags as the strings `"true"` and `"false"`; FX9600 uses JSON booleans.
- `isChannelSelectable` is documented only for FXR60/90.
- Kamali's review for these rows was not supplied; exact runtime responses still require confirmation.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, rows 33–34.
