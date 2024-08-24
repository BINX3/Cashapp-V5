import random
import string
import time

class User:
    def __init__(self, username, password):
        self.username = username
        self.password = password
        self.balance = 0
        self.transactions = []

    def deposit(self, amount):
        self.balance += amount
        self.transactions.append(f"Deposited: ${amount}")
        print(f"${amount} deposited successfully.")

    def withdraw(self, amount):
        if self.balance >= amount:
            self.balance -= amount
            self.transactions.append(f"Withdrew: ${amount}")
            print(f"${amount} withdrawn successfully.")
        else:
            print("Insufficient funds.")

    def transfer(self, amount, recipient):
        if self.balance >= amount:
            self.balance -= amount
            self.transactions.append(f"Transferred ${amount} to {recipient.username}")
            recipient.balance += amount
            recipient.transactions.append(f"Received ${amount} from {self.username}")
            print(f"${amount} transferred to {recipient.username} successfully.")
        else:
            print("Insufficient funds.")

    def view_balance(self):
        print(f"Your current balance is: ${self.balance}")

    def view_transactions(self):
        print("Transaction History:")
        for transaction in self.transactions:
            print(transaction)

class CashApp:
    def __init__(self):
        self.users = {}

    def register(self, username, password):
        if username in self.users:
            print("Username already exists.")
        else:
            self.users[username] = User(username, password)
            print("Registration successful.")

    def login(self, username, password):
        if username in self.users and self.users[username].password == password:
            print("Login successful.")
            return self.users[username]
        else:
            print("Invalid username or password.")
            return None

    def generate_otp(self, user):
        otp = ''.join(random.choices(string.digits, k=6))
        user.otp = otp
        print(f"Your OTP is: {otp}")
        time.sleep(30)
        user.otp = None

    def verify_otp(self, user, otp):
        if user.otp == otp:
            print("OTP verified.")
            return True
        else:
            print("Invalid OTP.")
            return False

    def send_money(self, user, recipient_username, amount):
        if recipient_username in self.users:
            recipient = self.users[recipient_username]
            if self.verify_otp(user, input("Enter OTP: ")):
                user.transfer(amount, recipient)
        else:
            print("Recipient user not found.")

    def request_money(self, user, sender_username, amount):
        if sender_username in self.users:
            sender = self.users[sender_username]
            if self.verify_otp(user, input("Enter OTP: ")):
                sender.transfer(amount, user)
        else:
            print("Sender user not found.")

# Example usage
cashapp = CashApp()

# Register users
cashapp.register("user1", "password1")
cashapp.register("user2", "password2")

# Login users
user1 = cashapp.login("user1", "password1")
user2 = cashapp.login("user2", "password2")

# Deposit money
user1.deposit(1000)
user2.deposit(500)

# Generate and verify OTP
cashapp.generate_otp(user1)
cashapp.verify_otp(user1, input("Enter OTP: "))

# Send money
cashapp.send_money(user1, "user2", 200)

# Request money
cashapp.request_money(user2, "user1", 100)

# View balance and transactions
user1.view_balance()
user1.view_transactions()

user2.view_balance()
user2.view_transactions()
