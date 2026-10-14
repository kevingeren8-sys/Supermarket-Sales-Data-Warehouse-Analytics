# Supermarket Sales Data Warehouse

An end-to-end data pipeline for 1,000 supermarket sales records: raw CSV, transformed with Pentaho Data Integration, loaded into a MySQL star-schema warehouse, and analyzed in Power BI.

## Pipeline

```
SuperMarket Analysis.csv  →  Pentaho PDI (ETL)  →  MySQL star schema  →  Power BI
```

| Layer | What it contains |
|---|---|
| Source | `SuperMarket Analysis.csv`, 1,000 sales records from three branches (Yangon, Mandalay, Naypyitaw) |
| ETL | One Pentaho transformation per table (`CustomerDimension.ktr`, `LocationDimension.ktr`, `ProductDimension.ktr`, `TimeDimension.ktr`, `SalesFact.ktr`) and a job, `Orchestration.kjb`, that runs them in order |
| Warehouse | Four dimension tables (customer, location, product, time) and one sales fact table. Export in `olap.sql` |
| Analytics | `VisualizationNew.pbix`, a Power BI report connected to the warehouse |

## Results from the source data

- Total sales: 322,966.75
- Gross income: 15,379.37
- Average customer rating: 6.97 / 10
- Top product line by revenue: Food and beverages

## My role

I worked on the ETL: transforming the raw CSV into dimension tables (customer, location, product, time) and a sales fact table, so the data was tidy enough to analyze and visualize. This was a team project with five contributors.

## Known issues

These are things I found when re-checking the project, and have not fixed yet.

- **Duplicated fact rows in the Power BI model.** The fact table in `VisualizationNew.pbix` has 3,380 rows for only 1,000 unique invoice IDs, so totals in that report are inflated (about 1.08M instead of 322,967). In `SalesFact.ktr` the table output step has "Truncate table" turned off, so running the job more than once appends the same rows again. I believe that is the cause, but I haven't confirmed it.
- **Header row in the product dimension.** The product dimension contains a row with the value "Product line", which is the CSV header loaded as data.
- **Incomplete SQL export.** `olap.sql` contains only 381 fact rows, so it is not a full snapshot of the warehouse.

**Planned fix:** enable "Truncate table" (or deduplicate on invoice ID) in the fact load, skip the header row in the product dimension, then reload and regenerate `olap.sql` and the Power BI report.

## Web dashboard

A web version of the dashboard, with figures calculated from the 1,000 unique invoices, is part of my portfolio: https://kgeren-portfolio.vercel.app/#
