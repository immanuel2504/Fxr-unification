# `PUT /cloud/apps/install` schema comparison

`*` indicates a required field.

```text
FXR 60/90                                                        FX9600
─────────                                                        ──────

PUT /cloud/apps/install                                         PUT /cloud/apps/install
│                                                                │
├─ url* (string)                                                 ├─ url* (string)
├─ filename* (string)                                            ├─ filename* (string)
├─ authenticationType* (NONE | BASIC)                            ├─ authenticationType* (NONE | BASIC)
├─ options (object)                                              ├─ options (object)
│  ├─ username* (string)                                         │  ├─ username (string)
│  └─ password* (string)                                         │  └─ password (string)
├─ verifyPeer (boolean)                                          ├─ verifyPeer (boolean, default: true)
├─ verifyHost (boolean)                                          ├─ verifyHost (boolean, default: true)
├─ CACertificateFileLocation (string)                            ├─ CACertificateFileLocation (string)
├─ CACertificateFileContent (string)                             ├─ CACertificateFileContent (string)
├─ publicKeyFileLocation (string) — FXR only                     ├─ headers (object)
├─ publicKeyFileContent (string) — FXR only                      ├─ retry (object) — FX9600 only
├─ privateKeyFileLocation (string) — FXR only                    │  ├─ type: randomWait
├─ privateKeyFileContent (string) — FXR only                     │  └─ policy
├─ installedCertificateType (string) — FXR only                  │     ├─ retries (1–50, default: 1)
├─ installedCertificateName (string) — FXR only                  │     └─ wait
└─ headers (object)                                              │        ├─ min (0–3600, default: 30)
                                                                 │        └─ max (1–3600, default: 300)
                                                                 └─ timeouts (object) — FX9600 only
                                                                    ├─ connection (1–3600, default: 60)
                                                                    └─ read (1–3600, default: 600)
```

## Key differences

- FX9600 defines `retry` and `timeouts`; these fields are not present in the current FXR60/90 schema.
- FXR60/90 adds public-key, private-key, and installed-certificate fields.
- Both require `url`, `filename`, and `authenticationType` and use `options` for BASIC credentials.
- Kamali's workbook feedback says FXR retry support is planned, but it gives conflicting Q3 and Q4 dates without a year or firmware version.
- Retry/timeouts must not be shown as released for FXR60/90 until the exact firmware release is confirmed.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 17.
