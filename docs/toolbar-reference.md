# Referência da Interface FTool 4.01.00

Mapeamento completo da barra de ferramentas, painéis, atalhos e menus.

## Importante: nomes reais usados pelo FTool

O FTool **não usa "Hinge"** no UI — o equivalente é **"Rotation Release"**. Sempre referencie pelo nome real do painel pra evitar confusão.

## Barra de ferramentas principal (2ª horizontal, "Attribute Toolbar")

Cada ícone abre um painel à direita. Da esquerda pra direita:

| Pos | Painel | Aparência | Função |
|-----|--------|-----------|--------|
| 1 | **Material Parameters** | Cilindro | Define E, ν, ρ, α |
| 2 | **Section Properties** | "I" itálico | Define seção transversal (Generic, Rectangle, Box, Tube, etc.) |
| 3 | **Support Conditions** | △ Triângulo | Apoios em NÓS (Free/Fix em Δx/Δy/θz, molas, recalques) |
| 4 | **Rotation Release** ⭐ | Símbolo de conexão | **= HINGE/RÓTULA**. Libera rotação em extremidade de barra |
| 5 | **Deformation Constraints** | Casa com brackets | Flexible vs Rigid Member; toggle Axial/Shear Deformation |
| 6 | **Nodal Loading** | Vetor de força | Cargas concentradas em nós (Fx, Fy, Mz) |
| 7 | **Member End Moments** | Seta curva Ma/Mb | Momento concentrado nas pontas de barras |
| 8 | **Uniform Loading** | ↧↧↧ brackets | Carga distribuída uniforme (Qx, Qy em kN/m) |
| 9 | **Linear Loading** | Trapézio | Carga distribuída variando linearmente (Pxi/Pyi/Pxj/Pyj) |
| 10 | **Thermal Loading** | 🌡️ | Gradiente térmico (Ty+, Ty- em °C) |
| 11 | **Load Train** | Label | Trem de cargas móveis (análise de pontes) |

Junto da barra: dropdowns **Load Case**, **Load Comb**, **Load Train**; label **Editing Mode** (Selection/Creation).

## Grid de ícones dentro de cada painel direito

Layout padrão repetido em quase todos os painéis:

| Linha 1 | Função |
|---------|--------|
| Ícone 1 | "Create new..." |
| Ícone 2 | "Save current..." |
| Ícone 3 | "Load saved..." |
| Ícone 4 | "Delete current..." (X vermelho) |
| Ícone 5 | "Copy paste..." |

| Linha 2 | Função |
|---------|--------|
| Ícone 1 | **"Apply to selected"** ⭐ aplicar nos elementos selecionados |
| Ícone 2 | "Apply to all" |
| Ícone 3 | "Remove from selected" |
| Ícone 4 | "Remove from all" |

| Linha 3 (só Sections) | Função |
|---------|--------|
| 4 ícones | Mirror H/V, Rotate Left/Right (orientar perfil) |

## Atalhos de teclado

| Tecla | Função |
|-------|--------|
| **K** | Toggle **Keyboard mode** (input numérico via popup) |
| **M** | **Insert Member** |
| **N** | **Insert Node** |
| **D** | **Insert Dimension Line** |
| **F** | **Zoom Fit** |
| **ESC** | **Select mode** (seta) |
| Ctrl+A | Seleciona tudo |
| Ctrl+Z/Y | Desfazer/Refazer |
| Ctrl+S | Salvar |
| Delete | Apaga selecionados |

## Diagramas de resultados (canto superior direito)

4 ícones depois do "Load Train: NONE":

| Ícone | Tooltip | Mostra |
|-------|---------|--------|
| ↔ | Axial force | Diagrama N (normal) |
| ↕ | Shear force | Diagrama V (cortante) |
| ↻ | Bending moment | Diagrama M (momento) |
| ⌒ | Deformed configuration | Deformada + submenu Dx/Dy/Du/Dv/Rz |

Clicar em qualquer um **roda a análise automaticamente** se ainda não foi rodada.

## Menus

### File
- New (Ctrl+N), Open, Save, **Save As**
- Import Properties, Export Line Results, **Export Model Screen** (PNG limpo!)
- Totals, Model View Limits

### Options
- **Analysis** (rodar análise via menu)
- Display sizes (Support, Load, Text)
- Save analysis neutral file
- **Units & Number Formatting**

### Display
- Backgrounds (White/Gray/Black)
- Toggles: Dimension Lines, Supports, Loading, Reactions, Node Numbers, Member Numbers

## Barra vertical esquerda

| Pos | Ícone | Função |
|-----|-------|--------|
| 1 | Seta | **Select mode** (Esc) |
| 2 | `/` | **Insert Member** (M) |
| 3 | • | **Insert Node** (N) |
| 4 | `L_J` | **Insert Dimension Line** (D) |
| 5 | Teclado | **Keyboard mode** toggle (K) |
| 6 | X | **Delete** selected |
| 7 | Lupa+/− | Zoom in / out |

## Bottom-right (rodapé)

- ☐ **Grid** — liga malha quadriculada
- **X: ___ m** | **Y: ___ m** — espaçamento da malha (sugestão: 0.1 m)
- ☐ **Snap** — cursor "gruda" na malha
- Coordenadas do cursor em tempo real

## Workflow padrão (validado)

1. **File → New**
2. **Insert Member (M)** + Keyboard mode (K) → digita coords pra criar geometria
3. **Material Parameters** → "Create new" → "Apply to selected" (com tudo selecionado)
4. **Section Properties** → "Create new" → "Apply to selected"
5. **Support Conditions** → seleciona nó → marca Fix/Free → "Apply"
6. **Loads** → Nodal/Uniform/Linear → "Create new" → seleciona elemento → "Apply"
7. **Clica em qualquer diagrama (N, V, M, D)** → roda análise auto
8. **File → Save As**

## Erros comuns e soluções

| Erro | Solução |
|------|---------|
| "You must define materials to all members!" | Material → Apply em todas barras |
| Apply desabilitado | Nada selecionado, ou config incompleta |
| Carga não aplica | Tem que **criar definição primeiro** ("Create new") antes do campo ficar editável |
| Coords imprecisas | Ligar **Keyboard mode (K)** e usar popup |
| Nó difícil de selecionar | Zoom in pra clicar com precisão |
