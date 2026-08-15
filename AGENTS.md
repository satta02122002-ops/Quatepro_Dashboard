# QuotePro Suite

Single-file, browser-only BI dashboard app for ISS Global Forwarding. See `README.md` for the product overview, workflow, business rules, and demo credentials.

## Cursor Cloud specific instructions

### What this is
- A static, client-side app. The entire application lives in `QuotePro_Suite_Dashboard.html` (`index.html` just redirects to it). There is **no backend, no database, no package manager, and no build step**. State is held in memory after upload plus browser `localStorage` (demo auth/theme).
- Third-party libraries (Chart.js, SheetJS/xlsx, html2canvas) are loaded at runtime from CDNs (`cdn.jsdelivr.net`, `cdn.sheetjs.com`). **Internet access to those CDNs is required** for file parsing, charts, and PNG/Excel export to work.

### Run (dev)
- Serve the repo root statically, then open the app in a browser:
  - `python3 -m http.server 8000` (built-in, no install) → http://localhost:8000/
  - or `npx serve .` (port 3000, needs network to fetch the `serve` package)
- The `_headers` CSP file is only applied by Cloudflare Pages, not by local static servers — this is expected and does not affect local dev.
- A harmless `favicon.ico 404` appears in the console locally; it has no functional impact.

### Lint / test / build
- There is **no lint config, no automated test suite, and no build step** in this repo. "Building" is not applicable; deployment is a static upload to Cloudflare Pages (`npx wrangler pages deploy .`).
- Verify changes by manual testing in the browser: log in (see `README.md` demo accounts, e.g. `Babu` / `Babu@2026`), pick a module, and upload a spreadsheet.

### Test data
- The dashboard is empty until a `.xlsx`/`.xls`/`.csv` is uploaded. Headers are matched case-insensitively (see `findCol`/`findCols`). For the **Quotation** module, useful columns include: `Quotation Number`, `Customer Name`, `Emirate`, `Scope of Work`, `Brand`, `Model`, `Quotation Type`, `Total Quotation Value`, `Sales Person`, `Quotation Sent Date`, `Date Approved By the Customer`, `Enquiry Date`. A row counts as *Approved* only when `Date Approved By the Customer` holds a valid date. The **PMC** module prefers a sheet named like "PMC"/"Track" and derives Contract Status from `Expiry Date`.
