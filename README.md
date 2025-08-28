# tele-expense-bot
The program use user's message to the telegram bot to log the user's expense or income.

I have this idea because I'm going to university, and I think it's good if I can take a note on my income and expense during my study. Just to make sure I don't overspend my money.

This project implement income and expense tracker using Telegran bot as the user interface. The goal is to make financial logging quick and seamless by allowing users to input expenses and incomes dirrectly through chat messages.

The feature include:
- Income and expense logging: Users can record their transactions in a structured text format. Expenses are saved as negative amounts, while incomes remain positive.
- Storage: Transactions are stored in a local SQLite database file (money_log.db), so that the transactions history is not lost when the bot is restarted.
- Export to excel: With telegram command (/download), users can ask the bot to generate an Excel file of all transactions. The exported spreadsheet includes column headers with bold and centered formatting for readablity.
- Error handling: The bot can handle invalid input such as invalid amounts. The bot will also provides instructions for correction when the users input the wrong formatted entries.

Project Files:
- money_logger.py
    The file contains all the logic of the bot and the process of storing user's data. It contains 5 functions (main, create_db, start, log, download).
    - main():
        initialize the bot application with the correct token, registers handlers for commands and messages, and begins polling for updates from Telegram.
    - create_db():
        Ensures the SQLite database exists and has the correct schema. The table expenses is created if it does not exist.
    - start():
        Handles the "/start" command, sending the users instructions about how to format their input and which commands are available.
    - log():
        Processes text messages that are not commands. This function splits the message by line breaks, validates the required fields, converts the amount of a number, and inserts the entry into the database (money_log.db). Expenses are stored as negative values, incomes as positive.
    - download():
        Handles the "/download" command by reading all rows from the database, creating an Excel workbook with headers and transaction rows, saving the file locally, and sending it back to the user.
- money_log.db:
    This is an SQLite file automatically created by the bot when the program starts to run. It contains the table expenses with the following schema:
    - id: Primary key, auto-incremented.
    - date: Timestamp of when the entry was inserted, defaulting to the current time.
    - category: Category of the transaction, provided by the user (e.g., Food, Transport, Salary, Pocket Money).
    - amount: A floating point number. Positive for income, negative for expense.
    - description: Optional text giving details about the transaction.
- money_log.xlsx:
    This is the Excel file generated on demand when the "/download" command is issued. It contains one sheet titled Expenses with the following columns:
    - Date
    - Category
    - Amount
    - Description

Design decisions:
- Database choice:
    SQLite was selected because it is lightweight and easy to integrate. Since this project is designed for a single-user environment and does not require multi threaded acccess at scale, SQLite is sufficient, providing both simplicity and reliability.
- Single-user choice:
    For a personal use, I think it is better to not spend money to rent a paid server, so I think it is better to run it on free server and use users' own bot for the sake of privacy. This make sure that none of the users' data get mixed up
- Data model:
    A design decision was made to store expenses as negative amounts rather than keeping separate "entry_type" column. This reduces schema complexity and allows quick calculations of total (e.g., summing all amount directly give net balance). The trade-off is that queries for only expenses or only incomes must filter by amount sign instead of entry type.
- Interface:
    Telegram was chosen as the users' interface because it is cross platform, familiar to users, and provide a clean API for interaction. This avoids the need for a separate frontend or mobile application. Users can log expenses from anywhere, even on the go, by simply chatting with the bot.
- Data export
    The decision to use Excel (.xlsx) instead of CSV was made for usability reasons. Excel is widely supported, allows richer formatting, and gives users the ability to perform analysis, filtering, and visualization directly. The openpyxl library makes it easy to programmatically format headers and extend functionality in the future. Furthermore, Excel file provide a more readable data for human than CSV fiile.
- Input Format:
    The bot expects a structured there or more line format:
    Expenses (Expense/Income)
    Category
    Amount
    Description (Optional)
    This design choice implement consistency at the cost of user convenience. Allowing free form inputs could improve user experience, it is achievable but would require more complex error handling.

Installation and Usage
1. Clone the repostiroy and install dependencies:
pip install python-telegram-bot==20.0 openpyxl
2. Replace the placeholder "YOUR_BOT_TOKEN" in money_logger.py with your actual Telegram bot token obtained from Botfather.
3. Run the program:
python money_logger.py
4. Interact with your bot in Telegram:
    - Use /start to see introductions.
    - Send a transaction in the required format.
    - Use /download to receive your expense history in Excel format

This project is open for anyone to use, modify, and adapt to their own needs. You are free to build on top of the code, improve it, or customize it for your personal use. All I ask is that if you share or publish your version of this project, you give proper credit to the original author: Andrew Zhuo
