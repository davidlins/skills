---
name: spec-obsidian-zettelkasten
description: >-
  Cria e mantém specs de software como notas Markdown no formato Obsidian seguindo o método
  Zettelkasten, dentro da pasta `spec/` do projeto. Faz quatro coisas: (1) cria notas atômicas
  de spec (uma ideia por nota) com frontmatter padronizado e nome de arquivo em slug ASCII
  (sem acento/espaço, evita findings de Sonar/Veracode), com o título legível no frontmatter;
  (2) conecta notas com links Markdown de caminho relativo (navegáveis em Obsidian, GitLab e
  IDEA) e mantém as relações coerentes; (3) refatora — divide
  notas grandes em atômicas, mescla duplicadas; (4) mantém MOCs (Maps of Content / índices).
  As notas documentam features: contexto, requisitos, regras de negócio, decisões técnicas,
  endpoints e referências ao código do repositório. Conteúdo sempre em PT-BR.
  Use SEMPRE que o usuário falar em "spec", "Zettelkasten", "Obsidian", "nota", "MOC",
  "documentar feature/decisão"; sempre que estiver criando ou editando arquivos `.md` dentro
  da pasta `spec/`; ou ao terminar/planejar uma feature e quiser registrá-la — mesmo que ele
  não cite o nome da skill explicitamente.
---

# Spec Obsidian Zettelkasten

Esta skill transforma conhecimento de software em **notas atômicas conectadas** dentro da
pasta `spec/` do projeto, em Markdown que navega em Obsidian, GitLab (repo) e IntelliJ IDEA
(links de caminho relativo no corpo + frontmatter YAML), seguindo o método Zettelkasten.

A ideia central do Zettelkasten é que o valor não está em cada nota isolada, mas na **rede de
ligações** entre ideias pequenas e autocontidas. Por isso a skill privilegia notas curtas e
focadas, fortemente linkadas, em vez de documentos longos. Quando você entender *por que* uma
nota deve ser atômica e linkada, vai conseguir tomar boas decisões mesmo em casos que estas
instruções não cobrem — confie nesse princípio mais do que em regras rígidas.

## Quando agir e o que o usuário costuma querer

A skill tem quatro operações. O usuário pode pedir uma específica ou descrever uma situação
da qual você infere a operação certa:

| Operação | Sinais típicos do usuário |
|----------|---------------------------|
| **Criar nota** | "documenta essa feature", "cria uma spec de X", "anota essa decisão" |
| **Linkar** | "conecta com a nota de Y", "isso se relaciona com...", "atualiza os links" |
| **Evoluir spec** | "mudou o comportamento de X", "card PA-XXXX pede...", "agora também faz Y", "atualiza a spec de Z" |
| **Refatorar** | "essa nota tá grande demais", "divide isso", "tem duplicada", "reorganiza" |
| **MOC / índice** | "cria um índice de X", "mapa de conteúdo", "visão geral do módulo" |

Quase sempre as operações se combinam: ao criar uma nota nova, você também a **linka** ao que
já existe e, se fizer sentido, a adiciona ao **MOC** do tema. Pense na pasta `spec/` como um
grafo vivo, não como uma pasta de arquivos soltos.

## Passo 0 — Localizar e entender a pasta `spec/`

Antes de criar ou editar qualquer coisa:

1. **Ache a pasta.** Procure `spec/` na raiz do projeto (working directory). Se não existir,
   confirme com o usuário que pode criá-la e crie. Se o projeto for um vault Obsidian dedicado
   (tem `.obsidian/`), a pasta pode ser a própria raiz ou uma subpasta — pergunte se houver
   ambiguidade.
2. **Leia o que já existe.** Liste as notas atuais e abra algumas (`ls spec/` + leitura das
   notas mais relacionadas ao tema). Você **precisa** conhecer o vocabulário, os títulos e as
   convenções já em uso antes de escrever — Zettelkasten cresce por conexão com o que já está
   lá, e notas com nome/slug divergente geram links duplicados ou quebrados. Nunca crie uma nota "Autenticação"
   se já existe "Autenticação JWT" sem antes decidir conscientemente entre linkar, estender
   ou dividir.
