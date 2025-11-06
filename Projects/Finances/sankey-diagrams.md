# Financial Sankey Diagrams Guide

## Overview

Sankey diagrams are powerful visualization tools for tracking money flow in personal finances. They show the flow from income sources through expenses, savings, and investments with proportional width representing the amount of money.

## What is a Sankey Diagram?

A Sankey diagram is a flow diagram where:

- **Nodes** represent categories (Income sources, expense categories, savings accounts)
- **Links** represent flows between categories
- **Width** of flows is proportional to the quantity (dollar amounts)
- **Direction** shows the flow of money from source to destination

## Financial Use Cases

### Personal Finance Applications

- **Income to Expenses**: Visualize how salary flows to different expense categories
- **Budget Planning**: Show planned vs actual spending flows
- **Investment Tracking**: Display how money flows from income to different investment vehicles
- **Debt Paydown**: Visualize debt reduction over time
- **Savings Allocation**: Show how savings are distributed across different accounts/goals

### Business Finance Applications

- **Revenue Streams**: Show different income sources flowing to business operations
- **Cost Allocation**: Visualize how operational costs are distributed
- **Profit Distribution**: Show how profits flow to reinvestment, dividends, reserves
- **Cash Flow Analysis**: Track money movement through business processes

## Tools for Creating Financial Sankey Diagrams

### 1. Online Tools

#### SankeyMATIC

- **URL**: https://sankeymatic.com/
- **Pros**: Free, easy to use, no registration required
- **Cons**: Limited customization options
- **Best for**: Quick personal finance visualizations

Example syntax:

```
Salary [3500] Rent
Salary [800] Food & Dining
Salary [500] Transportation
Salary [200] Entertainment
Salary [1000] Savings
```

#### Plotly (Online)

- **URL**: https://plotly.com/
- **Pros**: Professional quality, interactive diagrams
- **Cons**: Requires account for advanced features
- **Best for**: Professional presentations and detailed analysis

### 2. Programming Solutions

#### Python with Plotly

```python
import plotly.graph_objects as go

# Example: Monthly budget flow
fig = go.Figure(data=[go.Sankey(
    node = dict(
      pad = 15,
      thickness = 20,
      line = dict(color = "black", width = 0.5),
      label = ["Salary", "Side Income", "Rent", "Food", "Transportation", "Savings", "Investments"],
      color = ["blue", "green", "red", "orange", "yellow", "lightgreen", "purple"]
    ),
    link = dict(
      source = [0, 0, 0, 0, 0, 1, 5], # indices correspond to labels
      target = [2, 3, 4, 5, 6, 5, 6],
      value = [1200, 400, 300, 800, 600, 200, 400]
  ))])

fig.update_layout(title_text="Monthly Cash Flow", font_size=10)
fig.show()
```

#### R with networkD3

```r
library(networkD3)

# Create nodes dataframe
nodes <- data.frame(
  name = c("Salary", "Side Income", "Rent", "Food", "Transportation", "Savings", "Investments")
)

# Create links dataframe
links <- data.frame(
  source = c(0, 0, 0, 0, 0, 1, 5),
  target = c(2, 3, 4, 5, 6, 5, 6),
  value = c(1200, 400, 300, 800, 600, 200, 400)
)

# Create Sankey diagram
sankeyNetwork(Links = links, Nodes = nodes, Source = "source",
              Target = "target", Value = "value", NodeID = "name")
```

#### JavaScript with D3.js

```javascript
// Basic D3.js Sankey implementation
const data = {
	nodes: [
		{ name: 'Salary' },
		{ name: 'Side Income' },
		{ name: 'Rent' },
		{ name: 'Food' },
		{ name: 'Savings' },
	],
	links: [
		{ source: 0, target: 2, value: 1200 },
		{ source: 0, target: 3, value: 400 },
		{ source: 0, target: 4, value: 800 },
		{ source: 1, target: 4, value: 200 },
	],
};
```

### 3. Spreadsheet Solutions

#### Google Sheets with Add-ons

- **Lucidchart Diagrams**: Add-on for Google Sheets
- **Draw.io**: Integration available
- **Manual creation**: Using stacked bar charts as approximation

#### Excel Solutions

- **Power BI**: Microsoft's business intelligence tool
- **Third-party add-ins**: Various Sankey diagram add-ins available
- **Workarounds**: Using waterfall charts or stacked area charts

## Financial Sankey Diagram Templates

### 1. Monthly Budget Flow

```
Income Sources → Expense Categories → Specific Expenses

Salary [4000] → Housing [1200] → Rent/Mortgage
Salary → Food [600] → Groceries [400]
Salary → Food → Dining Out [200]
Salary → Transportation [500] → Car Payment [300]
Salary → Transportation → Gas [150]
Salary → Transportation → Insurance [50]
Salary → Savings [800] → Emergency Fund [400]
Salary → Savings → Retirement [400]
Salary → Entertainment [300]
Salary → Utilities [200]
Salary → Healthcare [200]
Salary → Miscellaneous [200]

Side Income [500] → Savings [300]
Side Income → Entertainment [200]
```

