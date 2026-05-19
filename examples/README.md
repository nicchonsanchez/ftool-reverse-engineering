# Exemplos / Examples

🇧🇷 Arquivos `.ftl` reais, abríveis no FTool 4.01.00, demonstrando o conhecimento documentado.

🇺🇸 Real `.ftl` files, openable in FTool 4.01.00, demonstrating the documented knowledge.

## Arquivos / Files

### `01-simple-beam-pinned-roller.ftl`

🇧🇷 Viga simples horizontal de 4 m. Apoio **articulado** na origem (0, 0), apoio **rolete** em (4, 0). Carga concentrada de 10 kN pra baixo no nó direito. Sem material/seção atribuídos (intencional — pra mostrar o "estado bruto").

🇺🇸 Simple 4 m horizontal beam. **Pinned** support at origin (0, 0), **roller** at (4, 0). 10 kN downward concentrated load at right node. No material/section assigned (intentional — to show "raw" state).

**Demonstra / Demonstrates:**
- Encoding articulado vs rolete (linha 30 vs linha 46)
- Definição de carga concentrada (linha 21)
- Aplicação de carga via slot pos 15 do bloco (linha 48)

### `02-portal-frame-fixed.ftl`

🇧🇷 Pórtico bi-engastado: 2 colunas (esquerda 2.5 m, direita 2.3 m) + 1 viga inclinada de 4.8 m de vão. Engastes nas duas bases. **Estrutura completa do "ponto de ônibus"** que motivou esta investigação.

🇺🇸 Fixed-base portal frame: 2 columns (left 2.5 m, right 2.3 m) + 1 inclined 4.8 m span beam. Fixed supports at both bases. **Complete "bus stop" structure** that motivated this investigation.

**Demonstra / Demonstrates:**
- Pórtico completo com 3 barras (todas `2 1`)
- 2 engastes em nós diferentes (origem na linha 37 + endpoint A na pos 13 da B3)
- Material e seção definidos (Aço A36 + Tubo 100x50x3)
- Carga uniforme em barra

### `03-bars-with-diagonals.ftl`

🇧🇷 Estrutura com 3 barras: 1 horizontal + 2 diagonais a 45° formando um "^". Mostra a diferença entre encoding `2 1` (creator) e `4 1` (connector).

🇺🇸 Structure with 3 bars: 1 horizontal + 2 diagonals at 45° forming a "^". Shows the difference between `2 1` (creator) and `4 1` (connector) encoding.

**Demonstra / Demonstrates:**
- Primeira barra: encoding `2 1` (cria endpoint A)
- Segunda barra (compartilha endpoint com a primeira): `2 1` ainda
- Terceira barra (conecta dois nós já existentes): `4 1` (bloco de 12 linhas!)

## Como usar / How to use

🇧🇷
1. Baixe o arquivo `.ftl`
2. Abra no FTool: File → Open → seleciona o arquivo
3. Explore: clique em nós, barras, painéis pra ver as configurações
4. Modifique: brinque com os valores e veja o resultado
5. Compare com [`../docs/ftl-format.md`](../docs/ftl-format.md) — abra o arquivo num editor de texto e veja o encoding

🇺🇸
1. Download the `.ftl` file
2. Open in FTool: File → Open → select the file
3. Explore: click nodes, bars, panels to see configurations
4. Modify: play with values and see results
5. Compare with [`../docs/ftl-format-en.md`](../docs/ftl-format-en.md) — open the file in a text editor to see the encoding

## Gerando seus próprios exemplos / Generating your own examples

🇧🇷 Use os [`../prompts/`](../prompts/) com sua IA preferida pra gerar `.ftl` customizados. Exemplo:

🇺🇸 Use the [`../prompts/`](../prompts/) with your favorite AI to generate custom `.ftl`. Example:

```
[Anexar/Attach: docs/ftl-format-en.md + this README.md]

[Prompt from prompts/01-generate-beam.md with your parameters]
```
