# Expense-Tracker
A web application for tracking income and expenses.
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Expense Tracker</title>

    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: Arial, sans-serif;
        }

        body {
            background: #f4f6f8;
            min-height: 100vh;
            padding: 30px;
        }

        .container {
            max-width: 700px;
            margin: auto;
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 5px 20px rgba(0, 0, 0, 0.1);
        }

        h1 {
            text-align: center;
            color: #333;
            margin-bottom: 25px;
        }

        .balance {
            background: #4CAF50;
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            margin-bottom: 20px;
        }

        .balance h2 {
            font-size: 32px;
            margin-top: 10px;
        }

        .summary {
            display: flex;
            gap: 15px;
            margin-bottom: 25px;
        }

        .summary div {
            flex: 1;
            padding: 15px;
            border-radius: 10px;
            text-align: center;
        }

        .income {
            background: #d4edda;
            color: #155724;
        }

        .expense {
            background: #f8d7da;
            color: #721c24;
        }

        .form {
            margin-bottom: 25px;
        }

        .form input,
        .form select {
            width: 100%;
            padding: 12px;
            margin: 8px 0;
            border: 1px solid #ccc;
            border-radius: 6px;
        }

        button {
            width: 100%;
            padding: 12px;
            background: #007bff;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 16px;
        }

        button:hover {
            background: #0056b3;
        }

        h3 {
            margin-bottom: 15px;
            color: #333;
        }

        #transactionList {
            list-style: none;
        }

        .transaction {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px;
            margin-bottom: 10px;
            border-radius: 8px;
            background: #f8f9fa;
            border-left: 5px solid;
        }

        .transaction.income-item {
            border-color: #28a745;
        }

        .transaction.expense-item {
            border-color: #dc3545;
        }

        .transaction-info {
            flex: 1;
        }

        .transaction-info strong {
            display: block;
            margin-bottom: 5px;
        }

        .transaction-info small {
            color: #777;
        }

        .amount {
            font-weight: bold;
            margin-right: 15px;
        }

        .delete-btn {
            width: auto;
            padding: 7px 10px;
            background: #dc3545;
            font-size: 14px;
        }

        .delete-btn:hover {
            background: #a71d2a;
        }

        @media (max-width: 600px) {
            body {
                padding: 15px;
            }

            .container {
                padding: 20px;
            }

            .summary {
                flex-direction: column;
            }
        }
    </style>
</head>

<body>

<div class="container">

    <h1>💰 Expense Tracker</h1>

    <div class="balance">
        <p>Current Balance</p>
        <h2 id="balance">₹0.00</h2>
    </div>

    <div class="summary">
        <div class="income">
            <p>Total Income</p>
            <h3 id="income">₹0.00</h3>
        </div>

        <div class="expense">
            <p>Total Expenses</p>
            <h3 id="expense">₹0.00</h3>
        </div>
    </div>

    <div class="form">
        <h3>Add Transaction</h3>

        <input
            type="text"
            id="description"
            placeholder="Enter description"
        >

        <input
            type="number"
            id="amount"
            placeholder="Enter amount"
        >

        <select id="type">
            <option value="expense">Expense</option>
            <option value="income">Income</option>
        </select>

        <button onclick="addTransaction()">
            Add Transaction
        </button>
    </div>

    <h3>Transaction History</h3>

    <ul id="transactionList"></ul>

</div>

<script>
    let transactions =
        JSON.parse(localStorage.getItem("transactions")) || [];

    function addTransaction() {

        const description =
            document.getElementById("description").value.trim();

        const amount =
            Number(document.getElementById("amount").value);

        const type =
            document.getElementById("type").value;

        if (description === "" || amount <= 0) {
            alert("Please enter a valid description and amount.");
            return;
        }

        const transaction = {
            id: Date.now(),
            description: description,
            amount: amount,
            type: type
        };

        transactions.push(transaction);

        saveTransactions();
        updateUI();

        document.getElementById("description").value = "";
        document.getElementById("amount").value = "";
    }

    function deleteTransaction(id) {

        transactions = transactions.filter(
            transaction => transaction.id !== id
        );

        saveTransactions();
        updateUI();
    }

    function saveTransactions() {
        localStorage.setItem(
            "transactions",
            JSON.stringify(transactions)
        );
    }

    function updateUI() {

        const transactionList =
            document.getElementById("transactionList");

        transactionList.innerHTML = "";

        let totalIncome = 0;
        let totalExpense = 0;

        transactions.forEach(transaction => {

            if (transaction.type === "income") {
                totalIncome += transaction.amount;
            } else {
                totalExpense += transaction.amount;
            }

            const li = document.createElement("li");

            li.className =
                `transaction ${
                    transaction.type === "income"
                        ? "income-item"
                        : "expense-item"
                }`;

            li.innerHTML = `
                <div class="transaction-info">
                    <strong>${transaction.description}</strong>
                    <small>
                        ${transaction.type === "income"
                            ? "Income"
                            : "Expense"}
                    </small>
                </div>

                <span class="amount">
                    ${transaction.type === "income" ? "+" : "-"}
                    ₹${transaction.amount.toFixed(2)}
                </span>

                <button
                    class="delete-btn"
                    onclick="deleteTransaction(${transaction.id})">
                    Delete
                </button>
            `;

            transactionList.appendChild(li);
        });

        const balance = totalIncome - totalExpense;

        document.getElementById("income").textContent =
            `₹${totalIncome.toFixed(2)}`;

        document.getElementById("expense").textContent =
            `₹${totalExpense.toFixed(2)}`;

        document.getElementById("balance").textContent =
            `₹${balance.toFixed(2)}`;
    }

    updateUI();
</script>

</body>
</html>