### 2. Investment Portfolio Flow

```
Income → Investment Accounts → Asset Classes

Salary [1000] → 401k [600] → Stock Index [400]
Salary → 401k → Bond Index [200]
Salary → Roth IRA [400] → Individual Stocks [250]
Salary → Roth IRA → REITs [150]

Bonus [2000] → Taxable Account [2000] → ETFs [1200]
Bonus → Taxable Account → Individual Stocks [800]
```

### 3. Debt Paydown Strategy

```
Income → Debt Payments → Principal/Interest

Salary [1500] → Credit Card 1 [500] → Principal [300]
Salary → Credit Card 1 → Interest [200]
Salary → Student Loan [400] → Principal [350]
Salary → Student Loan → Interest [50]
Salary → Car Loan [350] → Principal [300]
Salary → Car Loan → Interest [50]
Salary → Extra Payments [250] → Credit Card 1
```

## Best Practices for Financial Sankey Diagrams

### 1. Data Preparation

- **Accurate Numbers**: Use actual financial data, not estimates
- **Consistent Time Periods**: Ensure all flows represent the same time period
- **Complete Picture**: Include all significant income and expense flows
- **Categorization**: Group similar items for clarity

### 2. Visual Design

- **Color Coding**: Use consistent colors for similar categories
- **Node Ordering**: Arrange nodes logically (income → expenses → savings)
- **Proportional Width**: Ensure flow width accurately represents dollar amounts
- **Labels**: Clear, concise labels for all nodes and flows

### 3. Analysis Insights

- **Flow Balance**: Income flows should equal expense + savings flows
- **Bottlenecks**: Identify where money gets "stuck" or poorly allocated
- **Opportunities**: Spot areas for optimization or reallocation
- **Trends**: Create multiple diagrams to show changes over time

## Example Financial Scenarios

### Scenario 1: Young Professional Budget

```
Monthly Income: $4,500
- Salary: $4,000
- Side Hustle: $500

Monthly Expenses: $3,600
- Rent: $1,200 (27%)
- Food: $500 (11%)
- Transportation: $400 (9%)
- Entertainment: $300 (7%)
- Utilities: $200 (4%)
- Healthcare: $200 (4%)
- Miscellaneous: $300 (7%)
- Phone: $100 (2%)
- Subscriptions: $100 (2%)

Monthly Savings: $900 (20%)
- Emergency Fund: $400
- 401k: $300
- Roth IRA: $200
```

### Scenario 2: Family Budget with Multiple Goals

```
Monthly Income: $8,000
- Primary Income: $6,500
- Secondary Income: $1,500

Monthly Expenses: $5,500
- Housing: $2,000 (25%)
- Food: $800 (10%)
- Transportation: $600 (7.5%)
- Childcare: $1,200 (15%)
- Utilities: $300 (3.75%)
- Insurance: $400 (5%)
- Entertainment: $200 (2.5%)

Monthly Savings: $2,500 (31.25%)
- Emergency Fund: $500
- Retirement: $1,000
- Kids Education: $600
- Vacation Fund: $200
- Home Improvement: $200
```

## Tools and Resources

### Software Recommendations

1. **Plotly** - Most versatile for programming
2. **SankeyMATIC** - Best for quick, simple diagrams
3. **Lucidchart** - Good for collaborative work
4. **Power BI** - Best for business/detailed analysis

### Data Sources

- **Bank Statements**: Export transaction data
- **Budgeting Apps**: Mint, YNAB, Personal Capital exports
- **Spreadsheets**: Manual tracking data
- **Financial Software**: Quicken, QuickBooks exports

### Learning Resources

- [Plotly Sankey Documentation](https://plotly.com/python/sankey-diagram/)
- [D3.js Sankey Examples](https://observablehq.com/@d3/sankey-diagram)
- [SankeyMATIC Tutorial](https://sankeymatic.com/build/)
- [Financial Visualization Best Practices](https://www.storytellingwithdata.com/)

## Implementation Checklist

- [ ] Define the financial flow you want to visualize
- [ ] Gather accurate financial data
- [ ] Choose appropriate tool/platform
- [ ] Structure data in proper format
- [ ] Create initial diagram
- [ ] Review for accuracy and completeness
- [ ] Apply consistent styling and colors
- [ ] Add clear labels and legends
- [ ] Validate that flows balance correctly
- [ ] Export or share final visualization
- [ ] Set up regular updates if needed

## Advanced Techniques

### Time-based Analysis

- Create monthly diagrams to show seasonal patterns
- Year-over-year comparisons
- Before/after budget change analysis

### Scenario Modeling

- "What if" scenarios for different income levels
- Debt payoff strategy comparisons
- Retirement planning projections

### Interactive Dashboards

- Combine multiple Sankey diagrams
- Add filters for different time periods
- Include drill-down capabilities

---

_Last updated: November 2024_
