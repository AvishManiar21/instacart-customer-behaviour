# Instacart — Customer Behaviour Analysis

Behavioural analysis of **3.2 million grocery orders** from 206,209 Instacart
customers: what people reorder, how shopping habits form, and how quickly
discovery gives way to routine.

**Stack:** Python · pandas · NumPy · matplotlib · seaborn · Google Colab

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AvishManiar21/instacart-customer-behaviour/blob/main/Instacart.ipynb)

---

## Status

**Profiling complete** — all five tables, four data traps found and quantified.
Analysis in progress.

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

## Data traps found

Each produces a plausible but wrong answer, and each was verified rather than
assumed.

**1. `days_since_prior_order` is censored at 30.** The column stops dead at 30,
where 369,323 orders (11.5%) pile up against ~20,000 on each neighbouring day.
`30` means "30 or more".

The true gaps were destroyed before publication — but `order_dow` still encodes
them. A 30-day gap must shift the day of week by exactly `30 mod 7 = 2`; any
other shift proves a longer gap. Validated at **100.00% accuracy** on the 2.8M
orders where the gap is known, then applied: **314,925 orders (85.3%) are
provably longer than 30 days.**

Consequence: the mean gap (11.1 days) understates reality and is never quoted.
The median (7 days) sits far below the cap and is used throughout.

**2. Structural zeros in `reordered`.** A customer's first order cannot contain a
reorder. 2,078,068 rows sit in first orders and exactly **0** are flagged as
reorders — 15.6% of every zero in the column. Including them gives a 59.0%
reorder rate; excluding them, **63.0%**. Both are valid, for different questions.

**3. `eval_set` splits the data three ways.** `prior` (3.21M), `train` (131k),
`test` (75k). Verified that every `prior` order has product rows and **no `test`
or `train` order appears** in the prior file — the test orders have no product
data anywhere. Analysis uses `prior`.

**4. Placeholder categories.** `missing` (1,258 products) and `other` (548) are
not departments. Both appear at aisle *and* department level on exactly the same
rows — zero products are uncategorised at one level but not the other. Flagged
via `is_categorised` rather than silently dropped.

Also: `order_dow` has **no documented mapping** to real weekday names, so days
are referred to by number throughout rather than guessed at.

---

## Source

[Instacart Market Basket Analysis](https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis)
— 680 MB. Not committed; the notebook downloads it via the Kaggle API.

---

## Related

[olist-delivery-analytics](https://github.com/AvishManiar21/olist-delivery-analytics)
— SQL Server pipeline, star schema and Power BI dashboards on Brazilian
e-commerce delivery data.