3. **Detecte o MOC do tema**, se houver, para já ligar a nota nova a ele.

## Convenções (resumo)

A especificação completa — schema de frontmatter, ciclo de vida de `status`, regras de nome de
arquivo e de linkagem — está em **`references/convencoes.md`**. Leia esse arquivo na primeira
vez que for criar ou editar uma nota numa sessão. Resumo do essencial:

- **Nome do arquivo = slug ASCII do título**: minúsculas, sem acento, e **todo caractere fora de
  `[a-z0-9]` vira hífen** — parênteses `( )`, colchetes, aspas, `:`, `.` e demais pontuação somem
  (hífens repetidos colapsam). Resultado só `[a-z0-9-]` (ex.: título `Autenticação JWT` → arquivo
  `autenticacao-jwt.md`; `Disponibilidade (Avail)` → `disponibilidade-avail.md`). Evita findings de
  Sonar/Veracode em caminhos não-ASCII (o Sonar marca mesmo com a pasta `spec/` excluída) e
  problemas de filesystem. O **título legível** (com acento, em PT-BR) vive no frontmatter `title`
  e no `# H1` do corpo — **não** no nome do arquivo.
- **Links do corpo = caminho relativo Markdown**: escreva `[Título legível](caminho/relativo/slug.md)`
  — alvo = caminho do arquivo-alvo relativo ao arquivo atual, **com `.md`** (mesma pasta → `outra-nota.md`;
  outra área → `../9-depara/deduplicacao-giata.md`); texto entre `[ ]` = título legível (PT-BR, com acento).
  Esse formato navega no **Obsidian, no GitLab (repo) e no IntelliJ IDEA** — diferente de `[[wikilinks]]`,
  que **só o Obsidian** resolve (no GitLab/IDEA fica texto cru). Como o nome de arquivo é slug ASCII, o
  caminho não precisa de escape e fica Sonar-safe. **Exceção:** o `related:` do frontmatter continua em
  `[[slug|Título]]` (wikilink) — é metadado só-Obsidian (grafo/properties); GitLab/IDEA não renderizam
  frontmatter, então não atrapalha. Mantenha o título também em `aliases` (ajuda busca/quick-switcher).
- **Frontmatter YAML** com chaves em inglês (compatível com Dataview/plugins), valores em PT-BR.
  Campos: `title`, `aliases`, `tags`, `status`, `created`, `updated`, `related`, `code_refs`, `tickets`.
- **Rastreabilidade ao Jira**: toda nota nascida ou alterada por um card guarda o código dele
  em `tickets` (ex.: `tickets: [PA-8745]`). É como você liga a spec ao histórico de mudança.
- **Atomicidade**: uma ideia por nota. Se você está escrevendo "e também..." sobre um assunto
  diferente, provavelmente são duas notas ligadas por um link.
- **Links são de primeira classe**: toda nota deve apontar para pelo menos uma outra e ser
  alcançável a partir de um MOC. Nota órfã é cheiro de problema.
- **Datas** no formato `YYYY-MM-DD`. Atualize `updated` sempre que mexer no conteúdo.
- **Pastas (projetos grandes)**: a raiz guarda só o índice geral (`1. ...`); as demais notas vão
  em poucas pastas rasas por área, prefixadas com número (`2. ...`, `3. ...`). Os links do corpo
  carregam o caminho relativo até o arquivo-alvo — ver *Organização em pastas* abaixo e `references/convencoes.md`.

Os templates prontos estão em `assets/template-spec.md` e `assets/template-moc.md` — copie e
preencha em vez de montar do zero.

## Operação: Criar nota de spec

1. **Defina o escopo atômico.** Pergunte-se: "qual é a *uma* ideia desta nota?" Uma feature
   ampla (ex.: "Reserva de hotel") raramente é uma nota só — costuma virar um **MOC** que liga
   notas atômicas (regra de cancelamento, cálculo de markup, contrato do endpoint, etc.). Se o
   usuário descreveu algo grande, proponha essa decomposição antes de escrever.
