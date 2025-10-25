<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Daily Briefing</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: black(to right, #121111, #f1f8e9);
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
  <p id="officer-name">
    C/PVT (name of cadet) asking for permission to make a statement on behalf of the medics unit for the
    assignment to send safety precautions every 20 minutes.
  </p>
  <p id="Weather-forecast">
    Weather forecast for this morning according to DOST-PAGASA, Metro Manila will experience.
  </p>

  <div class="weather-box" id="weather-box">
    <p><b>Temperature:</b> <span id="temp"></span> °C</p>
    <p><b>Heat Index:</b> <span id="heat"></span> °C</p>
    <p><b>Wind:</b> <span id="wind"></span> km/h</p>
    <p><b>(POP):</b> <span id="pop"></span> mm</p>
    <p><b>Max UV Index:</b> <span id="uv"></span></p>
  </div>

  <p id="time"></p>
  <p id="valid-until"></p>
  <p id="source">Source: Open-Meteo (https://open-meteo.com)</p>
  <p>MAAM THANK YOU MAAM SIR THANK YOU SIR</p>

  <script>
    async function getWeather() {
      try {
        const url = "https://api.open-meteo.com/v1/forecast?latitude=14.6&longitude=121.0&current_weather=true";
        const response = await fetch(url);
        const data = await response.json();

        // Open-Meteo returns current_weather object
        const current = data.current_weather;
        document.getElementById("temp").textContent = current.temperature;
        document.getElementById("heat").textContent = current.temperature; // Open-Meteo does not provide apparent temp directly
        document.getElementById("wind").textContent = current.windspeed;
        document.getElementById("pop").textContent = "N/A"; // optional, POP not in this endpoint
        document.getElementById("uv").textContent = "N/A";  // optional, UV index not in this endpoint

        // Manila time
        const now = new Date().toLocaleString("en-PH", { timeZone: "Asia/Manila" });
        document.getElementById("time").textContent = "📅 Manila Time: " + now;

        // Valid until (20 mins later)
        const validUntil = new Date(Date.now() + 20 * 60000);
        document.getElementById("valid-until").textContent =
          "Valid until: " + validUntil.toLocaleTimeString("en-PH", { hour: "2-digit", minute: "2-digit" });
      } catch (err) {
        console.error("Error fetching weather:", err);
        document.getElementById("weather-box").textContent = "Error loading weather data.";
      }
    }

    // Call once immediately
    getWeather();

    // Auto-update every 20 minutes
    setInterval(getWeather, 20 * 60 * 1000);
  </script>
</body>
</html>
