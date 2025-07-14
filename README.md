# DeFi Wallet Credit Scoring

## Project Overview

This project aims to develop a credit scoring system for decentralized finance (DeFi) wallets based on their historical transaction behavior on the Aave V2 protocol. By analyzing various transaction actions such as deposits, borrows, repays, redemptions, and liquidations, the project engineers features to capture wallet activity patterns. These features are then used to train machine learning models that predict a credit score between 0 and 1000, indicating the reliability and risk level of a wallet.

## Data

The dataset used in this project consists of 100,000 raw, transaction-level records from the Aave V2 protocol. Each record details a specific interaction of a wallet with the protocol, including the action type, amount, asset involved, and timestamps.

## Methodology

The project follows these key steps:

1.  **Data Loading and Preprocessing**: The raw JSON data is loaded into a pandas DataFrame. Nested fields are flattened, and timestamp data is converted to datetime objects.
2.  **Exploratory Data Analysis (EDA)**: Initial analysis is performed to understand the distribution of transaction actions and the USD value of transactions.
3.  **Feature Engineering**: Features are engineered from the transaction data to represent different aspects of wallet behavior. These include:
    *   Total number of transactions
    *   Total USD value of transactions
    *   Average and standard deviation of transaction USD value
    *   First and last transaction timestamps
    *   Number of active days
    *   Transactions per day
    *   Counts and ratios of specific transaction actions (deposit, borrow, repay, redeemUnderlying, liquidationCall)
    *   An initial credit score based on the log-scaled total USD value.
4.  **Data Preparation for Modeling**: Relevant features are selected, and the data is split into training and testing sets. Features are scaled using `StandardScaler`.
5.  **Model Training and Evaluation**: Several regression models (Random Forest, Gradient Boosting, XGBoost, and Support Vector Regressor) are trained on the engineered features. Their performance is evaluated using Mean Squared Error (MSE) and R-squared (R2) score.
6.  **Model Selection**: The model with the best performance metrics (highest R2 and lowest MSE) is selected as the final model.
7.  **Score Prediction and Export**: The best-performing model is used to predict credit scores for all wallets in the dataset. The wallet addresses and their predicted scores are exported to a CSV file (`wallet_credit_scores.csv`).

## Credit Score Logic

The credit score is a value between 0 and 1000, with higher scores indicating more reliable wallet behavior. The initial credit score is based on the log10-scaled total USD value of transactions, mapping the minimum log-scaled value to 0 and the maximum to 1000. This initial score is then refined by the best-performing machine learning model (Random Forest Regressor), which considers additional behavioral features engineered from the transaction data.

The key features influencing the credit score include:

*   **Log-scaled Total USD Value**: A primary driver, reflecting the overall volume of activity.
*   **Transactions per Day**: Indicates the frequency of transactions.
*   **Total Transactions**: The overall number of interactions with the protocol.
*   **Action Ratios**: The proportion of different transaction types (deposit, borrow, repay, redeemUnderlying, liquidationCall), which can highlight specific behavioral patterns (e.g., high borrow-to-repay ratio might indicate higher risk).

The Random Forest Regressor, through its training process, learns the complex relationships between these features and the initial credit score, producing a refined and more comprehensive predicted credit score.

## Output

The final output of the project is a CSV file named `wallet_credit_scores.csv`. This file contains two columns:

*   `userWallet`: The unique address of the DeFi wallet.
*   `predicted_credit_score`: The credit score predicted by the best-performing model for the corresponding wallet, ranging from 0 to 1000.

## Extensibility

This framework can be extended by:

*   Including data from other DeFi protocols to create a more comprehensive view of a wallet's cross-protocol behavior.
*   Incorporating more advanced feature engineering techniques, such as time-series analysis of transaction patterns.
*   Experimenting with different machine learning models and hyperparameter tuning to further improve prediction accuracy.
*   Adding external data sources, such as on-chain analytics related to wallet age, associated addresses, or participation in other blockchain activities.
