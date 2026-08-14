# Instacart — Customer Behaviour Analysis

Behavioural analysis of **3.2 million grocery orders** from 206,209 Instacart
customers: what people reorder, how shopping habits form, and how quickly
discovery gives way to routine.

**Stack:** Python · pandas · NumPy · matplotlib · seaborn · Google Colab

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AvishManiar21/instacart-customer-behaviour/blob/main/Instacart.ipynb)

---

## Status

🚧 In progress.

---

## The dataset

Instacart released its own operational data in 2017 — anonymised, but real
orders from real customers.

| File | Rows | Grain |
|---|---:|---|
| `orders` | 3,421,083 | one row per order |
| `order_products__prior` | 32,434,489 | one row per **product per order** |
| `products` | 49,688 | one row per product |
| `aisles` | 134 | product aisle lookup |
| `departments` | 21 | department lookup |

**206,209 users**, each with between 4 and 100 orders (median 10). Unlike most
public e-commerce data, *every* customer here is a repeat customer — which is
what makes behavioural analysis possible.

### What this data does not have

Worth stating up front, because it shapes what can be asked:

- **No dates.** Only `order_dow` (0–6), `order_hour_of_day`, and
  `days_since_prior_order`. No timestamps, so no trends, seasonality or
  time series.
- **No prices.** No revenue, order value, or margin.

So this is a study of *behaviour*, not commerce.

---

## Known traps

Documented as they're found — each produces a plausible but wrong answer.

**1. `days_since_prior_order` is censored at 30.** 369,323 orders (11.5%) sit
at exactly 30, which means "30 or more". Treating it as a literal 30 understates
long gaps and corrupts any purchase-interval or churn calculation.

**2. `eval_set` splits the data three ways.** `prior` (3.21M), `train` (131k),
`test` (75k). The test orders have **no product rows at all** — they were what
competitors had to predict. Counting orders without filtering inflates totals.

**3. Order #1 has no `days_since_prior_order`.** 206,209 NULLs, exactly one per
user. Structural, not missing.

**4. Reorder rate is measured per item, not per order.** Averaging at the wrong
grain gives a different number.

---

## Source

[Instacart Market Basket Analysis](https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis)
— 680 MB. Not committed; the notebook downloads it via the Kaggle API.

---

## Related

[olist-delivery-analytics](https://github.com/AvishManiar21/olist-delivery-analytics)
— SQL Server pipeline, star schema and Power BI dashboards on Brazilian
e-commerce delivery data.