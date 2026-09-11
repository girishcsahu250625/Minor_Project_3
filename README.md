# Minor_Project_3
RedFlag Fraud Detection Dataset: SQL setup for a database containing 200,000 synthetic transactions from PayFast, a fictional Indian payment aggregator (Jan–Jun 2024). Includes table schemas, indexes, and bulk insert operations for analysis.

RedFlag: Fraud & Transaction Analysis with SQL

Hey there! Welcome to the RedFlag repository.
This project is a hands-on SQL analysis of a synthetic transactional dataset modeled after PayFast—a fictional payment gateway based in India. The goal here was to simulate real-world data engineering and analytics tasks: digging through large-scale transaction logs to catch potential fraud, spot failure patterns across payment modes, and extract actionable insights.

 What's inside?
The dataset simulates 200,000 transactions spanning six months (January 2024 to June 2024). It mimics the kind of data you'd see at a high-volume payment processor, covering major Indian cities and standard payment methods (UPI, CARD, NETBANKING, and WALLET).

Here’s a quick snapshot of what the core transactions table looks like:
txn_id: Unique ID for every transaction (Primary Key)
user_id / merchant_id: Indexed identifiers to track user and merchant activity
amount: Transaction value in INR
txn_time: Timestamp of the payment
status: Outcome (SUCCESS, FAILED, etc.)
payment_mode: The channel used (UPI, CARD, etc.)
city: Location associated with the user/transaction
txn_type: Classification (DEBIT vs REFUND)

🛠️ Getting Started & Import Guide
If you want to pull this into your local setup and run your own queries, follow these steps.

Prerequisites
MySQL Server (8.0 or newer recommended)
MySQL Workbench or your preferred SQL client/CLI

Setting up the Database
Heads up on importing: Because the script (Girish_Minor_Project_3_redflag_transactions_2.sql) contains 200,000 insert rows, MySQL Workbench might hit a timeout if left on default settings.

Open MySQL Workbench.
Click File > Open SQL Script and pick the .sql file. (If it warns you about file size, just click Continue Anyway).
To avoid mid-import crashes, bump up your timeout:
Go to Edit > Preferences > SQL Editor.
Change DBMS connection read timeout to 600 seconds.
Hit Ctrl + Shift + Enter (or Cmd + Shift + Enter on Mac) to execute.
Give it about a minute to finish loading the tables and indexes.
Once it's done, you can verify everything loaded properly with:
SQL:
USE redflag;
SELECT COUNT(*) FROM transactions;
-- Should return ~200,000

 Key Areas Analyzed
Here are a few things I focused on while querying the dataset:
Fraud & Anomaly Detection: Finding accounts pumping out unusually high transaction volumes within tight timeframes, or high-value transactions (> ₹30,000) popping up unexpectedly.
Failure Analysis: Breaking down which payment gateways/modes see the highest failure rates and why.
Geographic Trends: Tracking volume distribution across top metro cities like Delhi, Mumbai, Bengaluru, and Chennai.
Refund Dynamics: Measuring the balance between debits and refunds to flag potential merchant-side anomalies.

Example Queries
Here are a couple of quick queries to give you a feel for the dataset:
Catching High-Value Successful Payments
SQL:
SELECT txn_id, user_id, amount, payment_mode, city, txn_time 
FROM redflag.transactions 
WHERE amount > 30000 AND status = 'SUCCESS'
ORDER BY amount DESC;

Checking Failure Rates Across Payment Modes
SQL:
SELECT 
    payment_mode,
    COUNT(*) AS total_txns,
    SUM(CASE WHEN status = 'SUCCESS' THEN 1 ELSE 0 END) AS successful_txns,
    SUM(CASE WHEN status = 'FAILED' THEN 1 ELSE 0 END) AS failed_txns,
    ROUND((SUM(CASE WHEN status = 'FAILED' THEN 1 ELSE 0 END) * 100.0 / COUNT(*)), 2) AS failure_rate_pct
FROM redflag.transactions
GROUP BY payment_mode
ORDER BY total_txns DESC;
