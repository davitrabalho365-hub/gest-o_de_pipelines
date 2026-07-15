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

Achado do setup: a tag `D1` já existia no Kommo; `D2`, `D3` e `D6` foram criadas
durante a montagem dos fixtures (`D4` e `D5` ainda não existem).

---

## Bloco A — Leitura e status

### Teste 1 — Identificação do D atual
**Dado:** cards de teste com tags D1–D6 aplicadas (pelo menos dois Ds diferentes).
**Espero:** o agente identifica corretamente em qual D cada lead está, lendo as tags.
**Não aceito:** confundir a tag D com outras tags do card, ou chutar um D para lead sem tag.
**Status:** ☐

### Teste 2 — Lead em dia (nota de hoje)
**Dado:** lead em Dn com nota de interação datada de hoje (registrada via `/atualizar-lead`).
**Espero:** status "em dia" — o toque do dia conta como cumprido pela nota (fonte primária).
**Não aceito:** marcar como vencido um lead cujo toque está evidenciado por nota de hoje.
**Status:** ☐

### Teste 3 — Toque cumprido só por tarefa concluída
**Dado:** lead sem nota de hoje, mas com tarefa do Kommo concluída hoje (ex: por automação).
**Espero:** o sinal secundário conta — toque considerado cumprido.
**Não aceito:** exigir nota quando há tarefa concluída evidenciando o toque.
**Status:** ☐

### Teste 4 — Vencido (nenhum sinal no dia previsto)
**Dado:** lead cujo dia previsto do D atual já passou, sem nota nem tarefa concluída nesse dia.
**Espero:** status "vencido", indicando desde quando.
**Não aceito:** considerar cumprido por inferência ("provavelmente falou com ele") sem
evidência no card.
**Status:** ☐

### Teste 5 — Vencendo hoje
**Dado:** lead cujo dia previsto do D atual é hoje, ainda sem toque registrado.
**Espero:** status "vencendo hoje" — distinto de vencido.
**Não aceito:** classificar como vencido antes do dia acabar, ou omitir do relatório.
**Status:** ☐

## Bloco B — Relatório sob demanda

### Teste 6 — Relatório geral
**Dado:** mistura de leads em dia, vencendo hoje e vencidos.
**Espero:** relatório lista vencidos + vencendo hoje, priorizável (vencidos primeiro,
mais antigos no topo), sem incluir os em dia.
**Não aceito:** lista incompleta, leads em dia poluindo o relatório, ou ordem aleatória.
**Status:** ☐

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
**Status:** ☐

### Teste 9 — Salto com critério
**Dado:** lead em D2 cuja narração traz critério claro para pular (ex: lead pediu retorno
só na semana que vem → D4).
**Espero:** salto D2 → D4 com o critério registrado na nota.
**Não aceito:** saltar sem critério registrado, ou recusar salto quando o critério é claro.
**Status:** ☐

### Teste 10 — Tempo sozinho não avança
**Dado:** lead em D1 há 3 dias, sem nenhum toque registrado.
**Espero:** continua em D1 (vencido) — o agente reporta o atraso, não "corrige" a tag.
**Não aceito:** avançar a tag para compensar o tempo passado.
**Status:** ☐

## Bloco D — Fim de cadência

### Teste 11 — Pós-D6 sem resposta → nutrição
**Dado:** lead em D6 com toque cumprido e nenhum sinal de interesse ou desinteresse (sem
resposta ao longo da cadência).
**Espero:** encaminhado para nutrição, com nota registrando o motivo (cadência completa
sem resposta).
**Não aceito:** marcar como perdido sem desinteresse explícito, ou deixar o lead "preso"
em D6 sem sinalização.
**Status:** ☐

### Teste 12 — Desinteresse explícito → perdido
**Dado:** narração com desinteresse claro (ex: "disse que não quer, já fechou com outro"),
em qualquer D.
**Espero:** encaminhado para perdido, com o motivo registrado.
**Não aceito:** insistir na cadência (avançar D) depois de desinteresse explícito.
**Status:** ☐

### Teste 13 — Fim ambíguo → revisão manual
**Dado:** lead ao fim do D6 com sinais mistos (ex: respondeu uma vez "depois vemos", nunca
mais).
**Espero:** não decide entre nutrição e perdido — sinaliza revisão manual, mantendo o lead
onde está.
**Não aceito:** chutar um destino sem confiança.
**Status:** ☐

---

## Notas de execução

(preencher a cada rodada, como no TESTES.md da v1 — incluindo falhas e ajustes gerados)
