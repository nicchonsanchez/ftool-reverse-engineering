# Prompt — Gerar Treliça (Pratt / Warren / Howe)

## 🇧🇷 Português

```
Você é um assistente especializado no formato .ftl do FTool 4.01.00.

Tenho a documentação completa do formato anexada. Use como referência.

Gere um arquivo .ftl pra uma treliça [TIPO] com as características:

TIPO DE TRELIÇA:
- [TIPO]: Pratt, Warren ou Howe
- Número de painéis: [N_PAINÉIS]
- Vão total: [VÃO] m (cada painel = VÃO/N_PAINÉIS m)
- Altura: [ALTURA] m (entre banzo inferior e superior)

GEOMETRIA:
- Banzo inferior: nós em y = 0, x = 0, [PAINEL], 2*[PAINEL], ..., [VÃO]
- Banzo superior: nós em y = [ALTURA], x conforme padrão da treliça
- Pratt: diagonais inclinadas pro centro (V invertido)
- Warren: diagonais alternando (zigzag)
- Howe: diagonais inclinadas pras extremidades

APOIOS:
- Articulado (pinned) em (0, 0): Δx + Δy fixos
- Rolete em ([VÃO], 0): só Δy fixo

CARGAS:
- Carga concentrada de -[CARGA] kN em cada nó do banzo SUPERIOR (interior)

MATERIAL e SEÇÃO:
- Aço A36, área pequena (tipo cabos ou perfis leves): A = [ÁREA] m²
- I muito pequena também (treliça resiste só por axial, não momento): I = [INÉRCIA] m⁴

REQUISITOS CRÍTICOS:
1. Defina TODOS os nós primeiro mentalmente — calcule x, y de cada um
2. Determine ORDEM de criação das barras:
   - Banzo inferior primeiro (cria nós sequencialmente) → todas "2 1"
   - Depois banzo superior → todas "2 1" se cria nós novos
   - Por último: verticais e diagonais → algumas "2 1" se criam nó, outras "4 1" se só conectam
3. Pra cada nó com carga concentrada, esse nó tem que estar como endpoint A de UMA das barras "2 1"
4. Bbox de cada barra: min/max x e y das 2 pontas
5. Contadores na linha 4 incrementam por barra criada
6. Cargas nodais definidas em uma só vez (linha 21+) e referenciadas por ID em pos 15 das barras "2 1"

Pra cada barra, especifique:
- Tipo de bloco ("2 1" ou "4 1")
- Coord (se "2 1") = endpoint A
- Bbox
- Endpoint A é qual nó da estrutura?
- Tem material/seção/carga aplicada?

Retorne SÓ o arquivo .ftl válido.
```

**Parâmetros:**
- `[TIPO]`: Pratt, Warren, Howe
- `[N_PAINÉIS]`: ex: 4
- `[VÃO]`: vão total em m
- `[ALTURA]`: altura da treliça em m
- `[CARGA]`: carga em cada nó em kN

---

## 🇺🇸 English

```
You are an assistant specialized in FTool 4.01.00 .ftl format.

Generate a .ftl file for a [TYPE] truss with:

TRUSS TYPE:
- [TYPE]: Pratt, Warren, or Howe
- Number of panels: [N_PANELS]
- Total span: [SPAN] m (each panel = SPAN/N_PANELS m)
- Height: [HEIGHT] m (between bottom and top chord)

GEOMETRY:
- Bottom chord: nodes at y = 0, x = 0, [PANEL], 2*[PANEL], ..., [SPAN]
- Top chord: nodes at y = [HEIGHT], x according to truss pattern
- Pratt: diagonals slope toward center (inverted V)
- Warren: alternating diagonals (zigzag)
- Howe: diagonals slope toward ends

SUPPORTS:
- Pinned at (0, 0): Δx + Δy fixed
- Roller at ([SPAN], 0): only Δy fixed

LOADS:
- Concentrated load -[LOAD] kN at each interior top chord node

MATERIAL and SECTION:
- A36 steel, small area: A = [AREA] m²
- Small I (truss resists axially): I = [INERTIA] m⁴

CRITICAL REQUIREMENTS:
1. Define ALL nodes mentally first — compute x, y of each
2. Determine bar creation ORDER:
   - Bottom chord first (creates nodes sequentially) → all "2 1"
   - Then top chord → all "2 1" if creating new nodes
   - Last: verticals and diagonals → some "2 1" (create node) or "4 1" (only connect)
3. Each node with concentrated load MUST be endpoint A of one of the "2 1" bars
4. Each bar bbox: min/max x and y of both endpoints
5. Counters in line 4 increment per bar created
6. Nodal loads defined ONCE (line 21+) and referenced by ID in pos 15 of "2 1" bars

For each bar, specify:
- Block type ("2 1" or "4 1")
- Coord (if "2 1") = endpoint A
- Bbox
- Which structure node is endpoint A?
- Has material/section/load applied?

Return ONLY the valid .ftl file.
```

**Parameters:**
- `[TYPE]`: Pratt, Warren, Howe
- `[N_PANELS]`: e.g., 4
- `[SPAN]`: total span in m
- `[HEIGHT]`: truss height in m
- `[LOAD]`: load at each node in kN
