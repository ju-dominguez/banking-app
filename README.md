**Elite 102 - Starter Repo Setup Guide**

This guide will help students clone and run a starter banking app project with a `BankAccount` class. The project uses SQLite for data persistence and is designed to be run in the command line using Python.

---

## 🧱 Project Structure
```
banking-app/
├── main.py
├── bank_account.py
└── bank.db  (generated after running)
```

---

## 🧪 Step-by-Step Setup

### 1. Clone the Starter Repo
1. Log in to your GitHub account.
2. Create a new **public** repository (you can name it `banking-app` or something similar).
3. Open your terminal or command prompt.
4. Navigate to the folder where you want to store your project:
   ```bash
   cd path/to/your/folder
   ```
5. Clone the repo:
   ```bash
   git clone https://github.com/ju-dominguez/elite_102_project.git
   ```
6. Open the folder in VS Code:
   - `File > Open Folder > Select your cloned folder`

### 2. Install SQLite Viewer Extension in VS Code
1. In VS Code, go to the Extensions tab (left sidebar or press `Ctrl+Shift+X`)
2. Search for `SQLite Viewer`
3. Click "Install"
4. This will let you visually inspect your `bank.db` file and browse data easily

### 3. Add Starter Files
**Create a file called `bank_account.py` with the following content:**
```python
import sqlite3

class BankAccount:
    def __init__(self, user_id):
        self.user_id = user_id
        self.conn = sqlite3.connect('bank.db')
        self.cursor = self.conn.cursor()
        self.create_table()

    def create_table(self):
        self.cursor.execute('''
            CREATE TABLE IF NOT EXISTS accounts (
                user_id TEXT PRIMARY KEY,
                balance REAL DEFAULT 0.0
            )
        ''')
        self.conn.commit()

    def create_account(self):
        self.cursor.execute('INSERT OR IGNORE INTO accounts (user_id, balance) VALUES (?, ?)', (self.user_id, 0.0))
        self.conn.commit()

    def deposit(self, amount):
        self.cursor.execute('UPDATE accounts SET balance = balance + ? WHERE user_id = ?', (amount, self.user_id))
        self.conn.commit()

    def withdraw(self, amount):
        self.cursor.execute('SELECT balance FROM accounts WHERE user_id = ?', (self.user_id,))
        current_balance = self.cursor.fetchone()[0]
        if amount <= current_balance:
            self.cursor.execute('UPDATE accounts SET balance = balance - ? WHERE user_id = ?', (amount, self.user_id))
            self.conn.commit()
        else:
            print("Insufficient funds.")

    def get_balance(self):
        self.cursor.execute('SELECT balance FROM accounts WHERE user_id = ?', (self.user_id,))
        result = self.cursor.fetchone()
        return result[0] if result else 0.0
```

**Create a file called `main.py` with this test interface:**
```python
from bank_account import BankAccount

user_id = input("Enter your user ID: ")
account = BankAccount(user_id)
account.create_account()

while True:
    print("\nChoose an option:")
    print("1. Check Balance")
    print("2. Deposit")
    print("3. Withdraw")
    print("4. Exit")

    choice = input("Enter your choice (1–4): ")

    if choice == '1':
        print("Your balance is:", account.get_balance())
    elif choice == '2':
        amount = float(input("Enter deposit amount: "))
        account.deposit(amount)
    elif choice == '3':
        amount = float(input("Enter withdrawal amount: "))
        account.withdraw(amount)
    elif choice == '4':
        print("Goodbye!")
        break
    else:
        print("Invalid choice. Try again.")
```

---

## ▶️ How to Run the Project
In your terminal, navigate to the folder that contains `main.py` and run:
```bash
python main.py
```
You’ll be prompted to enter your user ID and then interact with your account via CLI.

---

## ✅ Testing It Manually
Try these steps after running `main.py`:
1. Enter a user ID like `student1`
2. Deposit `$100`
3. Check your balance (should now be $100)
4. Withdraw `$50`
5. Check your balance again (should now be $50)
6. Try withdrawing more than your balance to trigger an error message

---

## 💡 Tip
Install the **SQLite Viewer** extension in VS Code so you can view your `bank.db` database file visually and verify that your data is being saved correctly.

You’re now set up to start building and customizing your own banking app! 🎉