2. **Escolha o título** como o nome canônico da ideia (substantivo, específico) — em PT-BR,
   com acento, legível. Em seguida derive o **nome do arquivo** como o slug ASCII do título (minúsculas,
   sem acento, todo char fora de `[a-z0-9]` → hífen, colapsa repetidos, só `[a-z0-9-]`; ex.:
   `Cálculo de markup` → `calculo-de-markup.md`, `Disponibilidade (Avail)` → `disponibilidade-avail.md`).
   Esse slug é a chave estável: os links do corpo apontam para o caminho relativo até este arquivo
   (`[Cálculo de markup](../7-pricing/calculo-de-markup.md)`). Mantenha o título em `aliases` (busca/quick-switcher).
3. **Preencha o template** de `assets/template-spec.md`. Não invente requisitos: documente o
   que o usuário disse e o que está no código. Onde faltar informação, deixe um marcador
   explícito (`> ❓ A definir: ...`) em vez de preencher com suposição.
4. **Ligue ao código.** Em `code_refs` (frontmatter) e na seção *Referências no código*, aponte
   arquivos/classes reais do repositório (`src/main/java/.../AvailService.java`). Confirme que
   os caminhos existem antes de citá-los.
5. **Conecte.** Adicione links de caminho relativo para notas relacionadas que você encontrou no
   Passo 0, e registre a nota no MOC do tema (criando o MOC se ainda não houver e fizer sentido).

## Operação: Linkar / conectar notas

- **No corpo, use link Markdown de caminho relativo**: `[Título legível](caminho/relativo/slug.md)`.
  Ex.: de `2-avail/...` para pricing → `[Cálculo de markup](../7-pricing/calculo-de-markup.md)`;
  mesma pasta → `[Busca de quartos](busca-de-quartos.md)`. O texto entre `[ ]` é o título legível.
  Navega em Obsidian, GitLab (repo) e IDEA. Para cabeçalho: `[Título](caminho/slug.md#Heading)`
  (a âncora pode divergir entre GitLab e Obsidian — prefira link em nível de nota).
- **Só o `related:` do frontmatter usa `[[slug|Título]]`** (wikilink) — relação só-Obsidian para o
  grafo. No corpo **não** use `[[wikilinks]]`: GitLab e IDEA não os resolvem.
- O Obsidian gera backlinks automaticamente, mas a relação semântica ganha clareza quando
  **ambas** as notas se citam na seção *Notas relacionadas*. Ao criar A→B, avalie se cabe B→A.
- Antes de linkar, **confirme que a nota-alvo existe** e calcule o caminho relativo correto até ela
  (liste a pasta). Link para arquivo inexistente quebra a navegação — só aponte para nota ainda não
  escrita de propósito (ex.: marcar algo a escrever depois) e avise o usuário.
- Ao linkar, prefira conexões que digam *por quê* se relacionam (uma frase de contexto perto do
  link vale mais que um link solto).

## Operação: Evoluir spec existente

Uma feature mudou (tipicamente via card Jira). "Evoluir" **não** é o mesmo que "criar do zero":
o conhecimento já existe no grafo e a mudança precisa entrar sem duplicar nem quebrar links.
Antes de escrever, faça o Passo 0 e identifique **quais notas o assunto já toca**. Depois,
classifique a mudança e siga a régua:

1. **Aditiva e transversal** — a mudança é uma ideia nova que atravessa conceitos já
   documentados (ex.: uma nova regra que vale para *Avail* **e** *Rooms*). Crie uma **nota
   atômica nova** para a regra e **linke a partir** de cada nota afetada (e ela de volta).
   Documente a regra uma única vez; as notas existentes só ganham um link em *Notas
   relacionadas*. Duplicar o texto nas duas notas é o erro a evitar — sai de sincronia.
2. **Ajuste a um conceito existente** — a mudança refina algo que já é o tema de uma nota só.
   Edite essa nota no lugar, atualize o conteúdo, bumpe `updated` e **anexe o card em `tickets`**.
3. **Substituição de comportamento** — o novo comportamento substitui o antigo. Marque a nota
   antiga como `status: obsoleto`, adicione um link para a sucessora (nunca apague — o
   histórico da decisão importa), e crie/atualize a nota que descreve o comportamento novo.

