# Agente: Organização de Cadências (v1.1)

Este documento instrui o Claude Code sobre como operar a cadência D1–D6 dos leads no
Kommo. Complementa o `AGENTE.md` da v1 — as regras invioláveis de lá continuam valendo,
somadas às da `spec-cadencias.md` (seção 7).

Ler `spec-cadencias.md` antes de qualquer execução.

## Gatilhos

Dois modos de acionamento, sempre sob demanda:

1. **Relatório** — o usuário pede a visão da cadência (ex: "quem tá vencido?",
   "/cadencias", "cadência do funil Inbound").
2. **Avanço** — durante um `/atualizar-lead`, a narração evidencia um toque cumprido em
   um lead que está na cadência (tem tag D). O avanço da tag é um passo adicional do
   fluxo da v1, não um fluxo separado.

## Como calcular o status de um lead na cadência

Para cada lead com tag D1–D6:

1. **D atual** = a tag D presente no card. Sem tag D → fora da cadência (não incluir).
2. **Dia previsto do D atual** = data de entrada na cadência (primeira tag D, visível no
   histórico do card) + (n − 1) dias corridos, onde n é o número do D.
3. **Toque cumprido?** Duplo cheque, nesta ordem:
   - nota de interação datada do dia previsto (ou posterior) no card — fonte primária;
   - tarefa do Kommo concluída nesse dia — sinal secundário.
4. **Status:**
   - toque cumprido → **em dia** (aguardando próximo D);
   - dia previsto é hoje, sem toque ainda → **vencendo hoje**;
   - dia previsto passou, sem toque → **vencido** (registrar há quantos dias).

Nunca inferir toque sem evidência no card ("provavelmente falou") — sem sinal, não conta.

## Passos — Relatório sob demanda

1. Aplicar os filtros pedidos (responsável, funil). Sem filtro → tudo que tem tag D.
2. Calcular o status de cada lead (seção acima).
3. Montar o relatório com **vencidos + vencendo hoje** (os em dia ficam de fora):
   - vencidos primeiro, do atraso mais antigo para o mais recente;
   - depois os vencendo hoje;
   - cada linha: lead, D atual, dias de atraso (se houver), último toque registrado.
4. O relatório é leitura — **não alterar nenhum card durante o relatório**.

## Passos — Avanço de cadência (dentro do /atualizar-lead)

Quando o card tem tag D e a narração evidencia toque cumprido:

1. Confirmar a evidência: a própria nota que está sendo registrada é a fonte primária.
2. Avançar a tag: remover Dn, aplicar Dn+1.
3. **Salto** (ex: D2 → D4) só com critério claro na narração (ex: lead pediu retorno em
   data que corresponde a outro D). Registrar o critério na nota. Sem critério →
   sequencial.
4. Registrar a origem na nota (ex: "cadência avançada para D3 via áudio em 15/07").
5. Reportar ao usuário o avanço junto do relatório normal do /atualizar-lead.

## Passos — Fim de cadência

1. **D6 com toque cumprido e sem resposta do lead na cadência inteira** → mover para
   nutrição, nota com motivo ("cadência completa sem resposta").
2. **Desinteresse explícito em qualquer D** (ex: "não quero", "fechei com outro") → mover
   para perdido, nota com motivo. Não continuar a cadência. Ao mover, o Kommo exige
   escolher um "Motivo de perda" num dropdown nativo — mapear o motivo da narração para
   a opção mais próxima (ex: "fechei com outro" → "Comprado do concorrente") e registrar
   o motivo literal na nota.
3. **Sinais mistos / ambíguos** → não decidir. Manter onde está, registrar a informação e
   sinalizar revisão manual (mesma regra de confiança da v1).

## Mecânica do Kommo (achados operacionais das rodadas de teste)

- **Motivo de perda é obrigatório**: mover um card para "Perdido" abre um dropdown nativo
  de "Motivo de perda". A movimentação não completa sem escolher uma opção — decidir o
  mapeamento antes de arrastar o card (ver passo 2 do fim de cadência).
- **Corrida de foco no editor de nota**: logo após navegar para um card, o campo de nota
  pode ainda não ter foco real. Clicar no campo e **confirmar que o editor abriu** (ler a
  página de novo) antes de digitar — digitar às cegas já perdeu notas em teste.
- **Tarefa concluída aceita "resultado" em texto**: ao concluir uma tarefa no Kommo, o
  campo de resultado é um bom lugar para registrar o toque (o que foi feito, canal, data).
  Isso alimenta o sinal secundário do cálculo de status sem depender de nota separada.
- **Excluir leads exige 2FA na sessão**: a ação "excluir" da lista só completa se a sessão
  logada tiver autenticação de dois fatores ativa — sem 2FA, o Kommo bloqueia após a
  confirmação. A exclusão não é permanente: leads excluídos ficam recuperáveis pelo filtro
  "Excluídos" da visualização de Lista.

## Economia de contexto

- Ler cards por `get_page_text` / `read_page`. Screenshot só quando texto não resolver.
- Em varreduras de relatório com muitos leads, usar o subagente `revisor-pipeline` ou um
  subagente dedicado para a inspeção card a card, devolvendo só o consolidado.

## Regras invioláveis (além das 5 da v1)

6. Não alterar a tag D sem evidência de toque cumprido no card.
7. Não disparar nenhum toque — cadência é lida, reportada e marcada; nunca executada.
8. Sempre registrar a origem ao mudar tag ou destino (nutrição/perdido).
