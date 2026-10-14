# Automated Financial Reporting System (Power BI)

An end-to-end financial reporting solution built on ERP data to automate financial statements, cash flow reporting, VAT analysis, and budget performance monitoring. The project transforms accounting transactions into structured, interactive reports that support financial analysis and management decision-making.

## Business Problem

Financial statements and management reports were prepared manually during each reporting period. This process was time-consuming and prone to errors, while limiting management's ability to monitor profitability, liquidity, operating expenses, and VAT obligations between reporting cycles.

## Solution

An automated financial reporting workflow was developed to transform ERP accounting data into interactive Power BI dashboards.

**Workflow:** ERP API → Power Query (ETL) → Data Modeling → DAX Measures → Power BI

The solution organizes general ledger transactions into a structured data model and applies reusable calculations to support consistent financial reporting with less manual intervention.

## Dashboard Overview

The project includes six main reporting pages:

- **Executive Overview:** Provides a consolidated view of revenue trends, profitability margins, EBITDA, liquidity indicators, and overall financial performance.
- **Income Statement:** Presents revenue, cost of goods sold, gross profit, operating expenses, and net income to support profitability analysis.
- **Balance Sheet:** Analyzes assets, liabilities, and equity, with key liquidity ratios to evaluate the company's financial position.
- **Cash Flow:** Tracks cash movements, operating cash flow, and closing cash balances to improve visibility into liquidity.
- **VAT Report:** Analyzes input VAT, output VAT, net VAT payable, and related invoice information.
- **Budget vs Actual:** Compares actual financial results against budget, highlights variances, and supports scenario analysis.

## Data Model

The reporting model is centered around a general ledger fact table containing accounting transactions, connected to supporting dimensions such as accounts, journals, partners, and calendar dates.

Financial statement mapping tables organize accounts into reporting lines for the Income Statement, Balance Sheet, and Cash Flow Statement. Additional sales, purchase, and sales-order/purchase-order allocation data support VAT reporting and cost allocation.

The model also incorporates multi-currency handling through exchange-rate conversion.

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

## Tools and Technologies

Power BI, DAX, Power Query (M), Python, REST API, Data Modeling, ETL, Financial Reporting, and Financial Analysis.

## Business Value

The project demonstrates how ERP data, financial analysis, and business intelligence can be combined to reduce repetitive reporting work, improve visibility into financial performance, support reconciliation, and enable more informed business decisions.

## Limitations and Data Disclosure

This project showcases the analytical approach and reporting structure of a real-world financial reporting solution. Confidential company information has been protected by altering or replacing company names, account numbers, partner/customer names, and monetary values with synthetic equivalents.

The solution reflects a single-entity ERP reporting environment and has not been generalized for multi-entity consolidation. Report outputs depend on the completeness and accuracy of the underlying ERP data and account mappings.
