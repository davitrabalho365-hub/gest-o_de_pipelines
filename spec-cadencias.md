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
- **Avanço D → D+1 é por ação:** o lead só passa para o próximo D quando o toque daquele
  dia é efetivamente cumprido. Tempo passando sozinho não avança a cadência.
- **Vencimento:** o dia previsto para o D atual chegou (ou passou) e o toque não foi
  cumprido → o lead está vencido naquela etapa da cadência.

## 4. Escopo (v1.1 — cadências)

- Ler as tags D1–D6 dos cards.
- Determinar o status de cada lead na cadência: em dia / vencendo hoje / vencido.
- Gerar um **relatório sob demanda** (via comando) listando vencidos e vencendo, de forma
  priorizável.
- Avançar a tag D do lead quando um toque é cumprido, registrando a origem da mudança.

## 5. Fora de escopo

- Checagem automática / agendada rodando sozinha (fica para versão futura).
- Disparar os toques (mandar WhatsApp, e-mail, ligar) — continua valendo a regra da v1:
  **nada fora do CRM**. A cadência só é lida e marcada, nunca executada pelo agente.
- Criar a estrutura de cadência do zero — assume-se que as tags D1–D6 já estão em uso.
- Enriquecimento de dados de leads (é a outra feature da v1.1, spec separada).

## 6. Critérios de sucesso

1. Dado um conjunto de leads com tags D1–D6, o agente identifica corretamente o D atual de
   cada um.
2. O agente determina quem está vencido (o dia do D passou sem toque cumprido) e quem vence
   hoje.
3. O relatório sob demanda lista esses leads de forma clara e priorizável.
4. Quando um toque é cumprido, a tag do lead avança para o próximo D, com origem registrada.
5. O agente não avança nem marca como vencido nada por conta própria — só reflete o que os
   dados (tags, tempo, toques cumpridos) mostram.

## 7. Regras invioláveis

Herda as 5 regras invioláveis da v1 (ver `spec.md`), e acrescenta:

6. Não altera a tag D de um lead sem base num toque efetivamente cumprido.
7. Não dispara nenhum toque — só lê, reporta status e atualiza a tag.
8. Sempre registra a origem ao mudar a tag (ex: nota "cadência avançada para D3 via ...").

## 8. A validar (assunções a confirmar antes de escrever os testes)

Estas são decisões que ainda dependem de você. A spec assume um caminho, mas confirme ou
corrija cada ponto:

- **Contagem do dia:** a partir de quando conta o D1 — entrada na cadência? criação do
  lead? primeiro contato? E são dias corridos ou dias úteis?
- **Como o agente sabe que o toque foi cumprido:** tarefa marcada como concluída no Kommo?
  Uma nota? Você fala na narração ("fiz o follow-up do Fulano hoje")?
- **Depois do D6:** o lead sai da cadência? Vira outra coisa (ex: nutrição, perdido)?
- **Pode pular D?** Ou a sequência é sempre D1 → D2 → ... → D6 sem saltos?
- **Escopo do relatório:** quer vencidos e vencendo-hoje juntos, ou só vencidos? Filtra por
  responsável / funil?

## 9. Changelog

### v1.1 — cadências (em especificação)
- Rastreamento de tags D1–D6, cálculo de vencimento e relatório sob demanda.
