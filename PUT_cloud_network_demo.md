# `PUT /cloud/network` schema comparison

```text
FXR 60/90                              FX9600
─────────                              ──────

PUT /cloud/network                     PUT /cloud/network
│                                      │
└─ supported interfaces                ├─ eth0 — Ethernet
   ├─ eth0 — Ethernet                  └─ mlan0 — Wi-Fi
   ├─ mlan0 — Wi-Fi
   ├─ bnep0 — Bluetooth
   ├─ wan0 — WAN (FXR90 only)
   └─ uap0 — Wi-Fi hotspot
```

## Key differences

- Both reader families list `eth0` for Ethernet and `mlan0` for Wi-Fi.
- `bnep0` and `uap0` are listed only for FXR60/90.
- `wan0` is available only on FXR90.

Source: current `PUT /cloud/network` schemas for FXR60/90 and FX9600.
