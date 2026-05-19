# Prompts prontos / Ready prompts

🇧🇷 Templates de prompts pra usar com qualquer assistente de IA (Claude, GPT, Gemini, Llama 8B+) pra gerar arquivos `.ftl`.

🇺🇸 Prompt templates to use with any AI assistant (Claude, GPT, Gemini, Llama 8B+) to generate `.ftl` files.

## Como usar / How to use

🇧🇷 **Fluxo padrão:**

1. Abra sua conversa com a IA (Claude, ChatGPT, etc.)
2. **Anexe ou cole** o conteúdo completo de:
   - [`../docs/ftl-format.md`](../docs/ftl-format.md) (formato do arquivo)
   - [`../docs/toolbar-reference.md`](../docs/toolbar-reference.md) (referência UI)
   - Algum exemplo de [`../examples/`](../examples/) (boilerplate base)
3. **Cole o prompt** do template apropriado
4. **Ajuste os parâmetros** (geometria, cargas, material)
5. **Salve a resposta** como `meu-projeto.ftl`
6. **Abra no FTool** pra verificar

🇺🇸 **Standard flow:**

1. Open your AI conversation (Claude, ChatGPT, etc.)
2. **Attach or paste** the full content of:
   - [`../docs/ftl-format-en.md`](../docs/ftl-format-en.md) (file format)
   - [`../docs/toolbar-reference-en.md`](../docs/toolbar-reference-en.md) (UI reference)
   - One example from [`../examples/`](../examples/) (base boilerplate)
3. **Paste the prompt** from the appropriate template
4. **Adjust parameters** (geometry, loads, material)
5. **Save the response** as `my-project.ftl`
6. **Open in FTool** to verify

## Prompts disponíveis / Available prompts

| Arquivo / File | Caso de uso / Use case |
|----------------|-----------------------|
| [`01-generate-beam.md`](01-generate-beam.md) | Viga simples biapoiada / Simple supported beam |
| [`02-portal-frame.md`](02-portal-frame.md) | Pórtico engastado / Fixed portal frame |
| [`03-truss.md`](03-truss.md) | Treliça (Pratt/Warren) / Truss |
| [`04-modify-existing.md`](04-modify-existing.md) | Modificar `.ftl` existente / Modify existing `.ftl` |
| [`05-explain-ftl.md`](05-explain-ftl.md) | Engenharia reversa de `.ftl` desconhecido / Reverse-engineer unknown `.ftl` |
