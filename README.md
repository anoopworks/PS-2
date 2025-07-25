# PS-2
## The G-Drive Link for the Compound Dataset V2: 
https://drive.google.com/file/d/1FAV6GWHduK8o6lFTMRfpj9crqb0mutFe/view?usp=sharing

## Compound V2 Wallet Risk Scoring Model
This project assigns risk scores (0–1000) to wallets interacting with the Compound V2 protocol, using unsupervised machine learning to evaluate on-chain behavior.
Dataset
The dataset comprises 96 JSON files, each containing transaction lists for deposits, withdraws, borrows, repays, and liquidates. Each transaction includes account.id (wallet address), amountUSD (USD value), asset.symbol (e.g., USDC, DAI), and timestamp (Unix timestamp), covering activities of 100 wallet addresses on Compound V2.

## Data Collection
Transaction data collected from the web was provided with 96 JSON files, likely sourced from Compound V2's on-chain records via APIs (e.g., The Graph or Etherscan). These files are processed into a wallet_transactions dictionary, where each wallet ID maps to a list of transactions with keys account.id, amountUSD, asset.symbol, timestamp, and type, enabling structured analysis.

## Feature Selection Rationale & Engineering 
Transactions from 96 JSON files are aggregated by wallet address, counting occurrences and summing USD amounts for deposits, withdraws, borrows, repays, and liquidates. Derived metrics include borrow-to-deposit ratio, repay-to-borrow ratio, liquidation-to-borrow ratio, time span (days between first and last transaction), and unique assets (distinct asset.symbol values). These features capture financial behavior: high borrows or liquidations indicate risk, while frequent deposits and repays signal stability. Ratios quantify borrowing and default tendencies relative to deposits, reflecting leverage and repayment reliability. Time span and unique assets measure experience and diversification, reducing risk. Selected for their relevance to lending protocol dynamics, these features enable robust differentiation of risky (high borrows/liquidations) versus stable (high deposits/repays) wallet profiles.
Compound V2 Wallet Risk Scoring Model Documentation

### Features are engineered from transaction data to capture wallet behavior in the Compound V2 protocol:
Transaction Counts and Amounts: Number of transactions (num_transactions), deposits (num_deposits), borrows (num_borrows), repays (num_repays), withdrawals (num_withdrawals), and liquidations (num_liquidations), along with their total USD values (total_deposit_usd, total_borrow_usd, total_repay_usd, total_withdrawal_usd, total_liquidation_usd). These quantify activity volume and financial exposure.
Derived Ratios: Borrow-to-deposit ratio (total_borrow_usd / total_deposit_usd), repay-to-borrow ratio (total_repay_usd / total_borrow_usd), and liquidation-to-borrow ratio (total_liquidation_usd / total_borrow_usd). These measure leverage, repayment reliability, and default risk.
Temporal and Diversity Metrics: Time span (time_span, days between first and last transaction) and number of unique assets (num_unique_assets, distinct asset.symbol values). These reflect experience and diversification.
Rationale: High borrows or liquidations indicate risky behavior, while frequent deposits and repays suggest stability. Ratios provide normalized risk indicators, and time span/assets capture long-term engagement and portfolio diversity, critical for lending protocol risk assessment.

## Normalization Method
Features are normalized using StandardScaler from scikit-learn, which transforms each feature to have a mean of 0 and a standard deviation of 1. This ensures equal weighting in unsupervised models (Isolation Forest, K-Means) by standardizing scales (e.g., USD amounts vs. counts). The process:

Extract numerical features (excluding wallet ID).
Apply StandardScaler.fit_transform to produce a standardized feature matrix (X).
Standardize the ideal profile for scoring consistency.

## Scoring Logic
The model assigns risk scores (0–1000) using unsupervised learning:

Anomaly Detection: Isolation Forest (10% contamination) identifies anomalous wallets, scoring them 0–200. The formula score = max(0, 200 * (1 + anomaly_scores[i])) maps anomaly scores (-1 to 0) linearly, with more anomalous wallets (lower scores) receiving lower scores (higher risk).
Non-Anomalous Scoring: K-Means (5 clusters) groups non-anomalous wallets. Scores are computed based on Euclidean distance to an ideal profile (maximizing positive features like num_deposits, total_repay_usd, minimizing negative ones like num_liquidations):
Compute distances to the standardized ideal profile.
Normalize distances to [0, 1] using min-max normalization: (distances - min_dist) / (max_dist - min_dist + 1e-6).
Apply exponential decay: score = 700 + 300 * exp(-3 * normalized_distances), mapping scores to 700–1000. Closer profiles (lower distances) score closer to 1000 (lower risk).


Output: Scores are saved to compound_risk_scores.xlsx, with a histogram visualizing anomalous (0–200) and non-anomalous (700–1000) distributions.

This approach ensures robust risk assessment, leveraging financial behavior patterns in the absence of labeled data.

## Risk Indicators Justification
High borrow-to-deposit and liquidation-to-borrow ratios indicate risk, reflecting over-leveraging or default. Frequent deposits and repays signal responsibility, reducing risk. Time span and unique assets denote experience and diversification, lowering risk profiles. These align with lending protocol dynamics.
Setup and Usage

Install dependencies: pip install pandas numpy scikit-learn matplotlib seaborn
Place the 96 JSON files in a data/ directory.
Run generate_wallet_transactions.py to process JSONs into wallet_transactions.json.
Run compound_risk_scoring.py to compute scores:python compound_risk_scoring.py

Outputs: compound_risk_scores.csv (wallet scores) and a histogram.
