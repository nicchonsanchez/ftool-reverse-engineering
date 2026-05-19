# Practical FTool Workflow

Validated strategies for efficient work with FTool.

## When to generate `.ftl` programmatically vs use the GUI

| Situation | Path |
|-----------|------|
| Simple, well-defined model | Generate `.ftl` directly and open |
| Complex model (>10 bars) | GUI step by step |
| Learning FTool | GUI required |
| Programmatic / batch edits | Generate `.ftl` directly |
| Parametric study (sweep dimensions) | Script generating `.ftl` directly |

## GUI step-by-step

### 1. Geometry

Use **Insert Member (M)** with **Keyboard mode (K) on** — not Insert Node. Each bar creates 2 endpoint nodes automatically.

For each bar:
- 1st Node X / Y (type exact coordinates)
- 2nd Node X / Y
- OK

Default tolerance (1 mm) ensures nearby nodes are reused.

### 2. Material and section

Before applying:
- **Edit material**: **Material Parameters** panel → "Create new" → fill E, ν, α, ρ
- **Edit section**: **Section Properties** panel → "Create new" → choose type (recommend **Generic > Integral Properties** for direct A/I)

Apply to all bars:
- Ctrl+A (select all)
- Material panel → "Apply current material to selected members"
- Section panel → "Apply current section to selected members"

### 3. Supports

For each supported node:
- Selection mode (ESC) → click node (turns red)
- **Support Conditions** panel → mark Fix in appropriate fields:
  - Fixed: Δx + Δy + θz all Fix
  - Pinned: Δx + Δy Fix, θz Free
  - Roller: only Δy Fix
- Click **"Apply support conditions to selected nodes"**

### 4. Loads

- **Uniform Loading** panel (for distributed on bar) or **Nodal Loading** (for concentrated at node)
- "Create new" → define value (Qy=-1 for 1 kN/m gravity load, e.g.)
- Select target bar/node
- "Apply" in panel

### 5. Analysis

Click any of the 4 result diagrams (N, V, M, D in top-right corner). FTool auto-runs analysis.

### 6. Save and export

- **File → Save** (.ftl to keep editable)
- **File → Export Model Screen** (clean PNG of current diagram)

## Section types — when to use each

| Type | When to use |
|------|-------------|
| **Generic > Integral Properties** | ⭐ When you know A and I directly (from profile catalog). Most explicit. |
| Rectangle | Solid rectangular section (not a tube!) |
| Box | Hollow rectangular tube. CAUTION: FTool sometimes computes area differently than expected. Verify A. |
| Tube / Pipe | Round tube |
| I-shape | Standard I-profile |
| Profile Tables | Commercial Brazilian profiles (Vallourec, Gerdau-AçoMinas) — saves time if using tabulated profile |

## Common materials

| Material | E (MPa) | ν | α | ρ (kN/m³) |
|----------|---------|---|---|-----------|
| ASTM A36 steel | 200000 | 0.3 | 1.2e-5 | 78.5 |
| A572 Gr 50 steel | 200000 | 0.3 | 1.2e-5 | 78.5 |
| Concrete fck=25 | 28000 | 0.2 | 1e-5 | 25 |
| Wood C30 | 14500 | 0.3 | 5e-6 | 8 |

## Useful tricks

- **Grid + Snap** (bottom-right): turn on grid with X=Y=0.1 m spacing for visual alignment
- **Display → White Background**: for white background instead of black (easier on the eyes)
- **Ctrl+A** selects all bars for batch application
- **Zoom Fit (F)**: centers view after creating model
- **Insert Continuous Line** (4th left icon): draws connected polyline (multiple bars at once)

## Common errors

| Error | What happens | Solution |
|-------|-------------|----------|
| Material not assigned | Analysis fails, error popup | Ctrl+A → Material → Apply |
| Empty Q fields in load | Apply does nothing | Create definition first ("Create new") |
| Click outside node | Support won't apply | Zoom in + click exactly on node "+" |
| Panel doesn't change | Wrong Editing Mode | Click correct icon (not by panel name, click the toolbar icon) |

## Implicit topology

For two bars to share a node:
- Create 1st bar ending at (X, Y)
- Create 2nd bar starting EXACTLY at (X, Y) (use Keyboard mode for precision)
- FTool auto-recognizes same node (1 mm tolerance)
- Geometry becomes "welded" — moving the node moves both bars
