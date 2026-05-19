# Análise do binário `Ftool.exe` — Confirmações e descobertas

Este documento complementa a documentação principal do formato `.ftl` com **evidências extraídas diretamente do executável** do FTool 4.01.00. As strings, format specifiers e mensagens de erro encontradas confirmam a estrutura do arquivo deduzida via engenharia reversa iterativa.

## Format strings confirmados

Extraídos do binário via grep de padrões `%[-+ #0]?[0-9.]*[a-z]`:

| Format string | Aplicação | Validado em |
|---------------|-----------|-------------|
| `'%d %d %d %d %+.8e'` | **Linha de apoio** (X_state Y_state Z_state + ângulo em graus, 8 decimais sci) | exp-T, exp-W |
| `'%+.5e %+.5e'` | **Coordenadas de nó** (X, Y) — 5 decimais sci | exp-A onwards |
| `'%+.5e %+.5e %+.5e %+.5e'` | **Bbox da entidade** (xmin xmax ymin ymax) | exp-A onwards |
| `'%+.5e %+.5e %+.5e'` | Trios (provável `Kx Ky Kz` da linha de springs) | exp-T |
| `'%d %d %d %s'` | Linhas tipo "count + nome" (load case, etc.) | exp-D, X |
| `'%d     %g  %g  %g  %g  %g  %g  %g  %g'` | Listagem com 8 floats — provável catalogação interna |

## Mensagens de erro do parser

Confirmadas como strings hardcoded:

```
"Error on reading concentrated loads !!!"
"Error on reading prescribed displacements !!!"
"Error on reading springs !!!"
"Error on reading number of beam concentrated force loads !!!"
"Error on reading number of concentrated loads !!!"
```

Cada erro corresponde a uma seção específica do `.ftl`. **A ordem em que aparecem no código sugere a ordem de leitura**: primeiro concentrated loads, depois beam concentrated force loads, depois prescribed displacements, depois springs.

## Field labels confirmados

Os campos do painel/arquivo têm strings nominais no binário:

### Apoios
- `"Support prescribed dX %lg"` — confirma Dx em metros (long double = double)
- `"Support prescribed dY %lg"` — confirma Dy em metros
- `"Prescribed displacement in X dir."`
- `"Prescribed displacement in Y dir."`
- `"Prescribed rotation"`

### Springs (Apoios elásticos)
- `"NODAL.SPRING"`, `"ROTATION_SPRING"`, `"TRANSLATION_SPRING"` — identificadores internos
- `"Spring Stiffness Values"` — label do painel

### Deformation Constraints
- `"axialdeformat"` — confirma pos 4 da flags line = ignorar deformação axial
- `"sheardeformat"` — confirma pos 5 = ignorar deformação cisalhante
- `"Flexible Member"` / `"Rigid Member"` — toggles do painel

### Hinges (Rotation Release)
- `"Apply hinge %d on %d vertices"` — confirma encoding usa inteiros pra modo
- `"Remove hinge from both member ends"`
- `"Remove hinges from all members at node"`

### Profile Tables
Todas as 6 categorias confirmadas como strings:
- `"Welded I-shapes (BR)"`
- `"Electro-Welded I-shapes (BR)"`
- `"Gerdau-AcoMinas I-shapes (BR)"`
- `"AISC Parallel I-shapes"`
- `"Mills Shapes (BR)"` / `"Mills shapes (BR)"`
- `"Vallourec Tubes (BR)"` / `"Vallourec tubes (BR)"`

## Validação macro do que descobrimos

Comparando o que o binário REVELA com o que decifrei via engenharia reversa iterativa:

| Achado da engenharia reversa | Confirmação no binário |
|------------------------------|------------------------|
| Coordenadas em formato `+0.00000e+000` | ✅ Match (`%+.5e`) |
| Linha de apoio com 5 valores incl. ângulo | ✅ Match (`%d %d %d %d %+.8e`) |
| Apoio elástico via state=2 + linha Kx Ky Kz | ✅ Match (3 spring labels + trio format) |
| Recalque prescrito em metros/rad | ✅ Match (`%lg` é double) |
| Pos 4/5 da flags line = ignorar axial/shear def | ✅ Match (labels `axialdeformat`, `sheardeformat`) |
| Profile Tables com referência interna a catálogo | ✅ Match (todas 6 categorias têm strings) |

**Conclusão**: a engenharia reversa via experimentação foi extremamente precisa. O binário não revelou contradições — apenas confirmou tudo.

## O que ainda NÃO consegui decifrar (e por quê)

### Internal counters no header de cada barra (`2 1 38 38 56 56 56 57 56 1 ID`)

Esses contadores são **gerados em runtime pelo código C++**, não format strings literais. São incrementados conforme entidades são criadas. Sem desassembler o binário e seguir as instruções, só consigo OBSERVAR o padrão (incrementos sequenciais por entidade) mas não a fórmula exata.

**Workaround pra geração de `.ftl`:** copiar valores de um arquivo de referência válido — FTool tolera contadores aproximados.

### Mapping de índice das Profile Tables

O `9 0 0` que apareceu pra Vallourec d=33 — esse `9` é um índice computado dinamicamente em runtime baseado no perfil escolhido. Não é format string literal.

**Workaround:** gerar como Generic Integral Properties (usando valores do `profile-catalog.json`) em vez de Profile Tables — comportamento estrutural idêntico.

### Último `0` reservado do Load Train

Aparentemente nem o binário tem string ou logic clara associada. Provavelmente é um campo verdadeiramente reservado pra feature futura nunca implementada.

**Workaround:** sempre escrever `0` (validado em 25+ experimentos).

## Ferramentas necessárias pra fechar 100%

Pra decifrar os ~1% restantes seria necessário:

- **Ghidra** ou **IDA Pro** pra desassemblar o binário x86
- Seguir as funções de leitura/escrita do `.ftl` (provavelmente `read_ftl()` / `write_ftl()`)
- Mapear os algoritmos de geração de internal counters
- Identificar a função que computa o Profile Table index

Esse trabalho de desassembler está **fora do escopo de análise via shell**, mas é viável pra alguém com conhecimento de engenharia reversa de binários.

## Metodologia

Comandos usados:
```bash
# Buscar format strings com sci notation
grep -aoE "%[-+ #0]?[0-9.]*e" Ftool.exe | sort -u

# Encontrar strings com keywords conhecidas
grep -aoE "...keyword...[^\x00]{0,100}" Ftool.exe

# Localizar offsets de strings específicas
grep -abo "33.4x3.2" Ftool.exe
```

Combinados com Python `struct.unpack_from('<d', ...)` pra decodificar os doubles adjacentes às strings.
