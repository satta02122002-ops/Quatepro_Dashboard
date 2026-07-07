# QuotePro Suite — Intelligence Dashboard

Web-based BI dashboard for **ISS Global Forwarding (Contract Logistics)** with two modules:

- **Quotation Dashboard** — sales quotation analytics (revenue, customers, regions, brands, conversion)
- **PMC Dashboard** — Preventive Maintenance Contract analytics (trucks, expiry, customer mix, contract value)

Everything runs in the browser as a single-page app — upload an Excel file, optionally clean the data, then explore KPIs, filters, charts, tables and exports. No backend required.

## Files

| File | Purpose |
|---|---|
| `QuotePro_Suite_Dashboard.html` | The entire application (single deployable file) |
| `index.html` | Redirect to the app for root URL access |
| `_headers` | Cloudflare Pages security headers (CSP allows the CDN libraries) |
| `wrangler.toml` | Cloudflare Pages project configuration |

## Run locally

Open `QuotePro_Suite_Dashboard.html` directly in a browser, or serve the folder:

```bash
npx serve .        # or: python3 -m http.server
```

## Deploy to Cloudflare Pages

```bash
npx wrangler pages deploy .
```

Or connect the repository in the Cloudflare dashboard (framework preset: *None*, build output: `/`).

## Sign in

Browser-local demo accounts (stored hashed in `localStorage` — this is not server authentication):

| Role | Username | Password |
|---|---|---|
| Admin | `Babu` | `Babu@2026` |
| User | `user` | `User@2026` |

Admins can manage accounts and enable per-user TOTP two-factor (👥 Users on the module selector; works with Google Authenticator / Authy).

## Workflow

1. **Login** → optional 2FA step.
2. **Module Selector** — pick Quotation or PMC (theme selector: Dark / Light / Blue / Green).
3. **Upload** — drag & drop `.xlsx` / `.xls` / `.csv`. Sheet auto-selection: Quotation prefers a sheet named like "quote/quotation"; PMC prefers "PMC/Track"; otherwise the first sheet.
4. **Trim & Clean (optional)** — data-quality summary (empty rows, duplicates, whitespace), preview, auto-clean, find/replace per column, and *Smart Replace Similar Names* (merges near-duplicate customer spellings). Skip or Proceed.
5. **Dashboard** — global filters with chips, module tabs, KPIs, Chart.js charts, searchable/sortable tables paginated at 50 rows, and exports.

## Business rules implemented

- **Quotation approval**: a row is *Approved* when "Date Approved By the Customer" holds a valid date, otherwise *Pending*.
- **Report date rule**: approved quotes use the Approved Date; pending quotes fall back to the Quotation Sent Date. Year/Month filters, trends and monthly charts all use this rule (`getReportDate()`).
- **Pending KPI** shows the pending-rate %, and the conversion chart plots both Approved % and Pending %.
- **PMC Contract Status** is derived from Expiry Date (≥ today → Active, < today → Expired) and is what the Contracts table shows — not the Approval Status.
- **PMC Contract Value** comes from the Excel file when a value column exists and rolls up to the KPI and exports.
- Flexible, case-insensitive header matching; Excel serial dates and `dd/mm/yyyy` strings parsed correctly; duplicate date headers resolved by scoring which column actually contains dates; empty rows (no customer + no quote/contract id) skipped. Legacy PMC tracker columns (Quote Ref, Machines, Month & Year) still accepted.

## Exports (admin)

- **Excel workbook** — Quotation: Dashboard KPIs, Quotation Data, Customers, Regions, Brands, Monthly Trend, Sales Users. PMC: KPIs, Monthly, Top Customers, full PMC Data.
- **CSV** of the currently filtered records.
- **PNG** screenshot of the dashboard.
- **Power BI fact table** CSV with typed ISO dates and report year/month columns.

## Stack

Vanilla HTML/JS single file · [Chart.js](https://www.chartjs.org/) · [SheetJS](https://sheetjs.com/) · [html2canvas](https://html2canvas.hertzen.com/) (all via CDN) · state held in memory after upload.
