# DAX Measures Reference

Core measures behind the Automated Financial Reporting dashboard. Organized by
financial statement. Table and column names reflect the actual data model
(`Fact_MoveLines`, `Dim_Accounts`, `Calendar`, mapping tables); sample values
shown elsewhere in this repo are synthetic.

---

## Foundation Measures

Every other measure in the model is built on top of these three. `Normal_Sign`
(+1 or -1, assigned per account via the mapping tables) converts raw debit/credit
balances into a sign convention where positive always means "increases this
line item" — this is what lets Revenue, Expenses, Assets and Liabilities all
use the same downstream logic.

```dax
Signed Balance =
SUMX ( Fact_MoveLines, Fact_MoveLines[balance] * RELATED ( Dim_Accounts[Normal_Sign] ) )
```

```dax
Balance (Cumulative) =
CALCULATE (
    [Signed Balance],
    FILTER ( ALL ( Calendar ), Calendar[Date] <= MAX ( Calendar[Date] ) )
)
```

```dax
Balance at Period Start =
CALCULATE (
    [Signed Balance],
    FILTER ( ALL ( Calendar ), Calendar[Date] < MIN ( Calendar[Date] ) )
)
```

> **Why two balance measures exist:** the Income Statement reports *period
> movement* (`Signed Balance`, filtered by the visible date range), while the
> Balance Sheet reports a *point-in-time cumulative balance*
> (`Balance (Cumulative)`, which ignores the slicer's lower bound and always
> sums from inception to the selected end date). Mixing these up is the most
> common source of numbers that don't reconcile with the ERP.

---

## Income Statement

```dax
Total Revenue =
CALCULATE ( [Signed Balance], Dim_Accounts[IS_Level1] = "Revenue" )

Total COGS =
CALCULATE ( [Signed Balance], Dim_Accounts[IS_Level1] = "Cost of Revenue" )

Gross Profit =
[Total Revenue] - [Total COGS]

Total Operating Expenses =
CALCULATE ( [Signed Balance], Dim_Accounts[IS_Level1] = "Operating Expenses" )

Operating Income (EBIT) =
[Gross Profit] - [Total Operating Expenses]

Net Profit =
[Gross Profit] + [Total Other Income] - [Total Operating Expenses] - [Total Other Expenses]
```

**Margin ratios** — each expresses a different line item as a percentage of
revenue, so they should never be equal unless Other Income/Expense is zero
for the period:

```dax
Gross Profit Margin % =
DIVIDE ( [Gross Profit], [Total Revenue], 0 )

Operating Margin % =
DIVIDE ( [Operating Income (EBIT)], [Total Revenue], 0 )

Net Profit Margin % =
DIVIDE ( [Net Profit], [Total Revenue] + [Total Other Income], 0 )

OpEx Ratio % =
DIVIDE ( [Total Operating Expenses], [Total Revenue], 0 )
```

```dax
EBITDA =
[Operating Income (EBIT)]
    + CALCULATE ( [Signed Balance], Dim_Accounts[IS_Level2] = "Depreciation & Non-Cash Adj." )

EBITDA Margin % =
DIVIDE ( [EBITDA], [Total Revenue], 0 )
```

**Period-over-period trend:**

```dax
Revenue MoM % =
VAR CurrentRev = [Total Revenue]
VAR PriorRev = CALCULATE ( [Total Revenue], DATEADD ( Calendar[Date], -1, MONTH ) )
RETURN DIVIDE ( CurrentRev - PriorRev, ABS ( PriorRev ), BLANK () )
```

---

## Balance Sheet

```dax
Total Current Assets =
CALCULATE (
    [Balance (Cumulative)],
    Dim_Accounts[BS_Level1] = "Assets",
    Dim_Accounts[BS_SubClass] = "Current"
)

Total Current Liabilities =
CALCULATE (
    [Balance (Cumulative)],
    Dim_Accounts[BS_Level1] = "Liabilities",
    Dim_Accounts[BS_SubClass] = "Current"
)

Total Assets =
[Total Current Assets] + [Total Non-Current Assets]

Total Liabilities =
[Total Current Liabilities] + [Total Non-Current Liabilities]

Total Equity =
[Total Equity (Capital + Retained)] + [Current Year Net Income]
```

**Liquidity ratios:**

```dax
Current Ratio =
DIVIDE ( [Total Current Assets], [Total Current Liabilities], BLANK () )

Quick Ratio =
VAR Inventory_Prepaid =
    CALCULATE (
        [Balance (Cumulative)],
        Dim_Accounts[BS_Level2] IN { "Current Assets - Other", "Prepayments" }
    )
RETURN
DIVIDE ( [Total Current Assets] - Inventory_Prepaid, [Total Current Liabilities], BLANK () )
```

