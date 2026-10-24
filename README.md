# orquestrar

Plugin do Claude Code com a skill `/orquestrar`: divide uma tarefa grande em unidades, delega
cada uma a um worker Haiku (agente `orquestrar:worker`) e verifica cada entrega antes de aceitar.
O progresso aparece no painel do plugin [agents-progress](https://github.com/fabiopbarbieri/agents-progress).

## Instalar

Num shell:

```bash
# painel de workers (dependência)
claude plugin marketplace add fabiopbarbieri/agents-progress
claude plugin install agents-progress@agents-progress

# esta skill
claude plugin marketplace add leosilva-dev/orquestrar-skill
claude plugin install orquestrar@orquestrar-skill
```

Ou, dentro de uma sessão no terminal:

```
/plugin install agents-progress --marketplace fabiopbarbieri/agents-progress
/plugin install orquestrar --marketplace leosilva-dev/orquestrar-skill
```

Reinicie a sessão (ou rode `/reload-plugins`). Sem o agents-progress a skill funciona, só que sem painel.

## Usar

`/orquestrar:orquestrar <tarefa>`, ou peça "orquestra isso" / "delega pros workers".

- Painel: `/agents-info` abre e fecha.
- Opções do painel (idioma, `stepBrief`) ficam em `/config`.

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
