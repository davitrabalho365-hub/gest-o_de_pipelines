---
description: Preenche o card do lead aberto no Kommo a partir de uma narração de interação
---

Leia `AGENTE.md` e `spec.md` antes de executar. As regras invioláveis têm prioridade sobre qualquer atalho.

Narração do usuário: $ARGUMENTS

Execute os passos do AGENTE.md:
1. Use o Claude in Chrome para ler o estado atual do card já aberto no Kommo (nome do lead, etapa atual, observações existentes).
2. Extraia da narração: o que aconteceu, próximo passo (se houver), data combinada (se houver). Não invente nada que não foi dito.
3. Preencha os campos certos do card com o que foi extraído.
4. Decida a etapa cruzando a narração com as observações existentes:
   - Confiança clara → mover o card para a etapa correspondente.
   - Sem confiança suficiente → não mover; registrar a informação e sinalizar revisão manual.
5. Registre a origem da mudança no card (nota com data).
6. Reporte ao usuário: campos preenchidos, decisão de etapa tomada (ou não, e por quê).

Este card é usado tanto para leads reais quanto para casos de teste do funil "Inbound (Tráfego)" — trate ambos da mesma forma, sem pedir confirmação extra a não ser que a ação pareça ambígua ou arriscada.
