# Automated Financial Reporting System (Power BI)

End-to-end financial reporting solution built on ERP data: automated Income Statement,
Balance Sheet, Cash Flow, VAT reporting and Budget vs Actual analysis, replacing a manual,
Excel-based monthly close process.

> **Data Disclosure:** This repository showcases the structure, data model, and analytical
> approach of a real-world financial reporting project. To protect confidential business
> information, all monetary values, account numbers, company names, and partner/customer
> names shown here have been altered or replaced with synthetic equivalents. The data model,
> DAX logic, and report structure reflect the actual implementation.

## Business Problem
Financial statements and management reports were prepared manually each period, which was
slow, error-prone, and gave management no self-service visibility into margins, liquidity,
or VAT position between close cycles.

## Solution
An automated pipeline: **ERP API → Power Query (ETL) → Fact/Dimension Model → DAX → Power BI.**
Reports are generated directly from the general ledger, so new transactions and periods flow
into every statement without manual rework.

## Dashboard Pages
| Page | What it answers |
|---|---|
| Executive Overview | Margins, EBITDA, liquidity ratio, revenue waterfall and trend |
| Income Statement | Full P&L by line item, OpEx ratio, top expense accounts |
| Balance Sheet | Assets/liabilities breakdown, current & quick ratios |
| Cash Flow | Indirect-method cash flow, closing balance, trend over time |
| VAT Report | Input vs output VAT, net VAT due, invoice counts |
| Budget vs Actual | Variance by business line, scenario sliders |

## Data Model
Core fact table `Fact_MoveLines` (general ledger entries) linked to `Dim_Accounts`,
`Dim_Journals`, `Dim_Partners`, and a `Calendar` table. A statement-mapping layer
(`IS_Mapping`, `BS_Mapping`, `IS_ReportLines`, `BS_ReportLines`, `CF_ReportLines`) drives
the layout of each financial statement from the chart of accounts, so new accounts flow
into the correct report line automatically. Parsed sales/purchase tables and
`Fact_SO_PO_Allocation` support VAT reporting and cost allocation. Multi-currency amounts
are converted to the reporting currency.

![Data Model](docs/data_model.png)

## Key Technical Highlights
- Cleaned and validated raw ERP API exports before modeling
- Reusable DAX measures for margins, EBITDA, liquidity ratios, and budget variance
- Built-in reconciliation: Closing Cash in the Cash Flow statement ties to Bank & Cash
  in the Balance Sheet for the same period
- Multi-currency handling with exchange-rate conversion
- What-if parameters for scenario analysis in Budget vs Actual

## Sample DAX
See [docs/dax_measures.md](docs/dax_measures.md).

## Limitations
- Built for a single-entity, single-industry ERP setup; not yet generalized for
  multi-entity consolidation.
- Sample data in this repo is synthetic and does not reflect real transaction volumes
  or seasonality.

## Tools
Power BI · DAX · Power Query (M) · REST API · Data Modeling · ETL · Financial Reporting
