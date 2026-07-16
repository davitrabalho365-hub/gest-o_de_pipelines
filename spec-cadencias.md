# Spec: Organização de Cadências (v1.1)

> Feature da v1.1. Depende do que a v1 estabeleceu (ver `spec.md` e `AGENTE.md`).
> As regras invioláveis da v1 continuam valendo aqui.

## 1. Visão geral

Rastreamento da cadência de follow-up (D1–D6) dos leads e **relatório sob demanda** de
vencimentos. O SDR marca onde cada lead está na cadência via tags nativas do Kommo; o
agente lê essas tags, cruza com o tempo decorrido e os toques cumpridos, e — quando
solicitado — devolve um relatório de quem está vencido ou vencendo, para priorização.

Assim como na v1, isto **não roda sozinho 24/7**. É acionado por você, sob demanda.

## 2. Usuário e fluxo

Mesmo SDR/BDR da v1. Durante o dia ele cumpre os toques de cada lead. Quando quer uma
visão da cadência, pede o relatório e recebe a lista de leads vencidos / vencendo, para
saber quem contatar primeiro.

## 3. Modelo da cadência

- **D1–D6 = dias de follow-up.** Cada D representa um toque previsto num dia da sequência.
- **Tags nativas do card** marcam em qual D o lead está.
- **Contagem:** o relógio da cadência começa na **entrada do lead na cadência** (quando
  recebe a primeira tag D), não na criação do lead.
- **Avanço é por ação, não por tempo:** o lead só passa para o próximo D quando o toque é
  efetivamente cumprido. Tempo passando sozinho não avança a cadência.
- **Não é linear:** pode pular D (ex: D2 → D4) desde que haja critério claro registrado
  para o salto. Sem critério, avança sequencial.
- **Vencimento:** o dia previsto para o D atual chegou (ou passou) e o toque não foi
  cumprido → o lead está vencido naquela etapa da cadência.
- **Fim da cadência (pós-D6):** o lead sai da cadência para **nutrição** ou **perdido**,
  conforme o critério: cadência completa sem resposta → nutrição; desinteresse explícito
  em qualquer ponto → perdido. Sem confiança em nenhum dos dois → sinaliza revisão manual
  (mesma regra de confiança da v1).

### Como o agente sabe que um toque foi cumprido (duplo cheque)

O SDR não tem o hábito de concluir tarefas no CRM — e não deve precisar criar esse hábito.
A evidência de toque vem de dois sinais, em ordem de prioridade:

1. **Notas registradas no card** (fonte primária): cada atualização via `/atualizar-lead`
   grava uma nota datada com origem. Uma nota de interação datada de hoje = toque de hoje
   cumprido. O fluxo da v1 já produz esse rastro sem esforço extra.
2. **Tarefas concluídas no Kommo** (sinal secundário): quando existirem (ex: concluídas
   por automação nativa), contam como confirmação adicional.

Se nenhum dos dois sinais existe para o dia previsto, o toque é considerado não cumprido.

## 4. Escopo (v1.1 — cadências)

- Ler as tags D1–D6 dos cards.
- Determinar o status de cada lead na cadência: em dia / vencendo hoje / vencido.
- Gerar um **relatório sob demanda** (via comando) listando **vencidos + vencendo hoje**,
  com filtro por **responsável** e por **funil**, de forma priorizável.
- **Modo loop:** repetir o relatório em intervalo fixo dentro de uma sessão aberta pelo
  usuário (ex: `/loop 3h /cadencias`). O loop nasce e morre com a sessão — é o mesmo
  relatório sob demanda, repetido; não muda o que o agente pode fazer.
- Avançar a tag D do lead quando um toque é cumprido, registrando a origem da mudança.
- Ao final da cadência (ou diante de desinteresse explícito), encaminhar o lead para
  nutrição ou perdido conforme o critério — ou sinalizar revisão manual sem confiança.

## 5. Fora de escopo

- Checagem rodando sozinha **sem sessão aberta pelo usuário** (agendamento na nuvem,
  execução 24/7). O modo loop dentro de uma sessão que o usuário abriu e pode encerrar
  está dentro do escopo; autonomia além disso fica para versão futura.
