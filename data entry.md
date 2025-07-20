# Personal Finance Tracker - `data_entry.py`

This file contains helper functions responsible for collecting and validating user input for the Personal Finance Tracker application. These functions ensure that data such as dates, amounts, categories, and descriptions are in the correct format before being processed and stored.

---

## `data_entry.py`

```python
from datetime import datetime

# Define the standard date format for consistency
date_format = "%d-%m-%Y"

# Define accepted categories with their short-hand input options
CATEGORIES = {"I": "Income", "E": "Expense"}

def get_date(prompt, allow_default=False):
    """
    Prompts the user for a date and validates its format.
    If 'allow_default' is True and input is empty, today's date is used.

    Args:
        prompt (str): The message displayed to the user.
        allow_default (bool): If True, allows an empty input to default to today's date.

    Returns:
        str: The validated date string in dd-mm-yyyy format.
    """
    date_str = input(prompt)
    if allow_default and not date_str:
        return datetime.today().strftime(date_format)

    try:
        # Attempt to parse the date string to validate its format
        valid_date = datetime.strptime(date_str, date_format)
        # Return the formatted string back
        return valid_date.strftime(date_format)
    except ValueError:
        print("Invalid date format. Please enter the date in dd-mm-yyyy format.")
        # Recursively call the function until valid input is received
        return get_date(prompt, allow_default)

def get_amount():
    """
    Prompts the user for an amount and validates it as a positive number.

    Returns:
        float: The validated positive numerical amount.
    """
    while True: # Keep prompting until a valid amount is entered
        try:
            amount = float(input("Enter the amount: "))
            if amount <= 0:
                # Raise ValueError for invalid amounts to be caught by the except block
                raise ValueError("Amount must be a positive, non-zero value.")
            return amount
        except ValueError as e:
            # Print the specific error message and prompt again
            print(e)

def get_category():
    """
    Prompts the user to select a category ('I' for Income or 'E' for Expense)
    and validates the input.

    Returns:
        str: The full category name ('Income' or 'Expense').
    """
    while True: # Keep prompting until a valid category is entered
        category_input = input("Enter the category ('I' for Income or 'E' for Expense): ").upper()
        if category_input in CATEGORIES:
            return CATEGORIES[category_input]
        else:
            print("Invalid category. Please enter 'I' for Income or 'E' for Expense.")

def get_description():
    """
    Prompts the user for an optional description for the transaction.

    Returns:
        str: The entered description.
    """
    return input("Enter a description (optional): ")
