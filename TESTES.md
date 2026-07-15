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
**Status:** ☐

### Teste 2 — Extração de informação da narração
**Dado:** narração descrevendo o que aconteceu, incluindo próximo passo e/ou
data.
**Espero:** extrai corretamente o que aconteceu, o próximo passo e a data,
quando mencionados.
**Não aceito:** ignorar informação dita, ou misturar com outro lead.
**Status:** ☐

### Teste 3 — Preenchimento dos campos certos
**Dado:** informações já extraídas.
**Espero:** cada informação vai para o campo correto do card.
**Não aceito:** preencher campo errado, ou deixar em branco algo que foi
dito.
**Status:** ☐

### Teste 4 — Leitura de observações como contexto
**Dado:** card com observações anteriores já registradas.
**Espero:** lê essas observações antes de decidir qualquer coisa.
**Não aceito:** decidir usando só a narração nova, ignorando o histórico.
**Status:** ☐

### Teste 5 — Decisão de etapa com confiança
**Dado:** narração + observações indicando claramente que o lead deve
avançar.
**Espero:** move o card para a etapa correta.
**Não aceito:** mover para etapa errada, ou não mover quando era claro.
**Status:** ☐

### Teste 6 — Sem confiança, não decide
**Dado:** narração + observações que não dão informação suficiente.
**Espero:** registra a informação, não move de etapa, sinaliza revisão.
**Não aceito:** chutar uma etapa mesmo sem confiança.
**Status:** ☐

---

## Notas de execução

(preencher aqui, a cada rodada, o que foi observado — inclusive falhas e
ajustes feitos na AGENTE.md como resultado)
