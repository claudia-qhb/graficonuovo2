# graficonuovo2
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grafico Payoff Opzioni</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
    <h2>Grafico Payoff delle Opzioni</h2>

    <!-- Input per personalizzare il grafico -->
    <label>Strike Price: <input type="number" id="strikePrice" value="100"></label>
    <label>Premio: <input type="number" id="premio" value="10"></label>
    <label>Prezzo Sottostante: <input type="number" id="prezzoSottostante" value="100"></label>
    <label>Scadenza (gg): <input type="number" id="scadenza" value="30"></label>
    <label>Quantità: <input type="number" id="quantita" value="1"></label>
    <label>Posizione (positivo = long, negativo = short): 
        <input type="number" id="posizione" value="1">
    </label>
    <button onclick="aggiornaGrafico()">Aggiorna Grafico</button>

    <canvas id="payoffChart"></canvas>

    <script>
        let chart = null; // Variabile per salvare il grafico

        function aggiornaGrafico() {
            const strike = parseFloat(document.getElementById("strikePrice").value);
            const premio = parseFloat(document.getElementById("premio").value);
            const prezzoAttuale = parseFloat(document.getElementById("prezzoSottostante").value);
            const scadenza = parseInt(document.getElementById("scadenza").value);
            const quantita = parseInt(document.getElementById("quantita").value);
            const posizione = parseInt(document.getElementById("posizione").value);

            const minPrice = strike - 50;
            const maxPrice = strike + 50;
            const step = 2;

            let xValues = [];
            let payoffCall = [], payoffPut = [];

            for (let price = minPrice; price <= maxPrice; price += step) {
                xValues.push(price);

                let callProfit = quantita * posizione * (Math.max(price - strike, 0) - premio);
                let putProfit = quantita * posizione * (Math.max(strike - price, 0) - premio);

                payoffCall.push(callProfit);
                payoffPut.push(putProfit);
            }

            if (chart) {
                chart.destroy(); // Elimina il vecchio grafico per aggiornarlo
            }

            const ctx = document.getElementById('payoffChart').getContext('2d');
            chart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: xValues,
                    datasets: [
                        { label: 'Call', data: payoffCall, borderColor: 'blue', borderWidth: 2, fill: false },
                        { label: 'Put', data: payoffPut, borderColor: 'red', borderWidth: 2, fill: false }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        x: { title: { display: true, text: 'Prezzo del Sottostante' } },
                        y: { title: { display: true, text: 'Profitto / Perdita' } }
                    }
                }
            });
        }

        aggiornaGrafico(); // Disegna il grafico iniziale
    </script>
</body>
</html>
