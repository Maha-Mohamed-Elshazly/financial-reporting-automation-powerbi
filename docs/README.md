# Automated Financial Reporting System (Power BI)

End-to-end financial reporting solution built on ERP data: automated Income Statement, Balance Sheet, Cash Flow, VAT reporting and Budget vs Actual analysis, replacing a manual, Excel-based monthly close process.

> **Data Disclosure:** This repository showcases the structure, data model, and analytical approach of a real-world financial reporting project. To protect confidential business information, all monetary values, account numbers, company names, and partner/customer names shown here have been altered or replaced with synthetic equivalents. The data model, DAX logic, and report structure reflect the actual implementation.

## Business Problem

Financial statements and management reports were prepared manually each period, which was slow, error-prone, and gave management no self-service visibility into margins, liquidity, or VAT position between close cycles.

## Solution

An automated pipeline: **ERP API → Power Query (ETL) → Fact/Dimension Model → DAX → Power BI.**

Reports are generated directly from the general ledger, so new transactions and periods flow into every statement without manual rework.

## Dashboard Pages

| Page | What it answers | Screenshot |
|---|---|---|
| Executive Overview | Margins, EBITDA, liquidity ratio, revenue waterfall and trend | [View](docs/screenshots/01_executive_overview.png) |
| Income Statement | Full P&L by line item, OpEx ratio, top expense accounts | [View](docs/screenshots/02_income_statement.png) |
| Balance Sheet | Assets/liabilities breakdown, current & quick ratios | [View](docs/screenshots/03_balance_sheet.png) |
| Cash Flow | Indirect-method cash flow, closing balance, trend over time | [View](docs/screenshots/04_cash_flow.png) |
| VAT Report | Input vs output VAT, net VAT due, invoice counts | [View](docs/screenshots/05_vat.png) |
| Budget vs Actual | Variance by business line, scenario sliders | [View](docs/screenshots/06_budget_vs_actual.png) |

## Data Model

Core fact table `Fact_MoveLines` (general ledger entries) linked to `Dim_Accounts`, `Dim_Journals`, `Dim_Partners`, and a `Calendar` table.

A statement-mapping layer (`IS_Mapping`, `BS_Mapping`, `IS_ReportLines`, `BS_ReportLines`, `CF_ReportLines`) drives the layout of each financial statement from the chart of accounts, so new accounts flow into the correct report line automatically.

Parsed sales/purchase tables and `Fact_SO_PO_Allocation` support VAT reporting and cost allocation. Multi-currency amounts are converted to the reporting currency.

[View Data Model](docs/data_model.png)

## Key Technical Highlights

- Cleaned and validated raw ERP API exports before modeling
- Reusable DAX measures for margins, EBITDA, liquidity ratios, and budget variance
- Built-in reconciliation: Closing Cash in the Cash Flow statement ties to Bank & Cash in the Balance Sheet for the same period
- Multi-currency handling with exchange-rate conversion
- What-if parameters for scenario analysis in Budget vs Actual

## DAX Measures

The core DAX measures used to calculate financial statements, KPIs, ratios, cash flow, VAT, and budget variances are documented separately.

[View DAX Measures Reference](docs/dax_measures.md)

## Project Structure

```text
financial-reporting-automation-powerbi/
├── README.md
├── .gitignore
├── data/
│   └── sample/
├── powerbi/
│   └── financial_reporting_dashboard.pbix
└── docs/
    ├── data_model.png
    ├── dax_measures.md
    ├── data_dictionary.md
    └── screenshots/
        ├── 01_executive_overview.png
        ├── 02_income_statement.png
        ├── 03_balance_sheet.png
        ├── 04_cash_flow.png
        ├── 05_vat.png
        └── 06_budget_vs_actual.png
```

## Project Documentation

- [Data Model](docs/data_model.png)
- [DAX Measures](docs/dax_measures.md)
- [Data Dictionary](docs/data_dictionary.md)
- [Executive Overview](docs/screenshots/01_executive_overview.png)
- [Income Statement](docs/screenshots/02_income_statement.png)
- [Balance Sheet](docs/screenshots/03_balance_sheet.png)
- [Cash Flow](docs/screenshots/04_cash_flow.png)
- [VAT Report](docs/screenshots/05_vat.png)
- [Budget vs Actual](docs/screenshots/06_budget_vs_actual.png)

## Limitations

- Built for a single-entity, single-industry ERP setup; not yet generalized for multi-entity consolidation.
- Sample data in this repo is synthetic and does not reflect real transaction volumes or seasonality.
- Cash Gap and payment-matching measures depend on invoice-name matching between `Fact_Payments` and `Fact_MoveLines`; match-rate should be checked before relying on `Average Cash Gap`.

## Tools

Power BI · DAX · Power Query (M) · REST API · Data Modeling · ETL · Financial Reporting
