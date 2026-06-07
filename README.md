# S&P 500 Survival Analysis

Exploratory Data Analysis on which companies and sectors have survived longest 
in the S&P 500 index from 1975 to present.

## Dataset
- Source: S&P 500 Historical Constituents (Wikipedia + Yahoo Finance)
- Period: 1975–2026
- Total records: ~906 companies

## Research Questions
1. Which sectors have the most companies surviving over 50 years?
2. Which crisis period had the most companies removed?
3. Which sectors have the highest removal rate?

## Data Limitations
- Companies added before 1975 have unknown exact entry dates — years_in_index capped at 51.47 years
- GICS Sector data for some delisted companies filled manually (~251 companies)

## Tools
- Python (pandas, numpy, matplotlib, seaborn, yfinance)
- Google Colab
