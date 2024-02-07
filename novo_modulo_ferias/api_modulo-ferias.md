# API do Novo módulo de férias

## findVacationInfo

Traz as informações iniciais para alimentar tanto o calendário, quanto o painel esquerdo e inferior

```
POST /app/vacation_module/findVacationInfo
```

```json
{
  "user_id": 35000,
  "vacation_cycle_id": 15088
}
```

Retorno:

```json
{
  "vacation_cycle": {
    "id": 15088,
    "start_date": "2024-01-29",
    "end_date": "2025-01-28",
    "available_days": null,
    "employee": {
      "id": 35000,
      "name": "THIAGO PEREIRA DA SILVA",
      "id_name": "35000 - THIAGO PEREIRA DA SILVA",
      "role_string": "Gestor"
    },
    "rules": {
      "id": 4,
      "faults_lost_days": 0,
      "expiration_months": null,
      "allow_sell_days": 1,
      "sell_days_limit": 15,
      "allow_request_13": 1,
      "range_13": [
        "1",
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "11",
        "12"
      ],
      "limit_cycles": 3
    }
  },
  "subcycles": [],
  "holidays": [],
  "days_to_take": 30,
  "base_days_to_take": 30,
  "taken_days": 0,
  "programmed_days": 0,
  "sold_days": 0,
  "faults": 0,
  "lost_days": 0,
  "disregarded_days": 0,
  "expiration_date": null,
  "min_date": "2025-01-29",
  "max_date": "2025-12-29",
  "partition_rules": []
}
```

### Barra de Ações

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/1ba481f7-2c83-4758-804d-1f3509abed7b)

O botão de venda de férias só é mostrado quando `vacation_cycle.rules.allow_sell_days = 1`. O número que aparece no botão vem de `vacation_cycle.rules.sell_days_limit`

### Painel esquerdo

#### Dias a retirar

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/68ae02bc-f978-4b8c-9850-fc4dd779646a)

Essa informação vem de `days_to_take`

#### Tooltip

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/c1b4d544-887b-4f34-ad66-c25c2c71ac6d)

| Descrição           | Campo                            | Tipo     |
| ------------------- | -------------------------------- | -------- |
| Valor Base          | `base_days_to_take`              | Base     |
| Desconto por falta  | `lost_days`                      | Negativo |
| Abonado pelo Gestor | `disregarded_days`               | Positivo |
| Dias selecionados   | `taken_days` + `programmed_days` | Negativo |
| Dias vendidos       | `sold_days`                      | Negativo |

> [!TIP]
> Se algum valor for zero, não precisa aparecer. Se só houver o valor base e todos os outros forem zerados, não precisa mostrar o tooltip

#### Faltas no período

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/e489f368-026d-4022-8d07-d898049f1b88)

O seu valor vem de `faults`

#### Data de expiração

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/0d4c1d38-44bc-497b-bd25-c6065b45ca44)

Esse painel aparece no lugar do painel de **Faltas no período** quando `expiration_date != null`, seu valor vem do próprio `expiration_date`

#### Dias programados e retirados

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/d08b5d7d-5579-4f10-96ad-66157fadd2e0)

Seus valores vêm de `programmed_days` e `taken_days`, respectivamente

> [!IMPORTANT]
> Os dias são programados quando eles estão sendo lançados **DEPOIS** da data atual, caso contrário, são dias já retirados

## Calendário

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/e77a6a0d-7ba3-4634-a9a8-776463fd67a8)

A data inicial que aparece no calendário é o valor de `min_date` com o máximo definido por `max_date`. Esses valores podem ser nulos

### Cores

