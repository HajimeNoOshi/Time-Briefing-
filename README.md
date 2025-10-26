<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Daily Briefing</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to right, #ff0000, #ff99cc); /* red-pink gradient */
      color: white;
      padding: 20px;
      max-width: 600px;
      margin: 0 auto;
      border-radius: 20px;
      box-shadow: 0 0 20px rgba(0,0,0,0.2);
    }
    h2 { color: #fff176; text-align: center; }
    .weather-box {
      margin-top: 20px;
      padding: 15px;
      background: rgba(255,255,255,0.2);
      border-radius: 10px;
      color: white;
    }
    p { line-height: 1.5; }
  </style>
</head>
<body>
  <h2 id="greeting">GOOD MORNING!</h2>
  <p id="officer-name">
    C/PVT (name of cadet) asking for permission to make a statement on behalf of the medics unit for the
    assignment to send safety precautions every 20 minutes.
  </p>
  <p id="Weather-forecast">
    Weather forecast for this morning according to Open-Meteo:
  </p>

  <div class="weather-box" id="weather-box">
    <p><b>Temperature:</b> <span id="temp">--</span> °C</p>
    <p><b>Wind:</b> <span id="wind">--</span> km/h <span id="wind-dir">--</span></p>
    <p><b>Max UV Index:</b> <span id="uv">--</span></p>
    <p><b>(POP):</b> <span id="pop">--</span> mm</p>
  </div>

  <p id="time"></p>
  <p id="valid-until"></p>
  <p id="countdown">Next update in: 20:00</p>
  <p id="source">Source: Open-Meteo (https://open-meteo.com/)</p>
  <p>THANK YOU!</p>

  <script>
    // Convert wind degrees to compass direction
    function degToCompass(num) {
      const val = Math.floor((num / 22.5) + 0.5);
      const arr = ["N","NNE","NE","ENE","E","ESE","SE","SSE","S","SSW","SW","WSW","W","WNW","NW","NNW"];
      return arr[(val % 16)];
    }

    async function getWeather() {
      try {
        const lat = 14.5995; // Manila latitude
        const lon = 120.9842; // Manila longitude
        const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true&hourly=precipitation,uv_index`;

        const response = await fetch(url);
        const data = await response.json();
        const current = data.current_weather;

        document.getElementById("temp").textContent = current.temperature;
        document.getElementById("wind").textContent = current.windspeed;
        document.getElementById("wind-dir").textContent = degToCompass(current.winddirection);

        // Placeholder using first hourly values
        document.getElementById("uv").textContent = data.hourly.uv_index[0];
        document.getElementById("pop").textContent = data.hourly.precipitation[0];

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

    // Countdown timer
    let countdownSeconds = 20 * 60; // 20 minutes in seconds
    function startCountdown() {
      const countdownEl = document.getElementById("countdown");

      const interval = setInterval(() => {
        const minutes = Math.floor(countdownSeconds / 60);
        const seconds = countdownSeconds % 60;
        countdownEl.textContent = `Next update in: ${minutes.toString().padStart(2,'0')}:${seconds.toString().padStart(2,'0')}`;

        if (countdownSeconds <= 0) {
          clearInterval(interval);
          countdownSeconds = 20 * 60; // reset
          getWeather(); // refresh weather
          startCountdown(); // restart timer
        } else {
          countdownSeconds--;
        }
      }, 1000);
    }

    // Initial calls
    getWeather();
    startCountdown();
  </script>
</body>
</html>
