---
title: Welcome to my blog
---
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Altura Máxima en un Salto</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin-top: 30px;
    }
    input, button, select {
      padding: 10px;
      font-size: 16px;
      margin: 8px;
    }
    #grafica {
      max-width: 400px;
      margin: 30px auto;
    }
  </style>
</head>
<body>
  <h1>Altura Máxima en un Salto</h1>

  <p>Ambiente:</p>
  <select id="ambiente">
    <option value="cerrado">🏠 Cuarto cerrado</option>
    <option value="libre">🌬️ Aire libre</option>
  </select><br>

  <p>Tiempo total en el aire (en segundos):</p>
  <input type="number" id="tiempo" step="0.01" min="0" placeholder="Ej. 1.2"><br>

  <p>Masa corporal (en kilogramos):</p>
  <input type="number" id="masa" step="0.1" min="1" placeholder="Ej. 70"><br>

  <button onclick="calcularAltura()">Calcular Altura</button>

  <h2 id="resultado"></h2>

  <div id="grafica">
    <canvas id="barraAltura"></canvas>
  </div>

  <script>
    let chart = null;

    function calcularAltura() {
      const g = 9.8;
      const rho = 1.225;
      const A = 0.7;

      const tiempoTotal = parseFloat(document.getElementById("tiempo").value);
      const masa = parseFloat(document.getElementById("masa").value);
      const ambiente = document.getElementById("ambiente").value;

      const Cd = ambiente === "cerrado" ? 1.0 : 1.2;

      if (isNaN(tiempoTotal) || tiempoTotal <= 0 || isNaN(masa) || masa <= 0) {
        document.getElementById("resultado").textContent = "Por favor, ingresa valores válidos.";
        return;
      }

      const tSubida = tiempoTotal / 2;
      const v0 = g * tSubida;
      const hIdeal = 0.5 * g * Math.pow(tSubida, 2);
      const perdidaAltura = (Cd * rho * A * Math.pow(v0, 2)) / (4 * masa * g);
      const hReal = hIdeal - perdidaAltura;

      const hIdeal_cm = hIdeal * 100;
      const hReal_cm = hReal * 100;

      document.getElementById("resultado").innerHTML = `
        <strong>Altura sin resistencia:</strong> ${hIdeal_cm.toFixed(5)} cm<br>
        <strong>Altura con resistencia (${ambiente === "cerrado" ? "cuarto cerrado" : "aire libre"}):</strong> ${hReal_cm.toFixed(5)} cm
      `;

      actualizarGrafico(hIdeal_cm, hReal_cm, ambiente);
    }

    function actualizarGrafico(ideal, real, ambiente) {
      const ctx = document.getElementById('barraAltura').getContext('2d');
      if (chart) chart.destroy();

      chart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: ['Sin aire', ambiente === "cerrado" ? 'Cuarto cerrado' : 'Aire libre'],
          datasets: [{
            label: 'Altura (cm)',
            data: [ideal, real],
            backgroundColor: ['#4CAF50', '#2196F3']
          }]
        },
        options: {
          responsive: true,
          scales: {
            y: {
              beginAtZero: true,
              title: {
                display: true,
                text: 'Centímetros'
              }
            }
          }
        }
      });
    }

    // Tecla Enter
    ["tiempo", "masa", "ambiente"].forEach(id => {
      document.getElementById(id).addEventListener("keydown", function(e) {
        if (e.key === "Enter") calcularAltura();
      });
    });
  </script>
</body>
</html>
