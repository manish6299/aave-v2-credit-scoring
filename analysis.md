# DeFi Wallet Credit Scoring - Analysis

## Introduction

This document details the analysis performed to develop a credit scoring system for DeFi wallets based on their transaction history on the Aave V2 protocol. The goal is to assign a score between 0 and 1000 to each wallet, reflecting their reliability and risk.

## Data Loading and Preprocessing

The transaction data was loaded from a JSON file (`user-wallet-transactions.json`). The nested `actionData` field was flattened to extract relevant information such as `amount`, `assetSymbol`, and `assetPriceUSD`. A `usd_value` column was calculated by multiplying the amount and asset price. Timestamp fields were converted to datetime objects, and the MongoDB-style `_id` was simplified to a string. The original `actionData` column was then dropped.

## Exploratory Data Analysis (EDA)

Initial EDA was performed to understand the data distribution:

*   **Transaction Action Distribution**: A count plot showed the frequency of different transaction actions (`deposit`, `borrow`, `repay`, `redeemunderlying`, `liquidationcall`). This provided insights into the most common interactions with the protocol.

{cell_id: zf4s8fTbJs60, alt-text: Transaction Action Distribution}

*   **USD Value per Transaction**: A histogram of the `usd_value` (with a log scale) revealed the distribution of transaction values. This showed that most transactions have relatively small USD values, with a long tail of larger transactions.

{cell_id: E3QR8gjKKB1c, alt-text: USD Value per Transaction}

*   **Daily Transaction Volume (USD)**: A time series plot of the daily sum of `usd_value` showed the overall activity trend over time.

{cell_id: sU3UCADbKFI6, alt-text: Daily Transaction Volume (USD)}

## Feature Engineering

To capture wallet behavior, the following features were engineered by grouping the data by `userWallet`:

*   `total_txn`: Total number of transactions per wallet.
*   `total_usd`: Total USD value of all transactions per wallet.
*   `mean_txn_usd`: Average USD value per transaction.
*   `std_txn_usd`: Standard deviation of USD value per transaction.
*   `first_tx`: Timestamp of the first transaction.
*   `last_tx`: Timestamp of the last transaction.
*   `active_days`: Number of days between the first and last transaction (inclusive).
*   `tx_per_day`: Average number of transactions per active day.
*   Counts of each transaction action (`deposit`, `borrow`, `liquidationcall`, `redeemunderlying`, `repay`) per wallet.
*   Ratios of each transaction action to the total number of transactions (`_ratio` features).
*   `log_total_usd`: Log10-scaled `total_usd` (with a small constant added to handle zero values).
*   `credit_score`: An initial credit score calculated by scaling `log_total_usd` to a range of 0 to 1000.

## Data Preparation for Modeling

The engineered features (`log_total_usd`, `tx_per_day`, `total_txn`, and all `_ratio` features) were selected as the input (`X`) for the models. The `credit_score` calculated from `log_total_usd` was used as the target variable (`y`). The data was split into training (80%) and testing (20%) sets. Features were scaled using `StandardScaler` to normalize their ranges.

## Model Training and Evaluation

Four regression models were trained and evaluated:

*   Random Forest Regressor
*   Gradient Boosting Regressor
*   XGBoost Regressor
*   Support Vector Regressor (SVR)

The models were evaluated using Mean Squared Error (MSE) and R2 score on the test set. The results are summarized in the table below:

| Model                    | MSE         | R2        |
| :----------------------- | :---------- | :-------- |
| Random Forest            | 0.3850      | 1.0000    |
| Gradient Boosting        | 1.8942      | 1.0000    |
| XGBoost                  | 18.1968     | 0.9997    |
| Support Vector Regressor | 9106.6360   | 0.8457    |

## Model Comparison and Selection

Based on the evaluation metrics, the **Random Forest Regressor** demonstrated the best performance with the lowest MSE (0.3850) and the highest R2 score (1.0000). This indicates that the Random Forest model is highly accurate in predicting the credit score based on the engineered features.

## Prediction and Export

The best-performing model (Random Forest Regressor) was retrained on the entire dataset to utilize all available data for the final predictions. The retrained model was then used to predict the credit scores for all wallets. The resulting wallet addresses and their predicted credit scores were saved to a CSV file named `wallet_credit_scores.csv`.

## Conclusion

This analysis successfully developed a machine learning model to assign credit scores to DeFi wallets based on their Aave V2 transaction history. The Random Forest Regressor proved to be the most effective model for this task. The engineered features provide a comprehensive view of wallet behavior, and the predicted credit scores offer a valuable metric for assessing wallet reliability and risk within the Aave V2 protocol. The exported CSV file provides a clear output of the predicted scores for each wallet.
