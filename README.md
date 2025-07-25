# PS-2
## The G-Drive Link for the Compound Dataset V2: 
https://drive.google.com/file/d/1FAV6GWHduK8o6lFTMRfpj9crqb0mutFe/view?usp=sharing

## Compound V2 Wallet Risk Scoring Model
This project assigns risk scores (0–1000) to wallets interacting with the Compound V2 protocol, using unsupervised machine learning to evaluate on-chain behavior.
Dataset
The dataset comprises 96 JSON files, each containing transaction lists for deposits, withdraws, borrows, repays, and liquidates. Each transaction includes account.id (wallet address), amountUSD (USD value), asset.symbol (e.g., USDC, DAI), and timestamp (Unix timestamp), covering activities of 100 wallet addresses on Compound V2.

## Data Collection
Transaction data was provided as 96 JSON files, likely sourced from Compound V2's on-chain records via APIs (e.g., The Graph or Etherscan). These files are processed into a wallet_transactions dictionary, where each wallet ID maps to a list of transactions with keys account.id, amountUSD, asset.symbol, timestamp, and type, enabling structured analysis.

## Feature Engineering & Selection Rationale
Transactions from 96 JSON files are aggregated by wallet address, counting occurrences and summing USD amounts for deposits, withdraws, borrows, repays, and liquidates. Derived metrics include borrow-to-deposit ratio, repay-to-borrow ratio, liquidation-to-borrow ratio, time span (days between first and last transaction), and unique assets (distinct asset.symbol values). These features capture financial behavior: high borrows or liquidations indicate risk, while frequent deposits and repays signal stability. Ratios quantify borrowing and default tendencies relative to deposits, reflecting leverage and repayment reliability. Time span and unique assets measure experience and diversification, reducing risk. Selected for their relevance to lending protocol dynamics, these features enable robust differentiation of risky (high borrows/liquidations) versus stable (high deposits/repays) wallet profiles.

## Scoring Method
Isolation Forest (10% contamination) identifies anomalous wallets, scoring them 0–200 (higher risk) using linear mapping of anomaly scores. Non-anomalous wallets are clustered with K-Means (5 clusters) and scored 600–1000 based on Euclidean distance to an ideal profile (high deposits/repays, low borrows/liquidations), using exponential decay for even distribution.

## Risk Indicators Justification
High borrow-to-deposit and liquidation-to-borrow ratios indicate risk, reflecting over-leveraging or default. Frequent deposits and repays signal responsibility, reducing risk. Time span and unique assets denote experience and diversification, lowering risk profiles. These align with lending protocol dynamics.
Setup and Usage

Install dependencies: pip install pandas numpy scikit-learn matplotlib seaborn
Place the 96 JSON files in a data/ directory.
Run generate_wallet_transactions.py to process JSONs into wallet_transactions.json.
Run compound_risk_scoring.py to compute scores:python compound_risk_scoring.py

Outputs: compound_risk_scores.xlsx (wallet scores) and a histogram.
