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

<br>
<br>
<br>

# ⚙️ Batch Size vs Concurrent Connections in Azure Data Factory (ADF)

In **Azure Data Factory (ADF)**, both **Batch Size** and **Concurrent Connections** are important for **performance tuning**, but they control **different aspects** of the data movement process.

---

## 🔹 1. Batch Size

### 💡 What It Means

**Batch Size** defines the **number of rows** read or written per batch during a data transfer.

- Commonly used in **Copy Data activities**
- Most effective when writing to **SQL-based sinks** like:
  - Azure SQL Database
  - Synapse Analytics
- Not all connectors support this option

### 🛠 Example

```txt
Batch Size = 5000
```

- ADF writes 5,000 records in one go (single transaction)
- Reduces round-trips, improving performance

### ✅ Best For

- Reducing commit frequency
- Improving performance during **bulk loads**
- Avoiding row-by-row insert overhead

---

## 🔹 2. Concurrent Connections (Parallel Copy)

### 💡 What It Means

**Concurrent Connections** defines **how many threads** ADF opens in parallel during the copy process.

- Also known as **"Degree of Copy Parallelism"**
- Works best for **file-based sources**:
  - Blob Storage
  - ADLS
  - Amazon S3

### 🛠 Example

```txt
Concurrent Connections = 5
```

- ADF processes **5 files at a time**
- As one finishes, the next is picked, maintaining max concurrency

### ✅ Best For

- Copying large numbers of files
- Leveraging multi-threaded performance
- Accelerating file ingestion pipelines

---

## 🔄 Comparison Table

| Feature               | Batch Size                | Concurrent Connections              |
| --------------------- | ------------------------- | ----------------------------------- |
| **Focus Area**        | Number of rows per batch  | Number of parallel threads/files    |
| **Applies To**        | Mostly databases          | Mostly file-based stores            |
| **Improves**          | Insert/update efficiency  | Parallelism and throughput          |
| **Unit**              | Row count                 | Thread count                        |
| **Controlled Where?** | Sink settings (e.g., SQL) | Source settings (e.g., Blob folder) |
| **Example**           | 10,000 rows per batch     | 10 files processed simultaneously   |

---

## 🎯 Quick Analogy

> Imagine you're uploading data to a warehouse:

- **Batch Size** = How many boxes you pack into each truck
- **Concurrent Connections** = How many delivery trucks leave at the same time

---

<br>
<br>
<br>

# 📉 Disable Performance Metrics Analytics in Azure Data Factory (ADF)

This setting allows you to control whether **detailed performance metrics** are collected during execution of a **Copy Data activity** in ADF.

---

## 🔹 What Is It?

A checkbox in the **Copy Data activity** that toggles **metrics collection** for that specific operation.

---

## 🧠 When Enabled (Default)

ADF will collect and log:

- ✅ Rows read/written
- ✅ Throughput (rows/sec, MB/sec)
- ✅ Source/sink operation duration
- ✅ Partition statistics

📍 Metrics appear in:

- **Monitoring tab**
- **Activity output JSON**
- **Azure Monitoring Logs** (if configured)

---

## ❌ When Disabled

If you check **"Disable Performance Metrics Analytics"**:

- ADF skips collecting detailed metrics
- Logs show only **basic status** (e.g., success/failure, timestamps)

---

## ✅ When to Disable

| Scenario                           | Reason                                                            |
| ---------------------------------- | ----------------------------------------------------------------- |
| High-throughput, large-volume copy | Improve performance by reducing logging overhead                  |
| Sensitive environments             | Avoid logging row counts or schema details for compliance reasons |
| Minimal testing or troubleshooting | Keep logs simple and avoid noisy output                           |

---

## ⚠️ Important Notes

- You **won’t see row counts, size, partitions, or throughput** when disabled
- ✅ This setting **does not affect the actual data copy**, only the **metrics logging**
- Applies **only to the individual activity**, not the whole pipeline

---

## 🔁 Summary Table

| Option                   | Metrics Collected?   | Use When                                     |
| ------------------------ | -------------------- | -------------------------------------------- |
| ❌ **Disabled**          | No (basic logs only) | Optimize performance, reduce logging         |
| ✅ **Enabled** (default) | Yes (detailed logs)  | For monitoring, auditing, performance tuning |

---

<br>
<br>
<br>

---

# ⚙️ Type Conversion Settings in Azure Data Factory (ADF)

These settings ensure **data integrity** and proper **schema mapping** between **source and sink** in **Copy Data activities**.

---

## 🔹 Allow Data Truncation

- ✅ When enabled: ADF will **truncate data** that exceeds the destination column size
- ❌ When disabled (default): Copy **fails** if data exceeds the sink column width