- Disparar os toques (mandar WhatsApp, e-mail, ligar) — continua valendo a regra da v1:
  **nada fora do CRM**. A cadência só é lida e marcada, nunca executada pelo agente.
- Criar a estrutura de cadência do zero — assume-se que as tags D1–D6 já estão em uso.
- Enriquecimento de dados de leads (é a outra feature da v1.1, spec separada).

## 6. Critérios de sucesso

1. Dado um conjunto de leads com tags D1–D6, o agente identifica corretamente o D atual de
   cada um.
2. O agente determina quem está vencido (o dia do D passou sem toque cumprido) e quem vence
   hoje.
3. O relatório sob demanda lista esses leads de forma clara e priorizável, e filtra
   corretamente por responsável e por funil quando pedido.
4. Quando um toque é cumprido, a tag do lead avança para o próximo D, com origem registrada.
   Saltos (ex: D2 → D4) só acontecem com critério claro, registrado na nota.
5. O agente não avança nem marca como vencido nada por conta própria — só reflete o que os
   dados (tags, notas datadas, tarefas concluídas) mostram.
6. Pós-D6 (ou desinteresse explícito), o lead é encaminhado para nutrição ou perdido
   conforme o critério — e sem confiança, sinaliza revisão manual em vez de decidir.
7. **Meta operacional (do usuário, não do agente):** nenhum lead vencido fica sem toque
   até as 10h da manhã. O modo loop serve a essa meta dando visibilidade recorrente da
   fila — quem cumpre o toque é o SDR. O agente não persegue a meta por conta própria.

## 7. Regras invioláveis

Herda as 5 regras invioláveis da v1 (ver `spec.md`), e acrescenta:

6. Não altera a tag D de um lead sem base num toque efetivamente cumprido.
7. Não dispara nenhum toque — só lê, reporta status e atualiza a tag.
8. Sempre registra a origem ao mudar a tag (ex: nota "cadência avançada para D3 via ...").

## 8. Decisões tomadas (15/07)

Pontos que estavam em aberto e foram definidos com o usuário:

1. **Contagem do D1:** começa na entrada do lead na cadência (primeira tag D), não na
   criação do lead. *(Pendente menor: confirmar dias corridos vs. úteis — assumido
   corridos até segunda ordem.)*
2. **Detecção de toque cumprido:** duplo cheque — notas datadas do card (fonte primária,
   produzidas naturalmente pelo fluxo da v1) + tarefas concluídas no Kommo (sinal
   secundário). O SDR não precisa concluir tarefas manualmente no CRM.
3. **Pós-D6:** nutrição (cadência completa sem resposta) ou perdido (desinteresse
   explícito); sem confiança, revisão manual.
4. **Saltos:** permitidos com critério claro registrado; sem critério, sequencial.
5. **Relatório:** vencidos + vencendo hoje, com filtros por responsável e funil.

Definidos em 16/07:

6. **Loop, não goal:** a recorrência do relatório é enquadrada como **loop** (repetição
   determinística do `/cadencias` em intervalo fixo, dentro de sessão aberta), e não como
   goal com autonomia. Motivo: a regra 7 impede o agente de agir sobre a fila — um agente
   com objetivo e sem poder de ação tende a "ajudar demais", que é fora de escopo desde a
   v1. A meta ("fila de vencidos zerada até as 10h") é do usuário e virou o critério de
   sucesso 7; o loop é só o mecanismo de visibilidade. Autonomia sobre a fila, se um dia
   fizer sentido, é conversa de v2 com spec própria.

## 9. Changelog

### v1.1 — cadências (especificada em 15/07, aguardando testes)
- Rastreamento de tags D1–D6, cálculo de vencimento e relatório sob demanda.

### v1.1 — modo loop (16/07)
- Relatório recorrente via `/loop <intervalo> /cadencias` dentro de sessão aberta.
- Meta operacional do usuário (fila de vencidos zerada até as 10h) registrada como
  critério de sucesso 7.
