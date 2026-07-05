<!-- Arquivo = slug ASCII do título (minúsculas, sem acento, todo char fora de [a-z0-9]→hífen): ex. "Cálculo de markup"→calculo-de-markup.md, "Disponibilidade (Avail)"→disponibilidade-avail.md -->
---
title: {{TÍTULO}}
aliases: [{{TÍTULO}}]   # título legível p/ busca/quick-switcher do Obsidian
tags: [spec]
status: rascunho
created: {{YYYY-MM-DD}}
updated: {{YYYY-MM-DD}}
related: []   # relações p/ o grafo do Obsidian, em wikilink: - "[[slug-da-nota|Título legível]]"
code_refs: []
tickets: []
---

# {{TÍTULO}}

> Resumo em uma frase: qual é a *única* ideia desta nota.

## Contexto

Por que isso existe / qual problema resolve. Linke conceitos relacionados com caminho relativo: `[Título](caminho/slug.md)`.

## Requisitos

- Requisito funcional 1
- Requisito funcional 2

## Regras de negócio

- Regra 1 (condição → efeito)

## Decisões técnicas

- Decisão e o porquê (alternativas descartadas, trade-offs).

## Endpoints / Contratos

<!-- Se aplicável: método, rota, headers, payload, resposta. Remova a seção se não couber. -->

## Referências no código

- `src/main/java/.../Arquivo.java` — o que esse arquivo faz nesta spec.

## Notas relacionadas

<!-- Link de corpo = caminho relativo até o arquivo, com display legível:
     mesma pasta -> [Título legível](outra-nota.md); outra área -> [Título legível](../9-depara/slug-da-nota.md) -->
- [Título legível](caminho/relativo/slug-da-nota.md) — por que se relaciona.
