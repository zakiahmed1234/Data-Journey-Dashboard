
waafir architecture

Built 20 complex financial sql metrics (DPD, LTV, etc)

Ingestion layer uses FastAPI and Pydantic for data validation and input onto s3

aws sqs to upload data

data stored as partitioned parquet files on s3 optimised for high speed reads using a hive layout

s3 is used to store data cheaply and securely (silod for different users)

we employ a hybrid engine combining athena's scale with duckdb's in memory.

in particular duckdb is used for complex loan state and amortization

athena is used for simple queries

1) what did i build? Why is it important?
    - Calculates lending metrics (examples) based on daily loan tape inputs

    - Allows lenders to gauge their risk profile in real time and make decisions quickly
    
    - enables credit risk monitoring and ability to detect early delinquency signals

    - portfolio health tracking

    - reconstructs loan state over time
    - 100k+ loans, other metrics

2) 

I want to input todays new information

I have new loans i gave out and they have meaningful statistical different. so maybe a lot of my loans defaulted and my graph should change

and maybe it is heavily distributed in one area

1) i want to see a snippet of what the new data looks like

2) i want to see how the new data changes my graphs

blocky slider

fix 3, make things proportional


Lender A1B submits daily loan data, like new loans, new payments, writeoffs.

Loan data is partitioned by lender and date of entry for ease of use


Title: Top right
Real-Time Credit Risk & Lending Analytics Platform

underneath:
 - Turns daily loan data (payments, writeoffs)
 - Into financial metrics (DPD, LTV, risk profiles)
 - Shown as real time dashboards (Cohort based, cross sectional)
 - Improves risk assessment and decision making 
 - Has helped 10+ commercial banking clients supporting portfolios of 100k+ loans


1) Daily data upload

Lender_A1B submits his daily loan data for analysis through our API. 

( add more data to the table, change the name)


2) data stored in fat table

Data is stored in global table partitioned by lender and ingestion date.

This enables partition pruning for fast per-lender time-range queries.


data is silod off!!!


(add more data to table, maybe put arrows showing our privlidged lender, change name)

4) Performing Amortization calculations

- Outstanding balances evolve based upon interest accrual, writeoffs, late fees, etc.
- Each month's balance is dependent on previous month, following a recursive update rule (insert equation)
- DuckDB is used for complex recursive calculations, leveraging fast in-memory, iterative execution


new 
- Loan-level outstanding balances for today are computed for all loans withing a single lender's portfolio, and are stored in a "loan-state matrix", which represents the performance of the entire portfolio (highlight that term).


- This enables real-time analysis of delinquency behaviour, including late payments, DPD trajectories, and risk assessments


( change name, add labels to partial payment, missing payment, catchup payment stuff, change the table to add the loans one by one)
------



5) athena layer 

Athena is used as a serverless SQL layer over the precomputed loan-state matrix in S3, enabling scalable cohort and risk analytics without managing a database cluster

This cleanly separates heavy per-loan computation (DuckDB) from large-scale aggregation queries, improving scalability and simplifying the overall architecture

make a tree, inital node (loan state matrix)

leading into 4 or 5 aggregation metrics


6) dashboard 

We output real time dashboards that visualise Athena-aggregated cohort and risk metrics.

Supports portfolio monitoring, including delinquency tracking, cohort performance, and risk segmentation.

Used by 10+ commercial banking clients supporting portfolios of up to 100k+ loans.
