# Prompt — Gerar Viga Simples Biapoiada

## 🇧🇷 Português

```
Você é um assistente especializado no formato .ftl do FTool 4.01.00.

Tenho a documentação completa do formato (anexada acima). Use ela como referência.

Gere um arquivo .ftl completo pra uma viga biapoiada com as seguintes características:

GEOMETRIA:
- Viga horizontal de [VÃO] m de comprimento (de x=0 até x=[VÃO])
- Posição: y = 0

APOIOS:
- Apoio articulado (pinned) em x = 0, y = 0  (Δx + Δy fixos, rotação livre)
- Apoio rolete em x = [VÃO], y = 0  (só Δy fixo)

CARGAS:
- Carga distribuída uniforme em toda a viga: Qy = -[VALOR] kN/m
  (sinal negativo = pra baixo no eixo Y global)

MATERIAL:
- Aço ASTM A36: E = 2e+008 kN/m², ν = 0.3, ρ = 78.5, α = 1.2e-005

SEÇÃO:
- Generic (Integral Properties): A = [ÁREA] m², I = [INÉRCIA] m⁴

REQUISITOS:
1. Use formato Generic pra seção (6 floats)
2. Use encoding "2 1 ..." pra primeira barra (creator de novo nó)
3. Bbox da barra: ymin=ymax=0 (degenerado pra horizontal)
4. Contadores na linha 4 corretamente incrementados
5. Origem (0,0) terá o apoio articulado encodado na linha 37 do stub
6. Linha final: " 0" (com espaço inicial)

Retorne SÓ o conteúdo do arquivo .ftl, sem explicações adicionais.
```

**Parâmetros a substituir:**
- `[VÃO]`: comprimento em metros (ex: 8)
- `[VALOR]`: carga em kN/m (ex: 10)
- `[ÁREA]`: área da seção em m² (ex: 0.000864)
- `[INÉRCIA]`: inércia em m⁴ (ex: 1.12e-006)

---

## 🇺🇸 English

```
You are an assistant specialized in FTool 4.01.00 .ftl format.

I have the complete format documentation (attached above). Use it as reference.

Generate a complete .ftl file for a simply supported beam with:

GEOMETRY:
- Horizontal beam, [SPAN] m long (from x=0 to x=[SPAN])
- Position: y = 0

SUPPORTS:
- Pinned support at x = 0, y = 0  (Δx + Δy fixed, rotation free)
- Roller support at x = [SPAN], y = 0  (only Δy fixed)

LOADS:
- Uniform distributed load along the entire beam: Qy = -[VALUE] kN/m
  (negative = downward in global Y axis)

MATERIAL:
- ASTM A36 steel: E = 2e+008 kN/m², ν = 0.3, ρ = 78.5, α = 1.2e-005

SECTION:
- Generic (Integral Properties): A = [AREA] m², I = [INERTIA] m⁴

REQUIREMENTS:
1. Use Generic format for section (6 floats)
2. Use "2 1 ..." encoding for the first bar (creator of new node)
3. Bar bbox: ymin=ymax=0 (degenerate for horizontal)
4. Counters in line 4 correctly incremented
5. Origin (0,0) will have the pinned support encoded in line 37 of the stub
6. Final line: " 0" (with leading space)

Return ONLY the .ftl file content, no additional explanations.
```

**Parameters to replace:**
- `[SPAN]`: length in meters (e.g., 8)
- `[VALUE]`: load in kN/m (e.g., 10)
- `[AREA]`: section area in m² (e.g., 0.000864)
- `[INERTIA]`: inertia in m⁴ (e.g., 1.12e-006)
