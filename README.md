# 📊 Power BI Sales Dashboard Project
### Resume-Ready Project | Step-by-Step Guide

---

## 🎯 What You're Building

A **Sales Performance Dashboard** with 3 report pages:
1. **Executive Overview** — KPIs, revenue trend, regional performance
2. **Product Analysis** — Sales by category, top products, profit margins
3. **Salesperson Performance** — Individual vs target, leaderboard

This covers everything interviewers ask about: data modeling, DAX measures, relationships, slicers, and visuals.

---

## 📁 Your Dataset (3 Tables)

| Table | Description |
|-------|-------------|
| `Sales_Data` | 360 rows of orders (2023–2024) |
| `Targets` | Annual sales targets per salesperson |
| `Product_Lookup` | Product metadata |

---

## STEP 1 — Load Your Data

1. Open **Power BI Desktop** (free download from Microsoft)
2. Click **Home → Get Data → Excel**
3. Select `Sales_PowerBI_Dataset.xlsx`
4. Check all 3 tables in the Navigator: `Sales_Data`, `Targets`, `Product_Lookup`
5. Click **Transform Data** to open Power Query

---

## STEP 2 — Clean Data in Power Query

In the Power Query Editor, do these for `Sales_Data`:

- **Set column types**: Right-click `Date` → Change Type → Date. Set `Revenue`, `Profit`, `Cost` to Decimal Number.
- **Add a Year column**: Add Column → Custom Column → Name it `Year`, formula: `= Date.Year([Date])`
- **Add a Month column**: Add Column → Custom Column → Name it `Month_Num`, formula: `= Date.Month([Date])`
- **Add Month Name**: Add Column → Custom Column → Name it `Month`, formula:
  `= Date.ToText([Date], "MMM")`

Click **Close & Apply**.

---

## STEP 3 — Create a Date Table (Important!)

In Power BI, go to **Modeling tab → New Table** and paste:

```
Date_Table = 
CALENDAR(DATE(2023,1,1), DATE(2024,12,31))
```

Then add columns to it — go to **Modeling → New Column** for each:

```
Year = YEAR(Date_Table[Date])
Month = FORMAT(Date_Table[Date], "MMM")
Month_Num = MONTH(Date_Table[Date])
Quarter = "Q" & QUARTER(Date_Table[Date])
```

---

## STEP 4 — Build Relationships (Data Model)

Go to the **Model view** (icon on left sidebar).

Connect these:
- `Sales_Data[Date]` → `Date_Table[Date]` (Many to One)
- `Sales_Data[Product]` → `Product_Lookup[Product]` (Many to One)
- `Sales_Data[Salesperson]` → `Targets[Salesperson]` (Many to One)

> **Interview tip**: Always explain that you used a Star Schema — one fact table (Sales_Data) connected to dimension tables. This is the standard Power BI data modeling pattern.

---

## STEP 5 — Write Your DAX Measures

Go to your `Sales_Data` table, right-click → **New Measure**. Add each of these:

### Basic Measures
```dax
Total Revenue = SUM(Sales_Data[Revenue])

Total Profit = SUM(Sales_Data[Profit])

Total Units Sold = SUM(Sales_Data[Units_Sold])

Profit Margin % = DIVIDE([Total Profit], [Total Revenue], 0)

Total Orders = DISTINCTCOUNT(Sales_Data[Order_ID])
```

### Time Intelligence Measures
```dax
Revenue LY = 
CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(Date_Table[Date]))

Revenue YoY % = 
DIVIDE([Total Revenue] - [Revenue LY], [Revenue LY], 0)

Revenue MTD = 
TOTALMTD([Total Revenue], Date_Table[Date])

Revenue YTD = 
TOTALYTD([Total Revenue], Date_Table[Date])
```

### Target Measures
```dax
Total Target = SUM(Targets[Annual_Target])

Target Achievement % = DIVIDE([Total Revenue], [Total Target], 0)
```

> **Interview tip**: DIVIDE() is always preferred over "/" because it handles division-by-zero gracefully.

---

## STEP 6 — Build Page 1: Executive Overview

**Add these visuals:**

| Visual | Fields |
|--------|--------|
| **Card** | Total Revenue |
| **Card** | Total Profit |
| **Card** | Profit Margin % |
| **Card** | Revenue YoY % |
| **Line Chart** | X-axis: Month (from Date_Table), Values: Total Revenue, Revenue LY |
| **Bar Chart** | Axis: Region, Values: Total Revenue |
| **Donut Chart** | Legend: Customer_Segment, Values: Total Revenue |
| **Slicer** | Year (from Date_Table) |
| **Slicer** | Region |