Em todos os casos: registre o card em `tickets`, confirme `code_refs` apontando para o código
real que implementa (ou implementará) a mudança, e ao terminar varra a pasta por links
afetados (a régua de "nunca deixe link quebrado" do `references/convencoes.md` vale aqui também).

## Operação: Refatorar (dividir / mesclar)

- **Dividir**: quando uma nota mistura ideias (vários assuntos sob `##` independentes), extraia
  cada ideia para uma nota atômica nova, substitua o trecho original por um link, e
  preserve o histórico de conexões. A nota original pode virar um MOC daquele tema.
- **Mesclar duplicadas**: se duas notas cobrem a mesma ideia, escolha o título canônico, una o
  conteúdo, e converta a outra em um redirecionamento via `aliases` (adicione o título antigo
  como alias da nota mantida) **ou** atualize todos os links que apontavam pra ela. Nunca
  deixe links quebrados.
- Toda refatoração mexe em links de outras notas — depois de dividir/mesclar, **varra a pasta**
  pelos links de caminho afetados e atualize-os. Veja `references/convencoes.md` para a checklist.

## Operação: MOC (Map of Content / índice)

Um MOC é a porta de entrada de um tema: uma nota-índice que agrupa e dá ordem aos links
das notas atômicas daquele assunto. Use `assets/template-moc.md`. Tag `moc` no frontmatter.
Agrupe os links por subtema com cabeçalhos `##` e uma frase curta de orientação por grupo — o
MOC não é só uma lista, é um mapa que conta *como* navegar o conhecimento daquele tema.

## Organização em pastas (opcional)

Por padrão o Zettelkasten organiza pela rede de links, não por pastas — e para vaults pequenos
manter tudo numa pasta só é o mais simples. Mas em projetos grandes, **pastas rasas por área**
ajudam a navegar no explorer do Obsidian sem brigar com o método. O padrão recomendado:

- **A raiz guarda só o índice geral** (o MOC raiz), nomeado com prefixo numérico (slug ASCII) para
  ordenar no topo: arquivo `1-projeto-visao-geral.md`, título `1. Projeto (visão geral)`. Ao renomear
  o arquivo de um índice/nota que já existia, atualize os caminhos relativos que apontam para ele em
  todas as notas (o Obsidian/IDEA fazem isso ao renomear pela interface).
- **As demais notas vão em poucas pastas por área**, com **nome de pasta também em slug ASCII**
  prefixado com número (ex.: `2-negocio/`, `3-arquitetura/` — os nomes dependem do domínio do
  projeto). Cada área tem seu próprio MOC dentro da pasta.
- **Pastas organizam navegação; os links do corpo carregam o caminho.** Como os links são
  caminhos relativos (`../9-depara/...`), **mover uma nota entre pastas muda os caminhos** — faça o
  move pelo Obsidian ou pelo IDEA, que reescrevem os links automaticamente; num move manual, atualize
  os caminhos afetados. A organização do conhecimento continua nos links/MOCs; a pasta é um atalho
  visual. Como o nome de pasta entra no caminho (que o Sonar varre **e** que vai dentro do link),
  **a pasta também usa slug ASCII** (sem acento/espaço).
- **Mantenha raso** (um nível por área). Para estrutura mais fina, use MOCs e links — não subpastas
  profundas.

> Dica de ordenação: se o Obsidian estiver listando pastas antes de arquivos, ajuste em
> *Settings → Files & Links* (ou no menu de ordenação do explorer) para ordenar arquivos e pastas
> juntos — assim o índice `1. ...` aparece no topo.

## Princípios do método

Para casos não cobertos aqui (e para entender o *porquê* das regras acima), consulte
**`references/zettelkasten-principles.md`**: atomicidade, autonomia, conectividade e "escreva
com suas palavras", e como cada um se traduz para documentação de software.

## Fechamento

Ao terminar, diga ao usuário, de forma curta: quais notas criou/alterou (com os caminhos),
quais links novos surgiram, e se ficou algum marcador `❓ A definir` pendente. Isso mantém o
grafo auditável e deixa claro o que ainda precisa de decisão humana.
