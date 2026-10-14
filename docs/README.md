# Automated Financial Reporting System (Power BI)

An end-to-end financial reporting solution built on ERP data to automate financial statements, cash flow reporting, VAT analysis, and budget performance monitoring. The project transforms raw accounting transactions into structured, interactive reports that support financial analysis and management decision-making.

> **Data Disclosure:** This repository showcases the structure, data model, and analytical approach of a real-world financial reporting project. To protect confidential business information, company names, account numbers, partner/customer names, and monetary values have been altered or replaced with synthetic equivalents. The data model, analytical logic, and report structure reflect the project implementation.

## Business Problem

Financial statements and management reports were prepared manually during each reporting period. This process was time-consuming and prone to errors, while limiting management's ability to monitor profitability, liquidity, operating expenses, and VAT obligations between reporting cycles.

## Solution

An automated financial reporting workflow was developed to transform ERP accounting data into interactive Power BI reports.

**Workflow:** ERP API → Power Query (ETL) → Data Modeling → DAX Measures → Power BI Dashboards

The solution organizes general ledger transactions into a structured data model and applies reusable calculations to generate financial reports with less manual intervention.

## Dashboard Pages

| Report Page | Key Insights |
|---|---|
| Executive Overview | Revenue trends, profitability margins, EBITDA, liquidity indicators, and performance overview |
| Income Statement | Revenue, cost of goods sold, gross profit, operating expenses, and net income |
| Balance Sheet | Assets, liabilities, equity, and liquidity ratios |
| Cash Flow | Cash movements, operating cash flow, and closing cash balance |
| VAT Report | Input VAT, output VAT, net VAT payable, and invoice analysis |
| Budget vs Actual | Budget variances, performance by business line, and scenario analysis |

## Dashboard Screenshots

### Executive Overview

![Executive Overview](docs/screenshots/01_executive_overview.png)

### Income Statement

![Income Statement](docs/screenshots/02_income_statement.png)

### Balance Sheet

![Balance Sheet](docs/screenshots/03_balance_sheet.png)

### Cash Flow

![Cash Flow](docs/screenshots/04_cash_flow.png)

### VAT Report

![VAT Report](docs/screenshots/05_vat.png)

### Budget vs Actual

![Budget vs Actual](docs/screenshots/06_budget_vs_actual.png)

## Data Model

The data model is centered around `Fact_MoveLines`, which contains general ledger transactions and connects to supporting dimensions, including accounts, journals, partners, and calendar dates.

Financial statement mapping tables organize accounts into reporting lines for the Income Statement, Balance Sheet, and Cash Flow Statement. Additional sales, purchase, and sales-order/purchase-order allocation data supports VAT reporting and cost allocation.

The model also incorporates multi-currency handling through exchange-rate conversion.

![Financial Reporting Data Model](docs/data_model.png)

## Key Technical Highlights

- Extracted and validated accounting data from an ERP API.
- Used Power Query for data transformation and preparation.
- Designed a structured fact-and-dimension data model.
- Developed reusable DAX measures for financial statements and performance indicators.
- Implemented profitability and liquidity metrics, including margins, EBITDA, and financial ratios.
- Built cash flow reporting with reconciliation against the Balance Sheet cash position.
- Analyzed input VAT, output VAT, and net VAT payable.
- Compared actual financial performance against budget.
- Incorporated what-if parameters for scenario analysis.
- Designed interactive dashboards to support financial reporting and management decisions.

## Project Documentation

- [DAX Measures Reference](docs/dax_measures.md)
- [Data Dictionary](docs/data_dictionary.md)
- [Financial Reporting Data Model](docs/data_model.png)
- [Dashboard Screenshots](docs/screenshots/)

## Tools and Technologies

Power BI | DAX | Power Query (M) | Python | REST API | Data Modeling | ETL | Financial Reporting | Financial Analysis

## Limitations

- The solution reflects a single-entity ERP reporting environment and has not been generalized for multi-entity consolidation.
- Sample data is synthetic and does not represent actual company transactions, financial results, or transaction volumes.
- Report outputs depend on the completeness and accuracy of the underlying ERP data and account mappings.

## Project Objective

To demonstrate how ERP data, financial analysis, and business intelligence can be combined to automate financial reporting, improve visibility into business performance, and support data-driven financial decisions.
