import json
import os

DATABASE_FILE = "bank_data.json"
MINIMUM_BALANCE = 500

def load_data():
    """Loads account data from the JSON file."""
    if os.path.exists(DATABASE_FILE):
        with open(DATABASE_FILE, 'r') as f:
            try:
                return json.load(f)
            except json.JSONDecodeError:
                return {}
    else:
        return {}

def save_data(data):
    """Saves account data to the JSON file."""
    with open(DATABASE_FILE, 'w') as f:
        json.dump(data, f, indent=4)

def create_account(accounts):
    """Creates a new bank account."""
    account_number = input("Enter a unique account number: ")
    if account_number in accounts:
        print("Account already exists. Please log in.")
        login(accounts)
        return

    name = input("Enter your name: ")
    initial_deposit = float(input("Enter initial deposit amount (minimum {}): ".format(MINIMUM_BALANCE)))
    if initial_deposit < MINIMUM_BALANCE:
        print(f"Initial deposit must be at least {MINIMUM_BALANCE}.")
        return

    accounts[account_number] = {"name": name, "balance": initial_deposit}
    save_data(accounts)
    print(f"Account created successfully for {name} with account number {account_number}.")

def login(accounts):
    """Logs into an existing bank account."""
    account_number = input("Enter your account number: ")
    if account_number not in accounts:
        print("Invalid account number.")
        return None, None
    return account_number, accounts[account_number]

def deposit(account):
    """Deposits money into the account."""
    if not account:
        print("Please log in first.")
        return
    amount = float(input("Enter the amount to deposit: "))
    if amount > 0:
        account['balance'] += amount
        print(f"Deposited ${amount:.2f}. Current balance for {account['name']}: ${account['balance']:.2f}")
    else:
        print("Invalid deposit amount.")

def withdraw(account):
    """Withdraws money from the account."""
    if not account:
        print("Please log in first.")
        return
    amount = float(input("Enter the amount to withdraw: "))
    if amount > 0:
        if account['balance'] - amount >= MINIMUM_BALANCE:
            account['balance'] -= amount
            print(f"Withdrew ${amount:.2f}. Current balance for {account['name']}: ${account['balance']:.2f}")
        else:
            print(f"Withdrawal failed. Minimum balance of ${MINIMUM_BALANCE:.2f} must be maintained for {account['name']}.")
    else:
        print("Invalid withdrawal amount.")

def check_balance(account):
    """Checks and displays the current balance."""
    if not account:
        print("Please log in first.")
        return
    print(f"Current balance for {account['name']}: ${account['balance']:.2f}")
    if account['balance'] < MINIMUM_BALANCE:
        print(f"Warning: Your balance is below the minimum balance of ${MINIMUM_BALANCE:.2f} for {account['name']}.")

def main():
    """Main function to run the banking system."""
    accounts = load_data()

    while True:
        print("\nWelcome to the Banking System!")
        print("1. Create Account")
        print("2. Login to Existing Account")
        print("3. Exit")

        choice = input("Enter your choice: ")

        if choice == '1':
            create_account(accounts)
        elif choice == '2':
            account_number, current_account = login(accounts)
            if current_account:
                while True:
                    print(f"\nAccount Menu for {current_account['name']}:")
                    print("1. Deposit")
                    print("2. Withdraw")
                    print("3. Check Balance")
                    print("4. Logout")

                    account_choice = input("Enter your choice: ")

                    if account_choice == '1':
                        deposit(current_account)
                        save_data(accounts)
                    elif account_choice == '2':
                        withdraw(current_account)
                        save_data(accounts)
                    elif account_choice == '3':
                        check_balance(current_account)
                    elif account_choice == '4':
                        print("Logged out successfully.")
                        break
                    else:
                        print("Invalid choice. Please try again.")
        elif choice == '3':
            print("Thank you for using the Banking System!")
            break
        else:
            print("Invalid choice. Please try again.")

if _name_ == "_main_":
    main()
