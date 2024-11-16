#This is the base code that i've created. the code which is in the main.py is under developing the error handling part
and this is for safety purpose.

import json
import random

print("********** Welcome to the bank of China **********")

account_numbers = []

with open("data.json", 'r') as f:
    users = json.load(f)["account"]
with open("transactions.json", 'r') as j:
    trans = json.load(j)["transactions"]


def update_json():
    with open("data.json", 'w') as f:
        json.dump({"account": users}, f, indent=4)

    with open("transactions.json", 'w') as j:
        json.dump({"transactions": trans}, j, indent=4)
    print("Json updates successfully")


def acc_no_generation():
    while True:
        acc_no = random.randint(0000, 9999)
        if not any(user['account number'] == acc_no for user in users):
            return acc_no


def account_opening():
    acc_name = input("Enter your name: ")
    acc_no = acc_no_generation()
    balance = float(input("Enter the initial deposit amount: "))
    if balance < 0:
        print("Enter a valid amount")
    else:
        print("Your AC name: ", acc_name, "||", "Your ac no is: ", acc_no, "Balance:", balance)
        new_user = {
                    'account name': acc_name,
                    'account number': acc_no,
                    'balance': balance,
                   }
        users.append(new_user)
        update_json()
        return new_user


def check_balance():
    # while True:
    account_number = int(input("Pls enter your account number: "))
    found = False  # Flag to check that the account was found. Otherwise, the loop will iterate 4 times
    for user in users:
        if user['account number'] == account_number:
            print("ACC No: ", user["account number"], "Your current balance is: ", user["balance"])
            found = True
            break
    if not found:
        print("Pls enter your correct account number")


def deposit_money():
    account_number = int(input("Enter your account number: "))
    found = False
    for user in users:
        if user['account number'] == account_number:
            print(user["account name"], user["account number"], user["balance"])
            amount = float(input("Enter the depositing amount: "))
            if amount < 0:
                print("Pls enter a valid amount")
            elif user['balance'] < amount:
                print("Insufficient Funds")
            else:
                user["balance"] += amount
                print("Deposit Successful")
                print("Your current balance is: ", user["balance"])
                trans_details = {'account number': user['account number'],
                                 'account name': user['account name'],
                                 'transaction type': 'deposit',
                                 'current balance': user['balance']
                                 }
                trans.append(trans_details)
                update_json()
                found = True
                break
    if not found:
        print("Pls enter the correct account number")


def withdraw_money():
    account_number = int(input("Enter your account number: "))
    found = False
    for user in users:
        if user['account number'] == account_number:
            print(user["account name"], user["account number"], user["balance"])
            withdraw_amount = float(input("Enter the amount to withdraw"))
            if withdraw_amount < 0:   # or withdraw_amount
                print("Pls enter a valid amount")
            elif withdraw_amount > user["balance"]:  # user.get('balance'):
                print("Insufficient Funds")
            else:
                user["balance"] -= withdraw_amount
                print("Your current balance is: ", user["balance"])
                update_json()
                found = True
                break
            trans_details = {'account number': user['account number'],
                             'account name': user['account name'],
                             'transaction type': 'withdrawal',
                             'current balance': user['balance']}
            trans.append(trans_details)
            update_json()
    if not found:
        print("Pls enter the correct account number")


running = True
while running:
    user_input = input("Enter your choice: ")

    if user_input == 'add':  # Acc opening
        account_opening()

    elif user_input == 'bal':  # Check Balance
        check_balance()

    elif user_input == 'dep':  # Deposit Money
        deposit_money()

    elif user_input == 'wit':  # Withdraw Money
        withdraw_money()

    elif user_input == 'exit':
        print("Thank you for banking with us !")
        print("Come Again")
        break