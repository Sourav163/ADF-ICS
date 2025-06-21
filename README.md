
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
[
  "sales_2025_Jan.csv",
  "sales_2025_Feb.csv"
]
```

---

## 🚀 Summary Table

| Setting              | Purpose                                   | Good For                                   |
|----------------------|-------------------------------------------|--------------------------------------------|
| File path in dataset | Static or parameterized file path         | Fixed files or param-based logic           |
| Prefix               | Match files by name prefix                | Dynamic file selection by naming pattern   |
| Wildcard file path   | Use `*` or `?` to match multiple files    | Bulk file processing, dynamic input        |
| List of files        | Pass a list of files manually/dynamically | Fine-grained control with logic or filters |

---

## 📘 Notes

These file path strategies can significantly improve the flexibility and scalability of your ADF pipelines. Choose the type that aligns with your data patterns and automation needs.
