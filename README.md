<h1 align="center">🏦 Bank Statement Reconciliation Bot</h1>

<p align="center">
  An RPA bot built in <b>Automation Anywhere A360</b> that reconciles a bank statement<br>
  against company records and classifies every transaction automatically.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Automation%20Anywhere-A360-orange" alt="A360">
  <img src="https://img.shields.io/badge/RPA-Bot-blue" alt="RPA">
  <img src="https://img.shields.io/badge/Excel-Advanced-green" alt="Excel">
  <img src="https://img.shields.io/badge/Status-Completed-brightgreen" alt="Status">
</p>

---

## 📑 Table of Contents
- [Overview](#-overview)
- [Results](#-results)
- [Input Data](#-input-data)
- [Workflow](#-workflow)
- [Bot Walkthrough](#-bot-walkthrough)
- [Key Variables](#-key-variables)
- [Tech & Concepts](#-tech--concepts)
- [Challenges & Fixes](#-challenges--fixes)
- [Edge Cases](#-edge-cases)
- [Recreate It Yourself](#-recreate-it-yourself)
- [Future Improvements](#-future-improvements)
- [Author](#-author)

## 📌 Overview

Finance teams spend hours comparing bank statements with internal records by hand.
This bot removes that manual work. It reads two Excel files, compares every transaction by
**TransactionID** and **Amount**, and reports how many are matched, mismatched or missing.

**Highlights**
- Two-pass reconciliation (bank → company and company → bank)
- Detects amount differences and transactions missing on either side
- Cleans amount text (removes commas) before comparing numbers
- Clear summary message with all four counts

## 📊 Results

Output of the bot on the sample data:

| Status | Count | Meaning |
|---|:---:|---|
| ✅ Matched | **45** | ID found in both files and amounts are equal |
| ⚠️ Mismatch | **11** | ID found in both files but amounts differ |
| ❌ Missing in Company | **6** | ID exists only in the bank statement |
| ❌ Missing in Bank | **6** | ID exists only in the company records |

## 📂 Input Data

| File | Columns | Rows |
|---|---|:---:|
| `Bank_Statement.xlsx` | TransactionID, Date, Amount | 62 |
| `Company_Records.xlsx` | TransactionID, Date, Amount | 59 |

## ⚙️ Workflow

```mermaid
flowchart TD
    A([Start]) --> B["Read Bank_Statement.xlsx into vBankTable"]
    B --> C["Read Company_Records.xlsx into vCompanyTable"]
    C --> D["Initialize 4 counters to 0"]
    D --> E

    subgraph P1["Pass 1 - for each bank row"]
        E{"ID found in company table?"}
        E -- No --> F["Missing in Company"]
        E -- Yes --> G{"Amounts equal?"}
        G -- Yes --> H["Matched"]
        G -- No --> I["Mismatch"]
    end

    P1 --> J

    subgraph P2["Pass 2 - for each company row"]
        J{"ID found in bank table?"}
        J -- No --> K["Missing in Bank"]
        J -- Yes --> L["Already counted in Pass 1"]
    end

    P2 --> M["Show reconciliation summary"]
    M --> N([End])
```

## 🧭 Bot Walkthrough

The bot has **51 steps**, grouped into the stages below.

### 1️⃣ Load the bank statement (steps 1–4)
Opens `Bank_Statement.xlsx`, reads all cells into `vBankTable`, shows a confirmation message and closes the file.

### 2️⃣ Load the company records (steps 5–8)
Same process for `Company_Records.xlsx`, stored in `vCompanyTable`.

### 3️⃣ Initialize counters (steps 9–12)
Sets the four result counters to `0` so the totals start clean on every run.

### 4️⃣ Pass 1: loop over bank rows (steps 13–16)
For each bank row, stores its TransactionID, resets the `vFound` flag to `False` and resets `vCompanyAmount` to `0`.

### 5️⃣ Clean the bank amount (steps 17–19)
Reads the amount as text, removes commas and converts it to a number, so `12,450` becomes `12450`.

### 6️⃣ Search the company table (steps 20–26)
An inner loop scans every company row. When the IDs are equal, `vFound` becomes `True` and the company amount is cleaned and converted to a number.

### 7️⃣ Compare and classify (steps 27–36)
- `vFound = True` and amounts equal → **Matched**
- `vFound = True` and amounts differ → **Mismatch**
- `vFound = False` → **Missing in Company**

### 8️⃣ Pass 2: loop over company rows (steps 37–41)
For each company row, resets `vBankFound` to `False` and starts an inner loop over the bank table.

### 9️⃣ Missing in Bank check (steps 42–46)
If a bank row has the same ID, `vBankFound` becomes `True`. After the inner loop ends, `vBankFound = False` means the transaction is **Missing in Bank**.

### 🔟 Summary (steps 47–51)
Converts the four counters to text and shows the reconciliation summary in a message box.

## 🧩 Key Variables

| Variable | Type | Purpose |
|---|---|---|
| `vBankTable`, `vCompanyTable` | Table | Data read from the Excel files |
| `vCurrentBankRow`, `vCurrentCompanyRow` | Record | Current
## 👤 Author

**Sai Kumar Bulasala**

[GitHub](https://github.com/saikumarbulasala) · [LinkedIn](https://linkedin.com/in/bulasala-sai-kumar-18b83a360)

---

<p align="center">
  Built with ❤️ by <b>Sai Kumar</b>
</p>
