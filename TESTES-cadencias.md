# Cenários de teste — v1.1 Cadências

Mesmo formato da v1: Dado / Espero / Não aceito. Roteiros para rodar de verdade contra o
Kommo (com cards de teste tagueados D1–D6) e conferir manualmente antes de considerar a
feature pronta. Ver `spec-cadencias.md` para o modelo e as regras.

Marque cada um como ✅ (passou), ⚠️ (parcial, anotar o porquê) ou ❌ (falhou).

## Cards de teste (fixtures, criados em 15/07)

Todos no funil Inbound (Tráfego), etapa Tentativa de contato, com nota "FIXTURE DE
TESTE" simulando a data de entrada na cadência (os cards nasceram em 15/07):

| Card | ID | Tag | Cenário |
|------|----|-----|---------|
| Teste Cad A | #13016401 | D2 | Em dia — entrada 14/07, D1 cumprido 14/07, toque D2 registrado hoje |
| Teste Cad B | #13017073 | D1 | Vencendo hoje — entrada 15/07, nenhum toque ainda |
| Teste Cad C | #13017493 | D3 | Vencido — entrada 12/07, D1–D2 cumpridos, D3 previsto 14/07 sem toque |
| Teste Cad D | #13017927 | D6 | Fim de cadência — entrada 10/07, D1–D6 cumpridos, zero resposta → nutrição |

Fixtures adicionados na rodada 2:

| Card | ID | Tag | Cenário |
|------|----|-----|---------|
| Teste Cad E | #13027305 | D6 | Fim ambíguo — uma resposta "depois vemos" (D3), silêncio depois → revisão manual |
| Teste Cad F | #13027693 | D4 | Em dia via tarefa — sem nota de interação hoje, toque D4 evidenciado por tarefa concluída |

Achado do setup: a tag `D1` já existia no Kommo; `D2`, `D3`, `D4`, `D5` e `D6` foram
criadas durante a montagem dos fixtures — agora a estrutura D1–D6 está completa.

Limpeza (concluída em 16/07): Teste Cad A, B, E e F excluídos pelo usuário em 15/07.
Teste Cad D e Teste Luiz 1245 arquivados pelo agente em 16/07 — movidos para Perdido
(motivo indefinido), tarefas pendentes concluídas e nota de arquivamento registrada em
cada card (a exclusão via agente segue bloqueada pela exigência de 2FA; achado no
AGENTE-cadencias.md, seção "Mecânica do Kommo"). Teste Cad C permanece fechado em
Perdido desde o teste 12. Os três cards fechados ainda carregam tags D (D3/D6), por
isso o relatório varre só leads ativos.

---

## Bloco A — Leitura e status

### Teste 1 — Identificação do D atual
**Dado:** cards de teste com tags D1–D6 aplicadas (pelo menos dois Ds diferentes).
**Espero:** o agente identifica corretamente em qual D cada lead está, lendo as tags.
**Não aceito:** confundir a tag D com outras tags do card, ou chutar um D para lead sem tag.
**Status:** ✅ (identificou D1/D2/D3/D6 nos 4 fixtures e detectou tag solta "d" sem confundi-la com tag de cadência)

### Teste 2 — Lead em dia (nota de hoje)
**Dado:** lead em Dn com nota de interação datada de hoje (registrada via `/atualizar-lead`).
**Espero:** status "em dia" — o toque do dia conta como cumprido pela nota (fonte primária).
**Não aceito:** marcar como vencido um lead cujo toque está evidenciado por nota de hoje.
**Status:** ✅ (Teste Cad A classificado em dia pela nota de toque D2 de 15/07)

### Teste 3 — Toque cumprido só por tarefa concluída
**Dado:** lead sem nota de hoje, mas com tarefa do Kommo concluída hoje (ex: por automação).
**Espero:** o sinal secundário conta — toque considerado cumprido.
**Não aceito:** exigir nota quando há tarefa concluída evidenciando o toque.
**Status:** ✅ (Teste Cad F: D4 previsto hoje, sem nota de interação, tarefa concluída com resultado do toque → em dia)

