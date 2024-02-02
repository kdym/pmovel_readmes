# API do Novo módulo de férias

## findVacationInfo

Traz as informações iniciais para alimentar tanto o calendário, quanto o painel esquerdo e inferior

```
POST /app/vacation_module/findVacationInfo
```

```json
{
  "user_id": 35000, // ID do usuário atualmente logado
  "vacation_cycle_id": 15088 // ID do ciclo de férias
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
      "range_13": [""],
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

O botão de venda de férias só é mostrado quando `vacation_cycle.rules.allow_sell_days = 1`, o número que aparece no botão vem de `vacation_cycle.rules.sell_days_limit`

Existem outras regras que também ocultam esse botão:

- Já existe uma venda de férias para esse ciclo registrada
- A quantidade de dias que já foram retirados é maior ou igual ao limite de dias de venda

### Painel esquerdo

#### Dias a retirar

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/68ae02bc-f978-4b8c-9850-fc4dd779646a)

Essa informação vem de `days_to_take`

Quando um período de férias está sendo marcado, essa informação se atualiza temporariamente, junto com outros valores. Ao terminar ou cancelar a marcação, retorna-se ao valor de `days_to_take`

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/9fe6f5ad-5ab7-43db-ab67-05fdaac6f786)

Quando existem coisas que alteram o valor base dos dias a serem retirados, mostra-se um tooltip explicando os motivos:

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/c1b4d544-887b-4f34-ad66-c25c2c71ac6d)

As informações do tooltip se dividem entre **Valor Base**, **Valores negativos** e **Valores positivos**. Podem aparecer as seguintes informações:

| Descrição           | Campo                            | Tipo     |
| ------------------- | -------------------------------- | -------- |
| Valor Base          | `base_days_to_take`              | Base     |
| Desconto por falta  | `lost_days`                      | Negativo |
| Abonado pelo Gestor | `disregarded_days`               | Positivo |
| Dias selecionados   | `taken_days` + `programmed_days` | Negativo |
| Dias vendidos       | `sold_days`                      | Negativo |

Se algum valor for zero, não precisa aparecer. Se só houver o valor base e todos os outros forem zerados, não precisa mostrar o tooltip

#### Faltas no período

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/e489f368-026d-4022-8d07-d898049f1b88)

O seu valor vem de `faults`

#### Data de expiração

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/0d4c1d38-44bc-497b-bd25-c6065b45ca44)

Esse painel aparece no lugar do painel de Faltas no período quando `expiration_date != null`, seu valor vem do próprio `expiration_date`

#### Dias programados e retirados

![image](https://github.com/kdym/pmovel_readmes/assets/30319490/d08b5d7d-5579-4f10-96ad-66157fadd2e0)

Seus valores vêm de `programmed_days` e `taken_days`, respectivamente

Seus valores também são atualizados em tempo real quando se está realizando uma marcação

Os dias são programados quando eles estão sendo lançados **DEPOIS** da data atual, caso contrário, são dias já retirados

## Calendário

