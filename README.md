# 📂 ADF File Path Settings Overview

This guide breaks down the **file path-related settings** in **Azure Data Factory (ADF)** — especially useful when working with **datasets or source settings** for services like **Azure Blob Storage**, **Data Lake Storage**, etc.

---

## 🗂️ File Path Type

Specifies **how ADF should locate and read files** from a source location.

### Common Types:

1. **File path in dataset** – Static path defined inside the dataset.
2. **Prefix** – Reads all files that start with a specific text.
3. **Wildcard file path** – Matches files using wildcard patterns like `*.csv`, `2025*.json`.
4. **List of files** – You provide an array of specific file names to process.

---

## 📌 File Path in Dataset

You define a **fixed or parameterized path** directly inside the dataset  
(e.g., `container/rawdata/2025/06/sales.csv`)

✅ **Good for:**

- Simple pipelines with fixed folder/file structure
- Parameterized paths based on pipeline variables

---

## 🔠 Prefix

Reads **all files** that **start with a specific string** in their names.

### Example:

- **Prefix:** `sales_2025_`
- **Matches:** `sales_2025_Jan.csv`, `sales_2025_Feb.csv`

✅ **Good for:**

- Picking up files uploaded daily/monthly using a naming convention
- Avoiding hardcoded filenames

---

## 🌟 Wildcard File Path

Use **wildcards** to match file names or paths dynamically.

### Examples:

- `*.csv` → All CSV files
- `2025*.json` → JSON files starting with `2025`
- `*/data_*.csv` → Files inside any subfolder

✅ **Good for:**

- Processing multiple files in one run
- Flexible folder/file patterns

---

## 🗃️ List of Files

You provide a **manually defined array** of file names or paths.

✅ **Good for:**

- Custom logic to select specific files
- Combining with filters or conditions

Often used with:

- **Get Metadata** activity
- **ForEach** loop for iteration

### Example:

```json
["sales_2025_Jan.csv", "sales_2025_Feb.csv"]
```

---

## 🚀 Summary Table

| Setting              | Purpose                                   | Good For                                   |
| -------------------- | ----------------------------------------- | ------------------------------------------ |
| File path in dataset | Static or parameterized file path         | Fixed files or param-based logic           |
| Prefix               | Match files by name prefix                | Dynamic file selection by naming pattern   |
| Wildcard file path   | Use `*` or `?` to match multiple files    | Bulk file processing, dynamic input        |
| List of files        | Pass a list of files manually/dynamically | Fine-grained control with logic or filters |

---

## 📘 Notes

These file path strategies can significantly improve the flexibility and scalability of your ADF pipelines. Choose the type that aligns with your data patterns and automation needs.

<br>
<br>
<br>

# 🎯 Wildcard File Path vs Prefix in Azure Data Factory

This guide clears up the common confusion between **Wildcard File Path** and **Prefix** in **Azure Data Factory (ADF)**. While both filter files in a folder, they serve **different purposes** and behave **differently**.

---

## 🔹 1. Wildcard File Path

- **Purpose**: Match files using wildcards (`*`, `?`) like traditional pattern matching
- **File Path Type**: `Wildcard file path`

### 🧾 Format Examples:

- `folder/subfolder/*.csv` → matches all `.csv` files
- `folder/data2024??.json` → matches `data202401.json`, `data202402.json`, etc.

✅ **Use Case**:

> Load all files that match a certain pattern like `sales_*.csv` within a folder

---

## 🔹 2. Prefix

- **Purpose**: Load files that **start with a specific string**
- **File Path Type**: `Prefix`

### 🧾 Format Example:

- Prefix: `invoice_` → loads `invoice_Jan.csv`, `invoice_Feb.csv`

✅ **Use Case**:

> Load files starting with a consistent naming pattern like `daily_`, `log_`, `error_`

---

## 🔄 Key Differences

| Feature                  | Wildcard File Path      | Prefix                    |
| ------------------------ | ----------------------- | ------------------------- |
| Matching Style           | Pattern (`*`, `?`)      | StartsWith (plain string) |
| Flexibility              | High (complex patterns) | Low (simple startsWith)   |
| File Path Type Option    | `Wildcard file path`    | `Prefix`                  |
| Example                  | `sales_*.csv`           | `sales_`                  |
| File Extension Filtering | ✅ Yes                  | 🚫 Only if part of prefix |

---

## 🧠 Why Both Options Exist?

- **Wildcard**: Designed for **advanced dynamic file selection**
- **Prefix**: Offers **simplicity and better performance** (especially in Azure Blob Storage due to prefix-based listing optimizations)

---

## 🔍 Real-World Example

Blob Storage contains:

```
report_202401.csv
report_202402.csv
report_backup_202401.csv
summary_202401.csv
```

