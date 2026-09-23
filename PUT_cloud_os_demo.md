# `PUT /cloud/os` schema comparison

`*` indicates a required field.

```text
FXR 60/90                                                        FX9600
─────────                                                        ──────

PUT /cloud/os                                                    PUT /cloud/os
│                                                                │
├─ url* (string)                                                 ├─ url* (string)
│  └─ schemes: scp, https, sftp, ftps                            ├─ authenticationType* (NONE | BASIC)
├─ authenticationType* (NONE | BASIC)                            ├─ options (object)
├─ authenticationOptions (object)                                │  ├─ username (string)
│  ├─ username* (string)                                         │  └─ password (string)
│  └─ password* (string)                                         ├─ verifyPeer (boolean, default: true)
├─ verifyPeer (boolean)                                          ├─ verifyHost (boolean, default: true)
├─ verifyHost (boolean)                                          ├─ CACertificateFileLocation (string)
├─ CACertificateFileLocation (string)                            ├─ CACertificateFileContent (string)
├─ CACertificateFileContent (string)                             ├─ headers (object)
├─ publicKeyFileLocation (string) — FXR only                     │  └─ Authorization (string)
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

- FX9600 defines `retry` and `timeouts`; these fields are not declared in the FXR60/90 schema.
- FXR60/90 uses `authenticationOptions`, while FX9600 uses `options` for the username and password.
- FXR60/90 adds public-key, private-key and installed-certificate fields.
- `transfer_protocol` is not present in either current request schema.
- FXR60/90 retry/timeout support still requires confirmation; the workbook does not contain Kamali's OS-specific review.

Source: `Unified_FXR_Starfish_Review.xlsx` → **Field review**, row 28. The workbook says to preserve the FX9600 retry/backoff contract and obtain FXR OS-specific support and release confirmation.
