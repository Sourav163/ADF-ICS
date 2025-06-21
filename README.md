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

| Feature                  | Wildcard File Path       | Prefix                     |
|--------------------------|--------------------------|----------------------------|
| Matching Style           | Pattern (`*`, `?`)        | StartsWith (plain string)  |
| Flexibility              | High (complex patterns)   | Low (simple startsWith)    |
| File Path Type Option    | `Wildcard file path`      | `Prefix`                   |
| Example                  | `sales_*.csv`             | `sales_`                   |
| File Extension Filtering | ✅ Yes                    | 🚫 Only if part of prefix  |

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

| Desired File Match                 | Prefix Approach        | Wildcard Approach         |
|-----------------------------------|------------------------|---------------------------|
| Files starting with `report_`     | ✅ `report_`           | ✅ `report_*.csv`         |
| Files containing `backup`         | ❌ Not Possible        | ✅ `*backup*.csv`         |
| All `.csv` files                  | ❌                     | ✅ `*.csv`                |

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

| Scenario                              | Suggested Action                        |
|---------------------------------------|------------------------------------------|
| Speed up large file transfers         | 🔼 Increase concurrent connections       |
| Source/destination system is limited  | 🔽 Lower to avoid overload or throttling |
| Lots of small files or partitions     | 🔼 Increase for parallelism              |
| Using APIs with strict rate limits    | 🔽 Lower to prevent throttling           |

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

