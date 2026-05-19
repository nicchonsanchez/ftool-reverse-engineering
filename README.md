# FTool Reverse Engineering

> 🇧🇷 **Engenharia reversa do formato `.ftl` do FTool 4.01.00** — feita colaborativamente com IA, ao longo de ~30 iterações experimentais. Permite gerar arquivos `.ftl` válidos de forma programática.
>
> 🇺🇸 **Reverse engineering of the `.ftl` file format from FTool 4.01.00** — collaboratively decoded with AI assistance through ~30 experimental iterations. Enables programmatic generation of valid `.ftl` files.

---

## 🇧🇷 Português

### Sobre

O **[FTool](https://www.tecgraf.puc-rio.br/ftool/)** (PUC-Rio, Prof. Luiz Fernando Martha) é um software de análise estrutural 2D amplamente usado em cursos de Engenharia Civil no Brasil. Apesar de ser uma ferramenta consagrada, seu formato de arquivo `.ftl` é **proprietário e não-documentado publicamente**.

Este repositório documenta o resultado de uma **engenharia reversa sistemática do formato**, permitindo:

- Entender como cada feature (nós, barras, apoios, cargas, materiais, seções) é codificada no arquivo
- Gerar arquivos `.ftl` válidos do zero programaticamente
- Manipular projetos FTool fora da interface gráfica
- Servir de referência técnica pra estudantes e desenvolvedores

### O que tem aqui

| Arquivo | Conteúdo |
|---------|----------|
| [`docs/ftl-format.md`](docs/ftl-format.md) | Decodificação completa do formato `.ftl` — todas as seções do arquivo, encoding de cada feature |
| [`docs/toolbar-reference.md`](docs/toolbar-reference.md) | Referência da interface do FTool 4.01 — cada ícone, painel, atalho |
| [`docs/workflow.md`](docs/workflow.md) | Estratégia prática pra trabalhar com FTool eficientemente |
| [`examples/`](examples/) | Arquivos `.ftl` de exemplo gerados aplicando o conhecimento documentado |

### Metodologia

Esta documentação é fruto de uma **colaboração humano-IA**, com divisão de papéis honesta:

**O humano (Nicchon Sanchez) contribuiu:**
- O **insight inicial**: a ideia de treinar uma IA a entender e gerar arquivos `.ftl`
- A **operação do FTool**: clicar, salvar versões incrementais, tirar screenshots
- A **direção do escopo**: decidir até onde investigar, quando parar
- **Conhecimento contextual** sobre o software, sobre o público-alvo (estudantes de Engenharia), sobre o objetivo do trabalho que motivou a investigação (projeto de ponto de ônibus de uma colega de faculdade)

**A IA (Claude, da Anthropic) contribuiu:**
- A **análise técnica**: leu cada arquivo `.ftl` salvo, fez diff manual entre versões consecutivas, identificou padrões byte a byte
- A **formulação de hipóteses** sobre o significado de cada linha/posição
- A **proposta dos experimentos** pra confirmar ou refutar cada hipótese (ex: "salve agora SÓ adicionando um nó, depois SÓ um apoio, pra eu isolar o encoding de cada feature")
- A **síntese e redação** da documentação estruturada

Ao longo de ~30 iterações (em dois conjuntos: `step01` a `step08` no projeto-mãe e `exp-A` a `exp-F` em playground), o formato foi decodificado de "completamente opaco" pra "totalmente reproduzível".

A divisão prática:
- A IA não tem como rodar FTool — precisa do humano operando
- O humano não tem disposição/treino pra fazer engenharia reversa byte-a-byte — não é a sua atividade
- A colaboração só funciona porque cada lado faz o que o outro não consegue

Esse repositório é um experimento prático de **como usar IA pra documentar software fechado**. O conhecimento técnico aqui é majoritariamente extraído pela IA; o mérito do humano é ter tido a ideia, organizado o processo e tornado a publicação possível.

### Uso com Assistente de IA 🤖

Esta documentação foi escrita de forma **otimizada pra consumo por LLMs** (Claude, ChatGPT, Gemini, Llama, etc.). É o **caso de uso principal** deste repositório.

**Fluxo recomendado:**

1. Copie o conteúdo de [`docs/ftl-format.md`](docs/ftl-format.md) + [`docs/toolbar-reference.md`](docs/toolbar-reference.md) pro contexto da sua conversa com a IA
2. Peça pra IA gerar `.ftl` programaticamente, modificar arquivos existentes, ou explicar o que um `.ftl` específico contém
3. A IA terá conhecimento suficiente pra produzir arquivos válidos que abrem no FTool sem erro

**Exemplo de prompt:**

```
[Anexar: docs/ftl-format.md + docs/toolbar-reference.md como contexto]

Gere um arquivo .ftl pra uma viga biapoiada de 8 m, com:
- Apoio articulado em x=0
- Apoio em rolete em x=8
- Carga distribuída uniforme de 10 kN/m em toda a viga
- Material: aço ASTM A36
- Seção genérica: A = 8.64e-4 m², I = 1.12e-6 m⁴
```

**Por que isso funciona:**
- Documentação cobre 100% do encoding de cada feature
- Exemplos `.ftl` reais incluídos em [`examples/`](examples/)
- Prompts prontos em [`prompts/`](prompts/) pra usar direto
- Lista explícita de erros comuns ajuda IA a evitar armadilhas

**Modelos recomendados (confiabilidade pra gerar `.ftl` válidos):**

| Modelo | Confiabilidade | Comentário |
|--------|----------------|-----------|
| Llama 3.2:1b | ❌ | Pequeno demais, malformado quase sempre |
| Llama 3.2:3b | ⚠️ | Só casos triviais (1 barra) |
| Llama 3.3:8b / Qwen 2.5:7b | ⚠️ | Casos simples, falha em pórticos |
| Llama 3.3:70b (local) | ✅ | Bom mínimo pra rodar local |
| Claude 3.7+ / GPT-4+ / Gemini 2.5 Pro | ✅✅ | Funciona muito bem, recomendado |

Pra modelo local: priorize **8B+ parâmetros**. Pra resultado profissional, use modelo frontier (Claude, GPT, Gemini).

**Casos de uso típicos:**
- Geração automatizada de modelos pra estudos paramétricos (varrer dimensões, materiais, cargas)
- Conversão de outros formatos (DXF, IFC, JSON) pra FTool
- Validação automática de arquivos `.ftl` (CI/CD em curso de engenharia)
- Geração de exercícios pra alunos via IA
- Backup-as-code de projetos FTool (versionamento Git de modelos estruturais)

### Limitações

- Versão do FTool documentada: **4.01.00** (Janeiro 2018+ build). Versões anteriores podem ter formato diferente.
- Algumas features ainda não totalmente cobertas: cargas térmicas, load trains, análise dinâmica.
- Os IDs internos (contadores em algumas linhas) ainda têm semântica parcialmente desconhecida — mas o FTool tolera valores aproximados.

### Como usar este conhecimento

**Pra estudantes**: use `toolbar-reference.md` pra navegar o FTool com mais fluidez.

**Pra desenvolvedores**: use `ftl-format.md` pra gerar `.ftl` programaticamente — útil pra automação, geração em massa pra testes, integração com pipelines de modelagem paramétrica.

**Pra educadores**: use `workflow.md` como guia de boas práticas pra ensinar FTool em sala.

### Licença

[MIT License](LICENSE) — uso livre incluindo comercial. Pedimos só citação no caso de redistribuição.

---

## 🇺🇸 English

### About

**[FTool](https://www.tecgraf.puc-rio.br/ftool/)** (PUC-Rio, by Prof. Luiz Fernando Martha) is a 2D structural analysis software widely used in Civil Engineering courses in Brazil. Despite being a well-established tool, its `.ftl` file format is **proprietary and not publicly documented**.

This repository documents the result of a **systematic reverse engineering** of the format, enabling:

- Understanding how each feature (nodes, members, supports, loads, materials, sections) is encoded
- Programmatic generation of valid `.ftl` files from scratch
- Manipulation of FTool projects outside the GUI
- Technical reference for students and developers

### What's in here

| File | Content |
|------|---------|
| [`docs/ftl-format-en.md`](docs/ftl-format-en.md) | Complete decoding of the `.ftl` format — all file sections, encoding for each feature |
| [`docs/toolbar-reference-en.md`](docs/toolbar-reference-en.md) | FTool 4.01 UI reference — every icon, panel, shortcut |
| [`docs/workflow-en.md`](docs/workflow-en.md) | Practical strategy for efficient FTool usage |
| [`examples/`](examples/) | Example `.ftl` files generated by applying the documented knowledge |

### Methodology

This documentation is the result of a **human-AI collaboration**, with an honest division of roles:

**The human (Nicchon Sanchez) contributed:**
- The **initial insight**: the idea of teaching an AI to understand and generate `.ftl` files
- **Operating FTool**: clicking, saving incremental versions, taking screenshots
- **Directing scope**: deciding how far to investigate, when to stop
- **Contextual knowledge** about the software, target audience (engineering students), and the original project that motivated the investigation (a bus stop design for a college classmate)

**The AI (Claude, by Anthropic) contributed:**
- **Technical analysis**: read each saved `.ftl` file, manually diffed between consecutive versions, identified byte-by-byte patterns
- **Hypothesis formulation** about the meaning of each line/position
- **Experiment design** to confirm or refute each hypothesis (e.g., "now save with ONLY a new node added, then ONLY a support, so I can isolate the encoding of each feature")
- **Synthesis and writing** of the structured documentation

Across ~30 iterations (in two sets: `step01` to `step08` in the parent project and `exp-A` to `exp-F` in a playground), the format went from "completely opaque" to "fully reproducible."

The practical division:
- The AI cannot run FTool — it needs the human operator
- The human doesn't have the disposition/training for byte-level reverse engineering — that's not their craft
- The collaboration works precisely because each side does what the other can't

This repository is a practical experiment in **using AI to document closed-source software**. The technical content here is mostly extracted by the AI; the human's merit is having the idea, organizing the process, and making publication possible.

### AI-Assisted Usage 🤖

This documentation was written to be **optimized for LLM consumption** (Claude, ChatGPT, Gemini, Llama, etc.). This is the **primary use case** for this repository.

**Recommended flow:**

1. Copy the content of [`docs/ftl-format-en.md`](docs/ftl-format-en.md) + [`docs/toolbar-reference-en.md`](docs/toolbar-reference-en.md) into your AI conversation context
2. Ask the AI to generate `.ftl` programmatically, modify existing files, or explain what a specific `.ftl` contains
3. The AI will have enough knowledge to produce valid files that open in FTool without errors

**Example prompt:**

```
[Attach: docs/ftl-format-en.md + docs/toolbar-reference-en.md as context]

Generate a .ftl file for a simply supported beam, 8 m span, with:
- Pinned support at x=0
- Roller support at x=8
- Uniform distributed load of 10 kN/m along the entire beam
- Material: ASTM A36 steel
- Generic section: A = 8.64e-4 m², I = 1.12e-6 m⁴
```

**Why this works:**
- Documentation covers 100% of the encoding for each feature
- Real `.ftl` examples included in [`examples/`](examples/)
- Ready-to-use prompts in [`prompts/`](prompts/)
- Explicit list of common mistakes helps AI avoid pitfalls

**Recommended models (reliability for generating valid `.ftl`):**

| Model | Reliability | Notes |
|-------|-------------|-------|
| Llama 3.2:1b | ❌ | Too small, malformed almost always |
| Llama 3.2:3b | ⚠️ | Only trivial cases (1 bar) |
| Llama 3.3:8b / Qwen 2.5:7b | ⚠️ | Simple cases, fails on frames |
| Llama 3.3:70b (local) | ✅ | Good minimum for local inference |
| Claude 3.7+ / GPT-4+ / Gemini 2.5 Pro | ✅✅ | Works very well, recommended |

For local models: prioritize **8B+ parameters**. For professional results, use a frontier model (Claude, GPT, Gemini).

**Typical use cases:**
- Automated model generation for parametric studies (sweeping dimensions, materials, loads)
- Format conversion (DXF, IFC, JSON → FTool)
- Automated validation of `.ftl` files (CI/CD in engineering courses)
- AI-generated exercises for students
- Backup-as-code for FTool projects (Git versioning of structural models)

### Limitations

- FTool version documented: **4.01.00** (January 2018+ build). Earlier versions may have different formats.
- Some features not yet fully covered: thermal loads, load trains, dynamic analysis.
- Internal IDs (counters in some lines) still have partially unknown semantics — but FTool tolerates approximate values.

### How to use this knowledge

**For students**: use `toolbar-reference-en.md` to navigate FTool more fluidly.

**For developers**: use `ftl-format-en.md` to generate `.ftl` programmatically — useful for automation, mass generation for testing, integration with parametric modeling pipelines.

**For educators**: use `workflow-en.md` as a best-practices guide for teaching FTool in class.

### License

[MIT License](LICENSE) — free use including commercial. We just ask for attribution in case of redistribution.

---

## Contributing / Contribuir

Pull requests are welcome, especially for:
- Confirming behavior on different FTool versions
- Decoding features not yet covered (thermal, load trains)
- Adding more example files
- Translating to other languages

PRs são bem-vindos, especialmente pra:
- Confirmar comportamento em versões diferentes do FTool
- Decodificar features ainda não cobertas (térmica, trens de carga)
- Adicionar mais arquivos de exemplo
- Traduzir pra outros idiomas

## Disclaimer

This is an **unofficial** reverse engineering effort. The FTool software remains property of PUC-Rio / Tecgraf. This documentation is provided for educational and research purposes.

Este é um esforço de engenharia reversa **não-oficial**. O FTool permanece propriedade da PUC-Rio / Tecgraf. Esta documentação é fornecida para fins educacionais e de pesquisa.