📌 Example:

```sql
Source: VARCHAR(200) → Sink: VARCHAR(50)
```

- With truncation: Data is cut to 50 characters
- Without truncation: Activity fails

---

## 🔹 Treat Boolean as Number

- ✅ When enabled: Converts `true/false` → `1/0`
- ❌ When disabled: May cause failure if sink expects a number

📌 Example:

```sql
Source: true → Sink expects INT → Result: 1
```

---

## 🔹 Date Format

Helps parse string-formatted **dates** from the source.

📌 Example:

```txt
"13-06-2025" → Format: dd-MM-yyyy
```

---

## 🔹 DateTime Format

Parses **datetime strings** including time components.

📌 Example:

```txt
"2025-06-13 15:30:00" → Format: yyyy-MM-dd HH:mm:ss
```

---

## 🔹 DateTimeOffset Format

Use for datetime values with **timezone offset**.

📌 Example:

```txt
"2025-06-13T10:30:00+05:30" → Format: yyyy-MM-ddTHH:mm:sszzz
```

---

## 🔹 TimeSpan Format

Defines format for **duration values** (e.g., hours, minutes).

📌 Example:

```txt
"02:30:00" → Format: hh\:mm\:ss
```

---

## 🔹 Time Format

Used for **time-only fields**.

📌 Example:

```txt
"03:45 PM" → Format: hh:mm tt
"15:45" → Format: HH:mm
```

---

## 🌍 Culture

Controls **locale-specific formatting** for dates and numbers.

📌 Examples:

- `en-US` → MM/dd/yyyy, `1,000.50`
- `fr-FR` → dd/MM/yyyy, `1 000,50`

✅ Helps with:

- Correct parsing of date/time
- Proper decimal/thousand separator handling

---

## 🔁 Summary Table

| Setting                 | Purpose                             | Example Format           |
| ----------------------- | ----------------------------------- | ------------------------ |
| Allow Data Truncation   | Trim oversize data or fail          | Yes / No                 |
| Treat Boolean as Number | Convert true/false → 1/0            | Yes / No                 |
| Date Format             | Parse string as date                | `dd-MM-yyyy`             |
| DateTime Format         | Parse string as datetime            | `yyyy-MM-dd HH:mm:ss`    |
| DateTimeOffset Format   | Parse string with timezone          | `yyyy-MM-ddTHH:mm:sszzz` |
| TimeSpan Format         | Handle durations                    | `hh\:mm\:ss`             |
| Time Format             | Parse time-only values              | `HH:mm`, `hh:mm tt`      |
| Culture                 | Controls number/date parsing locale | `en-US`, `fr-FR`         |

---

<br>
<br>
<br>

# ⏱️ TimeSpan vs Time Format in Azure Data Factory (ADF)

In ADF, understanding the difference between **TimeSpan** and **Time Format** is crucial for parsing time-related data accurately. These formats are based on the **.NET DateTime formatting system**.

---

## 🔹 1. TimeSpan Format (`hh\:mm\:ss`)

Used when dealing with **durations** (e.g., time intervals).

### ✅ Example

- **Source Value**: `"02:30:00"`
- **Format String**: `hh\:mm\:ss`

### 🧠 Why the backslash (`\:`)?

In **.NET**, colons (`:`) are format specifiers. To include an actual colon as a literal, you must **escape it** using a backslash (`\:`).

### 🔍 Breakdown

- `hh` → Hours
- `mm` → Minutes
- `ss` → Seconds
- `\:` → Literal colon

✔ Without escaping, `.NET` misinterprets the colon and the format may fail.

---

## 🔹 2. Time Format (`hh:mm tt`)

Used for **clock times** (e.g., timestamps like `03:45 PM`).

### ✅ Example

- **Source Value**: `"03:45 PM"`
- **Format String**: `hh:mm tt`

### 🧠 Why no backslash?

In **DateTime** formats, colons (`:`) are treated as **literal characters by default** — no need to escape them.

### 🔍 Breakdown

- `hh` → Hour (12-hour format)
- `mm` → Minutes
- `tt` → AM/PM designator
- `:` → Literal colon is directly recognized

---

## 🔁 Summary Table

| Format Type  | Example Input | Format String | Colon Escaped? | Reason                                               |
| ------------ | ------------- | ------------- | -------------- | ---------------------------------------------------- |
| **TimeSpan** | `"02:30:00"`  | `hh\:mm\:ss`  | ✅ Yes         | .NET treats `:` as special in TimeSpan formats       |
| **Time**     | `"03:45 PM"`  | `hh:mm tt`    | ❌ No          | `:` is literal by default in DateTime format strings |

---

## 🧠 Final Tip