**Formatting tips:**
- Set card colors to match — use a dark blue (`#1F4E79`) header bar
- Format Revenue cards with `$` currency, 0 decimals
- Format Margin % as percentage with 1 decimal
- Title each visual clearly

---

## STEP 7 — Build Page 2: Product Analysis

**Add these visuals:**

| Visual | Fields |
|--------|--------|
| **Clustered Bar** | Axis: Product, Values: Total Revenue (sorted descending) |
| **Stacked Bar** | Axis: Category, Values: Total Revenue, Total Profit |
| **Matrix** | Rows: Category → Product, Values: Total Revenue, Total Profit, Profit Margin % |
| **Treemap** | Group: Category, Values: Total Revenue |
| **Slicer** | Category |
| **Slicer** | Year |

**One cool trick:** In the Matrix, enable **Conditional Formatting** on Profit Margin % (Background color, gradient from red to green). This impresses interviewers immediately.

---

## STEP 8 — Build Page 3: Salesperson Performance

**Add these visuals:**

| Visual | Fields |
|--------|--------|
| **Clustered Bar (grouped)** | Axis: Salesperson, Values: Total Revenue AND Total Target |
| **Card** | Target Achievement % |
| **Table** | Columns: Salesperson, Region, Total Revenue, Total Target, Target Achievement % |
| **Bar Chart** | Axis: Salesperson, Values: Total Profit |
| **Slicer** | Region |
| **Slicer** | Year |

**In the Table:** Apply conditional formatting to `Target Achievement %` — color cells red if below 80%, yellow 80-100%, green above 100%.

---

## STEP 9 — Polish the Report

- **Theme**: Go to View → Themes → pick "Accessible City Park" or "Executive" for a clean look
- **Navigation buttons**: Insert → Buttons → Navigator — add page navigation buttons on each page
- **Report title**: Add a Text Box at the top of every page with the page name + your name
- **Tooltips**: Right-click any visual → Tooltips → enable to show extra detail on hover

---

## STEP 10 — Publish (Optional but impressive)

1. Sign in with a free Microsoft work/school account
2. Click **Home → Publish → My Workspace**
3. Go to app.powerbi.com → you can share the link on your resume!

---

## 💼 How to Describe This on Your Resume

**Under Projects:**
> **Sales Performance Dashboard** | Power BI
> Designed and developed a multi-page interactive sales dashboard analyzing $2M+ in revenue across 360+ transactions (2023–2024). Built star schema data model with 3 related tables, authored 10+ DAX measures including time intelligence (YoY, YTD, MTD), and delivered executive-level KPI views, product profitability analysis, and salesperson-vs-target tracking using slicers, conditional formatting, and drill-through navigation.

---

## 🎤 Common Interview Questions & Your Answers

**Q: What is a Star Schema?**
A: A fact table (transactions) connected to dimension tables (dates, products, people). It's the best practice for Power BI because it keeps the model clean and DAX performs faster.

**Q: Why do we need a Date Table?**
A: Power BI's time intelligence functions (SAMEPERIODLASTYEAR, TOTALYTD) require a continuous, marked Date table. Without it, those functions don't work correctly.

**Q: What's the difference between CALCULATE and a regular measure?**
A: CALCULATE lets you modify the filter context. It's the most powerful DAX function — you use it to override or add filters to a calculation.

**Q: What is a slicer vs a filter?**
A: A slicer is a visual on the canvas that users interact with. A filter is applied in the Filters pane and can be hidden from users. Both limit what data is shown.

**Q: What does DIVIDE() do differently than "/"?**
A: DIVIDE(numerator, denominator, alternate) safely handles division by zero — it returns the alternate value (or blank) instead of an error.

**Q: How do you handle relationships between tables?**
A: In Model view, I connect tables on matching columns. The standard is Many-to-One from the fact table to dimension tables, with single-directional cross-filtering.

---

## 📚 Quick Reference: Key DAX Functions Used

| Function | What It Does |
|----------|-------------|
| `SUM()` | Adds up a column |
| `DIVIDE()` | Safe division (no errors) |
| `CALCULATE()` | Changes filter context |
| `SAMEPERIODLASTYEAR()` | Same period 12 months ago |
| `TOTALYTD()` | Year-to-date total |
| `TOTALMTD()` | Month-to-date total |
| `DISTINCTCOUNT()` | Count unique values |
| `FORMAT()` | Format a value as text |
| `CALENDAR()` | Generate a date range table |

---

*Built with Power BI Desktop | Dataset: 2023–2024 Fictional Sales Data*
