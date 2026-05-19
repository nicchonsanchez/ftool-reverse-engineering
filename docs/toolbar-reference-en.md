# FTool 4.01.00 Interface Reference

Complete mapping of toolbar, panels, shortcuts, and menus.

## Important: real names used by FTool

FTool **does not use "Hinge"** in the UI — the equivalent is **"Rotation Release"**. Always reference by the real panel name to avoid confusion.

## Main Attribute Toolbar (2nd horizontal)

Each icon opens a right-side panel. From left to right:

| Pos | Panel | Appearance | Purpose |
|-----|-------|------------|---------|
| 1 | **Material Parameters** | Cylinder | Defines E, ν, ρ, α |
| 2 | **Section Properties** | Italic "I" | Defines cross-section (Generic, Rectangle, Box, Tube, etc.) |
| 3 | **Support Conditions** | △ Triangle | Supports at NODES (Free/Fix on Δx/Δy/θz, springs, prescribed displacements) |
| 4 | **Rotation Release** ⭐ | Connection symbol | **= HINGE**. Releases rotation at a bar end |
| 5 | **Deformation Constraints** | House w/ brackets | Flexible vs Rigid Member; toggle Axial/Shear Deformation |
| 6 | **Nodal Loading** | Force vector | Concentrated loads at nodes (Fx, Fy, Mz) |
| 7 | **Member End Moments** | Curved arrow Ma/Mb | Concentrated moment at bar ends |
| 8 | **Uniform Loading** | ↧↧↧ brackets | Uniform distributed load (Qx, Qy in kN/m) |
| 9 | **Linear Loading** | Trapezoid | Linearly varying load (Pxi/Pyi/Pxj/Pyj) |
| 10 | **Thermal Loading** | 🌡️ | Thermal gradient (Ty+, Ty- in °C) |
| 11 | **Load Train** | Label | Moving load train (bridge analysis) |

Along the bar: dropdowns **Load Case**, **Load Comb**, **Load Train**; label **Editing Mode** (Selection/Creation).

## Icon grid inside each right-side panel

Standard layout repeated in nearly all panels:

| Row 1 | Function |
|-------|----------|
| Icon 1 | "Create new..." |
| Icon 2 | "Save current..." |
| Icon 3 | "Load saved..." |
| Icon 4 | "Delete current..." (red X) |
| Icon 5 | "Copy paste..." |

| Row 2 | Function |
|-------|----------|
| Icon 1 | **"Apply to selected"** ⭐ apply to selected elements |
| Icon 2 | "Apply to all" |
| Icon 3 | "Remove from selected" |
| Icon 4 | "Remove from all" |

| Row 3 (Sections only) | Function |
|-------|----------|
| 4 icons | Mirror H/V, Rotate Left/Right |

## Keyboard shortcuts

| Key | Function |
|-----|----------|
| **K** | Toggle **Keyboard mode** (numeric input via popup) |
| **M** | **Insert Member** |
| **N** | **Insert Node** |
| **D** | **Insert Dimension Line** |
| **F** | **Zoom Fit** |
| **ESC** | **Select mode** (arrow) |
| Ctrl+A | Select all |
| Ctrl+Z/Y | Undo/Redo |
| Ctrl+S | Save |
| Delete | Delete selected |

## Result diagrams (top-right corner)

4 icons after "Load Train: NONE":

| Icon | Tooltip | Shows |
|------|---------|-------|
| ↔ | Axial force | N diagram (axial) |
| ↕ | Shear force | V diagram (shear) |
| ↻ | Bending moment | M diagram (moment) |
| ⌒ | Deformed configuration | Deformed shape + submenu Dx/Dy/Du/Dv/Rz |

Clicking any of these **automatically runs the analysis** if not already done.

## Menus

### File
- New (Ctrl+N), Open, Save, **Save As**
- Import Properties, Export Line Results, **Export Model Screen** (clean PNG!)
- Totals, Model View Limits

### Options
- **Analysis** (run analysis via menu)
- Display sizes (Support, Load, Text)
- Save analysis neutral file
- **Units & Number Formatting**

### Display
- Backgrounds (White/Gray/Black)
- Toggles: Dimension Lines, Supports, Loading, Reactions, Node Numbers, Member Numbers

## Left vertical toolbar

| Pos | Icon | Function |
|-----|------|----------|
| 1 | Arrow | **Select mode** (Esc) |
| 2 | `/` | **Insert Member** (M) |
| 3 | • | **Insert Node** (N) |
| 4 | `L_J` | **Insert Dimension Line** (D) |
| 5 | Keyboard | **Keyboard mode** toggle (K) |
| 6 | X | **Delete** selected |
| 7 | Magnify+/− | Zoom in/out |

## Bottom-right (footer)

- ☐ **Grid** — toggle grid display
- **X: ___ m** | **Y: ___ m** — grid spacing (suggested: 0.1 m)
- ☐ **Snap** — cursor "snaps" to grid
- Real-time cursor coordinates

## Standard workflow (validated)

1. **File → New**
2. **Insert Member (M)** + Keyboard mode (K) → type coords to build geometry
3. **Material Parameters** → "Create new" → "Apply to selected" (with all selected)
4. **Section Properties** → "Create new" → "Apply to selected"
5. **Support Conditions** → select node → check Fix/Free → "Apply"
6. **Loads** → Nodal/Uniform/Linear → "Create new" → select element → "Apply"
7. **Click any diagram (N, V, M, D)** → analysis auto-runs
8. **File → Save As**

## Common errors and solutions

| Error | Solution |
|-------|----------|
| "You must define materials to all members!" | Material → Apply to all bars |
| Apply button disabled | Nothing selected, or config incomplete |
| Load won't apply | Must **Create definition first** before field becomes editable |
| Imprecise coords | Enable **Keyboard mode (K)** and use popup |
| Hard to select node | Zoom in to click precisely |