- ✅ Use `\:` when formatting **durations** (TimeSpan)
- 🚫 Don’t use `\:` for **clock time** (DateTime)

<br>
<br>
<br>

# 🔁 ADF Mapping Reset — Why Only Source Is Cleared?

In **Azure Data Factory (ADF)**, when you click the **"Reset"** button in the **Mapping** section of a **Copy Data activity**, here's what happens:

---

## 🔹 What Gets Cleared?

| Side      | Reset Happens? | Reason                                            |
| --------- | -------------- | ------------------------------------------------- |
| ✅ Source | Yes            | ADF can easily reload source schema automatically |
| ❌ Sink   | No             | Sink may contain custom settings or manual schema |

---

## 🧠 Why Only the Source?

1. **Source columns** are often **auto-detected**, so ADF can safely refresh them.
2. **Sink columns** are usually:
   - **Manually defined**
   - Or linked to a **database/table**
   - May include **custom expressions or data types**

Resetting the sink could accidentally remove these settings — so ADF avoids touching it.

---

## 🔒 ADF Plays It Safe

- ✅ Clears source mapping to allow fresh remapping
- 🚫 Keeps sink schema to avoid breaking your logic

---

## ✅ In Summary

| Action                   | Affects Source? | Affects Sink? | Why?                                           |
| ------------------------ | --------------- | ------------- | ---------------------------------------------- |
| 🔄 Click "Reset Mapping" | ✅ Yes          | ❌ No         | Refreshes source mapping, keeps sink unchanged |

---

<br>
<br>
<br>

# ⚙️ Maximum Data Integration Units (DIU) in Azure Data Factory (ADF)

The **Maximum DIU** setting in ADF controls how much **compute power** the **Azure Integration Runtime** can use during a **Copy Data** or **Data Flow** activity.

---

## 🔹 What is a Data Integration Unit (DIU)?

A **DIU** is a unit of computing power made up of:

- 🧠 **CPU**
- 🧮 **Memory**
- 🌐 **Network throughput**

> More DIUs = More power = Faster execution (up to a limit)

---

## 🔺 What Does “Maximum DIU” Do?

- Sets an **upper limit** for how many DIUs ADF can scale up to during execution
- ADF starts with a baseline and **auto-scales** up to the **maximum DIU** you define

---

## 📍 Where Can You Set It?

- In the **Copy Data Activity**, under **Performance settings**
- Applies **only** when using **Azure Integration Runtime**
- ❌ Not applicable for **Self-hosted Integration Runtime**

---

## 🧠 Why Set a Max DIU?

| Scenario                        | Why Set It                                  |
| ------------------------------- | ------------------------------------------- |
| **Large data volumes**          | 🚀 Increase max DIUs for faster performance |
| **Cost control for small jobs** | 💰 Set low DIUs to save on cost             |
| **Heavy network jobs**          | 🌐 Balance network throughput               |
| **Busy environments**           | ⚖️ Prevent one job from hogging resources   |

---

## 📊 Example

You're copying 200 GB from Blob Storage to Azure SQL DB:

- `Max DIU = 2` → Slower transfer, lower cost
- `Max DIU = 16` → Faster transfer, higher cost
- ADF automatically scales **up to** the max, based on workload

---

## 📝 Summary

| Setting         | Description                               |
| --------------- | ----------------------------------------- |
| **Maximum DIU** | Max compute ADF can use for that activity |
| High Value      | ⚡ Faster copy but higher cost            |
| Low Value       | 🐢 Slower copy but cheaper                |

---

<br>
<br>
<br>

# 🔀 Degree of Copy Parallelism in Azure Data Factory (ADF)

The **Degree of Copy Parallelism** setting controls how many **partitions, files, or objects** ADF copies **at the same time** during a **Copy Data activity**.

---

## 🔹 What Does It Mean?

> "How many chunks of data can ADF copy **in parallel**?"

This helps speed up data movement when working with:

- 🗂️ Multiple files (e.g., from Blob, ADLS)
- 🧩 Partitioned datasets
- 📊 Multiple tables (e.g., SQL databases)

---

## ⚙️ How It Works

ADF splits the source into **chunks or partitions** behind the scenes.  
**Degree of Copy Parallelism** tells ADF **how many to copy at once**.

### ✅ Example:

- You have 20 CSV files in Azure Blob
- `Degree of Parallelism = 5`
- ADF copies 5 files at the same time → when done, it picks the next 5, and so on

---

## 🎯 When Should You Adjust It?

| Situation                                     | What to Do            |
| --------------------------------------------- | --------------------- |
| High-concurrency source (e.g., Blob, Synapse) | 🔼 Increase value     |
| Source/destination is slow or rate-limited    | 🔽 Lower value        |
| Copying many small files                      | 🔼 Increase for speed |
| Seeing throttling/errors in pipeline          | 🔽 Lower to stabilize |

