# Amgen Inc. — Financial Intelligence Dashboard

A single-file, interactive dashboard over Amgen's (NASDAQ: **AMGN**) reported financials, covering the
**P&L, Balance Sheet and Free Cash Flow** with one-click drill-downs, a five-year trend layer,
a KPI library, statistical anomaly flags, product-level analysis, a working-capital / cash-conversion
cycle view, a valuation framework, an editable scenario model (base / bull / bear), an **Item 1A
risk-factor → working-capital analysis**, tailored read-outs for **seven stakeholder personas**, and a
**persona-gated chatbot**.

Open `index.html` in any browser — no build step, no server, no dependencies beyond a CDN chart library.

---

## What's inside

| File | Purpose |
|------|---------|
| `index.html` | The dashboard. Self-contained (HTML + CSS + JS, data embedded). Just open it. |
| `data.json` | The full dataset (statements, KPIs, anomalies, products, WC, DuPont, debt wall, market, scenarios). |
| `data_annual.csv` | FY2021–FY2025 P&L / Balance Sheet / Cash Flow line items, for transparency. |
| `data_products_q1fy26.csv` | Q1 FY2026 product-level sales (US / ex-US / YoY). |
| `build_data.py` | Rebuilds `data.json` from the verified source figures; runs KPI, WC, DuPont, anomaly, debt-wall and scenario-preset engines. |
| `build_html.py` | Regenerates `index.html` by embedding `data.json`, rendering all stakeholder personas in Python, and embedding the risk-factor dataset + chatbot logic. |

