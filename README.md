<!DOCTYPE html>
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
        <section id="controls">
            <label for="market">Select Market:</label>
            <select id="market">
                <option value="BTCUSDT">BTC/USD</option>
                <option value="ETHUSDT">ETH/USD</option>
                <option value="EURUSD">EUR/USD</option>
                <option value="GBPUSD">GBP/USD</option>
            </select>
        </section>

        <section id="chart-container">
            <canvas id="priceChart"></canvas>
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

#controls {
    text-align: center;
    margin-bottom: 2rem;
}

#controls label,
#controls select {
    margin-right: 1rem;
}

#chart-container {
    width: 100%;
    max-width: 800px;
    margin: auto;
    margin-bottom: 2rem;
    background: #fff;
    padding: 1rem;
    box-shadow: 0 0 10px rgba(0,0,0,0.1);
}

#signals {
    background-color: white;
    max-width: 800px;
    margin: auto;
    padding: 1rem;
    border: 1px solid #ddd;
}

footer {
    background-color: #333;
    color: white;
    text-align: center;
    padding: 1rem;
    margin-top: 2rem;

let priceHistory = [];
let chart;
let selectedPair = 'BTCUSDT';

document.getElementById('market').addEventListener('change', function () {
    selectedPair = this.value;
    priceHistory = [];
    if (chart) chart.destroy();
    document.getElementById('signal-list').innerHTML = '';
});

function fetchPrice(pair = 'BTCUSDT') {
    const url = pair.includes('USDT') ?
        `https://api.binance.com/api/v3/ticker/price?symbol=${pair}` :
        `https://freeforexapi.com/api/live?pairs=${pair}`;

    fetch(url)
        .then(res => res.json())
        .then(data => {
            const price = parseFloat(pair.includes('USDT') ? data.price : data.rates[pair].rate);
            priceHistory.push(price);
            if (priceHistory.length > 50) priceHistory.shift();

            updateChart(priceHistory);
            checkBreakoutRetestSignals(price);
        })
        .catch(err => console.error("Price fetch error", err));
}

function updateChart(data) {
    const ctx = document.getElementById('priceChart').getContext('2d');
    if (chart) chart.destroy();

    chart = new Chart(ctx, {
        type: 'line',
        data: {
            labels: data.map((_, i) => i + 1),
            datasets: [{
                label: `${selectedPair} Price`,
                data: data,
                borderColor: 'rgba(75, 192, 192, 1)',
                fill: false,
                tension: 0.3
            }]
        },
        options: {
            responsive: true,
            scales: {
                y: { beginAtZero: false }
            }
        }
    });
}

function fetchPivotPoints(pair = 'EURUSD') {
    fetch(`https://api.taapi.io/pivotpoints?secret=YOUR_SECRET_KEY&exchange=forex&symbol=${pair}&interval=1d`)
        .then(res => res.json())
        .then(data => {
            if (data.status === 'ok') {
                const { pp, r1, r2, r3, s1, s2, s3 } = data.value;
                detectBreakoutRetest(pp, r1, r2, r3, s1, s2, s3);
            }
        })
        .catch(err => console.error("Pivot fetch error", err));
}
function detectBreakoutRetest(pp, r1, r2, r3, s1, s2, s3) {
    const signalsList = document.getElementById('signal-list');
    signalsList.innerHTML = '';

    const currentPrice = priceHistory[priceHistory.length - 1];
    const lastPrice = priceHistory[priceHistory.length - 2] || currentPrice;

    const tolerance = 0.002; // 0.2% tolerance

    if (lastPrice < r1 && currentPrice >= r1 * (1 - tolerance)) {
        const li = document.createElement('li');
        li.textContent = `BUY SIGNAL: Retest & bounce from resistance at $${r1}`;
        signalsList.appendChild(li);
    }

    if (lastPrice > s1 && currentPrice <= s1 * (1 + tolerance)) {
        const li = document.createElement('li');
        li.textContent = `SELL SIGNAL: Retest & rejection from support at $${s1}`;
        signalsList.appendChild(li);
    }
}

setInterval(() => {
    fetchPrice(selectedPair);
    if (selectedPair.includes('USD')) {
        fetchPivotPoints(selectedPair);
    }
}, 5000);
