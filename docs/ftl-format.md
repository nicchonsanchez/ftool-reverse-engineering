# Formato `.ftl` do FTool 4.01.00 — Decodificação

Documentação técnica do formato de arquivo `.ftl` usado pelo [FTool](https://www.tecgraf.puc-rio.br/ftool/) versão 4.01.00 (PUC-Rio).

Texto plano, separação por espaço. **Não é binário**, mas tem IDs cruzados, bboxes pré-calculados e flags não-documentados publicamente.

---

## Estrutura macro

```
Linhas 1-16:     Cabeçalho (versão, viewport, contadores, display flags)
Linhas 17-18:    Load case (caso de carregamento)
Linhas 19-25:    Cargas pontuais nodais + cargas uniformes em barras
Linhas 26-28:    Materiais
Linhas 29-34:    Seções
Linhas 35-40:    "Stub" da entidade — apoio/carga no NÓ DA ORIGEM (0,0)
Linha 41+:       Entidades (barras) — bloco de 16 linhas cada (ou 12 pra "conectores")
Linha final:     " 0" (terminador, com espaço inicial)
```

## Cabeçalho (linhas 1-16)

Copie de um arquivo conhecido bom. Maioria das linhas é constante.

```
Linha 1:  "401 0"                                        # versão + flag (constante)
Linha 2:  "206"                                          # constante
Linha 3:  "1"                                            # constante
Linha 4:  "0 1 [matMax] [secMax] [nodeMax] [counter1] [counter2] 0 0 0 0"
          counter1 e counter2 incrementam por entidade criada (marcas d'água globais).
          counter2 = próximo ID disponível de entidade.
Linha 5:  "+X_cursor +Y_cursor"                          # posição cursor/centro
          Se modelo vazio: ±1e30 (sentinela "viewport não inicializado")
Linha 6:  "+xmin +xmax +ymin +ymax"                      # viewport bbox
          Se vazio: ±1e30 em todos os 4
Linha 7:  "0.01 0.01"                                    # escala/zoom (típico)
Linha 8:  "1 0 1 0 1 1 1 0 0 0 1 1 0 0 0"                # display flags
Linha 9:  "[step] 0"                                     # Step do topo da tela em metros (validado exp-X10)
Linhas 10-13: blocos constantes (counters internos, deixar igual)
Linha 14: "1 1"
Linha 15: "0 1 1 1 1 0"
Linha 16: "1 1 0"
```

## Load case (linhas 17-18)

```
Linha 17: "1"                                            # número de casos de carregamento
Linha 18: "'Load Case 01' 1"                             # 'nome' [flag]
```

⚠️ **Limitação importante**: múltiplos load cases e combinações (Load Comb) são **exclusivos da Advanced Edition** do FTool. Na Standard Edition (que a maioria usa), só rola 1 load case. Os ícones de "Load Case" e "Load Comb" ficam desabilitados na UI. O dropdown no topo só funciona pra alternar entre cases pré-existentes em arquivos Advanced.

Pra gerar `.ftl` com múltiplos cases programaticamente: linha 17 = N (número de cases), linhas 18..(17+N) = `'nome' flag` pra cada um. Mas o FTool Standard pode não conseguir abrir arquivos com N > 1 corretamente (não validado).

## Cargas (linhas 19-25)

```
Linha 19: "0"
Linha 20: [count_cargas_pontuais]                        # número de cargas nodais
Linha 21: "'nome' Fx Fy Mz"                              # repete N vezes pra N cargas
          Ex: "'F' 0 -10 0" = carga 10 kN pra baixo, sem momento
Linha após nodal: "0" (separador)
Próxima: [count_uniform]
Linhas: "[flag] 'nome' Qx Qy"                            # 4 valores
        Ex: "0 'Gravidade' 0 -1" = -1 kN/m vertical
Linha após uniform: "0" (separador)
Próxima: [count_linear]
Linhas: "[flag] 'nome' Pxi Pyi Pxj Pyj"                  # 6 valores (validado exp-H)
        Ex: "0 'Trapezoidal' 0 0 0 -5" = ramp de 0 a -5 kN/m
Linhas seguintes: "0" "0" "0"... (slots pra Member End Moments, Thermal, etc.)

**Load Trains** (cargas móveis pra análise de pontes) vêm DEPOIS de todos os tipos de carga estática, numa seção complexa. Estrutura decodificada (exp-X a X4):

```
Linha: [count_trains]                       # número de trens definidos
Pra cada trem:
  Linha: 'nome_train'                       # nome em linha separada
  Linha: [impact_factor]                    # fator de impacto (validado X1)
  Linha: [N = count_concentrated_loads]     # número de cargas concentradas
  N linhas: "x  P"                          # matriz: posição[m] força[kN]
  Linha: [M = count_distributed_loads]      # número de cargas distribuídas
  M linhas: "xa  xb  q'  q"                 # SEMPRE 4 valores (q'=q se single mode)
  Linha: [live_load_exterior]               # kN/m (validado X4)
  Linha: [live_load_interior]               # kN/m (validado X7)
  Linha: [length_m]                         # comprimento do trem (validado X2)
  Linha: [full_empty_flag]                  # 0=single car, 1=full/empty (X6)
  Linha: 0                                  # separador entre trens (sempre 0)
Após TODOS os trens:
  Linha: [selected_train_index]             # train selecionado no dropdown (0-indexed, X8)
  Linha: 0                                  # reservado (uso desconhecido)
```

Exemplo (Impact=3, Length=5, 3 cargas concentradas, Live Load Ext=-7):
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

⚠️ **Convenção de sinal**: FTool armazena cargas de trem como NEGATIVAS (assumindo gravidade pra baixo). Se você digitar 7 no campo Live Load Exterior, FTool salva -7 automaticamente.

Pontos confirmados via exp-X:
- Load Trains NÃO têm slot por barra — são globais ao modelo
- A seção fica entre cargas estáticas (Thermal) e stub da origem
- Contadores na linha 4 não mudam (load train não conta como "entidade")
```

## Materiais (linhas 26-28)

```
Linha 26: [count_materiais]                              # ex: "1"
Linha 27: "'nome' [flag]"                                # ex: "'Aço A36' 0"
Linha 28: " E ν ρ α"                                     # 4 floats, com leading space
```

**Materiais comuns:**

| Material | E (kN/m²) | ν | ρ | α |
|----------|-----------|---|---|---|
| Aço ASTM A36 | 2e+008 | 0.3 | 78.5 | 1.2e-005 |
| Concreto fck=25 MPa | 2.8e+007 | 0.2 | 25 | 1e-005 |
| Madeira (genérico) | 7.35e+006 | 0.3 | 10 | 1e-005 |

## Seções (linhas 29-34)

```
Linha 29: [count_secoes]                                 # ex: "1"
Linha 30: "'nome' [flag1] [flag2]"                       # ex: "'Tubo 100x50x3' 0 0"
Linha 31: " A I"                                         # 2 floats — formato Rectangle simples
ou
Linha 31: " A As I _ d ȳ"                                # 6 floats — formato Generic (Integral Properties) [recomendado]
          Ex: " 0.000864 0 1.12e-006 0 0.1 0"           # tubo 100x50x3 mm
```

**Recomendação**: use sempre **Generic Integral Properties** (6 floats) — é mais explícito e fácil de gerar.

## Stub do nó da ORIGEM (linhas 35-40)

A origem (0, 0) é especial: tem 6 linhas reservadas que armazenam seu apoio e carga.

```
Linha 35: "0"
Linha 36: "0"
Linha 37: "[results_flag] Fx Fy Mz +float"               # APOIO + CARGA NA ORIGEM
          Sem apoio:        "0 0 0 0 +0.00000000e+000"
          Rolete (Y fixo):  "0 0 1 0 +0.00000000e+000"
          Articulado (X+Y): "0 1 1 0 +0.00000000e+000"
          Engaste (X+Y+Z):  "0 1 1 1 +0.00000000e+000"
          Após análise:     "1 X X X +0" (pos 1 = result_flag = 1)
Linha 38: "0 0 0"
Linha 39: "0"
Linha 40: "0 0 0 0"
```

## Entidades (barras)

### Regra crítica: dois tipos de bloco

| Tipo | Quando | Tamanho |
|------|--------|---------|
| **`2 1 ...`** ("creator") | Barra que CRIA pelo menos 1 nó novo (endpoint A é o novo) | **16 linhas** |
| **`4 1 ...`** ("connector") | Barra entre 2 nós já existentes (nenhum nó novo) | **12 linhas** |

### Bloco `2 1 ...` — Creator (16 linhas)

```
Pos | Linha exemplo                                | Conteúdo
----|----------------------------------------------|----------
01  | "1"                                          | marker início entidade
02  | "2 1 [intCnt1] [intCnt1] [intCnt2] [intCnt3] [intCnt3] [intCnt4] [intCnt3] 1 [ID]"
    |                                              | header (11 ints)
03  | "+X +Y"                                      | coordenada de UMA das pontas (endpoint A)
04  | "0"                                          | constante
05  | "+xmin +xmax +ymin +ymax"                    | bbox que envelopa AMBAS pontas
    |                                              | A outra ponta = canto do bbox oposto à coord
06  | "[res] [hingeMa] [hingeMb] [no_axial] [no_shear] 1" | flags incl. rótulas + rigid (6 valores)
07  | "0" ou ID                                    | ID do MATERIAL aplicado (0 = nenhuma)
08  | "0" ou ID                                    | ID da SEÇÃO aplicada (0 = nenhuma)
09  | "0" ou ID                                    | **ID do MEMBER END MOMENT aplicado** (0 = nenhuma) — validado exp-I
10  | "0" ou ID                                    | ID da CARGA UNIFORME aplicada (0 = nenhuma)
11  | "0" ou ID                                    | **ID da CARGA LINEAR aplicada** (0 = nenhuma) — validado exp-H
12  | "0" ou ID                                    | **ID do THERMAL LOAD aplicado** (0 = nenhuma) — validado exp-J
13  | "[res] X_state Y_state Z_state ANGLE_DEG"    | **APOIO no endpoint Mb**. States: 0=free, 1=fix, 2=spring (validado exp-T). 5º float = ângulo em **GRAUS** (validado exp-W).
14  | "Kx Ky Kz"                                   | **Rigidezes das molas elásticas** (kN/m, kN/m, kNm/rad). Só não-zero se respectivo state = 2
15  | "0" ou ID                                    | NODAL LOAD ID no endpoint Mb (0 = nenhuma)
16  | "[flag] Dx Dy Rz"                            | **RECALQUE PRESCRITO** (validado exp-U). flag=1 se tem recalque, Dx Dy em METROS, Rz em RAD. Ex: `1 0 -0.01 0` = Dy=-10mm
```

### Bloco `4 1 ...` — Connector (12 linhas)

```
Pos | Linha exemplo                                | Conteúdo
----|----------------------------------------------|----------
01  | "1"                                          | marker
02  | "4 1 [refs internos] 1 [ID]"                 | header (11 ints, formato diferente)
03  | "+xmin +xmax +ymin +ymax"                    | bbox geral do MODELO (não desta entidade)
04  | "0"                                          | constante
05  | "+xmin +xmax +ymin +ymax"                    | bbox desta entidade
06  | "[result_flag] 0 0 0 0 1"                    |
07  | "0" ou "1"                                   | material aplicado
08  | "0" ou ID                                    | seção
09  | "0"                                          |
10  | "0" ou ID                                    | carga uniforme
11  | "0"                                          |
12  | "0"                                          |
```

**Por que 12 linhas?** Porque conector não "possui" nenhum nó — então não tem slots pra apoio (pos 13) nem carga nodal (pos 15) nem coord de endpoint A (pos 3). Material e seção continuam porque pertencem à BARRA.

## Topologia implícita (regra MASTER)

**FTool NÃO armazena nós como entidades separadas.** Em vez disso:

- Cada barra tem 2 endpoints implícitos: a coord (pos 3 do bloco `2 1`) + canto oposto do bbox
- 2 barras com endpoints iguais (tolerance 1 mm) → mesmo nó
- A topologia é **derivada** ao abrir, não armazenada

**Onde os dados de cada nó moram:**
- Nó na origem (0,0): linha 37 do stub
- Nó endpoint A de uma barra `2 1`: pos 13 (apoio) e pos 15 (carga) do bloco
- Nó endpoint B compartilhado: herda da barra que o criou como endpoint A

**Implicação pra gerar do zero:**
A ordem de criação das barras define qual barra "possui" o slot de cada nó. Planeje:
1. Defina a ordem de criação das barras
2. O 1º endpoint que você cria vira endpoint A (slot owner) daquela barra
3. Barras subsequentes que tocam esse nó usam bloco `4 1` (connector)

## Encoding de rótulas (Rotation Release)

Posição 6 do bloco `2 1 ...` (linha de flags, 6 valores):

| Pos | Significado |
|-----|-------------|
| 1 | Result flag (vira `1` após Solve) |
| **2** | **Rótula no nó INICIAL (Ma)** = endpoint OPOSTO ao coord: 0 = rígido, 1 = articulado |
| **3** | **Rótula no nó FINAL (Mb)** = endpoint AT coord: 0 = rígido, 1 = articulado |
| **4** | **Ignora deformação AXIAL** (Rigid axial): 0 = considera, 1 = ignora — validado exp-V |
| **5** | **Ignora deformação CISALHANTE** (Rigid shear): 0 = considera, 1 = ignora — validado exp-V |
| 6 | Constante `1` |

"Rigid Member" no painel Deformation Constraints = pos 4 + pos 5 ambos = 1 simultaneamente.

⚠️ **Importante**: o `coord` armazenado na pos 3 do bloco é o **end node (Mb)**, NÃO o initial node (Ma). O Ma é o canto bbox-oposto ao coord. A ordem é definida pela ordem de clique ao criar a barra (primeiro clique = Ma, segundo clique = Mb = coord).

Adicionar rótula:
- ❌ Não incrementa contadores da linha 4
- ❌ Não muda o tamanho do bloco (continua 16 linhas)
- ✅ Só toggla 1 bit na flags line

Exemplo (validado experimentalmente):
- Sem rótula: `0 0 0 0 0 1`
- Rótula só no Ma (início): `0 1 0 0 0 1` ✓ validado
- Rótula só no Mb (fim): `0 0 1 0 0 1` ✓ validado
- Rótula em ambos: `0 1 1 0 0 1` (esperado)

## Encoding de apoios

Posição 13 do bloco `2 1` (linha de apoio do endpoint Mb) ou linha 37 (origem):

Estrutura: `[result_flag] X_state Y_state Z_state ANGLE_DEG`

**Cada DOF tem 3 estados possíveis:**

| Valor | Significado |
|-------|-------------|
| 0 | Free (livre) |
| 1 | Fix (restringido completamente) |
| 2 | Spring (mola elástica) |

**Combinações típicas:**

| Tipo | Encoding (X Y Z) | Δx | Δy | θz |
|------|------------------|----|----|-----|
| Sem apoio | `0 0 0` | livre | livre | livre |
| Rolete em Y | `0 1 0` | livre | **fixo** | livre |
| Articulado | `1 1 0` | **fixo** | **fixo** | livre |
| Engaste | `1 1 1` | **fixo** | **fixo** | **fixo** |
| Mola Y | `0 2 0` | livre | **spring** | livre |
| Mola Z (apoio elástico rotacional) | `0 0 2` | livre | livre | **spring** |

**Quando state = 2 (spring), a próxima linha do bloco (pos 14) carrega os valores Kx Ky Kz**:
- Kx em kN/m (se X_state = 2)
- Ky em kN/m (se Y_state = 2)
- Kz em kNm/rad (se Z_state = 2)

Exemplo: rolete elástico em Y com K=1000 kN/m:
- Linha de apoio: `0 0 2 0 +0`
- Linha seguinte: `0 1000 0`

**Apoio inclinado**: o 5º valor da linha de apoio é o ângulo em **GRAUS** (não radianos!) — positivo no sentido anti-horário.

Exemplo: articulado inclinado em 30°:
- Linha de apoio: `0 1 1 0 +30` (validado exp-W)

Posição 1 (`0` ou `1`) é flag interno do FTool após análise — gerar sempre como `0`.

## Resultados de análise

O `.ftl` **NÃO armazena os resultados** (momentos, reações, deformadas). São recalculados em runtime ao abrir o arquivo.

Após Solve, mudam:
- Linha 37 pos 1: 0 → 1 (flag "modelo analisado")
- Linha 6 e linha 13 de cada bloco, pos 1: 0 → N (índices internos)

Esses são **índices internos** — não dados de resultado. Podem ser zerados pra forçar recálculo.

## Fluxo de geração válido (gerar `.ftl` do zero)

1. Cabeçalho copiado de arquivo bom (Pratt, ou exemplo do `examples/`)
2. Definir cargas (linhas 19-25) ANTES das entidades
3. Definir material(s) (linhas 26-28)
4. Definir seção(ões) (linhas 29-34) — preferir Generic (6 floats)
5. Stub origem (linhas 35-40): zerado ou com apoio se origem é engastada
6. Pra cada barra:
   - Decida: cria nó novo (`2 1`, 16 linhas) ou conecta existentes (`4 1`, 12 linhas)
   - Calcule bbox = (min/max x, min/max y das 2 pontas)
   - Pos 7 = 1 se material aplicado
   - Pos 8 = ID da seção (1 = primeira seção definida)
   - Pos 10 = ID da carga uniforme se aplicada
   - Pos 13 = apoio no endpoint A
   - Pos 15 = carga nodal no endpoint A
7. Atualizar contadores (linha 4): pos 6 e 7 = max ID criado
8. Linha final: ` 0` com espaço inicial

## Bar splitting — operação complexa

Inserir um nó NO MEIO de uma barra existente NÃO é simples. O FTool transforma 1 barra em **~5 entidades**:

1. Metade esquerda (`2 1`, coord = ponto do split)
2. Metade direita (`2 1`, coord = endpoint original)
3. Wrapper `4 1` com `±1e30 ±1e30 ±1e30 ±1e30` no pos 3 (ghost entity que mantém metadados)
4. Marker `-1 1 ...` no fim
5. (Possivelmente outras)

Distribuição das propriedades originais:
- **Apoio + nodal load** (no endpoint Mb original) → metade direita
- **MEM + Linear + Thermal loads** (cargas em barra inteira) → metade esquerda + wrapper
- **Hinge no Mb** → wrapper

⚠️ **Pra gerar `.ftl` programaticamente: evite splitting.** Crie barras já no tamanho final desejado. Wrappers e markers adicionam complexidade não-necessária.

Validado experimentalmente em exp-S-split-bar.

## Erros comuns

- ❌ Criar "entidades nó" isoladas — FTool descarta. Não existe entidade nó solo.
- ❌ Bboxes degenerados — Ok pra barras axis-aligned (ymin=ymax pra horizontal, xmin=xmax pra vertical). NÃO ok pra oblíquas.
- ❌ Confundir entidade "apoio elástico" (valores `±1e30`) com membro
- ❌ Não atribuir material/seção (pos 7/8 = 0) → erro "You must define materials to all members"
- ❌ Misturar formatos `2 1` e `4 1` arbitrariamente — siga a regra de "creator vs connector"
- ❌ Caracteres não-ASCII em nomes (ç, ã) podem causar problemas no save/load em alguns sistemas
