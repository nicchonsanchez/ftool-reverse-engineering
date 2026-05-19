# FTool Profile Catalog — Extracted from `Ftool.exe`

Extracted from the FTool 4.01.00 executable binary by analyzing string offsets and decoding adjacent IEEE 754 double-precision values.

## File: `profile-catalog.json`

Structured JSON with **27 clusters** containing **1836 profile entries** (W-shapes, Vallourec tubes, Gerdau VS series, etc.).

## Structure

```json
{
  "1": {
    "cluster_id": 1,
    "record_size": 136,
    "n_doubles": 14,
    "count": 66,
    "first_name": "W150x13.0",
    "last_name": "W200x52.0",
    "entries": [
      {
        "offset": 5512080,
        "name": "W150x13.0",
        "props": [0.148, 0.1, 0.0043, 0.0049, 0.138, 0.118, 0.00166, 6.35e-6, 8.58e-5, 0.0618, 8.2e-7, 1.64e-5, 0.0222, ...]
      },
      ...
    ]
  },
  ...
}
```

## Cluster reference (validated structures)

### Cluster 5 — Vallourec Tubes Circular (172 entries, 80-byte records, 7 doubles)

Property order (validated against FTool panel):
- props[0] = **d** (m) — outer diameter
- props[1] = **t** (m) — wall thickness
- props[2] = **A** (m²) — cross-section area
- props[3] = **Ix** (m⁴) — moment of inertia
- props[4] = **Wx** (m³) — elastic section modulus
- props[5] = **rx** (m) — radius of gyration
- props[6] = (additional, possibly torsional It)

Example — `33.4x3.2`:
- d = 33.4 mm, t = 3.2 mm
- A = 304 mm²
- Ix = 35,001 mm⁴
- Wx = 2,095.9 mm³
- rx = 10.74 mm

Verified against FTool's Section Properties panel: 100% match.

### Cluster 1 — Welded I-shapes BR (66 entries, 136-byte records, 14 doubles)

Likely property order (partially validated against `W150x13.0`):
- props[0] = **d** (m) — total height
- props[1] = **bf** (m) — flange width
- props[2] = **tw** (m) — web thickness
- props[3] = **tf** (m) — flange thickness (or related)
- props[4-5] = (additional geometric data)
- props[6] = **A** (m²)
- props[7] = **Ix** (m⁴)
- props[8] = **Wx** (m³)
- props[9] = **rx** (m)
- props[10] = **Iy** (m⁴)
- props[11] = **Wy** (m³)
- props[12] = **ry** (m)
- props[13] = (extra, possibly J/torsion)

### Cluster 6 — Vallourec Tubes Square (231 entries, 80-byte records, 7 doubles)

Same property structure as Cluster 5 (circular). The `d` field is the side length (= b for square).

### Clusters 7-26 — Vallourec Tubes Rectangular (112-byte records, 11 doubles)

Rectangular tubes have asymmetric inertia, so they need separate values for X and Y axes.

Likely structure (to be validated):
- props[0] = h (height)
- props[1] = b (width)
- props[2] = t (wall thickness)
- props[3] = A
- props[4] = Ix
- props[5] = Wx
- props[6] = rx
- props[7] = Iy
- props[8] = Wy
- props[9] = ry
- props[10] = (extra)

### Cluster 27 — Gerdau VS / Other (382 entries, 64-byte records, 5 doubles)

Mixed cluster including "VS" series (Gerdau-AçoMinas welded I-shapes). 5 doubles per record — fewer properties available than other I-shapes.

## How to use this data

### Option 1: Generate Generic Integral Properties section with catalog values

You CANNOT reference Profile Tables directly in generated `.ftl` files (those use internal FTool indexes that aren't documented), BUT you can:

1. Look up the desired profile in this JSON
2. Extract `A`, `Ix` (and `d` for visualization)
3. Generate a `Generic Integral Properties` section with those exact values
4. The structural analysis will be identical to using the Vallourec/Gerdau/W reference

Example: to use "W150x13.0":
- Look up cluster 1 → entries with name "W150x13.0"
- A = 0.00166 m² (or 1660 mm²)
- Ix = 6.35e-6 m⁴
- d = 0.148 m

In your generated `.ftl`:
```
'W150x13' 1 0
 0.00166 0 6.35e-006 0 0.148 0
```

This won't show "W150x13.0" with the catalog label in FTool's panel (it'll show as "Generic"), but the structural behavior is identical.

### Option 2: Direct binary patching (advanced)

You could potentially overwrite a specific record in `Ftool.exe` to add custom profiles. But this is risky and not recommended.

## Limitations

- The "index" used by FTool in `.ftl` Profile Table references (e.g., the `9` in `9 0 0` for cluster Vallourec) was NOT decoded. We don't know the exact integer that maps a profile to a record. Without that, you can extract data FROM the catalog but cannot WRITE a Profile Table reference INTO a `.ftl` programmatically.
- Some clusters (especially cluster 2 with 32-byte records and cluster 27 with mixed names) need additional analysis to fully understand their structure.

## Methodology

This catalog was extracted by:
1. Finding ASCII profile name strings in the `.exe` binary
2. Detecting record boundaries by typical offset gaps
3. Decoding the bytes after each name as little-endian IEEE 754 doubles
4. Validating against known FTool panel output (e.g., 33.4x3.2 verified 100%)

Full extraction script available on request.

## Disclaimer

This data was extracted by analyzing a copy of the FTool 4.01.00 binary for educational and interoperability purposes. The FTool software and its catalog database remain property of PUC-Rio / Tecgraf. Do not redistribute this catalog data commercially without permission from the original copyright holders. This file is provided as a reference for academic and research use to enable AI-assisted generation of `.ftl` files using the same catalog values as FTool's parametric Generic sections.
