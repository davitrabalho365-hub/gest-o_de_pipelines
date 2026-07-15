# Como construímos a v1 — e por que fizemos assim

Este documento conta a jornada da v1 deste projeto: **o que foi feito e por quê.** A ideia
é servir de playbook — você pode copiar essa estrutura para o seu próprio projeto no Claude
Code e seguir o mesmo caminho.

> TL;DR do método: **especificar antes de codar → git como rede de segurança → testar de
> verdade contra a ferramenta real → formalizar num comando → montar subagentes de QA.**
> Cada passo reduz o próximo em risco e em tokens.

---

## O que a v1 faz

Um agente que preenche o card de um lead no CRM (Kommo) a partir de uma narração falada.
Você conversa com o lead, volta pro CRM com o card aberto, narra o que rolou, e o agente
extrai as informações, preenche os campos certos e decide para qual etapa do funil o lead
vai — movendo só quando tem confiança, senão sinalizando revisão.

Nada disso é "código" no sentido tradicional. É um agente guiado por **documentos de
instrução versionados**.

---

## A jornada, passo a passo

### 1. Escrevemos a spec antes de qualquer coisa
Antes de pedir pro Claude "construir", escrevemos [`spec.md`](spec.md): visão, usuário,
o que está **fora** de escopo, critérios de sucesso e regras invioláveis. Definir o *fora
de escopo* pesou tanto quanto o escopo — é o que evita o agente "ajudar demais".

### 2. Transformamos a spec em instruções de comportamento
Criamos [`AGENTE.md`](AGENTE.md): o passo a passo que o agente segue e as regras que ele
nunca quebra (nunca inventa dado, nunca move etapa sem confiança, nunca apaga nota, nunca
age fora do CRM, sempre registra a origem da mudança). A spec explica o problema; o
AGENTE.md é o manual operacional — quando algo dá errado, você sabe qual dos dois ajustar.

### 3. Montamos a rede de segurança com git desde o primeiro dia
Inicializamos o repositório e passamos a commitar cada mudança significativa, com mensagem
explicando o porquê. Cada passo vira um ponto de restauração, e o histórico de commits
*é* a documentação da evolução.

### 4. Escrevemos os testes como roteiros reais — e rodamos de verdade
[`TESTES.md`](TESTES.md) não são asserts de código. São cenários (Dado / Espero / Não
aceito) que a gente **executou contra o Kommo real**, com um card de teste, narrando as
interações e conferindo o resultado à mão.

Foram 3 rodadas: um lead ainda indeciso (não mover etapa), um lead que confirmou reunião
(avançar etapa), e um no-show (recuar/registrar). Cada rodada foi anotada no arquivo.
Testar contra a ferramenta real pega o que teste de mentira não pega — foi assim que
confirmamos que as regras invioláveis se sustentavam na prática, não só no papel.

### 5. Formalizamos o fluxo num slash command
Depois que a lógica estava validada, empacotamos ela no comando
[`/atualizar-lead`](.claude/commands/atualizar-lead.md). Agora o uso diário é: abrir o card,
rodar `/atualizar-lead <narração>`, pronto. O comando carrega sozinho as regras do
AGENTE.md — sem repetir contexto a cada uso.

### 6. Criamos subagentes de projeto para controle de qualidade
Dois subagentes escopados só a esta pasta (`.claude/agents/`):
[`revisor-pipeline`](.claude/agents/revisor-pipeline.md) audita se as ações seguem as
regras, e [`verificador-fluxo`](.claude/agents/verificador-fluxo.md) confirma tecnicamente
que a gravação aconteceu. O trabalho pesado de inspecionar o card roda no contexto
isolado do subagente; só a conclusão volta pra conversa principal.

E funcionou de imediato: na primeira auditoria, os subagentes acharam uma lacuna que as 3
rodadas manuais tinham deixado passar (uma data indo pro texto da nota em vez do campo
estruturado). A correção virou um commit. Esse é o loop de qualidade fechando sozinho.

---

## A ordem

Cada passo torna o seguinte mais barato e mais seguro:

- A **spec** faz os testes saberem o que verificar.
- Os **testes reais** dão confiança pra formalizar no comando.
- O **comando** dá uma superfície estável pros subagentes auditarem.
- Os **subagentes** protegem tudo isso conforme o projeto cresce.

Pular etapa não economiza — só empurra o custo pra frente, quando é mais caro de consertar.

---

## Como copiar essa estrutura pro seu projeto

1. Crie uma pasta e escreva sua `spec.md` (visão, fora de escopo, critérios, regras invioláveis).
2. Escreva o `AGENTE.md` com o passo a passo e as regras que o agente nunca quebra.
3. `git init` e comece a commitar cada mudança com o porquê na mensagem.
4. Escreva `TESTES.md` como roteiros Dado/Espero/Não aceito e **rode contra a ferramenta real**.
5. Quando a lógica estiver validada, empacote num slash command em `.claude/commands/`.
6. Crie subagentes de QA em `.claude/agents/` para auditar e verificar sem inchar seu contexto.

O segredo não é nenhum passo isolado — é a **disciplina da ordem**. Especifica, protege,
testa de verdade, formaliza, audita. Nessa sequência.
