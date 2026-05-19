# Workflow Prático com FTool

Estratégias validadas pra trabalhar com FTool eficientemente.

## Quando gerar `.ftl` programaticamente vs usar GUI

| Situação | Caminho |
|----------|---------|
| Modelo simples bem definido em texto | Gerar `.ftl` direto e abrir |
| Modelo complexo (>10 barras) | GUI passo a passo |
| Aprender FTool | GUI obrigatório |
| Edits programáticos / em massa | Gerar `.ftl` direto |
| Estudo paramétrico (varrer dimensões) | Script gerando `.ftl` direto |

## Roteiro GUI passo a passo

### 1. Geometria

Use **Insert Member (M)** com **Keyboard mode (K) ligado** — não Insert Node. Cada barra cria os 2 nós nas pontas automaticamente.

Pra cada barra:
- 1st Node X / Y (digita coordenadas exatas)
- 2nd Node X / Y
- OK

Tolerance default (1 mm) faz nós próximos serem reaproveitados.

### 2. Material e seção

Antes de aplicar:
- **Edit material**: painel **Material Parameters** → "Create new" → preenche E, ν, α, ρ
- **Edit section**: painel **Section Properties** → "Create new" → escolhe tipo (recomendo **Generic > Integral Properties** pra A/I diretos)

Aplicar a todas barras:
- Ctrl+A (seleciona tudo)
- Painel Material → "Apply current material to selected members"
- Painel Section → "Apply current section to selected members"

### 3. Apoios

Pra cada nó com apoio:
- Selection mode (ESC) → clica no nó (vira vermelho)
- Painel **Support Conditions** → marca Fix nos campos apropriados:
  - Engaste: Δx + Δy + θz todos Fix
  - Articulado: Δx + Δy Fix, θz Free
  - Rolete: só Δy Fix
- Clica **"Apply support conditions to selected nodes"**

### 4. Cargas

- Painel **Uniform Loading** (pra distribuída em barra) ou **Nodal Loading** (pra concentrada em nó)
- "Create new" → define valor (Qy=-1 pra carga gravidade 1 kN/m, por exemplo)
- Seleciona a barra/nó alvo
- "Apply" no painel

### 5. Análise

Clica em qualquer dos 4 diagramas (N, V, M, D no canto superior direito). FTool roda análise automaticamente.

### 6. Salvar e exportar

- **File → Save** (.ftl pra manter editável)
- **File → Export Model Screen** (PNG limpo do diagrama atual)

## Tipos de seção — quando usar cada

| Tipo | Quando usar |
|------|-------------|
| **Generic > Integral Properties** | ⭐ Quando você sabe A e I direto (do catálogo do perfil). Mais explícito, sem ambiguidade. |
| Rectangle | Seção retangular maciça (não é tubo!) |
| Box | Tubo retangular oco. ATENÇÃO: FTool calcula área diferente do esperado às vezes. Confira A. |
| Tube / Pipe | Tubo redondo |
| I-shape | Perfil I padrão |
| Profile Tables | Perfis comerciais brasileiros (Vallourec, Gerdau-AçoMinas) — economiza tempo se for usar perfil tabelado |

## Materiais comuns

| Material | E (MPa) | ν | α | ρ (kN/m³) |
|----------|---------|---|---|-----------|
| Aço ASTM A36 | 200000 | 0.3 | 1.2e-5 | 78.5 |
| Aço A572 Gr 50 | 200000 | 0.3 | 1.2e-5 | 78.5 |
| Concreto fck=25 | 28000 | 0.2 | 1e-5 | 25 |
| Madeira C30 | 14500 | 0.3 | 5e-6 | 8 |

## Truques úteis

- **Grid + Snap** (bottom-right): liga grid com spacing X=Y=0.1 m pra alinhar visualmente
- **Display → White Background**: pra fundo branco em vez do preto (cansa menos a vista)
- **Ctrl+A** seleciona todas barras pra aplicação em lote
- **Zoom Fit (F)**: centraliza vista após criar modelo
- **Insert Continuous Line** (4º ícone esquerdo): desenha polyline conectada (várias barras de uma vez)

## Erros comuns

| Erro | O que acontece | Solução |
|------|---------------|---------|
| Material não atribuído | Análise não roda, popup de erro | Ctrl+A → Material → Apply |
| Cargas no campo Q vazias | Apply não aplica nada | Criar definição primeiro ("Create new") |
| Clica fora do nó | Apoio não aplica | Zoom in + clicar exato em cima do "+" do nó |
| Painel não muda | Editing Mode errado | Clica no ícone correto (não é selecionar painel pelo nome, é clicar no ícone da toolbar) |

## Quando aplicar topologia implícita do `.ftl`

Se você precisa que duas barras compartilhem um nó:
- Crie a 1ª barra terminando em (X, Y)
- Crie a 2ª barra começando EXATAMENTE em (X, Y) (use Keyboard mode pra precisão)
- FTool reconhece automaticamente que é o mesmo nó (tolerância 1 mm)
- A geometria fica "soldada" — mover o nó move ambas as barras
