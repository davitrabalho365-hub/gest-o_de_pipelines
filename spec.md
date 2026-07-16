# Spec: Agente Coringa de Preenchimento de CRM

## 1. Visão geral

O sistema é um fluxo, acessado via extensão Chrome, que você dispara gravando um áudio descrevendo uma interação com um lead (ex: "Rudi Car, respondeu que tá sem tempo, marcou sexta"). Ele extrai as informações do áudio e preenche automaticamente os campos certos no card do CRM que está aberto.

Ele **não** faz varredura automática de WhatsApp, não processa 24/7 sozinho, e não lida com imagens/prints. É acionado por você, sob demanda.

## 2. Usuário principal

Você, um SDR/BDR em qualquer operação (não é exclusivo de uma empresa ou projeto). Seu fluxo no sistema: você fala com um lead no WhatsApp, volta pro CRM (já com o card do lead aberto), grava um áudio descrevendo o que rolou, e o sistema preenche automaticamente os campos certos no card, além de decidir e mover o lead para a etapa correta do funil quando tiver confiança pra isso. Você nunca entra no WhatsApp dentro do fluxo, o CRM já tá aberto.

## 3. Fora de escopo

- Varredura automática do WhatsApp (só processa quando você grava áudio)
- Enriquecimento de dados de leads (fica pra v1.1)
- Funcionamento em múltiplos CRMs (só Kommo na v1; HubSpot e Go High Level ficam pra depois)
- Rastreamento de prazos de cadência / avisos de vencimento (fica pra v1.1, junto da organização de cadências)
- Interface própria / dashboard (roda só como extensão sobre o Kommo)
- Processamento de imagens ou prints (só áudio)
- Envio de mensagens, ligações ou qualquer ação fora do CRM

## 4. Critério de sucesso

1. Ao gravar um áudio com o card de um lead aberto no Kommo, o sistema identifica corretamente qual lead é (o que já está aberto na tela).
2. O sistema extrai do áudio as informações relevantes (o que aconteceu na conversa, próximo passo, data combinada).
3. O sistema preenche os campos certos do card no Kommo com essas informações (não deixa em branco, não escreve no campo errado).
4. O sistema lê as observações já existentes no card como contexto antes de decidir.
5. Com base no áudio + observações do card, o sistema decide para qual etapa do funil o lead deve ir, e move o card pra lá.
6. Se o áudio não tiver informação suficiente pra decidir a etapa com confiança, o sistema não move o card sozinho.

## 5. Regras invioláveis

1. Nunca inventa dado. Se o áudio não menciona algo, o sistema não completa por conta própria.
2. Nunca move o card de etapa se não tiver confiança na decisão. Nesse caso, registra a observação, deixa o card onde está, e sinaliza para revisão.
3. Nunca apaga observações anteriores do card. Só adiciona.
4. Nunca dispara ação fora do Kommo (não manda WhatsApp, não liga, não manda email). Só mexe no CRM.
5. Sempre deixa claro no card qual foi a origem da mudança (ex: nota tipo "atualizado via áudio em 15/07"), para permitir auditoria.

## 6. Changelog

### v1.0 (concluída em 15/07)
- Preenchimento de card via áudio, com decisão de etapa baseada em contexto das observações. Kommo apenas.
- Fluxo de disparo: manual via Claude Code + Claude in Chrome, formalizado no comando `/atualizar-lead`.
- Validada em 3 rodadas de teste contra o Kommo real (ver TESTES.md): identificação de lead, extração e preenchimento de campos, avanço de etapa com confiança, e transição para "No Show".

### v1.1 (em andamento)
- Organização de cadências — concluída (ver `spec-cadencias.md`): tags D1–D6, relatório
  sob demanda via `/cadencias`, modo loop e formato de saída (v1.1.1).
- Enriquecimento de leads — redefinido em 16/07 (ver `spec-enriquecimento.md`):
  padronização interna nota → campo, sob demanda via `/enriquecer`, sem busca externa.
  O horário fixo ("1x por semana, segunda às 14:30h") foi descartado como automação.

### v1.2 (planejado)
- Visibilidade do pipeline: leitura do que v1 e v1.1 produziram, apresentada de forma legível.

### Futuro
- Suporte a HubSpot e Go High Level.
- Detecção de deal parado com sinalização de causa (tarefa não preenchida vs. travado de fato).