### Dashboard tabs (10)
- **Landing** — all three statements on one page, each as a compact table with **columns for FY21 → FY25 + Q1'26** and a **5-year sparkline** on the right of every line item. Click expandable rows to drill into composition.
- **5-Yr Trends** — revenue & net income, margin profile, FCF vs dividends, leverage, R&D intensity, balance-sheet composition.
- **KPI Library** — 12 KPIs from the P&L, balance sheet and FCF, each with a 5-year sparkline.
- **Working Capital + WoCA** — DSO / DIO / DPO / CCC trend, operating cycle, DuPont decomposition of ROE, plus a **WoCA peer-comparison block** (Pfizer, Merck, AbbVie, Lilly, BMY, Gilead, Regeneron, Vertex + industry median).
- **Anomalies** — statement-line z-score flags (FY21→FY25) and product-level flags (Q1'26).
- **Risk Analysis** — **Item 1A Risk Factors → Working Capital impact.** 12 risk factors from Amgen's FY2024 10-K mapped to DSO / DIO / DPO / CCC, with: a risk-portfolio KPI strip, a severity-vs-WC-impact heatmap, per-component impact charts, a filterable detailed assessment (Critical / High / Medium / Low), CCC stress-scenario modeling, and a **quantified WC improvement roadmap** (~$1.2–2.0B release opportunity).
- **Product Drill-down** — Q1 FY2026 sales by product, US/ex-US, and therapeutic area.
- **Valuation** — EV bridge, debt-maturity wall (indicative), stylised SOTP, dividend-discount sensitivity, and an EV/FCF sensitivity grid.
- **Scenarios** — *interactive* base / bull / bear presets with editable sliders. Live 3-year P&L projection.
- **Stakeholder Views** — **seven** lenses, all generated in Python: Business CFO, Director — FP&A, Director of Business (BU), Treasurer, Strategy / Corp Dev, Market Leads, Equity Analyst.

### Chatbot (persona-gated)
The floating **💬 Ask Amgen** button (bottom-right) opens a chat assistant. It **asks the user to pick a
persona first** (CFO, FP&A, Commercial, Treasurer, Strategy, Market Lead, Analyst) and only then answers,
tailoring each answer to that lens. It covers revenue, FCF, net income, debt/leverage, dividends, cash,
margins, working capital, valuation, products, Horizon, peers, and **risk factors → working-capital
impact** (with persona-specific framing — e.g. the CFO lens surfaces the IRS exposure and de-levering,
the Treasurer lens surfaces wholesaler concentration and liquidity).

---

## Risk Analysis — methodology

Source: **Amgen Form 10-K, fiscal year ended December 31, 2024 (filed Feb 14, 2025), Item 1A Risk
Factors** (SEC EDGAR). Each of the 12 risk factors is assessed for:

1. Probability / severity tier (Critical, High, Medium, Low)
2. Financial magnitude ($M of working-capital pressure)
3. Direction of impact on each WC component (DSO ↑/↓, DIO ↑/↓, DPO ↑/↓, cash flow)
4. Mitigation actions, each with an expected outcome and timeline
5. Aggregate CCC stress range and the net WC-release opportunity if mitigations are executed

The roadmap targets a net **−18d DSO / −50d DIO / +30d DPO** swing → **$1.05–1.6B** cash release,
moving CCC from **274d** toward **230–240d** (peer median ≈ 183d). Figures are illustrative and assume
independent risk materializations.

---

## Architecture — Python is the source of truth

All financial data, KPI computations, anomaly detection, the risk-factor dataset, **and stakeholder
insight HTML** are generated in Python (`build_data.py` → `build_html.py`). The output is a single
self-contained `index.html`. JavaScript handles only client-side interactivity (tab switching, chart
rendering, drill-down toggles, chatbot) and contains **no hardcoded numbers or insight text** — those all
come from Python at build time.

```
build_data.py   ← verified Amgen financials → data.json (KPIs, anomalies, products)
      ↓
build_html.py   ← reads data.json, computes personas + risk dataset in Python, injects into HTML template
      ↓
index.html      ← single self-contained dashboard (open in any browser)
```

```bash
python3 build_data.py     # regenerates data.json (+ prints anomalies & KPIs)
python3 build_html.py     # regenerates index.html with the embedded data
```

Edit the figures at the top of `build_data.py` (or swap in your own CSV/GL feed) and re-run both scripts.

---

## Update an existing GitHub repo

These files are drop-in replacements. From your local clone:

```bash
cd path/to/your-local-clone
git pull origin main

# overwrite the tracked files with the versions from this folder
#   index.html, build_html.py, build_data.py, data.json,
#   data_annual.csv, data_products_q1fy26.csv, README.md

git add index.html build_html.py build_data.py data.json \
        data_annual.csv data_products_q1fy26.csv README.md
git commit -m "Add Item 1A risk-factor to working-capital analysis tab and persona-gated chatbot"
git push origin main
```

If GitHub Pages is already enabled (Settings → Pages → Deploy from a branch → `main` / root), the live
site redeploys automatically within ~1 minute at `https://YOUR-USERNAME.github.io/REPO-NAME/`.

> If `git pull` flags a conflict in `index.html`, just take this version wholesale — it is a generated
> artifact, so there is no value in hand-merging. Overwrite it (or re-run `python3 build_html.py`) and commit.

### First-time push (new repo)

```bash
cd amgen-dashboard
git init
git add .
git commit -m "Amgen financial intelligence dashboard"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/REPO-NAME.git
git push -u origin main
```

---

## Sources
- Amgen **Form 10-K**, fiscal year ended **Dec 31, 2024** (SEC EDGAR) — Item 1A Risk Factors.
- Amgen Form **10-Q**, period ended **Mar 31, 2026** (SEC EDGAR).
- Amgen **Q4 / FY2025** results (Feb 3, 2026) and **Q4 / FY2024** results (Feb 4, 2025).
- Amgen **2023 Form 10-K** and Letter to Shareholders; FY2021–FY2022 reported actuals.

All figures in USD millions unless noted. Headline revenue, net income, EPS, FCF and balance-sheet
totals are reported actuals; a few FY2024–FY2025 expense sub-lines are modeled from reported growth
rates/margins where the granular line was not in the retrieved source text. Risk-factor mappings and
WC-impact estimates are analytical and illustrative. **Not investment advice** — Amgen's own filings govern.