### Teste 4 — Vencido (nenhum sinal no dia previsto)
**Dado:** lead cujo dia previsto do D atual já passou, sem nota nem tarefa concluída nesse dia.
**Espero:** status "vencido", indicando desde quando.
**Não aceito:** considerar cumprido por inferência ("provavelmente falou com ele") sem
evidência no card.
**Status:** ✅ (Teste Cad C: D3 previsto 14/07 sem evidência → vencido há 1 dia)

### Teste 5 — Vencendo hoje
**Dado:** lead cujo dia previsto do D atual é hoje, ainda sem toque registrado.
**Espero:** status "vencendo hoje" — distinto de vencido.
**Não aceito:** classificar como vencido antes do dia acabar, ou omitir do relatório.
**Status:** ✅ (Teste Cad B: D1 previsto hoje sem toque → "vencendo hoje", distinto de vencido)

## Bloco B — Relatório sob demanda

### Teste 6 — Relatório geral
**Dado:** mistura de leads em dia, vencendo hoje e vencidos.
**Espero:** relatório lista vencidos + vencendo hoje, priorizável (vencidos primeiro,
mais antigos no topo), sem incluir os em dia.
**Não aceito:** lista incompleta, leads em dia poluindo o relatório, ou ordem aleatória.
**Status:** ✅ (vencido primeiro, vencendo hoje depois, em dia excluído; nenhum card alterado durante o relatório)

### Teste 7 — Filtros por responsável e funil
**Dado:** leads de responsáveis e funis diferentes no mesmo cenário do Teste 6.
**Espero:** pedindo filtro (ex: "só do funil Inbound", "só do responsável X"), o relatório
traz apenas os correspondentes.
**Não aceito:** vazamento de leads de outro responsável/funil no resultado filtrado.
**Status:** ☐

## Bloco C — Avanço de cadência

### Teste 8 — Avanço sequencial após toque
**Dado:** lead em Dn com toque cumprido (narração registrada via `/atualizar-lead`).
**Espero:** tag avança para Dn+1, com nota registrando a origem do avanço.
**Não aceito:** avançar sem evidência de toque, esquecer a nota de origem, ou remover a
tag antiga sem colocar a nova.
**Status:** ✅ (Teste Cad B: nota do toque D1 às 21:05, tag D1→D2 confirmada pelo verificador-fluxo)

### Teste 9 — Salto com critério
**Dado:** lead em D2 cuja narração traz critério claro para pular (ex: lead pediu retorno
só na semana que vem → D4).
**Espero:** salto D2 → D4 com o critério registrado na nota.
**Não aceito:** saltar sem critério registrado, ou recusar salto quando o critério é claro.
**Status:** ✅ (Teste Cad A: lead pediu retorno 18/07 = dia previsto de D5 → salto D2→D5 com critério na nota)

### Teste 10 — Tempo sozinho não avança
**Dado:** lead em D1 há 3 dias, sem nenhum toque registrado.
**Espero:** continua em D1 (vencido) — o agente reporta o atraso, não "corrige" a tag.
**Não aceito:** avançar a tag para compensar o tempo passado.
**Status:** ✅ (Teste Cad C: vencido em D3, tag mantida em D3 até haver evidência ou decisão — nunca "corrigida" por tempo)

## Bloco D — Fim de cadência

### Teste 11 — Pós-D6 sem resposta → nutrição
**Dado:** lead em D6 com toque cumprido e nenhum sinal de interesse ou desinteresse (sem
resposta ao longo da cadência).
**Espero:** encaminhado para nutrição, com nota registrando o motivo (cadência completa
sem resposta).
**Não aceito:** marcar como perdido sem desinteresse explícito, ou deixar o lead "preso"
em D6 sem sinalização.
**Status:** ✅ (rodada 2: Teste Cad D movido para "Nutrição" com nota de fechamento; verificado pelo verificador-fluxo)

