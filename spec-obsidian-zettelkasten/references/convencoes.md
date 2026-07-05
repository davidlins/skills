# Convenções — Spec Obsidian Zettelkasten

Especificação completa de frontmatter, nomes de arquivo, links e ciclo de vida. A `SKILL.md`
traz o resumo; este arquivo é a fonte canônica dos detalhes.

## Índice

- [Frontmatter](#frontmatter)
- [Ciclo de vida do `status`](#ciclo-de-vida-do-status)
- [Nome de arquivo / título](#nome-de-arquivo--título)
- [Organização em pastas e índice](#organização-em-pastas-e-índice)
- [Links e backlinks](#links-e-backlinks)
- [Tags](#tags)
- [Checklist de refatoração](#checklist-de-refatoração)

## Frontmatter

Bloco YAML no topo de **toda** nota. Chaves em inglês (compatível com Dataview e plugins do
Obsidian), valores em PT-BR.

Arquivo: `autenticacao-jwt.md` (slug ASCII do título). `title` legível repetido em `aliases`
(ajuda busca/quick-switcher do Obsidian).

```yaml
---
title: Autenticação JWT
aliases: [Autenticação JWT, Login JWT, Auth JWT]
tags: [spec, auth, seguranca]
status: rascunho
created: 2026-05-29
updated: 2026-05-29
related:
  - "[[gateway-de-autenticacao|Gateway de autenticação]]"
  - "[[renovacao-de-token|Renovação de token]]"
code_refs:
  - src/main/java/.../SecurityConfig.java
  - src/main/java/.../JwtFilter.java
tickets: [PA-8745]
---
```

| Campo | Obrigatório | Descrição |
|-------|-------------|-----------|
| `title` | sim | Título legível em PT-BR, **com acento** (ex.: `Cálculo de markup`). Não é o nome do arquivo — o arquivo é o slug ASCII do título (ver [Nome de arquivo / título](#nome-de-arquivo--título)). |
| `aliases` | sim | Contém o `title` legível (ajuda busca/quick-switcher pelo nome legível) + outros nomes/títulos de notas mescladas. Não é o que resolve links — links usam o slug do arquivo (ver [Links e backlinks](#links-e-backlinks)). |
| `tags` | sim | Pelo menos `spec`. Veja [Tags](#tags). |
| `status` | sim | Estado no ciclo de vida (abaixo). |
| `created` | sim | `YYYY-MM-DD`. Não muda depois de criada. |
| `updated` | sim | `YYYY-MM-DD`. Atualize a cada edição de conteúdo. |
| `related` | não | Notas relacionadas como **wikilinks** entre aspas (`- "[[slug|Título]]"`; YAML exige aspas em `[[...]]`). Exceção ao corpo, que usa caminho relativo — ver [Links e backlinks](#links-e-backlinks). |
| `code_refs` | não | Caminhos reais de arquivos/classes do repositório. Confirme que existem. |
| `tickets` | não | Códigos de card Jira (ex.: `[PA-8745]`) que criaram ou alteraram a nota. Rastreabilidade spec ↔ Jira; acumule (não sobrescreva) a cada evolução. |

> A data de hoje está disponível no contexto da sessão. Não invente datas.

## Ciclo de vida do `status`

```
rascunho → em-revisão → aprovado → implementado → (obsoleto)
```

| Status | Significado |
|--------|-------------|
| `rascunho` | Ideia capturada, ainda incompleta ou não revisada. |
| `em-revisão` | Conteúdo pronto para validação por outra pessoa. |
| `aprovado` | Spec validada, ainda não implementada no código. |
| `implementado` | Existe código correspondente (mantenha `code_refs` apontando pra ele). |
| `obsoleto` | Superada. Não apague — mantenha por histórico e adicione um link para a que a substituiu. |

Notas novas nascem `rascunho`, salvo se o usuário disser o contrário.

## Nome de arquivo / título

O **título** e o **nome do arquivo** são coisas separadas:

- **Título** (`title` no frontmatter + `# H1` no corpo): em PT-BR, legível, com acentos
  (ex.: `Cálculo de markup`). É a chave semântica e vira o texto exibido dos links `[Título](...)`.
- **Nome do arquivo = slug ASCII do título.** Algoritmo:
  1. minúsculas;
  2. transliterar acentos para ASCII (`á→a`, `ç→c`, `ã→a`, `é→e`, ...);
  3. **remover qualquer caractere que não seja `[a-z0-9]`** — isso elimina parênteses `( )`,
     colchetes `[ ]`, chaves `{ }`, aspas `" '`, e pontuação `. , : ; ! ?` etc., trocando-os
     (e os espaços) por hífen;
  4. colapsar hífens repetidos e remover hífen do início/fim.
  Resultado: só `[a-z0-9-]`. Ex.: `Cálculo de markup` → `calculo-de-markup.md`; `Autenticação JWT`
  → `autenticacao-jwt.md`; `Disponibilidade de hotéis (Avail)` → `disponibilidade-de-hoteis-avail.md`;
  `Markup: visão geral` → `markup-visao-geral.md`.

**Por quê:** o Sonar/Veracode marca caminhos não-ASCII (acento/espaço) como finding mesmo com a
pasta `spec/` excluída da análise; o slug ASCII elimina isso e evita problemas de filesystem.

- **`aliases` inclui o título legível** (ex.: `aliases: [Cálculo de markup]`) — ajuda busca e
  quick-switcher do Obsidian pelo nome legível.
- O slug não pode conter nada além de `[a-z0-9-]` — `/ \ : * ? " < > | ( ) [ ] { }` e demais
  pontuação já saem no passo 3. Se dois títulos
  geram o mesmo slug, desambigue o título (e portanto o slug) — slugs precisam ser únicos no vault.
- Slugs/caminhos são chaves estáveis: pense bem antes de renomear ou mover, porque isso muda o
  caminho relativo em todo link `[Título](.../slug.md)` que aponta para o arquivo. Renomeie/mova
  pelo Obsidian ou pelo IDEA (reescrevem os links); se mexer pela CLI, atualize os caminhos (e o
  `related:` no frontmatter) de todas as notas que apontam pra ela (veja checklist).

## Organização em pastas e índice

Para vaults pequenos, tudo numa pasta só basta — a organização vive nos links e MOCs. Em
projetos grandes, adote **pastas rasas por área** para facilitar a navegação no Obsidian, sem
abandonar o método:

- **Raiz = só o índice geral.** Deixe na raiz apenas o MOC raiz, com prefixo numérico para
  ordenar no topo do explorer: arquivo `1-projeto-visao-geral.md`, título `1. Projeto (visão geral)`.
  O `title` e o `# H1` levam o prefixo e o acento; o **arquivo é o slug ASCII**. Mantenha o título
  (e variações sem prefixo) em `aliases` (busca do Obsidian).
- **Pastas por área, numeradas — com nome em slug ASCII.** Agrupe as demais notas em poucas pastas
  de alto nível, prefixadas com número e **sem acento/espaço** (`2-negocio/`, `3-arquitetura/`, ...).
  Os nomes dependem do domínio. Cada área tem seu próprio MOC dentro da pasta, e o índice raiz
  aponta para esses MOCs. O nome da pasta também entra no caminho que o Sonar varre — por isso slug.
- **Pastas entram no caminho dos links.** Como os links do corpo são caminhos relativos
  (`../9-depara/slug.md`), a pasta da nota faz parte do link. Consequências práticas:
  - Mover uma nota entre pastas **muda os caminhos** que apontam para ela — mova pelo Obsidian ou
    pelo IDEA (reescrevem os links) ou atualize-os manualmente.
  - O `related:` do frontmatter, por usar wikilink (`[[slug|Título]]`), independe da pasta — o
    Obsidian resolve pelo nome do arquivo.
  - Slugs de arquivo precisam ser **únicos no vault**, mesmo em pastas diferentes (mantém o
    `related:` do Obsidian sem ambiguidade).
- **Mantenha raso.** Um nível de pastas por área. Estrutura mais fina = mais MOCs e links, não
  subpastas profundas.

> Ordenação no Obsidian: se o explorer listar pastas antes de arquivos, mude para ordenar
> arquivos e pastas juntos (*Settings → Files & Links*, ou o menu de ordenação do explorer) para
> o índice `1. ...` ficar no topo.

## Links e backlinks

Dois formatos, cada um no seu lugar:

**1. Corpo das notas → link Markdown de caminho relativo** (é o que um humano clica):

- Formato: `[Título legível](caminho/relativo/slug.md)`. O alvo é o caminho do arquivo-alvo
  **relativo ao arquivo atual**, com extensão `.md`:
  - mesma pasta → `[Busca de quartos](busca-de-quartos.md)`;
  - outra área → `[Deduplicação GIATA](../9-depara/deduplicacao-giata.md)`.
- O texto entre `[ ]` é o título legível (PT-BR, com acento). Para outro texto, troque só o `[ ]`.
- Cabeçalho: `[Cálculo de markup](../7-pricing/calculo-de-markup.md#Regras)`. Atenção: a forma da
  âncora difere entre GitLab (minúsculas-com-hífen) e Obsidian (texto cru do heading) — prefira
  link em nível de nota; use âncora só quando necessário.
- **Por que caminho relativo, não `[[wikilink]]`:** o `.md` renderizado precisa navegar nos **três**
  ambientes — Obsidian, GitLab (repo) e IntelliJ IDEA. O GitLab (repo) e o IDEA **não** resolvem
  `[[wikilinks]]` (viram texto cru / link quebrado); só o Obsidian resolve. O link Markdown relativo
  funciona nos três. Como o nome de arquivo é slug ASCII (`[a-z0-9-]`), o caminho não precisa de
  escape de URL e fica livre de finding de caminho não-ASCII no Sonar.
- **Vault Obsidian:** em `spec/.obsidian/app.json`, defina `"useMarkdownLinks": true` e
  `"newLinkFormat": "relative"` — assim os links criados pelo autocomplete do Obsidian já saem
  nesse formato.

**2. `related:` no frontmatter → `[[slug|Título]]`** (wikilink, exceção):

- Mantenha `related:` em wikilink: `- "[[calculo-de-markup|Cálculo de markup]]"`. O Obsidian só
  reconhece relação no YAML via wikilink (alimenta grafo e painel de propriedades). GitLab e IDEA
  **não renderizam frontmatter**, então o wikilink ali não atrapalha nenhum dos dois.
- É a única exceção: tudo que aparece no **corpo** usa caminho relativo; só o `related:` usa wikilink.

**Regras gerais:**

- **Sempre verifique que o alvo existe** (liste a pasta) e calcule o caminho relativo certo antes de
  linkar, salvo quando for criar de propósito uma nota-fantasma para escrever depois — avise o usuário.
- Relações importantes devem ser **bidirecionais e explícitas** na seção *Notas relacionadas*
  das duas notas, além do backlink automático do Obsidian.

## Tags

- `spec` em toda nota de especificação.
- `moc` em notas-índice (Maps of Content).
- Tags de domínio em kebab-case sem acento quando possível (`auth`, `reservas`, `pagamento`,
  `markup`). Reaproveite tags já existentes no vault — liste-as antes de inventar uma nova.

## Checklist de refatoração

Ao **dividir** uma nota:

1. Crie as notas atômicas novas (cada uma com frontmatter completo).
2. Substitua os trechos extraídos na nota original por links de caminho relativo para as novas.
3. Considere converter a nota original em MOC do tema.
4. `grep` por `slug-original.md` no vault (corpo) e por `[[slug-original` (frontmatter `related:`) —
   os links continuam válidos? Ajuste caminhos e wikilinks se preciso.

Ao **mesclar** duplicadas:

1. Escolha o título canônico (o mais usado/claro).
2. Una o conteúdo na nota canônica; atualize `updated`.
3. Adicione o título da nota descartada em `aliases` da canônica.
4. `grep` por `slug-descartado.md` (corpo) e `[[slug-descartado` (frontmatter) e aponte para a
   canônica: link `[Título](caminho/slug-canonico.md)` no corpo, `[[slug-canonico|Título]]` no `related:`.
5. Apague a nota descartada só depois que não houver mais nenhum link apontando pra ela.

Regra de ouro: **nunca deixe link quebrado**. Depois de qualquer refatoração, varra a pasta
por links (caminhos no corpo + wikilinks de `related:`) afetados.
