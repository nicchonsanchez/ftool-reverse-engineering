# Prompt — Gerar Pórtico Engastado

## 🇧🇷 Português

```
Você é um assistente especializado no formato .ftl do FTool 4.01.00.

Tenho a documentação completa do formato (anexada acima). Use como referência.

Gere um arquivo .ftl completo pra um pórtico bi-engastado com:

GEOMETRIA:
- Coluna esquerda: vertical de (0, 0) a (0, [ALTURA_ESQ])
- Viga horizontal/inclinada: de (0, [ALTURA_ESQ]) a ([VÃO], [ALTURA_DIR])
- Coluna direita: vertical de ([VÃO], [ALTURA_DIR]) a ([VÃO], 0)

APOIOS:
- Engaste em (0, 0): Δx + Δy + θz todos fixos
- Engaste em ([VÃO], 0): Δx + Δy + θz todos fixos

CARGAS:
- Carga distribuída uniforme NA VIGA (não nas colunas): Qy = -[VALOR] kN/m

MATERIAL:
- Aço ASTM A36: E = 2e+008, ν = 0.3, ρ = 78.5, α = 1.2e-005

SEÇÃO:
- Generic: A = [ÁREA] m², I = [INÉRCIA] m⁴

REQUISITOS CRÍTICOS:
1. Ordem das barras (importa pra topologia):
   - B1 = coluna esquerda (cria nó topo-esq)
   - B2 = viga (cria nó topo-dir, reaproveita topo-esq)
   - B3 = coluna direita (reaproveita topo-dir e bottom-dir)
2. B1 e B2 usam encoding "2 1 ..." (16 linhas) — criam nó novo
3. B3 usa encoding "4 1 ..." (12 linhas) — só conecta nós existentes
4. Bbox de cada barra: min/max das duas pontas
5. Engaste em (0,0) vai na linha 37 (stub origem)
6. Engaste em ([VÃO], 0) vai no pos 13 da barra B3 (mas B3 é connector "4 1" que NÃO tem slot pos 13!) 
   - SOLUÇÃO: a barra que tem ([VÃO], 0) como endpoint A é a B3 quando essa for "2 1"
   - Reordene barras se preciso pra que o nó com engaste esteja como endpoint A de uma barra "2 1"
7. Material aplicado (pos 7 = 1) e seção (pos 8 = 1) em todas as barras
8. Carga uniforme aplicada (pos 10 = 1) só na B2

Retorne SÓ o arquivo .ftl.
```

**Parâmetros:**
- `[ALTURA_ESQ]`, `[ALTURA_DIR]`: alturas das colunas em m
- `[VÃO]`: distância entre colunas em m
- `[VALOR]`: carga distribuída em kN/m
- `[ÁREA]`, `[INÉRCIA]`: propriedades da seção

---

## 🇺🇸 English

```
You are an assistant specialized in FTool 4.01.00 .ftl format.

Generate a complete .ftl file for a fixed-base portal frame:

GEOMETRY:
- Left column: vertical from (0, 0) to (0, [HEIGHT_L])
- Beam (horizontal or inclined): from (0, [HEIGHT_L]) to ([SPAN], [HEIGHT_R])
- Right column: vertical from ([SPAN], [HEIGHT_R]) to ([SPAN], 0)

SUPPORTS:
- Fixed support at (0, 0): Δx + Δy + θz all restricted
- Fixed support at ([SPAN], 0): Δx + Δy + θz all restricted

LOADS:
- Uniform distributed load ON THE BEAM only: Qy = -[VALUE] kN/m

MATERIAL:
- ASTM A36: E = 2e+008, ν = 0.3, ρ = 78.5, α = 1.2e-005

SECTION:
- Generic: A = [AREA] m², I = [INERTIA] m⁴

CRITICAL REQUIREMENTS:
1. Bar order (matters for topology):
   - B1 = left column (creates top-left node)
   - B2 = beam (creates top-right node, reuses top-left)
   - B3 = right column (reuses top-right and bottom-right)
2. B1 and B2 use "2 1 ..." encoding (16 lines) — create new nodes
3. B3 uses "4 1 ..." encoding (12 lines) — only connects existing
4. Bar bbox: min/max of the two endpoints
5. Fixed support at (0,0) goes in line 37 (origin stub)
6. Fixed support at ([SPAN], 0): tricky! "4 1" connector doesn't have pos 13 slot.
   SOLUTION: Reorder bars so the supported node is endpoint A of a "2 1" bar.
7. Material applied (pos 7 = 1) and section (pos 8 = 1) on all bars
8. Uniform load applied (pos 10 = 1) only on B2

Return ONLY the .ftl file.
```

**Parameters:**
- `[HEIGHT_L]`, `[HEIGHT_R]`: column heights in m
- `[SPAN]`: distance between columns in m
- `[VALUE]`: distributed load in kN/m
- `[AREA]`, `[INERTIA]`: section properties