**Integrity check** — should always evaluate to zero (Assets = Liabilities + Equity):

```dax
Balance Sheet Check =
[Total Assets] - [Total Liabilities] - [Total Equity]
```

---

## Cash Flow Statement (Indirect Method)

Built period-by-period from the same `Dim_Accounts` mapping, classifying each
balance sheet account into Operating (working capital), Investing (CapEx), or
Financing (owner's equity):

```dax
Account WC Effect (Period) =
IF (
    HASONEVALUE ( Dim_Accounts[BS_Level1] ),
    VAR IsAsset = SELECTEDVALUE ( Dim_Accounts[BS_Level1] ) = "Assets"
    VAR StartBal = [Balance at Period Start]
    VAR EndBal = [Balance at Period End]
    RETURN IF ( IsAsset, StartBal - EndBal, EndBal - StartBal ),
    BLANK ()
)

Cash Flow from Operations =
CALCULATE ( [CF Report Value], CF_ReportLines[LineItem] = "Net Cash from Operating Activities" )

Free Cash Flow =
CALCULATE ( [CF Report Value], CF_ReportLines[LineItem] = "Net Cash from Operating Activities" )
    + CALCULATE ( [CF Report Value], CF_ReportLines[LineItem] = "Net Cash used in Investing Activities" )
```

**Reconciliation control:** the model computes a closing cash balance two
independent ways — starting cash plus net change (`Closing Cash Balance
(Computed)`) versus the actual ending ledger balance (`Closing Cash Balance
(Actual)`) — and exposes the difference as `Unreconciled Difference`, which
should be zero. This is what lets the Cash Flow page's closing balance tie
out to the Balance Sheet's Bank & Cash total for the same date.

---

## VAT

```dax
Sales Base (Taxable Value) = SUM ( Dim_CustomerInvoices_Parsed[amount_untaxed_signed] )
Output VAT (Collected on Sales) = SUM ( Dim_CustomerInvoices_Parsed[amount_tax_signed] )
Purchase Base (Taxable Value) = ABS ( SUM ( Dim_VendorBills_Parsed[amount_untaxed_signed] ) )
Input VAT (Paid on Purchases) = ABS ( SUM ( Dim_VendorBills_Parsed[amount_tax_signed] ) )

Net VAT Due =
[Output VAT (Collected on Sales)] - [Input VAT (Paid on Purchases)]
```

VAT is computed directly from invoice-level fields rather than from the
general ledger, because this is the version that reconciles with the VAT
return. A small gap against the source ERP report can remain from
prior-period adjustments posted outside the invoice tables.

---

## Budget vs Actual

```dax
Variance Revenue = [Total Revenue] - [Budget Revenue]
Variance Revenue % = DIVIDE ( [Variance Revenue], ABS ( [Budget Revenue] ), 0 )
```

Budget baselines are generated from a trailing 12-month statistical average,
then adjusted using Power BI **What-If Parameters** (not manually generated
series) so the report supports live scenario sliders for Revenue, COGS and
OpEx growth assumptions.

---

## Dynamic Report-Line Pattern

The Income Statement, Balance Sheet and Cash Flow pages don't hard-code each
row — a single measure per statement resolves the correct value for whichever
line the visual is currently rendering, using `SELECTEDVALUE` against a
report-line mapping table:

```dax
IS Report Value =
VAR CurrentLine = SELECTEDVALUE ( IS_ReportLines[LineItem] )
VAR CurrentCode = SELECTEDVALUE ( IS_ReportLines[Code] )
VAR CurrentRowType = SELECTEDVALUE ( IS_ReportLines[RowType] )
RETURN
IF (
    CurrentRowType = "Detail",
    CALCULATE ( [Signed Balance], Dim_Accounts[code] = CurrentCode ),
    SWITCH (
        CurrentLine,
        "Total Revenue", [Total Revenue],
        "Gross Profit", [Gross Profit],
        "Net Profit", [Net Profit],
        BLANK ()
    )
)
```

This is what allows a new GL account to appear on the correct statement line
automatically once it's added to the mapping table, with no changes to the
report itself.

---

## Notes & Known Limitations

- **Sign convention:** `Normal_Sign` must be correct for every account in the
  mapping tables, or downstream measures silently flip sign. This is checked
  via `Balance Sheet Check`, which should always equal zero.
- **Calendar table** must be marked as a Date Table (required for `DATEADD` /
  period comparisons) and its range should cover the full data history —
  dates outside the Calendar's range appear as `(Blank)` in slicers.
- Cash Gap and payment-matching measures depend on invoice-name matching
  between `Fact_Payments` and `Fact_MoveLines`; match-rate should be checked
  before relying on `Average Cash Gap`.