| Cor                                                          | Descrição                               |
| ------------------------------------------------------------ | --------------------------------------- |
| ![#4299e1](https://via.placeholder.com/15/4299e1/4299e1.png) | Período sendo selecionado para marcação |
| ![#f56565](https://via.placeholder.com/15/f56565/f56565.png) | Data ou período inválido                |
| ![#ecc94b](https://via.placeholder.com/15/ecc94b/ecc94b.png) | Período marcado aguardando aprovação    |
| ![#48bb78](https://via.placeholder.com/15/48bb78/48bb78.png) | Período marcado aprovado                |
| ![#a0aec0](https://via.placeholder.com/15/a0aec0/a0aec0.png) | Período marcado de outro ciclo          |
| ![#38b2ac](https://via.placeholder.com/15/38b2ac/38b2ac.png) | Período sendo atualmente editado        |

### Data atual

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/0ca38d6a-3f7a-42e4-a920-d226bf4083ba)

Vem do sistema

### Feriado

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/f38621aa-921b-4342-9ca0-4f7663a96907)

Vem de `holidays`

### Subcycles

O array `subcycles` alimenta tanto o calendário quanto a tabela abaixo do calendário e podem ocorrer os seguintes casos:

```json
{
  "id": 4701,
  "start_date": "2025-02-10",
  "end_date": "2025-02-14",
  "requested_13": 0,
  "days": 5,
  "type": "Férias",
  "status": "approved",
  "status_string": "Aprovado",
  "approvers": [
    {
      "date": "2024-02-07 13:02:15",
      "approver": {
        "name": "3 - DDIT - THIAGO CEZAR",
        "role": "Gestor"
      },
      "status": "approved",
      "obs": null
    },
    {
      "date": "2024-02-07 11:10:15",
      "approver": {
        "name": "212 - HENRIQUE ALVES SIMEÃO",
        "role": "Gestor RH"
      },
      "status": "approved",
      "obs": null
    }
  ],
  "calendar": {
    "tooltip": "Período de férias aprovado",
    "color": "green"
  },
  "can": {
    "approve": false,
    "deny": false,
    "edit": false,
    "delete": true
  },
  "in_cycle": true
}
```

A informação que será mostrada no calendário utiliza o `start_date` e o `end_date`. A cor e os dados que vão aparecer no tooltip vem de `calendar`

O registro só aparece na tabela quando `in_cycle=true`. O registro aparece no calendário em qualquer um dos casos, mas a cor muda seguindo o código de cores do calendário (veja [Cores](#cores))

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/85daa906-7591-4d42-8f85-18b7f675f69d)

| Coluna | Descrição                                                                         |
| ------ | --------------------------------------------------------------------------------- |
| 1      | Um número sequencial, não representa o ID do registro                             |
| 2      | Período selecionado, vem de `start_date` e `end_date`                             |
| 3      | Dias selecionados, vem de `days`                                                  |
| 4      | Tipo de lançamento, vem de `type`                                                 |
| 5      | Status, vem de `status_string`                                                    |
| 6      | Ações que podem ser realizadas, a visibilidade dos botões é controlada pelo `can` |

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/1be215fd-1340-4f2c-8ea9-98579322a39f)

Quando `requested_13=1`, esse tooltip é mostrado

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/c6ed2d48-3b68-49d3-80c9-ab9f00376501)

Quando o período de férias foi aprovado, o array `approvers` alimenta esse tooltip

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/bc3bdd57-a614-4510-80d6-eab6a55554ba)

Quando o período for negado, a justificativa vem de `approvers.obs`

```json
{
  "id": 4704,
  "selling": true,
  "days": 15,
  "type": "Venda de dias",
  "can": {
    "delete": true
  },
  "in_cycle": true
}
```

Quando `selling=true`, o registro não aparece no calendário, somente na tabela, contanto que `in_cycle=true`

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/92e0fc3f-78c6-412b-85b4-53da8bcc1747)

## Solicitar adiantamento do 13º

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/9d78ba5a-ed4d-4c1b-883e-d470a17038df)

Quando `vacation_cycle.rules.allow_request_13=1` e o mês selecionado estiver dentro de `vacation_cycle.rules.range_13`, esse botão é mostrado

## Regras de particionamento

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/06878bec-e9cf-4e9b-9237-cd08ed8e57cc)

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/c442463b-6ce6-4ce0-ba6a-901733b36ad4)

A tabela é alimentada por `partition_rules`