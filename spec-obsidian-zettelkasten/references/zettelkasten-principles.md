# Princípios do Zettelkasten aplicados a specs de software

O Zettelkasten ("caixa de fichas", método de Niklas Luhmann) é um sistema de notas em que o
conhecimento emerge da **rede de ligações** entre ideias pequenas, não de documentos longos.
Quatro princípios sustentam o método. Entendê-los ajuda a decidir bem nos casos que as regras
da skill não cobrem.

## 1. Atomicidade — uma ideia por nota

Cada nota trata de **um** conceito autocontido. Em software, isso significa separar, por
exemplo: a *regra de cancelamento*, o *contrato do endpoint de reserva* e a *decisão de usar
cache distribuído* em três notas distintas — mesmo que pertençam à mesma feature.

**Por quê:** notas atômicas são reutilizáveis (uma regra pode ser linkada por várias features),
fáceis de revisar e versionar, e tornam as conexões precisas. Um documento gigante "Reservas"
não pode ser linkado a um ponto específico; três notas atômicas sim.

**Sinal de que precisa dividir:** a nota tem várias seções `##` que não dependem uma da outra,
ou você escreve "além disso..." mudando de assunto.

## 2. Autonomia — a nota se sustenta sozinha

Cada nota deve ser compreensível por si, sem exigir leitura de outra antes. Inclua o contexto
mínimo e linke o resto. Isso permite que a nota sobreviva mesmo que as vizinhas mudem ou sejam
apagadas.

**Em software:** a spec de uma regra de negócio deve enunciar a regra completa, não "a regra
descrita na nota X". Linke X para aprofundar, mas não dependa dela para o sentido básico.

## 3. Conectividade — o valor está nos links

Uma nota isolada vale pouco; o conhecimento vive nas ligações. Ao criar/editar uma nota,
pergunte sempre: "com o que isto se conecta?" e materialize com `[[wikilinks]]`. Prefira links
que expliquem *por que* duas ideias se relacionam — um link comentado vale mais que dez links
soltos.

**Em software:** uma decisão técnica linka ao requisito que a motivou; um endpoint linka às
regras de negócio que aplica; uma spec linka ao arquivo de código que a implementa (`code_refs`).
Notas órfãs (sem nenhum link de entrada ou saída) são cheiro de problema — toda nota deve ser
alcançável a partir de um MOC.

## 4. Escreva com suas palavras

Reescrever a ideia com as próprias palavras (em vez de colar trechos) força o entendimento e
revela lacunas. Para specs: não copie o ticket nem o código verbatim — explique a intenção, o
porquê, os trade-offs. O código já diz *o que* faz; a spec deve dizer *por que* e *o que se
espera*.

## MOCs — Maps of Content

Conforme o vault cresce, MOCs (notas-índice) dão estrutura emergente sem impor hierarquia
rígida de pastas. Um MOC agrupa e ordena os links de um tema, servindo de ponto de partida
para navegar. A organização do conhecimento vive nos **links**, não na árvore de diretórios —
por isso MOCs dispensam subpastas profundas. Crie um MOC quando um tema acumular notas
suficientes para que valha a pena ter um mapa de entrada (tipicamente 3+ notas relacionadas).

Isso não proíbe pastas: em projetos grandes, **pastas rasas por área** (um nível, prefixadas
com número) são um bom atalho de navegação no Obsidian e convivem com os MOCs — desde que
permaneçam só um auxílio visual. Como os `[[wikilinks]]` resolvem pelo nome da nota, a pasta
nunca participa do grafo de conhecimento. Ver *Organização em pastas e índice* em
`references/convencoes.md`.

## Resumo para decisões rápidas

- Está misturando assuntos? → **divida** (atomicidade).
- A nota só faz sentido lendo outra antes? → adicione contexto (autonomia).
- A nota não aponta para nada nem é apontada? → **conecte** (conectividade).
- Copiou ticket/código? → **reescreva** o porquê com suas palavras.
- Um tema ganhou massa crítica? → crie/atualize um **MOC**.
