# FTool 4.01.00 `.ftl` Format — Decoded

Technical documentation of the `.ftl` file format used by [FTool](https://www.tecgraf.puc-rio.br/ftool/) version 4.01.00 (PUC-Rio).

Plain text, space-separated. **Not binary**, but has cross-referenced IDs, pre-computed bboxes, and undocumented flags.

---

## Macro structure

```
Lines 1-16:      Header (version, viewport, counters, display flags)
Lines 17-18:     Load case
Lines 19-25:    Concentrated nodal loads + uniform bar loads
Lines 26-28:    Materials
Lines 29-34:    Sections
Lines 35-40:    Entity "stub" — support/load on ORIGIN NODE (0,0)
Line 41+:       Entities (bars) — 16-line block each (or 12 for "connectors")
Final line:     " 0" (terminator, with leading space)
```

## Header (lines 1-16)

Copy from a known-good file. Most lines are constant.

```
Line 1:  "401 0"                                       # version + flag (constant)
Line 2:  "206"                                          # constant
Line 3:  "1"                                            # constant
Line 4:  "0 1 [matMax] [secMax] [nodeMax] [counter1] [counter2] 0 0 0 0"
         counter1 and counter2 increment per created entity (global watermarks).
         counter2 = next available entity ID.
Line 5:  "+X_cursor +Y_cursor"                          # cursor/center position
         Empty model: ±1e30 (sentinel "viewport not initialized")
Line 6:  "+xmin +xmax +ymin +ymax"                      # viewport bbox
         Empty: ±1e30 on all 4
Line 7:  "0.01 0.01"                                    # scale/zoom (typical)
Line 8:  "1 0 1 0 1 1 1 0 0 0 1 1 0 0 0"                # display flags
Lines 9-13: constant blocks (internal counters, leave as-is)
Line 14: "1 1"
Line 15: "0 1 1 1 1 0"
Line 16: "1 1 0"
```

## Load case (lines 17-18)

```
Line 17: "1"                                            # number of load cases
Line 18: "'Load Case 01' 1"                             # 'name' [flag]
```

⚠️ **Important limitation**: multiple load cases and combinations (Load Comb) are **exclusive to FTool Advanced Edition**. In the Standard Edition (used by most), only 1 load case is available. The "Load Case" and "Load Comb" toolbar icons are disabled. The top dropdown only works to switch between pre-existing cases in Advanced files.

For programmatic generation of `.ftl` with multiple cases: line 17 = N (case count), lines 18..(17+N) = `'name' flag` for each. But Standard Edition FTool may not open multi-case files correctly (not validated).

## Loads (lines 19-25)

```
Line 19: "0"
Line 20: [count_concentrated_loads]                     # nodal load count
Line 21: "'name' Fx Fy Mz"                              # repeat N times
         Ex: "'F' 0 -10 0" = 10 kN downward, no moment
Line after nodal: "0" (separator)
Next: [count_uniform_loads]
Lines: "[flag] 'name' Qx Qy"                            # 4 values
        Ex: "0 'Gravity' 0 -1" = -1 kN/m vertical
Line after uniform: "0" (separator)
Next: [count_linear_loads]
Lines: "[flag] 'name' Pxi Pyi Pxj Pyj"                  # 6 values (validated exp-H)
        Ex: "0 'Trapezoidal' 0 0 0 -5" = ramp 0 to -5 kN/m
Lines after: "0" "0" "0"... (slots for Member End Moments, Thermal, etc.)

**Load Trains** (moving loads for bridge analysis) come AFTER all static load types, in a complex section. Structure decoded (exp-X through X4):

```
Line: [count_trains]                        # number of trains defined
For each train:
  Line: 'train_name'                        # name on its own line
  Line: [impact_factor]                     # impact factor (validated X1)
  Line: [N = count_concentrated_loads]      # number of concentrated loads
  N lines: "x  P"                           # matrix: position[m] force[kN]
  Line: [M = count_distributed_loads]       # number of distributed loads
  M lines: "xa  xb  q'  q"                  # ALWAYS 4 values (q'=q in single mode)
  Line: [live_load_exterior]                # kN/m (validated X4)
  Line: [live_load_interior]                # kN/m
  Line: [length_m]                          # train length (validated X2)
  Line: [full_empty_flag]                   # 0=single load car, 1=full/empty car (X6)
  Line: 0                                   # reserved (unknown use)
  Line: 0                                   # reserved (unknown use)
```

Example (Impact=3, Length=5, 3 concentrated loads, Live Load Ext=-7):
```
31: 1               # count trains
32: 'TremTeste'
33: 3               # impact factor
34: 3               # 3 concentrated loads
35: 0  -10
36: 2  -10
37: 4  -5
38: 0               # 0 distributed loads
39: -7              # Live Load Exterior
40: 0               # Live Load Interior
41: 5               # Length
42: 0
43: 0
44: 0               # 3 trailing zeros (flags)
```

⚠️ **Sign convention**: FTool stores train loads as NEGATIVE (assumes gravity downward). If you enter 7 in Live Load Exterior, FTool saves -7 automatically.

Validated via exp-X:
- Load Trains have NO per-bar slot — they are global to the model
- The section sits between static loads (Thermal) and origin stub
- Line 4 counters do NOT change (load train doesn't count as "entity")
```

## Materials (lines 26-28)

```
Line 26: [count_materials]                              # ex: "1"
Line 27: "'name' [flag]"                                # ex: "'A36 Steel' 0"
Line 28: " E ν ρ α"                                     # 4 floats, leading space
```

**Common materials:**

| Material | E (kN/m²) | ν | ρ | α |
|----------|-----------|---|---|---|
| ASTM A36 Steel | 2e+008 | 0.3 | 78.5 | 1.2e-005 |
| Concrete fck=25 MPa | 2.8e+007 | 0.2 | 25 | 1e-005 |
| Wood (generic) | 7.35e+006 | 0.3 | 10 | 1e-005 |

## Sections (lines 29-34)

```
Line 29: [count_sections]                               # ex: "1"
Line 30: "'name' [flag1] [flag2]"                       # ex: "'Tube 100x50x3' 0 0"
Line 31: " A I"                                         # 2 floats — simple Rectangle
or
Line 31: " A As I _ d ȳ"                                # 6 floats — Generic (Integral Properties) [recommended]
         Ex: " 0.000864 0 1.12e-006 0 0.1 0"           # 100x50x3 tube
```

**Recommendation**: always use **Generic Integral Properties** (6 floats) — most explicit, easiest to generate.

## ORIGIN node stub (lines 35-40)

The origin (0, 0) is special: 6 reserved lines that store its support and load.

```
Line 35: "0"
Line 36: "0"
Line 37: "[results_flag] Fx Fy Mz +float"               # SUPPORT + LOAD AT ORIGIN
         No support:      "0 0 0 0 +0.00000000e+000"
         Roller (Y fix):  "0 0 1 0 +0.00000000e+000"
         Pinned (X+Y):    "0 1 1 0 +0.00000000e+000"
         Fixed (X+Y+Z):   "0 1 1 1 +0.00000000e+000"
         After analysis:  "1 X X X +0" (pos 1 = result_flag = 1)
Line 38: "0 0 0"
Line 39: "0"
Line 40: "0 0 0 0"
```

## Entities (bars)

### Critical rule: two block types

| Type | When | Size |
|------|------|------|
| **`2 1 ...`** ("creator") | Bar that CREATES at least 1 new node (endpoint A is the new one) | **16 lines** |
| **`4 1 ...`** ("connector") | Bar between 2 existing nodes (no new nodes) | **12 lines** |

### `2 1 ...` block — Creator (16 lines)

```
Pos | Example line                                 | Content
----|----------------------------------------------|----------
01  | "1"                                          | entity start marker
02  | "2 1 [intCounters] 1 [ID]"                   | header (11 ints)
03  | "+X +Y"                                      | ONE endpoint coordinate (endpoint A)
04  | "0"                                          | constant
05  | "+xmin +xmax +ymin +ymax"                    | bbox enveloping BOTH endpoints
    |                                              | Other endpoint = opposite bbox corner
06  | "[res] [hingeMa] [hingeMb] [no_axial] [no_shear] 1" | flags incl. hinges + rigid (6 values)
07  | "0" or ID                                    | MATERIAL ID applied (0 = none)
08  | "0" or ID                                    | SECTION ID applied (0 = none)
09  | "0" or ID                                    | **MEMBER END MOMENT ID applied** (0 = none) — validated exp-I
10  | "0" or ID                                    | UNIFORM LOAD ID applied (0 = none)
11  | "0" or ID                                    | **LINEAR LOAD ID applied** (0 = none) — validated exp-H
12  | "0" or ID                                    | **THERMAL LOAD ID applied** (0 = none) — validated exp-J
13  | "[res] X_state Y_state Z_state ANGLE_DEG"    | **SUPPORT at endpoint Mb**. States: 0=free, 1=fix, 2=spring (validated exp-T). 5th float = angle in **DEGREES** (validated exp-W).
14  | "Kx Ky Kz"                                   | **Elastic spring stiffnesses** (kN/m, kN/m, kNm/rad). Non-zero only if respective state = 2
15  | "0" or ID                                    | NODAL LOAD ID at endpoint Mb
16  | "[flag] Dx Dy Rz"                            | **PRESCRIBED DISPLACEMENT** (validated exp-U). flag=1 if prescribed, Dx Dy in METERS, Rz in RAD. Ex: `1 0 -0.01 0` = Dy=-10mm
```

### `4 1 ...` block — Connector (12 lines)

```
Pos | Example line                                 | Content
----|----------------------------------------------|----------
01  | "1"                                          | marker
02  | "4 1 [internal refs] 1 [ID]"                 | header (11 ints, different format)
03  | "+xmin +xmax +ymin +ymax"                    | MODEL-wide bbox (not this entity's)
04  | "0"                                          | constant
05  | "+xmin +xmax +ymin +ymax"                    | this entity's bbox
06  | "[result_flag] 0 0 0 0 1"                    |
07  | "0" or "1"                                   | material applied
08  | "0" or ID                                    | section
09  | "0"                                          |
10  | "0" or ID                                    | uniform load
11  | "0"                                          |
12  | "0"                                          |
```

**Why 12 lines?** Connectors don't "own" any node — so no slots for support (pos 13), nodal load (pos 15), or endpoint A coord (pos 3). Material and section remain because they belong to the BAR.

## Implicit topology (MASTER rule)

**FTool does NOT store nodes as separate entities.** Instead:

- Each bar has 2 implicit endpoints: the coord (pos 3 of `2 1` block) + opposite bbox corner
- Two bars with matching endpoints (1 mm tolerance) → same node
- Topology is **derived** at file open, not stored

**Where each node's data lives:**
- Origin (0,0) node: line 37 of the stub
- Endpoint A of a `2 1` bar: pos 13 (support) and pos 15 (load) of the block
- Shared endpoint B: inherits from the bar that created it as endpoint A

**Implication for generation:**
Bar creation ORDER defines which bar "owns" each node's slot. Plan:
1. Define bar creation order
2. First endpoint you create becomes endpoint A (slot owner) of that bar
3. Subsequent bars touching that node use `4 1` (connector) block

## Hinge encoding (Rotation Release)

Position 6 of `2 1 ...` block (flags line, 6 values):

| Pos | Meaning |
|-----|---------|
| 1 | Result flag (becomes `1` after Solve) |
| **2** | **Hinge at INITIAL node (Ma)** = endpoint OPPOSITE to coord: 0 = rigid, 1 = released |
| **3** | **Hinge at END node (Mb)** = endpoint AT coord: 0 = rigid, 1 = released |
| **4** | **Ignore AXIAL deformation** (Rigid axial): 0 = consider, 1 = ignore — validated exp-V |
| **5** | **Ignore SHEAR deformation** (Rigid shear): 0 = consider, 1 = ignore — validated exp-V |
| 6 | Constant `1` |

"Rigid Member" in Deformation Constraints panel = both pos 4 and pos 5 set to 1.

⚠️ **Important**: the `coord` stored in pos 3 of the block is the **end node (Mb)**, NOT the initial node (Ma). Ma is the bbox-opposite corner. Order is defined by click order when creating the bar (first click = Ma, second click = Mb = coord).

Adding a hinge:
- ❌ Does NOT increment line 4 counters
- ❌ Does NOT change block size (still 16 lines)
- ✅ Only toggles 1 bit in the flags line

Example (experimentally validated):
- No hinge: `0 0 0 0 0 1`
- Hinge at Ma only (start): `0 1 0 0 0 1` ✓ validated
- Hinge at Mb only (end): `0 0 1 0 0 1` ✓ validated
- Hinges at both: `0 1 1 0 0 1` (expected)

## Support encoding

Position 13 of `2 1` block (endpoint Mb support line) or line 37 (origin):

Structure: `[result_flag] X_state Y_state Z_state ANGLE_DEG`

**Each DOF has 3 possible states:**

| Value | Meaning |
|-------|---------|
| 0 | Free |
| 1 | Fix (fully restrained) |
| 2 | Spring (elastic support) |

**Typical combinations:**

| Type | Encoding (X Y Z) | Δx | Δy | θz |
|------|------------------|----|----|-----|
| No support | `0 0 0` | free | free | free |
| Roller Y | `0 1 0` | free | **fix** | free |
| Pinned | `1 1 0` | **fix** | **fix** | free |
| Fixed | `1 1 1` | **fix** | **fix** | **fix** |
| Spring Y | `0 2 0` | free | **spring** | free |
| Rotational spring | `0 0 2` | free | free | **spring** |

**When state = 2 (spring), the next block line (pos 14) carries the Kx Ky Kz values**:
- Kx in kN/m (if X_state = 2)
- Ky in kN/m (if Y_state = 2)
- Kz in kNm/rad (if Z_state = 2)

Example: elastic roller in Y with K=1000 kN/m:
- Support line: `0 0 2 0 +0`
- Next line: `0 1000 0`

**Inclined support**: the 5th value of the support line is the inclination angle in **DEGREES** (not radians!) — positive counter-clockwise.

Example: pinned support inclined 30°:
- Support line: `0 1 1 0 +30` (validated exp-W)

Position 1 (`0` or `1`) is FTool's internal post-analysis flag — generate always as `0`.

## Analysis results

The `.ftl` does **NOT store results** (moments, reactions, deformations). Recomputed at runtime when file opens.

After Solve, what changes:
- Line 37 pos 1: 0 → 1 (flag "model analyzed")
- Line 6 and line 13 of each block, pos 1: 0 → N (internal indices)

These are **internal indices** — not result data. Can be zeroed to force recomputation.

## Valid generation flow (from-scratch `.ftl`)

1. Header copied from known-good file (Pratt, or one from `examples/`)
2. Define loads (lines 19-25) BEFORE entities
3. Define material(s) (lines 26-28)
4. Define section(s) (lines 29-34) — prefer Generic (6 floats)
5. Origin stub (lines 35-40): zeroed or with support if origin is fixed
6. For each bar:
   - Decide: creates new node (`2 1`, 16 lines) or connects existing (`4 1`, 12 lines)
   - Compute bbox = (min/max x, min/max y of 2 endpoints)
   - Pos 7 = 1 if material applied
   - Pos 8 = section ID (1 = first section defined)
   - Pos 10 = uniform load ID if applied
   - Pos 13 = support at endpoint A
   - Pos 15 = nodal load at endpoint A
7. Update counters (line 4): pos 6 and 7 = max created IDs
8. Final line: ` 0` with leading space

## Bar splitting — complex operation

Inserting a node IN THE MIDDLE of an existing bar is NOT trivial. FTool transforms 1 bar into **~5 entities**:

1. Left half (`2 1`, coord = split point)
2. Right half (`2 1`, coord = original endpoint)
3. Wrapper `4 1` with `±1e30 ±1e30 ±1e30 ±1e30` at pos 3 (ghost entity holding metadata)
4. Marker `-1 1 ...` at the end
5. (Possibly others)

Distribution of original properties:
- **Support + nodal load** (at original Mb endpoint) → right half
- **MEM + Linear + Thermal loads** (full-bar distributed) → left half + wrapper
- **Hinge at Mb** → wrapper

⚠️ **For programmatic `.ftl` generation: avoid splitting.** Create bars at the final intended sizes from the start. Wrappers and markers add unnecessary complexity.

Experimentally validated in exp-S-split-bar.

## Common errors

- ❌ Creating isolated "node entities" — FTool discards. There's no standalone node entity.
- ❌ Degenerate bboxes — OK for axis-aligned bars (ymin=ymax horizontal, xmin=xmax vertical). NOT OK for oblique.
- ❌ Confusing "elastic support" entity (`±1e30` values) with a member
- ❌ Not assigning material/section (pos 7/8 = 0) → "You must define materials to all members" error
- ❌ Mixing `2 1` and `4 1` formats arbitrarily — follow the "creator vs connector" rule
- ❌ Non-ASCII chars in names (ç, ã) may cause save/load issues on some systems
