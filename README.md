# T\&D – RFV 

## Estrutura do repositório

```
data/
  └── customer_dataset.csv
notebooks/
  └── rfv_processing.ipynb
```

## O que foi feito

1. **Importação e validação de esquema**

   * Leitura do `customer_dataset.csv` com `inferSchema=True`.
   * Verificação se o **schema inferido** bate com o esperado e se o DataFrame ficou no **formato correto** (tipos e colunas).

2. **Estatísticas básicas (numéricas)**

   * Cálculo de **média** e **mediana** para as colunas numéricas:
     `total_orders`, `total_spent`, `returns_count`, `avg_order_value`,
     `days_since_last_purchase`, `customer_lifetime_months`.

3. **RFV por abordagem de *rank*** (dataset pequeno: **35 linhas**)

   * **R (Recency)**: derivada de `days_since_last_purchase` (**quanto menor, melhor**).
   * **F (Frequency)**: `total_orders/customer_lifetime_months`.
   * **V (Value)**: `total_spent-(returns_count*avg_order_value)`.
   * Como a base é pequena, foi usada uma abordagem de **`rank()`** por métrica para **distribuir a pontuação quase homogeneamente** em **faixas 1–5**:
   * Construção do **RFV\_code**: `Concat(R,F,V)` (ex.: R=5, F=4, V=3 → **543**).

4. **Regras de segmentação (a partir do RFV\_code) no seguinte segmentos:**

   * **Champions**
   * **Loyal customers**
   * **Potential loyalists**
   * **Recent Customers**
   * **Promissing**
   * **Need Attention**
   * **Cannot Lose Them**
   * **At risk**
   * **Hibernating**
   * **Lost**

## Resultados (preencha após executar o notebook)

**Médias e medianas**

| Coluna                     | Média | Mediana |
| -------------------------- | ----: | ------: |
| `total_orders`             | `10.82` | `8` |
| `total_spent`              | `7965.51` | `6432` |
| `returns_count`            | `0.88` | `1` |
| `avg_order_value`          | `766.85` | `759` |
| `days_since_last_purchase` | `92.2` | `53` |
| `customer_lifetime_months` | `15.54` | `18` |

## Como funciona uma análise RFV (visão geral)

* **R – Recency**: quão **recentemente** o cliente comprou. Menos dias desde a última compra ⇒ **score maior**.
* **F – Frequency**: **quantidade de compras** no período analisado. Mais compras ⇒ **score maior**.
* **V – Value**: **valor gasto** no período (ex.: `total_spent`). Maior gasto ⇒ **score maior**.

A junção (**RFV**) permite **priorizar ações** (retenção, upsell, reativação). Em bases pequenas, calcular **scores por rank/percentis** evita concentração de clientes em poucos valores e produz uma **segmentação mais equilibrada**.
