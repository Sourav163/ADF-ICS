Alright — let’s fully flesh out your Scenario 5 into a complete, real-world ADF + MySQL project specification with all required sample source data, business rules, and transformation details so you could actually build and practice it end-to-end.


---

Enhanced Problem Statement: MySQL → Data Flow Transformation → Azure Synapse

Business Context

You are working for an e-commerce company that records all customer purchase transactions in a MySQL database.
However, the analytics team uses Azure Synapse Analytics for reporting and dashboards.

Currently, MySQL data:

Contains inconsistent date formats

Allows duplicate transactions due to system retries

Has separate price and quantity fields (needs total calculation)

Needs to filter out test transactions (Order IDs starting with TEST)


Your job:
Use Azure Data Factory to extract, clean, transform, and load this data from MySQL → Azure Synapse using a Mapping Data Flow.


---

Requirements

Step 1 – Source: MySQL Table

Table Name: Transactions

Schema:

Column Name	Data Type	Notes

transaction_id	VARCHAR(50)	May contain duplicates
customer_id	INT	Foreign key to Customers table
product_id	INT	Foreign key to Products table
transaction_date	VARCHAR(50)	Stored inconsistently (YYYY-MM-DD, MM/DD/YYYY)
quantity	INT	Always > 0
unit_price	DECIMAL(10,2)	Price per item
status	VARCHAR(20)	completed, cancelled, pending, or test
created_at	DATETIME	Raw system insert timestamp



---

Sample MySQL Data:

INSERT INTO Transactions VALUES
('TXN001', 101, 501, '2025-07-25', 2, 15.50, 'completed', '2025-07-25 10:15:32'),
('TXN002', 102, 502, '07/25/2025', 1, 200.00, 'completed', '2025-07-25 11:20:10'),
('TXN003', 103, 503, '2025-07-26', 3, 99.99, 'pending', '2025-07-26 09:10:05'),
('TXN002', 102, 502, '07/25/2025', 1, 200.00, 'completed', '2025-07-25 11:20:10'), -- Duplicate
('TEST123', 999, 999, '2025-07-27', 1, 0.00, 'test', '2025-07-27 12:00:00');


---

Step 2 – Transformation Rules

In ADF Mapping Data Flow:

1. Standardize Date Format
Convert transaction_date to yyyy-MM-dd.


2. Remove Duplicates
Keep only the first occurrence of each transaction_id based on created_at ascending.


3. Filter Test Transactions
Exclude records where transaction_id starts with 'TEST' or status = 'test'.


4. Calculate Total Amount
total_amount = quantity * unit_price


5. Ensure Only Completed Transactions
Keep only rows where status = 'completed'.


6. Audit Column
Add column load_date = pipeline execution date (use @utcnow()).




---

Step 3 – Sink: Azure Synapse Table

Table Name: Fact_Transactions

Schema:

Column Name	Data Type

transaction_id	VARCHAR(50)
customer_id	INT
product_id	INT
transaction_date	DATE
quantity	INT
unit_price	DECIMAL(10,2)
total_amount	DECIMAL(12,2)
load_date	DATE



---

Expected Output (After Transformation):

INSERT INTO Fact_Transactions VALUES
('TXN001', 101, 501, '2025-07-25', 2, 15.50, 31.00, '2025-07-28'),
('TXN002', 102, 502, '2025-07-25', 1, 200.00, 200.00, '2025-07-28');


---

ADF Pipeline Design

1. Linked Services:

MySQL (Self-Hosted IR if on-prem, Azure IR if cloud MySQL)

Azure Synapse Analytics



2. Pipeline Steps:

Source: MySQL dataset pointing to Transactions table

Mapping Data Flow:

Standardize date using toDate(transaction_date, 'MM/dd/yyyy') OR toDate(transaction_date, 'yyyy-MM-dd') (handle both formats with conditional logic)

Remove duplicates via Aggregate or Surrogate Key + Window function

Filter rows (status == 'completed' AND NOT startsWith(transaction_id, 'TEST'))

Derived column: total_amount = quantity * unit_price

Derived column: load_date = currentDate()


Sink: Azure Synapse table Fact_Transactions with Upsert or Append mode





---

Skills Tested

MySQL as source in ADF

Complex Mapping Data Flow transformations

Conditional date parsing

Duplicate handling

Data filtering & calculated columns

Loading into Synapse



---

If you want, I can extend this with:

ADF Mapping Data Flow JSON export so you can import it directly

MySQL DDL + sample inserts in .sql format

Synapse table creation script


That way, you can literally run the entire project before your review.

Do you want me to prepare that import-ready ADF pipeline JSON + SQL scripts for this scenario?





Got it ✅ — I’ll give you a larger MySQL source dataset (about 40 rows) for the Transactions table so you can properly test your ADF → Data Flow → Synapse pipeline.

I’ll make the data realistic, with:

Mixed date formats (YYYY-MM-DD, MM/DD/YYYY)

Duplicates

Test transactions

Multiple products & customers

Mixed statuses (completed, pending, cancelled, test)



---

MySQL Source Data – Transactions

CREATE TABLE Transactions (
    transaction_id   VARCHAR(50),
    customer_id      INT,
    product_id       INT,
    transaction_date VARCHAR(50),
    quantity         INT,
    unit_price       DECIMAL(10,2),
    status           VARCHAR(20),
    created_at       DATETIME
);