### Teste 12 — Desinteresse explícito → perdido
**Dado:** narração com desinteresse claro (ex: "disse que não quer, já fechou com outro"),
em qualquer D.
**Espero:** encaminhado para perdido, com o motivo registrado.
**Não aceito:** insistir na cadência (avançar D) depois de desinteresse explícito.
**Status:** ✅ (Teste Cad C: desinteresse explícito → "Perdido" com motivo "Comprado do concorrente", sem avançar cadência)

### Teste 13 — Fim ambíguo → revisão manual
**Dado:** lead ao fim do D6 com sinais mistos (ex: respondeu uma vez "depois vemos", nunca
mais).
**Espero:** não decide entre nutrição e perdido — sinaliza revisão manual, mantendo o lead
onde está.
**Não aceito:** chutar um destino sem confiança.
**Status:** ✅ (Teste Cad E: uma resposta "depois vemos" + silêncio → mantido na etapa, nota "REVISÃO MANUAL NECESSÁRIA")

## Bloco E — Formato de saída (fix v1.1.1)

### Teste 14 — Relatório no formato tabela + parágrafo crítico
**Dado:** qualquer execução do `/cadencias` (sob demanda ou ciclo de loop) com leads na fila.
**Espero:** saída com exatamente duas partes — tabela (Lead | Cadência | Vencimento, placar
opcional de uma linha antes) e um parágrafo final de **até 250 caracteres** com o ponto
crítico do dia (ou "Sem pontos críticos hoje.").
**Não aceito:** texto corrido além do parágrafo final, parágrafo acima de 250 caracteres,
seções extras, ou detalhes (IDs, evidências) sem o usuário pedir.
**Status:** ☐

---

## Notas de execução

(preencher a cada rodada, como no TESTES.md da v1 — incluindo falhas e ajustes gerados)

### Rodada 1 — 15/07 — relatório sob demanda contra os 4 fixtures
Primeira rodada usando o subagente `revisor-pipeline` para a varredura card a card
(leitura por texto, sem screenshots) — só o consolidado voltou ao contexto principal
(~17k tokens no contexto isolado do subagente). Classificação bateu 100% com o gabarito:
A em dia (excluído do relatório), B vencendo hoje, C vencido há 1 dia, D sinalizado para
nutrição. Testes 1, 2, 4, 5 e 6 ✅; Teste 11 ⚠️ (sinalização ok, movimentação pendente).
Bônus: o subagente detectou uma tag solta "d" no Teste Cad C (resíduo de digitação do
setup) sem confundi-la com tag de cadência — remover essa tag na limpeza dos fixtures.
Pendentes para as próximas rodadas: toque via tarefa (3), filtros (7), avanço/salto de
tag (8, 9, 10), perdido (12) e fim ambíguo (13).

### Rodada 2 — 15/07 — ações de escrita (avanço, salto, destinos) contra os fixtures
Executados os 7 testes pendentes menos o de filtros. Resultados: toque via tarefa
concluída (3 ✅, card F), avanço sequencial D1→D2 com nota (8 ✅, card B), salto
D2→D5 com critério do lead (9 ✅, card A), tag vencida não "corrigida" por tempo
(10 ✅, card C), fim de cadência → Nutrição (11 ✅, card D), desinteresse explícito →
Perdido com motivo "Comprado do concorrente" (12 ✅, card C), fim ambíguo → revisão
manual sem mover (13 ✅, card E). Todas as 5 escritas verificadas pelo subagente
`verificador-fluxo` em contexto isolado (~20k tokens lá): etapa, tag e nota bateram
em 100% dos cards, histórico preservado. Limpeza: tag solta "d" removida do card C.
Teste 7 (filtros) segue pendente — os fixtures têm um único responsável e funil, o que
não permite provar ausência de vazamento; testar quando houver dados de mais de um
responsável. Achados operacionais do Kommo: (a) mover para "Perdido" exige escolher
"Motivo de perda" num dropdown nativo — mapear o motivo da narração para as opções;
(b) o editor de nota tem uma corrida de foco após navegar para o card — clicar no campo
e só digitar depois de confirmar o editor aberto (2 notas se perderam e foram refeitas);
(c) concluir tarefa aceita um "resultado" em texto — bom lugar para registrar o toque.
