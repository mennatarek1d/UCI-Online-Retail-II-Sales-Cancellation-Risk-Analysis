# UCI Online Retail II — Sales & Cancellation Risk Analysis

> **Revenue held steady despite fewer orders, and cancellation losses appeared to double. A closer look shows that two single bulk orders explain more than half of that jump.**

![MySQL](https://img.shields.io/badge/MySQL-Data%20Cleaning%20%26%20Validation-4479A1?logo=mysql&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Interactive%20Dashboard-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-KPIs%20%26%20Dynamic%20Top--N-blue)
![Rows](https://img.shields.io/badge/Dataset-1M%2B%20transactions-green)

An end-to-end retail performance analysis of the UCI Online Retail II dataset (1M+ transaction lines). It investigates how revenue stayed resilient while order volume declined, and whether the sharp rise in cancellation losses is a real business risk or a data artifact.

---

## Executive Summary

Using **MySQL** and **Power BI**, I analyzed 2010–2011 online retail transactions to uncover revenue drivers, product performance, cancellation risk, and seasonal demand patterns.

- **Revenue rose by £0.1M in 2011** (£9.38M → £9.48M) with ~1,400 fewer orders. UK average order value (AOV) rose, but **Non-UK markets supplied the growth** (+£135K, +10%) while UK revenue was flat.
- **Cancellation impact nearly doubled** (-£238.5K → -£458.4K). Two bulk orders that were fully reversed (£168K and £77K) account for **54% of 2011 cancellation value**. Excluding them, underlying cancellations *fell* ~11% to -£212.7K.
- **A clear August–November seasonal peak** drives 43–46% of annual sales. November is the strongest month in both years, and six products rank in the top 10 in both years.
- **The December 2011 "decline" is a data-coverage effect.** The dataset ends on **9 December 2011**, so December 2011 contains only 8 trading days. On a like-for-like basis (1–9 December), orders were flat: 830 vs 816.

---

## Business Priorities

1. **Prepare inventory for the August–November sales surge** by prioritizing high-demand products and monitoring promising new products.
2. **Investigate the decline in customer loyalty alongside the sharp increase in cancellations** to identify potential quality or fulfillment issues.
3. **Resolve the December 2011 order decline** to understand why order volume dropped despite higher UK AOV *(resolved: see Open Questions)*.

---

## Business Problem

The business needs to understand what is driving revenue performance and where sales value is being lost through order cancellations. Revenue increased in 2011 despite fewer total orders, while the value lost to cancellations grew significantly year-over-year. This analysis answers two questions:

1. **What actually drove the revenue increase, given that order volume fell?**
2. **What is driving the sharp rise in cancellation impact, and is it a recurring risk or a one-off event?**

---

## Technical Implementation

**SQL (MySQL)**
- Data cleaning, validation, and duplicate handling
- Window functions with `ROW_NUMBER()`
- CTEs, subqueries, `JOIN`, `GROUP BY`, and `HAVING`
- Product, cancellation, and time-based analysis

**Power BI & DAX**
- KPI creation and an interactive 4-page dashboard (Welcome, Overview, Customers, Products) with page navigation
- Dynamic filtering and Top-N product analysis
- DAX functions including `CALCULATE()`, `RANKX()`, `FILTER()`, `ADDCOLUMNS()`, `DIVIDE()`, and `DISTINCTCOUNT()`
- Custom DAX-driven SVG/HTML visuals (ranked bars, price-band donut, cancellation bars, customer pie charts)

---

## Methodology

**MySQL:** data cleaning, validation, and initial business analysis (handling nulls, duplicate checks, cancellation flagging, aggregate validation).

**Power BI:** DAX measures, KPI cards, and interactive visualization with slicers for **Month, Year, and Transaction Type (Sale / Cancellation)**.

![MySQL to Power BI methodology](images/mysql_powerbi_methodology_v3.png)

**Key measures (DAX)**

| Measure | Logic |
|---|---|
| Revenue | `SUMX(Quantity × UnitPrice)` |
| Sales Revenue / Cancellation Value | `CALCULATE` filtered on Transaction Type |
| Total Orders | `DISTINCTCOUNT(InvoiceNo)` |
| AOV | `DIVIDE(Revenue, Total Orders)` |
| Repeat Customer Rate | Customers with more than 1 invoice ÷ all customers |
| Product Rank | `RANKX` that flips direction when the Cancellation slicer is selected |

**Key analysis areas**
- Revenue & order trends (2010 vs 2011)
- Average order value (AOV) analysis, UK vs Non-UK
- Product-level performance (top revenue, top cancellations)
- Market performance (UK vs Non-UK, by quarter)
- Customer-level cancellation concentration
- Cancellation analysis: frequency vs. value

---

## Key Findings

| Metric | 2010 | 2011 | Change |
|---|---|---|---|
| Sales revenue (excl. cancellations) | £9.38M | £9.48M | +£0.1M |
| Total orders | ~19.6K | ~18.2K | -1.4K (-7%) |
| Cancellation impact | -£238.5K | -£458.4K | +£219.9K (~92%) |
| *Cancellation impact, excl. 2 reversed bulk orders* | *-£238.5K* | *-£212.7K* | *-£25.8K (~-11%)* |
| Cancellation impact as % of sales | 2.5% | 4.8% | adjusted: 2.2% |
| UK AOV | £444 | £488 | +£44 |
| Non-UK AOV | £888 | £825 | -£63 |
| UK revenue | £8.06M | £8.03M | -£28K |
| Non-UK revenue | £1.32M | £1.46M | +£135K (+10%) |

**Where the growth came from.** UK AOV rose, but UK order count fell ~9% (18.1K → 16.5K), so UK revenue was flat. Non-UK orders grew ~19% (1.5K → 1.8K), which is where the net revenue gain came from. Australia (+£108K) and France (+£47K) led, while EIRE fell (-£82K).

---

## Dashboards & Analytics Deep Dive

### Sales Performance (2010 vs 2011)
Revenue, orders, customers, repeat-customer rate, Top-5 products (dynamic), UK vs Non-UK AOV, and revenue by quarter and market.

| Sales 2010 | Sales 2011 |
|---|---|
| ![Sales 2010](images/dashboard_sales_view.png) | ![Sales 2011](images/dashboard_sales_view.png) |

### Cancellation & Risk Analysis (2010 vs 2011)
Cancellation value, top cancelled products, monthly cancellation trend, and customer-level concentration.

| Cancellations 2010 | Cancellations 2011 |
|---|---|
| ![Cancellations 2010](images/dashboard_cancellation_view.png) | ![Cancellations 2011](images/dashboard_cancellation_view.png) |

> Use the Year, Month, and Transaction Type slicers in `powerbi/online_retail_dashboard.pbix` to reproduce each view.

---

## Key Business Insights

1. **Order volume fell, but the sale mix improved.** Jan–Nov sales grew 3.1% (£8.60M → £8.87M) on 3.7% fewer orders, supported by a higher UK AOV. The full-year order drop looks larger only because December 2011 is incomplete.
2. **Non-UK markets, not the UK, delivered the net revenue growth.** UK AOV gains (+£44) only offset a ~9% fall in UK orders.
3. **The cancellation spike is concentrated, not systemic.** Two reversed bulk orders drove most of it:
   - **£168K** — Paper Craft, Little Birdie (80,995 units, one customer, ordered and cancelled on 9 Dec 2011)
   - **£77K** — Medium Ceramic Top Storage Jar (74,215 units, one customer, ordered and cancelled in Jan 2011)

   The top 5 customers account for **63% of 2011 cancellation value** (vs 21% in 2010). These two products also appear as top-revenue items in 2011, but their sales were reversed, so gross product rankings should be read with caution.
4. **One genuine recurring cancellation pattern exists.** Regency Cakestand 3 Tier, the best-selling product, was cancelled in **167 orders by 108 customers** in 2011. The value is small (£8.7K), but the frequency points to a possible quality, packaging, or delivery issue worth investigating.
5. **Clear August–November seasonal peak.** August–November delivers 43% (2010) and 46% (2011) of annual sales. November alone is ~15% of the year, and is the strongest month in both years. **Six products rank in the top 10 in both years:** Regency Cakestand 3 Tier, White Hanging Heart T-Light Holder, Jumbo Bag Red Retrospot, Paper Chain Kit 50's Christmas, Assorted Colour Bird Ornament, and Popcorn Holder.
6. **Product 23084 (Rabbit Night Light) is a breakout new product.** Launched in May 2011, it generated **487 orders from 325 customers in November 2011 alone** and ~£67K for the year, which makes it a priority candidate for inventory monitoring.
7. **Repeat-customer rate was stable across the full year** (66% in 2010 vs 64% in 2011). November was flat at 32%. The apparent December drop (30% → 17%) reflects the 9-day December window rather than a confirmed loyalty decline.

---

## Recommendations

**1. Prepare inventory before Q4**
Increase stock coverage for the six consistently top-ranked products before August, with additional monitoring through October–November, when ~15% of annual sales land in a single month.

**2. Monitor new-product performance**
Build an early-warning rule for new products that rapidly gain customers, orders, or revenue. Rabbit Night Light went from zero to 487 November orders within six months of launch, and earlier stock scaling would have captured more of that demand.

**3. Strengthen UK retention while growing export markets**
The UK is still ~85% of revenue but its order count is shrinking. Focus retention and promotions on high-value UK customers, and back the Non-UK momentum (Australia, France, Norway, Spain, Japan) with targeted shipping and promotional offers.

**4. Add controls for large-order cancellations**
A single order cancelled in full can distort annual results. Introduce approval or verification for bulk orders (for example, above 10,000 units) and report cancellation metrics both *with* and *without* outliers.

**5. Investigate the recurring cancellation pattern**
Review order accuracy, product quality, packaging, and customer complaints for the Regency Cakestand, which is cancelled across 108 customers.

**6. Improve data quality for customer analytics**
About 22% of transaction lines (13% of sales revenue) have no customer ID. Capturing customer IDs at checkout would make loyalty and repeat-purchase analysis more reliable.

---

## Open Questions / Further Investigation

**December 2011 order drop: resolved.** Orders appeared to fall sharply (about 2,000 → 816), but the dataset **ends on 9 December 2011**, so December 2011 covers 8 trading days against 20 in December 2010. On a like-for-like basis (1–9 December):

| | Dec 1–9, 2010 | Dec 1–9, 2011 |
|---|---|---|
| Orders | 830 | 816 |
| Revenue | £408K | £446K (excl. the £168K reversed order) |

Orders are essentially flat (-2%), so no external cause such as competitor pricing is needed to explain the headline drop. The higher December UK AOV (£490 → £542, excl. the bulk order) reflects larger average baskets in the partial month.

**Remaining questions**
- Were the two bulk orders data-entry errors, test orders, or genuine wholesale cancellations? Order-level context would settle this.
- Is the Regency Cakestand cancellation pattern a product issue or a fulfillment issue? Cancellation reason codes would help.
- Full-year 2011 data (including late December) would allow a complete year-over-year comparison.

---

## Repo Structure

```
├── README.md
├── images/
│   ├── dashboard_sales_view.png
│   ├── dashboard_cancellation_view.png
│   └── mysql_powerbi_methodology_v3.png
├── sql/
│   └── data_cleaning_and_validation.sql
├── powerbi/
│   └── online_retail_dashboard.pbix
└── data/
    └── UCI_online_retail_II.csv
```

---

## Author

**Menna Tarek**
Data Analyst | SQL · Power BI · DAX

[LinkedIn](#) · [Email](#) · [Portfolio](#)
