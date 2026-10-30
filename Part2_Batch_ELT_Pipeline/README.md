## Task 1 – Extract the Source Data

For this task, I used Pandas to read the original `food_orders.csv` dataset. I configured the batch size as 100 records and extracted the data in batches using the `chunksize` parameter.

For each batch, I displayed the batch number and the number of records extracted.

The original dataset was not cleaned, modified, or transformed during extraction. All original values, missing values, and duplicate records were kept unchanged.

The dataset contained 1,010 records, which were extracted in 11 batches. The first 10 batches contained 100 records each, and the last batch contained 10 records.

**Result:** Successfully extracted 1,010 records in 11 batches.

---

## Task 2 – Load Raw Data into MySQL

For this task, I created a MySQL database named `food_orders_raw_db` and a table named `raw_food_orders`.

I used Python and MySQL Connector to load the original CSV records into the raw table in batches of 100 rows.

The original values, missing values, and duplicate records were preserved. No cleaning or transformation was performed during this loading process.

I used `executemany()` for batch insertion and database transactions to save each successfully inserted batch. If a batch insertion failed, the transaction was rolled back.

Finally, I used the SQL `COUNT(*)` query to verify the total number of records loaded into the raw table.

**Result:** Successfully loaded 1,010 records into `raw_food_orders` across 11 batches and verified the record count in MySQL.

---

## Task 3 – Retrieve Raw Data from MySQL

For this task, I connected Python to the `food_orders_raw_db` database using MySQL Connector.

I retrieved the records from the `raw_food_orders` table in batches of 100 rows using `fetchmany()`.

Each retrieved batch was converted into a separate Pandas DataFrame. I displayed the batch number and the number of records retrieved.

The raw MySQL table was not modified during this process. Finally, I checked the total number of retrieved records to make sure all records were fetched successfully.

## Task 4 – Transform Data Using Pandas

For this task, I retrieved the raw records from MySQL in batches of 100 and transformed each batch using Pandas.

The transformation included the following operations:

1. Removed exact duplicate records, including duplicates occurring across different batches.
2. Replaced missing Payment Method values with `Unknown`.
3. Replaced missing Discounts and Offers values with `Not Specified`.
4. Converted order and delivery timestamps into datetime format.
5. Converted financial columns into numeric data types.
6. Created `Delivery_Time_Minutes` using the difference between delivery and order timestamps.
7. Created `Platform_Net_Revenue` using the specified revenue formula.
8. Identified records with missing or invalid timestamps and negative delivery durations.

Missing financial values were retained instead of replacing them with zero. Platform net revenue was calculated only when all four required financial components were available.

The valid records and rejected records were separated for the next validation stage. Rejected records were also assigned reasons to explain why they were excluded.


## Task 6 – Load Transformed Data into a Separate MySQL Database

In this task, I created a separate MySQL database named `food_orders_analytics_db` and a table named `cleaned_food_orders`.

I defined suitable data types for identifiers, timestamps, financial values and calculated columns.

Before loading, I performed the transformation and validation steps. Only the validated records were loaded into the analytics database.

The records were inserted in batches of 100 using `executemany()`. Each batch was committed using transactions. If an insertion failed, the current batch was rolled back and an error message was displayed.

Finally, I verified the records using a SQL COUNT query.

### Task 6 Results

- Analytics database: `food_orders_analytics_db`
- Analytics table: `cleaned_food_orders`
- Batch size: 100
- Successful batches: 10
- Total records loaded: 1,000
- Final MySQL record count: 1,000
- Loading status: PASS


## Task 7 – Pipeline Execution

In this task, I organized the complete ELT workflow into separate Python files. I created `elt_batch_pipeline.py` to execute the workflow in the correct order.

The pipeline performs the following steps:

1. Prepare the raw database and table.
2. Load source data when the raw table is empty.
3. Retrieve raw records from MySQL in batches.
4. Transform the retrieved data using Pandas.
5. Validate transformed records and identify rejected records.
6. Load validated records into the analytics database when it is empty.
7. Verify the final record count.

I also included record-count checks to avoid inserting the same records again when the pipeline is executed.

### Task 7 Results

- Raw records: 1,010
- Retrieved records: 1,010
- Retrieved batches: 11
- Valid transformed records: 1,000
- Rejected records: 10
- Analytics records: 1,000
- Validation status: PASS
- Pipeline status: PASS