---

## 📍 Where to Set It?

- Go to **Copy Activity → Source tab → Additional Settings**
- Field: `Degree of copy parallelism`
- Default: Typically `4` or **auto**

---

## ⚠️ Important Notes

- Setting it too high might cause:

  - ❌ Source system overload
  - ❌ Throttling
  - ❌ Failures in pipeline

- Can be fine-tuned alongside:
  - **Max Concurrent Connections**
  - **Max DIUs (for compute scaling)**

---

## 🧾 Summary Table

| Setting                    | What It Controls                          |
| -------------------------- | ----------------------------------------- |
| Degree of Copy Parallelism | Number of files/partitions copied at once |
| Higher value               | ⚡ Faster, more resource-intensive        |
| Lower value                | 🐢 Slower, but safer for fragile systems  |

---

<br>
<br>
<br>

# 🔄 Degree of Copy Parallelism vs Max Concurrent Connections in Azure Data Factory (ADF)

A **common source of confusion** in ADF. Both settings deal with **parallelism and throughput**, but they control **different layers** of how data is transferred.

---

## 🧩 Key Differences Summary

| Feature                    | **Degree of Copy Parallelism**                                  | **Max Concurrent Connections**                                      |
| -------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------- |
| **Scope**                  | ADF Copy Activity level                                         | Linked Service or Source/Sink level                                 |
| **Controls**               | Number of **parallel threads** ADF uses to copy data internally | Number of **simultaneous connections** ADF opens to the source/sink |
| **Applies To**             | All sources/sinks that support partitioning or file-based input | Mostly file-based and database connectors                           |
| **Configuration Location** | Copy Activity → Source tab → Performance settings               | Dataset → Linked Service (via UI or JSON)                           |
| **Use Case**               | Copying **partitions, files, or slices** in parallel            | Managing **network/database connection pooling**                    |
| **Example**                | 10 files, Parallelism = 5 → 5 copied simultaneously             | SQL pool connection limit = 4                                       |

---

<br>

## 🔹 1. Degree of Copy Parallelism

### 💡 What Is It?

Defines how many **parallel threads** ADF can use to process files, partitions, or chunks in a **single Copy Activity**.

### 🛠 Example

You have 100 files to copy from Azure Blob Storage.

```text
Degree of Copy Parallelism = 10
→ ADF copies 10 files at a time (in parallel threads)
```

### 🔍 Applies To:

- Azure Blob, ADLS, Amazon S3
- Partitioned data (e.g., PolyBase reads in Synapse)

---

## 🔹 2. Max Concurrent Connections

### 💡 What Is It?

Limits how many **simultaneous network/database connections** ADF opens to a source or sink system.

### 🛠 Example

You copy data into Azure SQL Database:

```text
Max Concurrent Connections = 4
→ ADF opens a maximum of 4 SQL connections at once
```

### 🔍 Applies To:

- Azure SQL, SQL Server, Oracle
- File systems (e.g., Blob, ADLS)

---

## 🎯 Key Insight

> **Max Concurrent Connections** = Controls **network or database connections** > **Degree of Copy Parallelism** = Controls **how many parts/files are copied at once**

They **can work together**, and tuning both helps improve performance:

| Problem                      | Setting to Adjust                     |
| ---------------------------- | ------------------------------------- |
| Slow reads/writes            | 🔼 Increase **Degree of Parallelism** |
| Connection errors/throttling | 🔽 Decrease **Max Connections**       |

---

## 🧠 Real-World Analogy

> Imagine you're downloading files:

- **Max Concurrent Connections** = How many **browser tabs** you can open at once
- **Degree of Copy Parallelism** = How many **downloads** are happening at once per tab

---

<br>
<br>
<br>

# 🎯 Imagine You Are Copying 100 Files From One Folder to Another

Now think of two things:

---

### 🔹 **1. Degree of Copy Parallelism** = "How many files you can copy **at the same time**"

- Let’s say you set it to **5**.
- Then ADF will copy **5 files at once**, then the next 5, and so on.
- It's like having **5 workers** copying files at the same time.

➡️ **More parallelism = faster copy**, but only if your system can handle it.

---

### 🔹 **2. Max Concurrent Connections** = "How many doors (connections) you can open to talk to the storage/database"

- Let’s say you set it to **4**.
- Then ADF will use up to **4 connections** to talk to the source or destination.
- It’s like saying “You can have at most 4 communication lines open with the server.”

➡️ Too many connections can **overload the server** or hit **connection limits**.

---

## ✅ Summary:

