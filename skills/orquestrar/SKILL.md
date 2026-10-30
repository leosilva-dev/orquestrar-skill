---
name: orquestrar
description: Divide uma tarefa grande em partes, delega cada uma a workers baratos (agente `orquestrar:worker`, Haiku ou Sonnet) e verifica cada entrega antes de aceitar. Use quando o usuário pedir "orquestra isso", "delega pros workers", "usa os workers" ou quando a tarefa for grande e repetitiva, por exemplo mexer em muitos arquivos, renomear em massa, migrar testes ou aplicar o mesmo padrão em vários lugares. Não use para tarefas pequenas ou que dependem de julgamento.
---

Você é o orquestrador: planeja, delega e verifica. O trabalho pesado (ler muitos arquivos,
rodar comandos, editar) fica com os workers, para que esse volume não entre no seu contexto.

## 1. Planejar
- Quebre a tarefa em unidades independentes, cada uma com um critério de pronto verificável.
- Se a tarefa depender muito de julgamento ou for pequena, não delegue: faça você mesmo e
  diga o porquê.
- Escolha o modelo de cada unidade:
  - `haiku` (padrão): trabalho mecânico, como editar seguindo um padrão, renomear, levantar dados
    ou rodar comandos;
  - `sonnet`: unidade que exige entender código, como refatorar uma lógica, corrigir um bug ou
    escrever testes não triviais.
- Mostre o plano em poucas linhas: as unidades, o modelo de cada uma e como cada uma será verificada.
- Se o pedido tiver `--plan`, pare aqui e espere o ok do usuário. Sem `--plan`, siga direto.

## 2. Delegar
- Para cada unidade, chame o agente `orquestrar:worker` passando `model` (`haiku` ou `sonnet`)
  e um prompt neste formato:

  ```
  OBJETIVO: <o que entregar, em uma frase>
  ARQUIVOS: <caminhos envolvidos>
  COMO: <passos ou padrão a seguir, com um exemplo se houver>
  NAO MEXER: <arquivos ou áreas proibidas>
  PRONTO QUANDO: <critério verificável>
  VERIFICACAO: <comando exato a rodar>
  ```

- O worker não vê esta conversa, então o prompt precisa se sustentar sozinho.
- Rode as unidades independentes em paralelo (várias chamadas na mesma resposta).
- Não mande unidades que mexem nos mesmos arquivos ao mesmo tempo.

## 3. Verificar (barato)
- Confira o RELATORIO de cada worker.
- Valide por sinais baratos: rode os testes/lint/build, `git diff --stat`, grep do que
  deveria ter mudado. Leia só os trechos que levantarem suspeita, nunca o diff inteiro por padrão.
- Se o worker respondeu `DUVIDA:`, responda você mesmo se souber; senão, pergunte ao usuário.

## 4. Ajustar
- Se algo faltar ou estiver errado, use SendMessage para o MESMO worker com o ajuste
  específico (o que está errado e como verificar). Não crie um worker novo para corrigir.
- SendMessage costuma ser deferred: se ele aparecer só pelo nome, carregue-o antes com
  ToolSearch (`select:SendMessage`).
- Depois de 2 tentativas falhas numa unidade, faça você mesmo.

## 5. Fechar
Dê ao usuário um resumo curto:
- o que foi feito, por unidade;
- o que foi verificado e como (comandos e resultados);
- o que ficou pendente;
- um placar: workers usados (quantos com `haiku` e quantos com `sonnet`), ajustes enviados e
  unidades que você fez sozinho.
