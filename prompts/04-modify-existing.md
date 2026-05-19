# Prompt — Modificar `.ftl` Existente

## 🇧🇷 Português

```
Você é um assistente especializado no formato .ftl do FTool 4.01.00.

Tenho a documentação completa do formato anexada. Use como referência.

Vou te dar um arquivo .ftl existente. Faça a seguinte modificação:

[DESCREVA A MODIFICAÇÃO, EX:]
- "Trocar o apoio articulado em (0,0) por engaste"
- "Adicionar uma viga horizontal a y=1.5m conectando as duas colunas"
- "Aumentar o vão da viga de 4m pra 6m mantendo as outras propriedades"
- "Trocar a seção de Tubo 100x50x3 por uma Generic com A=0.001, I=2e-6"
- "Adicionar carga concentrada de 5 kN pra baixo no nó central"

REGRAS:
1. PRESERVE tudo que não foi pedido pra mudar
2. Atualize contadores na linha 4 se adicionar/remover entidades
3. Mantenha encoding correto (2 1 vs 4 1)
4. Recalcule bboxes afetados
5. Se mover um nó, recalcule o bbox da barra desse nó

ARQUIVO ORIGINAL:
```
[COLAR CONTEÚDO DO .FTL AQUI]
```

Retorne o arquivo modificado completo, sem omitir linhas. Nem explicações extras — só o arquivo.
```

---

## 🇺🇸 English

```
You are an assistant specialized in FTool 4.01.00 .ftl format.

I have the complete format documentation attached. Use it as reference.

I'll give you an existing .ftl file. Make the following modification:

[DESCRIBE THE CHANGE, e.g.:]
- "Change the pinned support at (0,0) to a fixed support"
- "Add a horizontal beam at y=1.5m connecting the two columns"
- "Increase the beam span from 4m to 6m, keeping other properties"
- "Replace the Tubo 100x50x3 section with Generic A=0.001, I=2e-6"
- "Add a concentrated load of 5 kN downward at the central node"

RULES:
1. PRESERVE everything not asked to change
2. Update counters in line 4 if entities added/removed
3. Keep correct encoding (2 1 vs 4 1)
4. Recompute affected bboxes
5. If moving a node, recompute that node's bar bbox

ORIGINAL FILE:
```
[PASTE .FTL CONTENT HERE]
```

Return the modified file complete, without omitting lines. No extra explanations — just the file.
```