| Term                           | Think of it as...             | Controls...                                | Example                                   |
| ------------------------------ | ----------------------------- | ------------------------------------------ | ----------------------------------------- |
| **Degree of Copy Parallelism** | How many files copied at once | **How many things ADF copies in parallel** | Set to 5 → 5 files copied at once         |
| **Max Concurrent Connections** | How many doors to source/sink | **How many connections ADF uses at once**  | Set to 4 → 4 connections to server at max |

---

<br>

## 🧠 Simple Analogy:

> You have 100 boxes to move.
>
> - **Degree of Copy Parallelism** = how many movers (people) are carrying boxes at once
> - **Max Concurrent Connections** = how many trucks you’re allowed to load at the same time

Both are important for performance — but they control **different things**.

<br>

# ⚙️ You Set:

- **Degree of Copy Parallelism = 5**
- **Max Concurrent Connections = 4**

Now, what happens?

---

## 💡 Important Difference:

These **two settings control different layers** of the operation.

---

## ✅ What Happens in This Scenario?

- **ADF will try to copy up to 5 files in parallel** (because of Degree of Copy Parallelism = 5).
- But it can only **open 4 connections at a time** to the source or sink (because of Max Concurrent Connections = 4).

👉 **So only 4 files will be copied at the same time**, not 5 — because ADF **runs into the connection limit**.

---

## 🔄 In Simple Terms:

> ADF wants to copy 5 files in parallel (based on your `parallelism` setting)
> But it hits a limit: **only 4 connections** allowed
> ✅ So it ends up copying **4 files at once**
> Then, when one finishes, another starts — still keeping the number of connections **within 4**

---

## 📊 Visual Example:

| File # | Will it start immediately?         |
| ------ | ---------------------------------- |
| File 1 | ✅ Yes (Connection 1)              |
| File 2 | ✅ Yes (Connection 2)              |
| File 3 | ✅ Yes (Connection 3)              |
| File 4 | ✅ Yes (Connection 4)              |
| File 5 | ⏳ Waits (no connection available) |

➡️ Once File 1 finishes, **File 5 starts using that freed-up connection**.

---

## 🧠 Key Point:

> The **smaller value between these two settings becomes the limit**.

So in your case:

- `Degree of Copy Parallelism = 5`
- `Max Concurrent Connections = 4`
  👉 **Only 4 files are copied in parallel**, because **only 4 connections** are allowed.

---

## ✅ Best Practice:

To get full parallelism, **make sure `Max Concurrent Connections ≥ Degree of Copy Parallelism`**.

<br>

# ⚙️ Settings:

- **Degree of Copy Parallelism = 5**
- **Max Concurrent Connections = 7**

---

## ✅ What Happens Now?

- ADF is allowed to use **up to 7 simultaneous connections** to your source or sink.
- But the **Copy Activity will still only run 5 threads in parallel** (because **parallelism = 5**).

So:

> ✅ **ADF will copy 5 files at a time.**
> 🛑 The extra 2 connections (from the 7 allowed) will remain **unused** — because the activity is **limited to 5 parallel copies**.

---

## 🔁 Visual Flow:

| File # | Started Immediately? | Connection Used |
| ------ | -------------------- | --------------- |
| File 1 | ✅ Yes               | Connection 1    |
| File 2 | ✅ Yes               | Connection 2    |
| File 3 | ✅ Yes               | Connection 3    |
| File 4 | ✅ Yes               | Connection 4    |
| File 5 | ✅ Yes               | Connection 5    |
| File 6 | ⏳ Waits             | –               |
| File 7 | ⏳ Waits             | –               |

➡️ As soon as one file finishes, another will start using a **free connection** — keeping the total active threads at **5**.

---

## 🎯 Key Rule:

> The **Degree of Copy Parallelism is the actual engine of parallelism**.
> The **Max Concurrent Connections is just an upper safety limit**.

### So:

- **You can have more connections than needed** — that’s fine.
- **But if your `parallelism` is low, not all connections will be used.**

---

## 🔧 Best Practice:

| Scenario                             | What To Do                              |
| ------------------------------------ | --------------------------------------- |
| Want to copy more files in parallel  | Increase **Degree of Copy Parallelism** |
| Getting "Too many connections" error | Reduce **Max Concurrent Connections**   |
| Want full speed?                     | Set **Max Connections ≥ Parallelism**   |

---

<br>
<br>
<br>

# ✅ Data Consistency Verification in Azure Data Factory (ADF)

Ensure that **what you read from the source** matches exactly **what gets written to the sink** — that’s what **Data Consistency Verification** is all about.

---

## 🔹 What Is It?

> This setting enables ADF to perform **automatic checks** to validate data integrity — by comparing **row counts** and/or **checksum values**.

### 💬 In simple terms:

