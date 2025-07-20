# Personal Finance Tracker - Code Files

This document contains the complete source code for the Personal Finance Tracker application, split into its respective Python files.

---

## `finance_app.py`

```python
import pandas as pd
import csv
from datetime import datetime
from data_entry import get_amount, get_category, get_date, get_description # Corrected typo here

class CSV:
    CSV_FILE = "finance_data.csv"
    COLUMNS = ["date", "amount", "category", "description"]
    FORMAT = "%d-%m-%Y"
    INCOME_CATEGORY = "Income"
    EXPENSE_CATEGORY = "Expense"

    @classmethod
    def initialize_csv(cls):
        """
        Initializes the CSV file with headers if it does not already exist.
        """
        try:
            pd.read_csv(cls.CSV_FILE)
        except FileNotFoundError:
            df = pd.DataFrame(columns=cls.COLUMNS)
            df.to_csv(cls.CSV_FILE, index=False)
            print(f"Created CSV file: {cls.CSV_FILE}")

    @classmethod
    def add_entry(cls, date, amount, category, description):
        """
        Adds a new transaction entry to the CSV file.
        """
        new_entry = {
            "date": date,
            "amount": amount,
            "category": category,
            "description": description,
        }
        try:
            with open(cls.CSV_FILE, "a", newline="") as csvfile:
                writer = csv.DictWriter(csvfile, fieldnames=cls.COLUMNS)
                writer.writerow(new_entry)
            print("Entry added successfully.")
        except IOError as e:
            print(f"Error writing to CSV file: {e}")

    @classmethod
    def get_transactions(cls, start_date_str, end_date_str):
        """
        Retrieves transactions within a specified date range from the CSV file,
        calculates summary statistics, and prints them.

        Args:
            start_date_str (str): The start date in dd-mm-yyyy format.
            end_date_str (str): The end date in dd-mm-yyyy format.

        Returns:
            pandas.DataFrame: A DataFrame containing the filtered transactions.
        """
        try:
            df = pd.read_csv(cls.CSV_FILE)
        except FileNotFoundError:
            print("Finance data file not found. Please add some entries first.")
            return pd.DataFrame(columns=cls.COLUMNS)
        except pd.errors.EmptyDataError:
            print("Finance data file is empty. Please add some entries first.")
            return pd.DataFrame(columns=cls.COLUMNS)

        try:
            df["date"] = pd.to_datetime(df["date"], format=cls.FORMAT)
            start_date = datetime.strptime(start_date_str, cls.FORMAT)
            end_date = datetime.strptime(end_date_str, cls.FORMAT)
        except ValueError:
            print("Error: Date format in CSV or input is incorrect. Expected dd-mm-yyyy.")
            return pd.DataFrame(columns=cls.COLUMNS)

        # Filter transactions based on date range
        mask = (df["date"] >= start_date) & (df["date"] <= end_date)
        filtered_df = df.loc[mask].copy() # Use .copy() to avoid SettingWithCopyWarning

        if filtered_df.empty:
            print("No transactions found in the given date range.")
        else:
            print(f"\nTransactions from {start_date.strftime(cls.FORMAT)} to {end_date.strftime(cls.FORMAT)}:")
            # Format date column back to string for display
            filtered_df["date"] = filtered_df["date"].dt.strftime(cls.FORMAT)
            print(
                filtered_df.to_string(
                    index=False
                )
            )

            total_income = filtered_df[filtered_df["category"] == cls.INCOME_CATEGORY]["amount"].sum()
            total_expense = filtered_df[filtered_df["category"] == cls.EXPENSE_CATEGORY]["amount"].sum()
            net_savings = total_income - total_expense

            print("\nSummary:")
            print(f"Total Income: ${total_income:,.2f}")
            print(f"Total Expense: ${total_expense:,.2f}")
            print(f"Net Savings: ${net_savings:,.2f}")

        return filtered_df


def add():
    """
    Handles the process of adding a new transaction entry.
    """
    CSV.initialize_csv()
    date = get_date(
        "Enter the date of the transaction (dd-mm-yyyy) or enter for today's date: ",
        allow_default=True,
    )
    amount = get_amount()
    category = get_category()
    description = get_description() # Corrected typo here
    CSV.add_entry(date, amount, category, description)


def plot_transactions(df):
    """
    Generates and displays a plot of income and expenses over time.

    Args:
        df (pandas.DataFrame): The DataFrame containing transactions to plot.
    """
    if df.empty:
        print("No data to plot.")
        return

    # Ensure 'date' column is datetime type for plotting
    df['date'] = pd.to_datetime(df['date'], format=CSV.FORMAT, errors='coerce')
    df.dropna(subset=['date'], inplace=True) # Drop rows where date conversion failed

    if df.empty:
        print("No valid date data to plot.")
        return

    df.set_index("date", inplace=True)

    # Resample daily and fill NaNs with 0 for continuous lines
    income_df = df[df["category"] == CSV.INCOME_CATEGORY]["amount"].resample("D").sum().fillna(0)
    expense_df = df[df["category"] == CSV.EXPENSE_CATEGORY]["amount"].resample("D").sum().fillna(0)

    plt.figure(figsize=(12, 6)) # Increased figure size for better readability
    plt.plot(income_df.index, income_df, label="Income", color="forestgreen", marker='o', linestyle='-', markersize=4)
    plt.plot(expense_df.index, expense_df, label="Expense", color="firebrick", marker='x', linestyle='--', markersize=4)

    plt.xlabel("Date", fontsize=12)
    plt.ylabel("Amount ($)", fontsize=12)
    plt.title("Income and Expenses Over Time", fontsize=14, fontweight='bold')
    plt.legend(fontsize=10)
    plt.grid(True, linestyle='--', alpha=0.7)
    plt.tight_layout() # Adjust layout to prevent labels from overlapping
    plt.show()


def main():
    """
    Main function to run the Personal Finance Tracker application.
    """
    print("Welcome to your Personal Finance Tracker!")
    while True:
        print("\n--- Main Menu ---")
        print("1. Add a new transaction")
        print("2. View transactions and summary within a date range")
        print("3. Exit")
        choice = input("Enter your choice (1-3): ")

        if choice == "1":
            add()
        elif choice == "2":
            start_date = get_date("Enter the start date (dd-mm-yyyy): ")
            end_date = get_date("Enter the end date (dd-mm-yyyy): ")

            # Validate date range order
            try:
                if datetime.strptime(start_date, CSV.FORMAT) > datetime.strptime(end_date, CSV.FORMAT):
                    print("Error: Start date cannot be after end date. Please try again.")
                    continue
            except ValueError:
                print("Error: Invalid date format entered. Please use dd-mm-yyyy.")
                continue

            df = CSV.get_transactions(start_date, end_date)
            # Only offer to plot if there are transactions in the filtered DataFrame
            if not df.empty and input("Do you want to see a plot? (y/n) ").lower() == "y":
                plot_transactions(df)
            elif df.empty:
                print("No transactions to plot for the selected date range.")
        elif choice == "3":
            print("Exiting Personal Finance Tracker. Goodbye!")
            break
        else:
            print("Invalid choice. Please enter 1, 2, or 3.")


if __name__ == "__main__":
    main()
