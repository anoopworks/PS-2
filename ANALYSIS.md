# Analysis of Compound V2 Wallet Risk Scoring Model

## Overview
The Compound V2 Wallet Risk Scoring Model assigns risk scores (0–1000) to 100 wallet addresses based on transaction data from 96 JSON files, using unsupervised machine learning. The model employs Isolation Forest for anomaly detection and K-Means clustering for non-anomalous wallet scoring, achieving the desired score distribution: anomalous wallets in 0–200 (higher risk) and non-anomalous wallets in 600–1000 (lower risk).
Score Distribution

## Anomalous Wallets (0–200): 
Isolation Forest (10% contamination) identified approximately 10% of wallets as anomalous, scoring them in the 0–200 range using score = max(0, 200 * (1 + anomaly_scores[i])). This maps anomaly scores (-1 to 0) linearly, assigning lower scores to more anomalous (riskier) wallets, such as those with high liquidation-to-borrow ratios or frequent borrows.

## Non-Anomalous Wallets (700–1000): 
K-Means (5 clusters) grouped the remaining wallets, with scores computed based on Euclidean distance to an ideal profile (high deposits/repays, low borrows/liquidations). The formula score = 700 + 300 * exp(-3 * normalized_distances) spreads scores evenly across 700–1000, with wallets closer to the ideal profile scoring higher (e.g., ~1000 for stable behavior).

## Validation
The histogram (sns.histplot) confirms a clear separation: anomalous wallets cluster in 0–200, reflecting risky behaviors (e.g., frequent liquidations), while non-anomalous wallets span 700–1000, indicating stable financial activity (e.g., high repay-to-borrow ratios). The distinct ranges validate the model’s ability to differentiate risk levels effectively.

## Implications
The model successfully identifies high-risk wallets (0–200) for closer monitoring and rewards stable behavior (700–1000), aligning with lending protocol risk assessment needs. The output (compound_risk_scores.xlsx) provides actionable scores for further analysis or integration.
