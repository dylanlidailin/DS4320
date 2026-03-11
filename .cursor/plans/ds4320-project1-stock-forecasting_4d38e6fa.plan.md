---
name: ds4320-project1-stock-forecasting
overview: Design a DS 4320 Project 1 around forecasting stock prices using a relational dataset (D1), with a clear problem definition, relational schema, pipeline, and press-release-style communication.
todos:
  - id: pick-stocks
    content: Choose basket of 5–10 related stocks and date range for forecasting problem
    status: pending
  - id: design-relational-schema
    content: Finalize tables (`tickers`, `daily_prices`, `daily_features`, `calendar`) and key relationships
    status: pending
  - id: raw-to-d1-pipeline
    content: Create data creation notebook to ingest raw price data and output normalized CSV tables
    status: pending
  - id: metadata-and-er
    content: Draft ER diagram, table summary, and full data dictionary with uncertainty notes
    status: pending
  - id: duckdb-pipeline
    content: "Implement DuckDB-based analysis notebook: load CSVs, join tables, build modeling dataset"
    status: pending
  - id: model-and-metrics
    content: Train simple next-day forecasting model, compute metrics, and interpret results
    status: pending
  - id: visualization-and-press
    content: Create publication-quality chart and write press-release markdown
    status: pending
  - id: repo-readme-structure
    content: Organize GitHub repo and README to match rubric (links, DOI, license, background readings)
    status: pending
isProject: false
---

# DS 4320 Project 1 Plan: Stock Price Forecasting (Relational Model)

## 1. Refine the Problem

- **General problem**: Forecasting stock prices.
- **Specific problem**: Forecast short-horizon daily price movements for a small basket of related stocks (e.g., 5–10 U.S. tech stocks), using a relational dataset that combines daily OHLCV prices with derived features and basic reference metadata.
- **Target output**: A simple model that predicts next-day return or direction (up/down) and a press release explaining the findings to a non-technical audience.

## 2. Raw Data and D1 (Relational Dataset) Design

- **Raw data source (D0)**:
  - Use freely available historical daily data (open, high, low, close, volume, adjusted close) for 5–10 stocks from a consistent market (e.g., NASDAQ/NYSE tech stocks).
  - Implementation options (you can choose later):
    - Use `yfinance`/Yahoo Finance in a notebook to download prices; or
    - Download CSVs manually from a site like Yahoo Finance and store them in UVA OneDrive.
- **Relational dataset D1** (what the project must produce and store as CSVs in OneDrive):
  - **Tables (logical level)**:
    - `tickers`:
      - One row per stock; columns: `ticker`, `name`, `sector`, `exchange`, optional `currency`.
    - `daily_prices`:
      - One row per ticker per date; columns: `ticker`, `date`, `open`, `high`, `low`, `close`, `adj_close`, `volume`.
    - `daily_features`:
      - One row per ticker per date; engineered columns such as `return_1d`, `return_5d`, `volatility_5d`, moving averages, etc.
    - `calendar` (optional but helps relational model):
      - One row per date; flags like `is_trading_day`, `day_of_week`, `month`, and holiday indicators.
  - **Keys & relationships**:
    - `tickers.ticker` is primary key.
    - `daily_prices.(ticker, date)` is primary key; foreign key `ticker` → `tickers.ticker`.
    - `daily_features.(ticker, date)` primary key; foreign key to `daily_prices.(ticker, date)`.
    - `daily_prices.date` and `daily_features.date` optionally reference `calendar.date`.
  - **Storage**:
    - Export each table as a separate CSV stored in UVA OneDrive and linked in the repo README.
    - Optionally create Parquet versions for the “scale” rubric item.

## 3. Data Creation & Provenance Section

- **Provenance narrative**:
  - Explain which API or site you used, the date/time range selected (e.g., last 5 years), the ticker list, and any filters (e.g., excluding days with missing data).
  - Discuss how you derived D1 from D0: cleaning steps, alignment across tickers, handling missing trading days, and computing features.
- **Code organization**:
  - One notebook (or Python script) dedicated to raw data download/ingestion and writing normalized tables as CSV.
  - Include a small “Code Table” in markdown listing each script/notebook, its purpose, and links.
