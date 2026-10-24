---
name: orquestrar
description: Divide uma tarefa grande em partes e delega para workers Haiku (agente `orquestrar:worker`), verificando cada entrega antes de aceitar. Use quando o usuário pedir "orquestra isso", "delega pros workers", "usa os workers haiku" ou numa tarefa grande e mecânica que ele queira fazer gastando menos tokens.
---

Você é o orquestrador: planeja, delega e verifica. O trabalho pesado (ler muitos arquivos,
rodar comandos, editar) fica com os workers, para que esse volume não entre no seu contexto.

## Painel de workers (agents-progress)
O progresso aparece na barra e no painel do plugin `agents-progress`. O plugin já pede a cada
worker que reporte os próprios passos; você reporta o plano e o aceite com
`mcp__agents-progress__progress`.
- Se a tool aparece só pelo nome (deferred), carregue-a com ToolSearch
  (`select:mcp__agents-progress__progress`).
- Se ela não existir (plugin não instalado), siga sem painel e avise o usuário uma vez.
- Campos omitidos mantêm o valor anterior.

## 1. Planejar
- Quebre a tarefa em unidades independentes, cada uma com um critério de pronto verificável.
- Se a tarefa depender muito de julgamento ou for pequena, não delegue: faça você mesmo e
  diga o porquê.
- Mostre o plano ao usuário em poucas linhas (unidades e como cada uma será verificada) e siga.
- Antes da primeira chamada ao Agent, reporte o plano: `title` (poucas palavras),
  `phase: "delegate"` e `tasks` em ordem, uma por unidade, com:
  - `title` idêntico, palavra por palavra, à `description` que você vai passar ao Agent
    (é assim que o painel liga a execução à tarefa);
  - `tier: "light"` (Haiku);
  - `after` com os números (a partir de 1) das unidades pelas quais ela espera.

## 2. Delegar
- Para cada unidade, chame o agente `orquestrar:worker` com um prompt autossuficiente: objetivo,
  arquivos envolvidos, restrições, o que não mexer e o comando de verificação.
- Rode as unidades independentes em paralelo (várias chamadas na mesma resposta).
- Não mande unidades que mexem nos mesmos arquivos ao mesmo tempo.

## 3. Verificar (barato)
- Ao começar a conferir as entregas, reporte `phase: "review"`.
- Confira o RELATORIO de cada worker.
- Valide por sinais baratos: rode os testes/lint/build, `git diff --stat`, grep do que
  deveria ter mudado. Leia só os trechos que levantarem suspeita, nunca o diff inteiro por padrão.
- Se o worker respondeu `DUVIDA:`, responda você mesmo se souber; senão, pergunte ao usuário.
- A cada entrega aceita, reporte `done` com o total aceito até agora.

## 4. Ajustar
- Se algo faltar ou estiver errado, use SendMessage para o MESMO worker com o ajuste
  específico (o que está errado e como verificar). Não crie um worker novo para corrigir.
- Depois de 2 tentativas falhas numa unidade, faça você mesmo.
- Se o plano mudar (unidade nova, dividida ou removida), reporte as novas `tasks`.

## 5. Fechar
Reporte `phase: "close"` e `finished: true`. Depois dê ao usuário um resumo curto:
- o que foi feito, por unidade;
- o que foi verificado e como (comandos e resultados);
- o que você teve que refazer ou fazer sozinho;
- o que ficou pendente.
