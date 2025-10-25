<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Daily Briefing</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to right, #e0f7fa, #f1f8e9);
      color: #222;
      padding: 20px;
      max-width: 600px;
      margin: 0 auto;
      border-radius: 20px;
      box-shadow: 0 0 20px rgba(0,0,0,0.1);
    }
    h2 { color: #00796b; }
    .weather-box {
      margin-top: 10px;
      padding: 15px;
      background: #ffffffb8;
      border-radius: 10px;
    }
  </style>
</head>
<body>
  <h2 id="greeting">MAAM GOOD MORNING MAAM SIR GOOD MORNING SIR!</h2>
  <p id="officer-name">C/PVT (name of cadet) asking for permission to make a statement on behalf of the medics unit for the
  assignment to send safety precautions every 20 minutes.</p>
  <p id="Weather-forecast">Weather forecast for this morning according to DOST-PAGASA, Metro Manila will experience.</p>

  <div class="weather-box">
    <p><b>Temperature:</b> <span id="temp"></span> °C</p>
    <p><b>Heat Index:</b> <span id="heat"></span> °C</p>
    <p><b>Wind:</b> <span id="wind"></span> direction-km/h</p>
    <p><b>(POP):</b> <span id="pop"></span> mm</p>
    <p><b>Max UV Index:</b> <span id="uv"></span></p>
  </div>

  <p id="time"></p>
  <p id="valid-until"></p>
  <p id="source">Source: Open-Meteo (https://open-meteo.com)</p>
  <p>MAAM THANK YOU MAAM SIR THANK YOU SIR</p>

  <script>
    async function getWeather() {
      const url = "https://api.open-meteo.com/v1/forecast?latitude=14.6&longitude=121.0&current=temperature_2m,apparent_temperature,precipitation,wind_speed_10m,uv_index";
      const response = await fetch(url);
      const data = await response.json();

      const current = data.current;
      document.getElementById("temp").textContent = current.temperature_2m;
      document.getElementById("heat").textContent = current.apparent_temperature;
      document.getElementById("wind").textContent = current.wind_speed_10m;
      document.getElementById("pop").textContent = current.precipitation;
      document.getElementById("uv").textContent = current.uv_index;

      // Manila time
      const now = new Date().toLocaleString("en-PH", { timeZone: "Asia/Manila" });
      document.getElementById("time").textContent = "📅 Manila Time: " + now;

      // Valid until (20 mins later)
      const validUntil = new Date(Date.now() + 20 * 60000);
      document.getElementById("valid-until").textContent =
        "Valid until: " + validUntil.toLocaleTimeString("en-PH", { hour: "2-digit", minute: "2-digit" });
      
    getWeather();
  </script>
</body>
</html>