- **Bias & uncertainty**:
  - Briefly discuss biases: survivorship bias (choosing currently listed stocks), look-ahead bias (must avoid using future data when computing features), and market regime changes.
  - Explain how uncertainty in numerical features is quantified (e.g., via rolling standard deviations, confidence intervals on returns) and how this appears in the data dictionary.

## 4. Metadata & ER Diagram

- **Schema section**:
  - Draw an ER diagram at the logical level showing entities (`tickers`, `daily_prices`, `daily_features`, `calendar`) and key relationships.
  - Emphasize that D1 is “constructed using the relational model” with at least 4 tables.
- **Data table overview**:
  - Markdown table listing each table name, 1–2 sentence description, and link to the CSV.
- **Data dictionary**:
  - For every column in every table, define: name, type, description, example value.
  - For numerical features (like returns and volatility), add how uncertainty is quantified (e.g., moving-window variance, range, or confidence intervals used later).

## 5. Problem Solution Pipeline (Notebook + Markdown)

- **Loading & preparation**:
  - Use DuckDB in Python to read CSVs from OneDrive into a relational database (as required by the rubric).
  - Show SQL queries that:
    - Join `daily_prices` and `daily_features` for modeling.
    - Filter dates and tickers to create a modeling dataset.
    - Create a train/test split by date (e.g., first 80% of time for training, last 20% for testing).
- **Modeling**:
  - Start with a baseline model that predicts **next-day direction** (up/down) or **next-day return**:
    - E.g., logistic regression on sign of `return_1d` using lagged features and moving averages; or
    - Linear regression for `return_1d` using features like `return_1d` lagged, `volatility_5d`, `MA_5`, etc.
  - Clearly describe feature selection and how you avoid look-ahead bias.
- **Analysis rationale**:
  - Explain why your features are reasonable signals for short-term movement and why you chose the particular model (simplicity, interpretability, matches course content).
  - Discuss model performance metrics (accuracy for classification, RMSE/MAE for regression) and practical interpretation.
- **Visualization**:
  - Create at least one publication-quality chart, for example:
    - Actual vs. predicted cumulative returns for a chosen ticker over the test period; or
    - Accuracy over time; or
    - Distribution of predicted vs. actual returns.
  - Explain why the chart design (axes, colors, annotations) supports your message.

## 6. Press Release

- **Separate markdown file** with:
  - **Headline (L1)**: Concise, attention-grabbing summary of your key finding (e.g., “Simple Signals Can Predict Tech Stock Moves One Day Ahead, But With Limits”).
  - **Hook (L2)**: Motivation—why short-term forecasting for a basket of tech stocks matters (e.g., for retail investors or risk management).
  - **Problem Statement (L2)**: Current challenge (noisy prices, difficulty forecasting) and your specific problem.
  - **Solution Description (L2)**: High-level explanation of your relational dataset and forecasting approach without heavy math.
  - **Chart (L2)**: Embed or link the main visualization that supports your conclusion.

## 7. Repository & README Structure

- **Top-level structure**:
  - `README.md` with:
    - L1 title `DS 4320 Project 1: <Your Stock Forecasting Title>`.
    - Executive summary paragraph.
    - Name, NetID, DOI, links to press release, data folder, pipeline notebook, and license.
  - Folders:
    - `data/` (or similar) – with subfolder(s) for CSVs and maybe Parquet; note that actual data live in UVA OneDrive and are linked.
    - `notebooks/` – data creation notebook, pipeline notebook.
    - `press_release/` – markdown for press release.
    - `background/` – PDFs or links for domain readings.
- **Coding standards**:
  - All code in Python, SQL, Markdown.
  - Inline comments for each function; basic error handling and logging in Python where appropriate.

## 8. How the Final Project Will Look

- A **GitHub repo** that a reviewer can open and immediately see:
  - A clear README following the rubric’s header structure.
  - Links to:
    - A OneDrive folder with at least 4 relational CSV tables for stock data.
    - A pipeline notebook (plus markdown export) that loads those tables with DuckDB, prepares features, trains a model, and visualizes results.
    - A press-release markdown file that tells the story in plain language.
  - A metadata section (ER diagram, data dictionary, bias and uncertainty discussion) documenting D1 as a carefully designed relational dataset for stock forecasting.

