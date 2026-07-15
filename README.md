# Gestão de Pipeline — Agente Coringa de Preenchimento de CRM

Agente que preenche automaticamente o card de um lead no CRM (Kommo) a partir de uma
narração falada da interação. Você conversa com o lead, volta pro CRM com o card aberto,
narra o que aconteceu, e o agente extrai as informações, preenche os campos certos e
decide (com critério) para qual etapa do funil o lead deve ir.

## Como funciona

1. Você abre o card do lead no Kommo (via Claude in Chrome).
2. Roda `/atualizar-lead <narração>` no Claude Code.
3. O agente:
   - lê o estado atual do card (etapa, observações existentes) como contexto;
   - extrai da narração o que aconteceu, próximo passo e data (sem inventar nada);
   - preenche os campos certos do card;
   - decide a etapa — move só quando há confiança clara, senão sinaliza revisão;
   - registra a origem da mudança (nota com data) para auditoria.

## Regras invioláveis

1. Nunca inventa dado que não foi dito na narração.
2. Nunca move de etapa sem confiança — melhor sinalizar do que chutar.
3. Nunca apaga observações anteriores — só adiciona.
4. Nunca age fora do CRM (nada de WhatsApp, ligação, e-mail).
5. Sempre registra a origem da mudança no card.

## Estrutura

| Arquivo | O que é |
|---------|---------|
| [`spec.md`](spec.md) | Especificação completa: escopo, critérios de sucesso, changelog de versões |
| [`AGENTE.md`](AGENTE.md) | Instruções de comportamento do agente (passo a passo + regras) |
| [`TESTES.md`](TESTES.md) | Cenários de teste v1 e notas das rodadas executadas contra o Kommo real |
| [`.claude/commands/atualizar-lead.md`](.claude/commands/atualizar-lead.md) | Slash command que dispara o fluxo |
| [`.claude/agents/revisor-pipeline.md`](.claude/agents/revisor-pipeline.md) | Subagente que audita se as ações seguem as regras invioláveis (só leitura) |
| [`.claude/agents/verificador-fluxo.md`](.claude/agents/verificador-fluxo.md) | Subagente que confirma tecnicamente que a atualização foi gravada (só leitura) |

## Status

- **v1.0** — concluída e validada em 3 rodadas de teste contra o Kommo real
  (identificação de lead, extração/preenchimento de campos, avanço de etapa com
  confiança, transição para "No Show").

### Próximas versões (planejadas)

- **v1.1** — organização de cadências (tags D1–D6, prazos, avisos) e enriquecimento de leads.
- **v1.2** — visibilidade do pipeline: leitura legível do que v1 e v1.1 produziram.
- **Futuro** — suporte a HubSpot e Go High Level; detecção de deal parado.

## Escopo da v1

Só Kommo. Só áudio/narração (sem imagens ou prints). Acionado sob demanda por você
(não faz varredura automática de WhatsApp nem roda sozinho 24/7). Não envia mensagens
nem age fora do CRM.
