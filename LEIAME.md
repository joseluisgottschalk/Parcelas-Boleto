# Parcelas Boleto – portador e relatório PDF

## Arquivos

| Arquivo | Onde vai |
|---|---|
| `github/index.html` | Repositório **Parcelas-Boleto** no GitHub (substitui o atual) |
| `n8n/1_API_Parcelas_Boleto.json` | n8n – substitui o workflow "API - Parcelas Boleto" (agora traz o `Cd_lancamento`) |
| `n8n/2_API_Portador_Parcelas_Boleto.json` | n8n – workflow novo: lista de portadores + gravação do `Cd_portador` |

## 1. n8n

1. Abra o workflow **API - Parcelas Boleto** e **desative/apague** (o novo usa o mesmo endereço `/webhook/parcelas-boleto`).
2. Em *Workflows → Import from File*, importe `1_API_Parcelas_Boleto.json` e **ative**.
3. Importe `2_API_Portador_Parcelas_Boleto.json` e **ative**.
4. Se a credencial do SQL aparecer com aviso, selecione **Microsoft SQL account** nos nós de SQL.

Endereços criados:

- `GET  http://localhost:5678/webhook/parcelas-boleto` – parcelas com portador "bol" (com `Cd_lancamento`)
- `GET  http://localhost:5678/webhook/portadores` – portadores ativos da GFPORTAD
- `POST http://localhost:5678/webhook/parcelas-boleto-portador` – grava o portador

Corpo do POST:

```json
{ "itens": [ { "cd_lancamento": "4262336", "portador": "B00" } ] }
```

Regras da gravação:

- Identifica cada parcela pelo `Cd_lancamento` (chave única da GFLANCAM).
- Só aceita portador de 3 letras/números **cadastrado na GFPORTAD**. Se algum não existir, nada é gravado.
- Só altera parcelas que ainda estão com portador **"bol"**; as outras voltam como "ignoradas".
- Altera **apenas** o campo `Cd_portador`, dentro de uma transação.
- Os webhooks só aceitam chamadas de `https://joseluisgottschalk.github.io`.

## 2. GitHub

No repositório **Parcelas-Boleto**: *Add file → Upload files*, envie `index.html` (substitui o atual) e faça o commit.
O GitHub Pages atualiza em cerca de 1 minuto.

## Como usar no dashboard

1. Digite o portador (3 caracteres) na coluna **Portador da nota** para aplicar a todas as parcelas da nota, ou no campo de cada parcela.
   A lista de portadores aparece como sugestão; código não cadastrado fica em vermelho.
2. O quadro **Portadores atribuídos** mostra notas, parcelas e valor por portador, com o botão **Gerar PDF**.
3. **Gravar portadores no sistema** pede confirmação, grava no banco, baixa o PDF de cada portador gravado e recarrega a lista.

As atribuições ficam salvas no navegador até serem gravadas ou limpas.
