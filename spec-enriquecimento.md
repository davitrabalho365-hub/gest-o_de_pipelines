# Spec: Enriquecimento de Leads (v1.1)

> Segunda feature da v1.1, ao lado das cadências. Depende do que a v1 estabeleceu
> (ver `spec.md` e `AGENTE.md`). As regras invioláveis da v1 continuam valendo aqui.

## 1. Visão geral

Padronização interna dos cards: o agente lê o que **já está registrado** no card
(notas datadas, histórico, campos soltos) e preenche os **campos estruturados vazios**
com essa informação. Nada de busca externa — o discovery de dados novos (site,
Instagram, telefone da empresa) é atividade humana da primeira etapa da prospecção
outbound, e ferramentas dedicadas já existem para isso. Esta feature só completa a
estruturação: informação que entrou por narração e ficou "presa" no texto da nota
passa a existir no campo certo, onde o Kommo consegue filtrar e lembrar.

Sob demanda, como tudo no projeto: não roda sozinho, não tem horário.

## 2. Usuário e fluxo

Mesmo SDR/BDR da v1. Ao longo da semana, as narrações via `/atualizar-lead` vão
acumulando informação nas notas. Quando quiser (ex: hábito de segunda à tarde), roda
`/enriquecer` e recebe: o que foi preenchido (lead, campo, fonte interna) e o que
continua vazio por falta de fonte.

## 3. Modelo

- **Fonte de verdade: o próprio card.** Notas datadas, histórico de eventos, campos já
  preenchidos e tags. Nada além disso.
- **Alvo: campos estruturados vazios** (ex: Dor, Situação relatada, Instagram, Site,
  Telefone, E-mail, Já investiu?, Faturamento, Próximo agendamento).
- **Movimento: nota → campo.** O dado precisa existir literalmente no card; o agente
  só o move para o lugar estruturado. Sem fonte interna → campo continua vazio.
- **Rastreabilidade:** cada preenchimento gera nota de origem citando a fonte interna
  (ex: "Dor preenchida a partir da nota de 14/07 — via /enriquecer em 16/07").

## 4. Escopo (v1.1 — enriquecimento)

- Varrer **leads ativos com campos estruturados vazios** (filtro opcional por funil e
  responsável, como no `/cadencias`).
- Extrair das notas e do histórico do card informações que correspondem a campos
  estruturados do Kommo.
- Preencher **somente campos vazios** — nunca sobrescrever.
- Relatório final: preenchidos (lead, campo, fonte) e lacunas sem fonte interna.

## 5. Fora de escopo

- **Busca externa** (Google, Instagram, site da empresa, APIs de dados) — já existem
  ferramentas para isso; o discovery faz parte da primeira atividade da prospecção
  outbound do funil, feita por gente.
- Sobrescrever ou "corrigir" campos já preenchidos, mesmo que pareçam divergentes.
- Rodar sozinho ou agendado — o "1x por semana, segunda 14:30" da spec da v1 vira
  hábito do usuário, não automação.
- Criar leads, contatos ou empresas novas.
- Alterar etapa, tag de cadência ou qualquer coisa além dos campos vazios + nota.

## 6. Critérios de sucesso

1. Identifica corretamente os leads ativos com campos estruturados vazios, respeitando
   os filtros pedidos.
2. Só preenche campo vazio — nunca sobrescreve valor existente.
3. Todo dado preenchido tem fonte interna rastreável (nota/histórico do próprio card),
   citada na nota de origem.
4. Não inventa: sem fonte interna, o campo fica vazio e entra no relatório como lacuna.
5. Relatório claro no formato do projeto: o que foi preenchido, o que ficou sem fonte.

## 7. Regras invioláveis

Herda as 5 da v1 (ver `spec.md`); as 6–8 são das cadências (`spec-cadencias.md`).
Acrescenta:

9. A fonte é o próprio card — não busca dado fora do CRM.
10. Não sobrescreve campo preenchido, em nenhuma hipótese.
11. Sempre registra em nota qual fonte interna originou cada preenchimento.

## 8. Decisões tomadas (16/07)

1. **Sem busca externa:** contraproducente construir isso — já existem ferramentas
   dedicadas, e o discovery é parte da primeira atividade da prospecção outbound.
   A feature só completa a estruturação interna.
2. **Gatilho sob demanda** via `/enriquecer`, mesmo padrão do `/cadencias`. O horário
   fixo da spec v1 (segunda 14:30) foi descartado como automação.
3. **Universo:** leads ativos com campos vazios (com filtros opcionais).
4. **Escrita:** só preenche vazio, com nota de origem por preenchimento.

## 9. Changelog

### v1.1 — enriquecimento (especificada em 16/07, aguardando manual, testes e comando)
- Padronização interna nota → campo, sob demanda, sem busca externa.
