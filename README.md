<!DOCTYPE html>
<html>
<head>
    <title>Expense Tracker Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body {
            font-family: Arial;
            background: #f4f6f9;
            margin: 0;
            padding: 0;
        }
        header {
            background: #4CAF50;
            color: white;
            padding: 15px;
            text-align: center;
        }
        .container {
            width: 90%;
            margin: 20px auto;
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
        }
        .card {
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        input, select, button {
            padding: 8px;
            margin: 5px;
            width: 95%;
        }
        button {
            background: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
        }
        table {
            width: 100%;
            margin-top: 10px;
            border-collapse: collapse;
        }
        table, th, td {
            border: 1px solid #ddd;
        }
        th, td {
            padding: 8px;
            text-align: center;
        }
        .total {
            font-size: 20px;
            margin-top: 10px;
            font-weight: bold;
        }
    </style>
</head>
<body>

<header>
    <h2>💰 Expense Tracker Dashboard</h2>
</header>

<div class="container">

    <!-- Add Expense -->
    <div class="card">
        <h3>Add Expense</h3>
        <input type="number" id="amount" placeholder="Amount">
        <select id="category">
            <option>Food</option>
            <option>Transport</option>
            <option>Shopping</option>
            <option>Entertainment</option>
            <option>Bills</option>
        </select>
        <input type="month" id="month">
        <button onclick="addExpense()">Add</button>

        <div class="total">
            Total: ₹ <span id="totalAmount">0</span>
        </div>

        <table id="expenseTable">
            <thead>
                <tr>
                    <th>Amount</th>
                    <th>Category</th>
                    <th>Month</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>
    </div>

    <!-- Charts -->
    <div class="card">
        <h3>Expense Analysis</h3>
        <canvas id="pieChart"></canvas>
        <br>
        <canvas id="barChart"></canvas>
    </div>

</div>

<script>
let expenses = JSON.parse(localStorage.getItem("expenses")) || [];
let total = 0;

const pieCtx = document.getElementById("pieChart").getContext("2d");
const barCtx = document.getElementById("barChart").getContext("2d");

let pieChart = new Chart(pieCtx, {
    type: "pie",
    data: {
        labels: ["Food","Transport","Shopping","Entertainment","Bills"],
        datasets: [{ data: [0,0,0,0,0] }]
    }
});

let barChart = new Chart(barCtx, {
    type: "bar",
    data: {
        labels: ["Food","Transport","Shopping","Entertainment","Bills"],
        datasets: [{ label: "Expenses", data: [0,0,0,0,0] }]
    }
});

function addExpense() {
    let amount = parseFloat(document.getElementById("amount").value);
    let category = document.getElementById("category").value;
    let month = document.getElementById("month").value;

    if (!amount || !month) {
        alert("Please enter valid data");
        return;
    }

    expenses.push({amount, category, month});
    localStorage.setItem("expenses", JSON.stringify(expenses));

    document.getElementById("amount").value = "";
    displayExpenses();
}

function deleteExpense(index) {
    expenses.splice(index, 1);
    localStorage.setItem("expenses", JSON.stringify(expenses));
    displayExpenses();
}

function displayExpenses() {
    let tbody = document.querySelector("#expenseTable tbody");
    tbody.innerHTML = "";
    total = 0;

    let categoryTotals = {
        Food:0, Transport:0, Shopping:0, Entertainment:0, Bills:0
    };

    expenses.forEach((exp, index) => {
        total += exp.amount;
        categoryTotals[exp.category] += exp.amount;

        tbody.innerHTML += `
            <tr>
                <td>₹ ${exp.amount}</td>
                <td>${exp.category}</td>
                <td>${exp.month}</td>
                <td><button onclick="deleteExpense(${index})">Delete</button></td>
            </tr>
        `;
    });

    document.getElementById("totalAmount").innerText = total;

    pieChart.data.datasets[0].data = Object.values(categoryTotals);
    pieChart.update();

    barChart.data.datasets[0].data = Object.values(categoryTotals);
    barChart.update();
}

displayExpenses();
</script>

</body>
</html>