| Desired File Match            | Prefix Approach | Wildcard Approach |
| ----------------------------- | --------------- | ----------------- |
| Files starting with `report_` | ✅ `report_`    | ✅ `report_*.csv` |
| Files containing `backup`     | ❌ Not Possible | ✅ `*backup*.csv` |
| All `.csv` files              | ❌              | ✅ `*.csv`        |

---

<br>
<br>
<br>

# 🧩 Enable Partition Discovery in Azure Data Factory

This guide explains the **Enable Partition Discovery** feature in **Azure Data Factory (ADF)**, especially useful when working with **folder-based partitioned file structures** in **Azure Data Lake Storage (ADLS)** or **Azure Blob Storage**.

---

## 📌 What is Partition Discovery?

When **partition discovery is enabled**, ADF automatically:

- Detects subfolders that represent **partition columns** (e.g., `year`, `month`, `region`)
- Extracts those folder names as **columns** in your dataset

---

## 📁 Example Structure

Given the folder path: `/raw/sales/year=2025/month=06/day=13/file.csv`

If **Enable Partition Discovery** is turned **ON**:

- ADF reads `year=2025`, `month=06`, and `day=13` as **dataset columns**
- No manual mapping is required

---

## 🔧 How to Enable It

- In the **dataset settings**, check the box labeled **"Enable partition discovery"**
- Works with file formats like:
  - **Parquet**
  - **CSV**
  - **JSON**

---

## ✅ Use Cases

| Scenario                             | Benefit                           |
| ------------------------------------ | --------------------------------- |
| Folder names used as data dimensions | Automatically adds as columns     |
| Loading only specific partitions     | Can filter using partition values |
| Time-series data stored by folders   | No manual parsing of paths        |

---

## ⚠️ Notes

- Works best when folder structure follows `column=value` format
- You must **manually check** the option in the dataset UI
- Columns are added **automatically** when reading from the source

---

<br>
<br>
<br>

# 🔗 Max Concurrent Connections in Azure Data Factory (ADF)

This setting determines **how many simultaneous connections** ADF can make to a data store during a **Copy Data activity**. It's a key parameter for controlling **parallelism** and optimizing performance.

---

## 🧠 What Does It Mean?

Controls the number of **concurrent threads** used when copying data — particularly useful when working with **multiple files, partitions, or tables**.

---

## 📌 Where to Find It

Located in the **Source** or **Sink** tab of a **Copy Data activity**, under **"Additional Settings."**

---

## 🔧 Purpose

> Set the upper limit for **how many files or connections ADF processes in parallel**.

---

## 💡 Example

Copying **100 CSV files** from Azure Blob Storage:

- If `Max Concurrent Connections = 5`, ADF copies **5 files at a time**
- Remaining files are **queued** and copied in **batches**

---

## ✅ When to Adjust It

| Scenario                             | Suggested Action                         |
| ------------------------------------ | ---------------------------------------- |
| Speed up large file transfers        | 🔼 Increase concurrent connections       |
| Source/destination system is limited | 🔽 Lower to avoid overload or throttling |
| Lots of small files or partitions    | 🔼 Increase for parallelism              |
| Using APIs with strict rate limits   | 🔽 Lower to prevent throttling           |

---

## ⚠️ Notes

- **Default Value**: Typically `4`  
  (but can vary depending on integration runtime and connector)
- **Too high values** may cause:
  - Network congestion
  - Throttling by the source/destination
- Tune based on:
  - **Source system performance**
  - **Network capacity**

---

## 🛠️ Real-World Configuration

Copying from Blob Storage with 8 concurrent threads:

```json
"maxConcurrentConnections": 8
```

ADF will then copy up to 8 blobs at once, speeding up large file transfers through parallelism.

<br>
<br>
<br>

# 🖊️ Write Behavior in Azure Data Factory (ADF)

The **Write Behavior** setting in **Azure Data Factory (ADF)** defines how data is written to the **sink** (destination) in activities like **Copy Data**. Different modes offer flexibility for handling insert-only, update, and complex logic scenarios.

---

## 🔹 1. Insert

> Simply inserts new rows into the sink table or destination.

✅ **Use when:**

- You're adding **fresh data**
- No possibility of duplicate records

❌ **Does not** check for existing records.

🛠️ **Common Use Cases:**

- Loading logs
- Appending historical records

🧠 **Think of it as:**

```sql
INSERT INTO table (col1, col2) VALUES (...)
```

---

## 🔹 2. Upsert

> Insert new rows or update existing ones based on a key match.

✅ **Use when:**

- Syncing data where some records **may already exist**
- You want to **update existing** or **insert new** records

🔑 **Requirements:**

- A **key column** for matching (e.g., `id`, `customer_id`)
- ADF must support upsert for the chosen sink (e.g., Azure SQL, Synapse)

🧠 **Think of it as:**

```sql
IF EXISTS (SELECT * FROM table WHERE id = @id)
  UPDATE table SET ...
ELSE
  INSERT INTO table ...
```

---

## 🔹 3. Stored Procedure