> "Did I copy all the data correctly?"

---

## 🔍 Types of Verifications

When enabled, ADF may apply:

1. **✔️ Row Count Comparison**

   - Compares number of rows read vs written

2. **✔️ Checksum Validation** _(for supported connectors)_
   - Compares hash values of the content to ensure no corruption

---

## ⚙️ Where to Enable It?

- Inside **Copy Data Activity → Sink Tab → Additional Settings**
- Check the box: **"Enable data consistency verification"**

---

## 🧠 When Should You Use It?

| Scenario                                   | Why Enable It?                     |
| ------------------------------------------ | ---------------------------------- |
| Copying financial or mission-critical data | Detect data loss or corruption     |
| Working with unreliable or slow systems    | Prevent partial or silent failures |
| Regulatory or auditing environments        | Prove end-to-end data integrity    |

---

## ⚠️ Important Notes

- 🕒 **Slight performance overhead** (varies by dataset size):

  - ✅ Row count checks → Light
  - 🔁 Checksum → Medium/Heavy (if supported)

- ❗ Works **only with compatible connectors** (e.g., Blob → SQL)

- ❌ If mismatch is found → ADF **fails the Copy Activity** with an error

---

## 🧾 Example

You're copying 10 million rows from Azure Data Lake to Azure SQL Database.

- ✅ **With verification ON**: ADF confirms all 10M rows are written
- ❌ If only 9,999,000 rows are written → ADF throws a **mismatch error**

---

## 📋 Summary

| Setting                       | Description                          | Performance Impact |
| ----------------------------- | ------------------------------------ | ------------------ |
| Data Consistency Verification | Ensures read and write match exactly | 🔼 Slight overhead |
| Row Count Check               | Compares total rows                  | ✅ Lightweight     |
| Checksum Validation           | Verifies content integrity           | ⚠️ Medium to heavy |

---

<br>
<br>
<br>

✅ In Azure Data Factory (ADF), **Data Consistency Verification** ensures that data is **accurately transferred** from the **source to the sink** in a **Copy Activity**.

But the **details of how it works are limited to logs**, and Microsoft doesn't expose full internal verification logic — however, **you _can_ observe its impact and behavior** through the following methods:

---

## 🔍 1. **Monitor Tab (UI Level)**

Go to:

- ADF Studio → **Monitor** → Select your pipeline run → Click on the **Copy Activity**

There you’ll see:

- ✅ **Status** (Succeeded, Failed, Skipped)
- ✅ **Rows read vs. written**
- ✅ **Data consistency verification** (enabled/disabled)

> If the number of **rows read ≠ rows written**, and verification is **enabled**, ADF will **fail the activity**.

🧠 This is your first indication that **consistency check caught a mismatch**.

---

## 🔍 2. **Copy Activity Output Logs (Detailed)**

In the **Activity run output** JSON (click the Copy Activity > "Output" tab), look for:

```json
{
  "dataConsistencyVerification": {
    "verificationEnabled": true,
    "verificationStatus": "Passed",
    "rowsRead": 10000,
    "rowsWritten": 10000
  }
}
```

You might see:

- `"verificationStatus": "Passed"` or `"Failed"`
- Row counts that help confirm the match

> 🔧 If consistency fails, it logs a warning or error, and the activity **may fail** depending on your settings.

---

## 🔍 3. **Activity Logs via Azure Monitor / Log Analytics (Advanced)**

If you enabled **diagnostic logging** and sent logs to **Log Analytics**:

- You can query it with **Kusto** using `ADFActivityRun` or `ADFActivityRunOutput`
- Search by activity type: `copy`
- Filter by pipeline name, timestamp, etc.

This lets you **automate detection** of consistency issues across many runs.

---

## 🧪 Bonus: Enable/Disable Option

You can turn this on/off in Copy Activity:

- Go to **Copy Activity > Settings**
- Enable or disable **Data Consistency Verification**

> ⚠️ Disabling this skips row count checks — useful for performance, but **risky for accuracy**.

---

### 🚫 What You _Can't_ See:

- ADF doesn’t show _exact row mismatches_ (like "row 123 failed")
- It doesn’t show partial content or row-level comparison
- No direct access to checksum-level verification (unless custom logic is added)

---

### ✅ Summary

| Method         | What You See                                 |
| -------------- | -------------------------------------------- |
| Monitor tab    | Success/failure, rows read/written           |
| Output JSON    | Row counts + consistency status              |
| Log Analytics  | Full audit trail (optional setup)            |
| Error messages | If mismatch occurs, you’ll get error details |

---

<br>
<br>
<br>

# 🔁 **Copy Behavior** in Azure Data Factory (ADF)

## 🔹 **What is it?**

