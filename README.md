mkdir uncle-steve
cd uncle-steve
uncle-steve/
├── index.html
├── style.css
└── script.js
!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Uncle Steve - Breakout & Retest Strategy</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <h1>Uncle Steve</h1>
        <p>Breakout & Retest Strategy for Forex & Crypto</p>
    </header>
    <main>
        <section id="chart-container">
            <div id="chart"></div>
        </section>
        <section id="controls">
            <label for="market">Select Market:</label>
            <select id="market">
                <option value="BTC/USD">BTC/USD</option>
                <option value="ETH/USD">ETH/USD</option>
                <option value="EUR/USD">EUR/USD</option>
                <option value="GBP/USD">GBP/USD</option>
            </select>
            <button id="loadChart">Load Chart</button>
        </section>
        <section id="signals">
            <h2>Trading Signals</h2>
            <ul id="signal-list"></ul>
        </section>
    </main>
    <footer>
        <p>Powered by Uncle Steve</p>
    </footer>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="script.js"></script>
</body>
</html>
style.css

body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f4f4f4;
}

header {
    background-color: #333;
    color: white;
    text-align: center;
    padding: 1rem;
}

main {
    padding: 2rem;
}

#chart-container {
    width: 100%;
    height: 400px;
    background-color: white;
    margin-bottom: 2rem;
}

#controls {
    margin-bottom: 2rem;
}

#controls label,
#controls select,
#controls button {
    margin-right: 1rem;
}

#signals {
    background-color: white;
    padding: 1rem;
    border: 1px solid #ddd;
}

footer {
    background-color: #333;
    color: white;
    text-align: center;
    padding: 1rem;
    position: fixed;
    width: 100%;
    bottom: 0;
}
script.js

document.getElementById('loadChart').addEventListener('click', function() {
    const market = document.getElementById('market').value;
    loadChart(market);
    checkSignals(market);
});

function loadChart(market) {
    const ctx = document.getElementById('chart').getContext('2d');
    // Fetch market data and render chart
    // For simplicity, using static data here
    const data = {
        labels: ['January', 'February', 'March', 'April', 'May'],
        datasets: [{
            label: market,
            data: [65, 59, 80, 81, 56],
            borderColor: 'rgba(75, 192, 192, 1)',
            borderWidth: 1
        }]
    };
    new Chart(ctx, {
        type: 'line',
        data: data,
        options: {
            responsive: true,
            scales: {
                y: {
                    beginAtZero: true
                }
            }
        }
    });
}

function checkSignals(market) {
    const signals = document.getElementById('signal-list');
    signals.innerHTML = ''; // Clear previous signals

    // Example logic for signal detection
    if (market === 'BTC/USD') {
        const signal = document.createElement('li');
        signal.textContent = 'Buy Signal: Breakout above $60,000';
        signals.appendChild(signal);
    } else if (market === 'ETH/USD') {
        const signal = document.createElement('li');
        signal.textContent = 'Sell Signal: Retest at $4,000';
        signals.appendChild(signal);
    }
}