INSERT INTO Transactions VALUES
-- Valid Completed Transactions
('TXN001', 101, 501, '2025-07-25', 2, 15.50, 'completed', '2025-07-25 10:15:32'),
('TXN002', 102, 502, '07/25/2025', 1, 200.00, 'completed', '2025-07-25 11:20:10'),
('TXN003', 103, 503, '2025-07-26', 3, 99.99, 'completed', '2025-07-26 09:10:05'),
('TXN004', 104, 504, '07/26/2025', 5, 10.00, 'completed', '2025-07-26 12:15:50'),
('TXN005', 105, 505, '2025-07-27', 2, 49.50, 'completed', '2025-07-27 14:25:40'),
('TXN006', 106, 506, '07/27/2025', 4, 5.75, 'completed', '2025-07-27 15:40:22'),
('TXN007', 107, 507, '2025-07-28', 1, 299.99, 'completed', '2025-07-28 09:00:00'),
('TXN008', 108, 508, '07/28/2025', 6, 12.50, 'completed', '2025-07-28 09:15:15'),
('TXN009', 109, 509, '2025-07-29', 2, 120.00, 'completed', '2025-07-29 13:10:10'),
('TXN010', 110, 510, '07/29/2025', 3, 75.25, 'completed', '2025-07-29 17:05:30'),

-- Pending or Cancelled (Should be filtered out)
('TXN011', 111, 511, '2025-07-25', 2, 45.00, 'pending', '2025-07-25 10:30:00'),
('TXN012', 112, 512, '07/25/2025', 1, 89.99, 'cancelled', '2025-07-25 10:35:00'),
('TXN013', 113, 513, '2025-07-26', 2, 10.00, 'pending', '2025-07-26 12:10:00'),
('TXN014', 114, 514, '07/26/2025', 1, 150.00, 'cancelled', '2025-07-26 15:00:00'),

-- Test Transactions (Should be filtered out)
('TEST001', 999, 999, '2025-07-25', 1, 0.00, 'test', '2025-07-25 08:00:00'),
('TEST002', 999, 999, '07/26/2025', 1, 0.00, 'test', '2025-07-26 08:30:00'),

-- Duplicates (Same transaction_id, same data)
('TXN002', 102, 502, '07/25/2025', 1, 200.00, 'completed', '2025-07-25 11:20:10'),
('TXN004', 104, 504, '07/26/2025', 5, 10.00, 'completed', '2025-07-26 12:15:50'),

-- Duplicates with later created_at (Should keep earliest)
('TXN006', 106, 506, '07/27/2025', 4, 5.75, 'completed', '2025-07-27 16:00:00'),
('TXN009', 109, 509, '2025-07-29', 2, 120.00, 'completed', '2025-07-29 14:00:00'),

-- More Valid Completed Transactions
('TXN015', 115, 515, '2025-07-30', 1, 20.00, 'completed', '2025-07-30 10:10:10'),
('TXN016', 116, 516, '07/30/2025', 2, 15.00, 'completed', '2025-07-30 11:20:20'),
('TXN017', 117, 517, '2025-07-31', 4, 8.50, 'completed', '2025-07-31 09:45:45'),
('TXN018', 118, 518, '07/31/2025', 3, 45.00, 'completed', '2025-07-31 14:30:30'),
('TXN019', 119, 519, '2025-08-01', 5, 9.99, 'completed', '2025-08-01 08:15:15'),
('TXN020', 120, 520, '08/01/2025', 6, 6.25, 'completed', '2025-08-01 08:45:45'),

-- Mixed formats, same day
('TXN021', 121, 521, '2025-08-02', 2, 100.00, 'completed', '2025-08-02 10:05:05'),
('TXN022', 122, 522, '08/02/2025', 1, 250.00, 'completed', '2025-08-02 10:15:15'),
('TXN023', 123, 523, '2025-08-03', 3, 15.50, 'completed', '2025-08-03 11:25:25'),
('TXN024', 124, 524, '08/03/2025', 4, 7.75, 'completed', '2025-08-03 12:35:35'),

-- Random duplicates
('TXN023', 123, 523, '2025-08-03', 3, 15.50, 'completed', '2025-08-03 11:25:25'),

-- Late date but earlier created_at (still should be earliest created_at kept)
('TXN025', 125, 525, '08/04/2025', 2, 18.00, 'completed', '2025-08-04 13:45:45'),
('TXN025', 125, 525, '08/04/2025', 2, 18.00, 'completed', '2025-08-04 13:50:00'),

-- Few more valid
('TXN026', 126, 526, '2025-08-05', 1, 200.00, 'completed', '2025-08-05 09:00:00'),
('TXN027', 127, 527, '08/05/2025', 2, 300.00, 'completed', '2025-08-05 09:15:15'),
('TXN028', 128, 528, '2025-08-06', 3, 150.00, 'completed', '2025-08-06 14:20:20'),
('TXN029', 129, 529, '08/06/2025', 4, 125.00, 'completed', '2025-08-06 14:25:25'),
('TXN030', 130, 530, '2025-08-07', 5, 110.00, 'completed', '2025-08-07 16:35:35');


---

Why This Dataset Is Perfect for ADF Testing

Date Normalization — Mixed formats will test conditional date parsing in Mapping Data Flow.

Duplicate Removal — Same transaction_id with same/different created_at will test row ranking logic.

Filtering — Test & non-completed transactions ensure filtering logic works.

Calculations — quantity * unit_price total calculation.

Realistic Size — 40 rows for proper test but still manageable for debugging.



---

If you want, I can now design the Mapping Data Flow transformations step-by-step for this dataset so you can just import and run it in ADF without trial and error.

Do you want me to prepare that ADF Mapping Data Flow design next? That would make this scenario ready to implement in minutes.