> The **Copy Behavior** setting defines **how the data is physically copied** from source to sink — specifically when writing to file-based sinks like **Blob Storage**, **ADLS**, or **file systems**.

It determines **what happens to file structures**, **file names**, and **data formats** during the copy process.

---

## ⚙️ **Available Copy Behavior Options (based on sink type):**

> (Exact options vary depending on your **sink connector** — here are the most common ones.)

---

### 1. 📝 **Preserve Hierarchy**

- Maintains the **original folder structure** from the source.
- Example:

  - Source: `/raw/2025/06/file1.csv`
  - Sink: `/processed/2025/06/file1.csv`

✅ Good for:

- Folder-based ingestion
- Partitioned data (year/month/day folders)

---

### 2. 📂 **Flatten Hierarchy**

- Ignores folder structure and **copies all files into a single sink folder**.
- Example:

  - Source: `/raw/2025/06/file1.csv`, `/raw/2025/07/file2.csv`
  - Sink: `/processed/file1.csv`, `/processed/file2.csv`

✅ Good for:

- Merging all files into one folder
- Simple archiving

---

### 3. 🔀 **Merge Files**

- Merges **multiple source files** into a **single output file** in the sink.
- Example:

  - Source: 100 `.csv` files → Sink: `merged.csv`

✅ Good for:

- Preparing consolidated files for reporting or downstream systems
- Exporting to tools that expect **single-file output**

⚠️ Available only for:

- File-based sources (Blob, ADLS, etc.)
- File-based sinks

---

### 4. 📎 **None**

- Just performs a **plain copy**, file-by-file, without modifying structure or merging.

---

## 🧾 Summary Table:

| Copy Behavior      | Description                             | Good For                      |
| ------------------ | --------------------------------------- | ----------------------------- |
| Preserve Hierarchy | Keeps folder path as-is                 | Partitioned or organized data |
| Flatten Hierarchy  | Drops all files into one folder         | Simplified outputs            |
| Merge Files        | Combines multiple source files into one | Reporting, archiving          |
| None               | Direct 1-to-1 copy                      | Simple file transfers         |

---

## ⚠️ Notes:

- Some behaviors only apply to **binary/text file sources**.
- Not supported in all connectors (e.g., databases or APIs).
- **Merge Files** can't be used when sink is partitioned or expects multiple outputs.

---

<br>
<br>
<br>

# 📦 **Block Size (MB)** in Azure Data Factory

## 🔹 **What is it?**

> **Block Size (in MB)** defines the **chunk size** (in megabytes) of data that ADF uses when uploading files to **file-based sinks**, like:

- **Azure Blob Storage**
- **Azure Data Lake Storage**
- **File system sinks**

Essentially, it's the **buffer size** per data block during file write operations.

---

## 🧠 Why does it matter?

When ADF uploads a large file to Blob or ADLS, it splits the file into **blocks**, uploads them in parallel, and then commits the file.

- **Smaller blocks** → More blocks → Higher metadata overhead
- **Larger blocks** → Fewer blocks → More efficient uploads, but higher memory use

---

## 📊 Typical Values:

- **Default**: Varies depending on sink type and file size (often around 4 MB or 8 MB)
- **Custom Range**: You can specify it manually (e.g., 1–100 MB per block)

---

## 🔧 Use Cases:

| Scenario                              | Recommended Block Size                        |
| ------------------------------------- | --------------------------------------------- |
| Small files (<100 MB)                 | 4–8 MB (default is fine)                      |
| Large files (>500 MB)                 | 16–100 MB (larger block = better performance) |
| Slow network or limited bandwidth     | Smaller blocks for retry efficiency           |
| Uploading to Blob/ADLS in large scale | Larger blocks for better throughput           |

---

## ⚠️ Considerations:

- Applies only when writing to **file-based sinks** (not SQL, REST, etc.)
- Setting it too high may cause **memory issues**
- Setting it too low increases **overhead and write time**

---

## 📝 Example:

You’re copying a **2 GB file** to Azure Blob. You set:

```text
Block Size (MB) = 16
```

→ ADF will upload the file in **2 GB / 16 MB = 128 blocks**

---

## ✅ Summary:

| Setting        | Purpose                              | When to Tune                    |
| -------------- | ------------------------------------ | ------------------------------- |
| **Block Size** | Controls chunk size for file uploads | Large files or network tuning   |
| Higher value   | Better for large file performance    | Use with big data pipelines     |
| Lower value    | Better for reliability, small files  | Use with limited memory systems |

---

<br>
<br>
<br>

# 🧾 **Metadata** in Azure Data Factory (ADF)

## 🔹 **What is Metadata in ADF?**

