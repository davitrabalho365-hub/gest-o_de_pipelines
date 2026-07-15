# Cenários de teste — v1

Formato: Dado / Espero / Não aceito. Não são asserts de código — são roteiros
que você roda de verdade (narrando pro Claude Code, com um card de teste
aberto no Kommo) e confere manualmente o resultado antes de considerar a v1
pronta.

Marque cada um como ✅ (passou), ⚠️ (passou parcialmente, anotar o porquê) ou
❌ (falhou) depois de rodar.

---

### Teste 1 — Identificação do lead
**Dado:** narração feita com o card de um lead aberto no Kommo.
**Espero:** a atualização é aplicada nesse card específico.
**Não aceito:** perguntar qual lead é, ou aplicar em outro card.
**Status:** ✅

### Teste 2 — Extração de informação da narração
**Dado:** narração descrevendo o que aconteceu, incluindo próximo passo e/ou
data.
**Espero:** extrai corretamente o que aconteceu, o próximo passo e a data,
quando mencionados.
**Não aceito:** ignorar informação dita, ou misturar com outro lead.
**Status:** ✅

### Teste 3 — Preenchimento dos campos certos
**Dado:** informações já extraídas.
**Espero:** cada informação vai para o campo correto do card.
**Não aceito:** preencher campo errado, ou deixar em branco algo que foi
dito.
**Status:** ✅

### Teste 4 — Leitura de observações como contexto
**Dado:** card com observações anteriores já registradas.
**Espero:** lê essas observações antes de decidir qualquer coisa.
**Não aceito:** decidir usando só a narração nova, ignorando o histórico.
**Status:** ✅

### Teste 5 — Decisão de etapa com confiança
**Dado:** narração + observações indicando claramente que o lead deve
avançar.
**Espero:** move o card para a etapa correta.
**Não aceito:** mover para etapa errada, ou não mover quando era claro.
**Status:** ✅ (validado na rodada 2 — ver notas de execução)

### Teste 6 — Sem confiança, não decide
**Dado:** narração + observações que não dão informação suficiente.
**Espero:** registra a informação, não move de etapa, sinaliza revisão.
**Não aceito:** chutar uma etapa mesmo sem confiança.
**Status:** ✅

---

## Notas de execução

(preencher aqui, a cada rodada, o que foi observado — inclusive falhas e
ajustes feitos na AGENTE.md como resultado)

### Rodada 1 — 15/07 — card "Teste Luiz 1245" (#12813597)
Todos os 6 testes passaram no primeiro round contra o Kommo real. Narração:
ligação com o lead, cotando preços, comparando concorrentes, sem anúncio
único identificado, pedindo gestão completa (tráfego pago + automações +
atendimento comercial). Sem data/próximo passo mencionado — sistema não
moveu a etapa e registrou nota com origem e timestamp, sem apagar histórico
anterior. Nenhum ajuste necessário na AGENTE.md até aqui.

### Rodada 2 — 15/07 — mesmo card, avanço de etapa
Narração: Luiz ligou de volta, confirmou que quer seguir com a proposta, e
combinou reunião para quinta-feira 17/07 às 15h. Sistema extraiu data e
próximo passo claramente, registrou nota com origem/timestamp mantendo o
histórico anterior, e moveu o card de "Tentativa de contato" para "Agendado"
com confiança justificada na própria nota. Teste 5 confirmado. Nenhum ajuste
necessário na AGENTE.md.

### Rodada 3 — 15/07 — mesmo card, caso de "No Show"
Narração: chegou o horário da reunião marcada (quinta 17/07, 15h) e o Luiz
não apareceu; tentativa de ligação e WhatsApp sem resposta. Sistema
registrou a nota com origem/timestamp, preservando histórico, e moveu o
card de "Agendado" para "No Show" com base no fato relatado (ausência na
reunião), sem inventar motivo. Interessante: ao entrar em "No Show", o
próprio Kommo disparou automaticamente uma tarefa de acompanhamento
("Ligar para remarcar + Mensagem WhatsApp") — confirma que a automação
nativa do funil reage corretamente à mudança de etapa feita pelo agente.
Nenhum ajuste necessário na AGENTE.md.
