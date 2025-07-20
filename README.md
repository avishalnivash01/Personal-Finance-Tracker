# 💰 Personal Finance Tracker (Console + CSV + Matplotlib)

This is a simple yet effective command-line Personal Finance Tracker built with Python. It helps users manage their income and expenses through a CSV file and visualize the data using Matplotlib.

## 📂 Project Structure

```
📦 Personal-Finance-Tracker
├── main.py              # Main logic and user interaction
├── data_entry.py        # User input functions
├── finance_data.csv     # Data storage (generated after first run)
└── README.md            # Project documentation
```

## 🚀 Features

- Add new transactions (Income or Expense)
- View transactions between a custom date range
- Automatically calculates total income, expenses, and net savings
- Plot income vs. expense trend over time using Matplotlib
- Stores all records in a CSV file for simplicity and portability

## 🛠 Technologies Used

- Python 3
- pandas
- matplotlib
- csv
- datetime

## 📦 How It Works

1. When the program starts, it shows a menu with options to add/view/exit.
2. User inputs transaction details (date, amount, category, description).
3. The data is stored in `finance_data.csv`.
4. Optionally, a plot of Income vs. Expense can be generated.

## 🔁 Example Usage

```bash
1. Add new transaction
2. View transactions and a summary within a date range
3. Exit
```

When viewing transactions, you’ll see:
- A table of transactions
- A financial summary:
  - Total Income
  - Total Expense
  - Net Savings
- A matplotlib graph if chosen

## 📥 Input Validations

- Date format: `dd-mm-yyyy`
- Amount must be positive
- Category: `I` for Income or `E` for Expense

## 🧪 Sample Data Format

```csv
date,amount,category,description
20-07-2025,5000,Income,Salary
21-07-2025,200,Expense,Snacks
```

## 📌 Future Improvements

- GUI using Tkinter or PyQt
- Monthly or category-wise summary reports
- Export visualizations as images or PDFs
- Budget planning feature

## 👨‍💻 Author

Created by [Your Name]. Contributions are welcome!

## 📝 License

This project is licensed under the MIT License.
