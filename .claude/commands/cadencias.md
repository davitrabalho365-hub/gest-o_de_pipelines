---
description: Relatório da cadência D1-D6 no Kommo — quem está vencido, vencendo hoje e por quê
---

Leia `AGENTE-cadencias.md` e `spec-cadencias.md` antes de executar. As regras invioláveis (as 5 da v1 no `AGENTE.md` + as 3 da v1.1) têm prioridade sobre qualquer atalho.

Filtros pedidos pelo usuário (responsável, funil — vazio = tudo que tem tag D): $ARGUMENTS

Execute os passos de relatório do AGENTE-cadencias.md:
1. Use o Claude in Chrome para varrer os cards com tag D1–D6 no Kommo, aplicando os filtros pedidos. Em varreduras com muitos leads, use o subagente `revisor-pipeline` para a inspeção card a card, trazendo só o consolidado de volta.
2. Calcule o status de cada lead: D atual pela tag, dia previsto = entrada na cadência + (n − 1) dias corridos, toque cumprido por nota do dia (fonte primária) ou tarefa concluída (sinal secundário). Sem evidência no card, o toque não conta.
3. Monte o relatório só com **vencidos + vencendo hoje** (em dia fica de fora): vencidos primeiro, do atraso mais antigo para o mais recente; cada linha com lead, D atual, dias de atraso e último toque registrado.
4. O relatório é leitura — **não altere nenhum card durante o relatório**. Se encontrar algo que peça ação (fim de cadência, desinteresse, ambiguidade), aponte no relatório e aguarde o usuário decidir.
5. **Modo loop:** se este comando está rodando via `/loop` e já houve um relatório nesta sessão, lidere com o delta (quem saiu da fila, quem venceu desde o último ciclo) e resuma a fila completa depois — ver a seção "Modo loop" do AGENTE-cadencias.md.

Este comando é usado tanto para leads reais quanto para os fixtures de teste do funil "Inbound (Tráfego)" — trate ambos da mesma forma.
