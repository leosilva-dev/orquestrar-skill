# orquestrar

Plugin do Claude Code com a skill `/orquestrar`: divide uma tarefa grande em unidades, delega
cada uma a um worker Haiku ou Sonnet (agente `orquestrar:worker`) e verifica cada entrega antes de aceitar.

## Instalar

```bash
claude plugin marketplace add leosilva-dev/orquestrar-skill
claude plugin install orquestrar@orquestrar-skill
```

Reinicie a sessão (ou rode `/reload-plugins`).

## Usar

`/orquestrar:orquestrar <tarefa>`, ou peça "orquestra isso" / "delega pros workers".
Adicione `--plan` para revisar o plano antes de qualquer worker rodar.

## Atualizar

```bash
claude plugin marketplace update orquestrar-skill
claude plugin update orquestrar@orquestrar-skill
```

## Desenvolver

```bash
claude plugin marketplace add ~/dev/orquestrar-skill   # usa o clone local
claude plugin validate . --strict
```