> Executes a custom **stored procedure** in the sink system.

✅ **Use when:**

- You need **custom logic** for insert/update
- Using **Azure SQL**, **SQL Server**, or other SP-supported sinks

🛠️ **Features:**

- Call stored procedures with input parameters
- Supports **table-valued parameters (TVPs)** for bulk loads

🧠 **Example:**

```sql
EXEC dbo.InsertSalesData @Region = 'East', @SalesAmount = 1000
```

---

## 📋 Summary Table

| Write Behavior       | Description                        | Good For                                |
| -------------------- | ---------------------------------- | --------------------------------------- |
| **Insert**           | Add new rows only                  | Clean new data, logs                    |
| **Upsert**           | Insert or update if match is found | Data sync, merge scenarios              |
| **Stored Procedure** | Call SQL proc with logic           | Complex validation, logging, pre-checks |

---

## ⚠️ Notes

- **Not all sinks** support every write behavior:

  | Sink Type    | Supported Behaviors              |
  | ------------ | -------------------------------- |
  | Blob Storage | Insert only                      |
  | Azure SQL DB | Insert, Upsert, Stored Procedure |

- In **Mapping Data Flows**, similar logic is controlled via the **Alter Row** transformation.

---

<br>
<br>
<br>

# 🔒 Bulk Insert Table Lock in Azure Data Factory (ADF)

When performing **Bulk Insert** into SQL-based sinks like **Azure SQL Database**, **SQL Server**, or **Synapse SQL**, ADF provides an option to enable or disable **Table Locking**.

---

## 🔹 What Does It Do?

Controls whether ADF will **lock the entire target table** during the bulk insert process.

---

## ✅ Yes — Enable Table Lock

- ADF places a **table-level lock** on the destination.
- Best for **high-throughput, fast inserts** when the table is not being accessed concurrently.

### ⚙️ Benefits:

- 🔼 **Improved performance** for large data loads

### ⚠️ Trade-Offs:

- ❌ **Blocks other transactions** from reading or writing to the table until the load completes

🧠 Equivalent SQL:

```sql
BULK INSERT WITH (TABLOCK)
```

---

## ❌ No — Do Not Lock Table

- Rows are inserted without locking the entire table.

### ⚙️ Benefits:

- ✅ **Non-blocking** — other users/apps can access the table during load

### ⚠️ Trade-Offs:

- 🔽 **Slightly slower** performance for large datasets

🧠 Equivalent SQL:

```sql
BULK INSERT without TABLOCK
```

---

## 🧾 Summary Table

| Option  | Locks Table? | Performance | Best Used When...                        |
| ------- | ------------ | ----------- | ---------------------------------------- |
| **Yes** | ✅ Yes       | 🔼 Faster   | Nightly ETL, staging loads, isolated ops |
| **No**  | ❌ No        | 🔽 Slower   | Live systems, during business hours      |

---

## 💡 Best Practices

- Use **Yes (Enable Table Lock)** when:

  - Performing **large batch loads**
  - No concurrent reads/writes expected

- Use **No (Disable Table Lock)** when:
  - Table is used in **real-time queries**
  - You need to **minimize impact** on users

---

<br>
<br>
<br>

# 🧾 Pre-copy Script in Azure Data Factory (ADF)

The **Pre-copy script** is a powerful feature in **Azure Data Factory (ADF)** that allows you to run **custom SQL commands** before the **Copy Data activity** starts — available only for **SQL-based sinks** like:

- **Azure SQL Database**
- **SQL Server**
- **Synapse Analytics**

---

## 🎯 Purpose

Used to **prepare the sink (target)** before new data is loaded.

---

## 🔧 What Can You Do With It?

✅ **Common Use Cases:**

1. **Truncate the target table** before loading:

   ```sql
   TRUNCATE TABLE sales_staging;
   ```

2. **Delete specific rows**:

   ```sql
   DELETE FROM sales_staging WHERE sale_date = '2025-06-13';
   ```

3. **Drop and recreate temp tables**:

   ```sql
   DROP TABLE IF EXISTS temp_load;
   CREATE TABLE temp_load (id INT, name NVARCHAR(50));
   ```

4. **Disable constraints or indexes** for better performance
5. **Perform audit logging or data pre-checks**

---

## 💡 Where to Configure

- Go to the **Sink** tab inside your **Copy Data activity**
- Scroll down to **"Pre-copy script"**
- Paste your SQL statement

🛠️ ADF will run this **using the sink's linked service** _before any data is written_

---

## ⚠️ Notes

- Must be a **valid SQL statement**
- Runs **once per activity**, not per row or per batch
- Only available when the **sink is a database**
  - ❌ Not applicable for sinks like Blob Storage or ADLS

---

## 🧠 Example Scenario

You load **daily sales data** into a staging table, but need to clear yesterday’s data first:

```sql
TRUNCATE TABLE staging_sales;
```
