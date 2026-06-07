# Methodology for S&P 500 Historical Constituents Data Collection (1975-2026)

This document outlines the systematic approach used to extract, process, and clean the 50-year historical constituents of the S&P 500 index.

## 1. Data Source Identification and Extraction
* **Source:** Wikipedia's "List of S&P 500 companies" (publicly available open-source record).
* **Extraction:** 
  * We utilized Python (`pandas.read_html`) to scrape two primary tables:
    1. **Current Components:** Contains the latest active 500 companies in the index.
    2. **Selected Changes to the List of S&P 500 Components:** Contains historical additions and removals dating back to July 1, 1976.
  * **Initial Script:** We retained the base extraction logic in `generate_sp500_csv.py` for future reproducible extractions.

## 2. Parsing and Merging Spells (Data Consolidation)
* The S&P 500 index undergoes constant changes. A company can be added, removed, and later added again.
* We established an event-driven chronological timeline ("spells"):
  * For every **Addition**, we recorded the company, date added, and reason.
  * For every **Removal**, we matched it with the active addition spell, set the `Date Removed`, and appended the reason.
  * If a company was currently active but added before the detailed historical logs began (i.e., before 1975), its `Date Added` was labeled appropriately.

## 3. Data Cleaning and Formatting
* **Standardizing Dates:** 
  * Removed "Present" for active companies and replaced it with the explicit current date (`2026-06-07`) to facilitate direct programmatic filtering and backtesting.
  * Future addition/removal announcements (e.g., changes scheduled for late June 2026) were filtered out to reflect the *exact* reality of the index as of today.
  * Empty or unspecified addition dates for historical companies were standardized to `pre1975`.
* **Standardizing Sectors (GICS):** 
  * Active companies were populated with their current GICS sectors directly from the Wikipedia "Current" table.
  * Historical (removed) companies inherently lacked sector labels ("Unknown") in the changes log.

## 4. Missing GICS Sector Enrichment (`yfinance`)
* **Automated Retrieval:** To resolve the missing sectors, we wrote a multithreaded Python script leveraging the `yfinance` library. 
* We queried Yahoo Finance for all the "Unknown" historical tickers. This automatically mapped and restored the correct `GICS Sector` for over **133** historical companies.

## 5. Manual Remediation
* **Edge Cases & Delisted Stocks:** A significant subset of companies (totaling **236**) were not easily retrievable via standard ticker querying due to M&A activities, delisting, or ticker changes over the past 30-50 years.
* **Expert Mapping:** The remaining unknown sectors were completely resolved through explicit manual dictionary mappings provided by the user. 
* **Completion:** The dataset achieved 100% GICS Sector mapping for all constituent spells from 1975 to 2026.

## Final Output Structure
The final cleaned data is exported to `s&p500_1975-2026.csv` with the following columns:
- `Symbol`: Ticker symbol
- `Security`: Company name
- `GICS Sector`: Global Industry Classification Standard
- `Date Added`: (YYYY-MM-DD or `pre1975`)
- `Date Removed`: (YYYY-MM-DD)
- `Reason`: Cause of index entry/exit