In Azure Data Factory, **metadata refers to the information about your data structure** — such as:

- **File names, sizes, types**
- **Folder names**
- **Schema (columns, data types)**
- **Last modified date/time**
- **Partition info** (in some scenarios)

This is used **not to move data**, but to **inspect, list, or make decisions** based on file/table characteristics.

---

## 🔧 **Where is "Metadata" Used in ADF?**

### ✅ **1. Get Metadata Activity**

This activity retrieves metadata from:

- **Files or folders** (Blob, ADLS, file system)
- **Tables** (SQL, Synapse, etc.)

📌 Example:
You want to:

- Check if a file exists
- Get file size before copying
- Filter files by lastModified date

You’d use **Get Metadata** to do that.

---

## 🔍 **Common Field Names from Get Metadata:**

| Field            | Description                          |
| ---------------- | ------------------------------------ |
| **itemName**     | File or folder name                  |
| **childItems**   | List of files/folders in a directory |
| **lastModified** | Last modified timestamp              |
| **size**         | File size in bytes                   |
| **exists**       | Boolean (true/false) if file exists  |
| **structure**    | Schema of a file or table            |
| **columnCount**  | Number of columns                    |

---

## 🧠 **Use Cases:**

| Scenario                               | Metadata Use                     |
| -------------------------------------- | -------------------------------- |
| **Check if a file arrived**            | Use `exists` property            |
| **Only copy large files**              | Use `size` property              |
| **Trigger only on new files**          | Use `lastModified` timestamp     |
| **Loop through all files in a folder** | Use `childItems` to get the list |
| **Dynamic column mapping**             | Use `structure` to fetch schema  |

---

## 🛠️ Example Workflow:

1. Use **Get Metadata** to fetch files in `/sales/2025/06/`
2. Use **ForEach** to loop over `childItems`
3. Inside loop, use **Copy Data** to load files

---

## 🧾 Summary:

| Feature                | Description                                                 |
| ---------------------- | ----------------------------------------------------------- |
| **Metadata**           | Info about structure and properties of files/tables         |
| **Used In**            | Get Metadata activity, dynamic control flows                |
| **Typical Properties** | `exists`, `size`, `childItems`, `lastModified`, `structure` |

---

<br>
<br>
<br>

# 🧾 **File Pattern** in Azure Data Factory (ADF)

## 🔹 **What is File Pattern?**

> **File Pattern** tells ADF **how to read multiple files** from a folder, especially when working with **delimited text formats** like **CSV**, **JSON**, or **Parquet**.

It defines **how to treat the structure and schema across multiple files** in the **same dataset or source folder**.

---

## ⚙️ **Where You Set It:**

- In the **source settings** of a **Copy Data activity**
- Or in the **dataset settings** (for file-based sources like Blob, ADLS)

---

## 📂 **Common File Pattern Options:**

| Option                       | Description                                                                                                        |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Set of Files** _(default)_ | ADF treats **all matching files** as a single logical dataset (merges them row by row). Schema must be consistent. |
| **Wildcard File**            | Use a pattern like `sales_*.csv` or `*.json` to pick files                                                         |
| **File List** _(dynamic)_    | Uses a list of files (e.g., from Get Metadata) for processing one-by-one                                           |
| **Single File Only**         | Tells ADF to read **just one file** — no merging, no directory scan                                                |

---

## 🧠 **Why File Pattern Matters:**

If you're copying from a folder with multiple CSV files:

- **Set of Files** = All files read together (e.g., `UNION ALL`)
- **Single File** = Reads only one specific file
- **Wildcard** = Reads files based on name patterns
- **List of Files** = You manually supply the file list

---

## ✅ **Best Practices:**

| Scenario                                              | Use This Pattern                           |
| ----------------------------------------------------- | ------------------------------------------ |
| Daily partitioned files (e.g., `sales_2025_06_*.csv`) | **Wildcard**                               |
| All files have same schema, to be read together       | **Set of Files**                           |
| Reading a known, fixed file (e.g., `latest.csv`)      | **Single File**                            |
| Dynamically filtered list from logic                  | **File List (via Get Metadata + ForEach)** |

---

## 📝 Example:

You have:

```
/sales/2025/
  - sales_2025_06_01.csv
  - sales_2025_06_02.csv
  - sales_2025_06_03.csv
```

📌 File Pattern: **Wildcard**
📌 Wildcard File Name: `sales_2025_06_*.csv`

→ ADF reads all 3 files in one go.

---

## ⚠️ Notes:

- For **Set of Files**, ensure **schema is consistent** across all files
- You can combine **File Pattern + Partition Discovery + Wildcard Path** for advanced use cases
- Some file types (like binary) don’t support all file pattern options

---

<br>
<br>
<br>
