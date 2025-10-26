<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Daily Briefing</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #000000; /* black background */
      color: #ffffff;      /* default text white */
      padding: 20px;
      max-width: 600px;
      margin: 0 auto;
      border-radius: 20px;
      box-shadow: 0 0 20px rgba(255,255,255,0.1);
    }
    h2 { color: #ffffff; text-align: center; }
    .weather-box {
      margin-top: 20px;
      padding: 15px;
      background: rgba(255,255,255,0.1); /* semi-transparent overlay */
      border-radius: 10px;
      color: white;
    }
    p { line-height: 1.5; }
    #countdown { color: #ffeb3b; font-weight: bold; }
    #temp { color: #ff4500; }
    #heat { color: #ff6347; }
    #wind { color: #00ffff; }
    #wind-dir { color: #ffffff; margin-left: 5px; }
    #pop { color: #00ff00; }
    #uv { color: #ff1493; }
    #source a { color: #00ff00; text-decoration: underline; }

    /* Arrow style */
    .wind-arrow {
      display: inline-block;
      transition: transform 0.5s ease;
      color: #ffffff;
      font-weight: bold;
      margin-left: 5px;
    }
  </style>
</head>
<body>
  <h2 id="greeting">MAAM GOOD MORNING MAAM SIR GOOD MORNING SIR</h2>

  <p id="officer-name">
    C/PVT (name of cadet) asking permission to make a statement on behalf of the medics unit for the assignment to send safety precautions every 20 minutes.
  </p>

  <!-- Customizable Weather Summary -->
  <p id="weather-summary">Loading weather summary...</p>

  <div class="weather-box" id="weather-box">
    <p><b>Temperature:</b> <span id="temp">--</span> °C</p>
    <p><b>Heat Index:</b> <span id="heat">--</span> °C</p>
    <p>
      <b>Wind:</b> <span id="wind">--</span> km/h 
      <span id="wind-dir">--</span>
      <span class="wind-arrow" id="wind-arrow">↑</span>
    </p>
    <p><b>(POP):</b> <span id="pop">--</span> mm</p>
    <p><b>Max UV Index:</b> <span id="uv">--</span></p>
  </div>

  <p id="time"></p>
  <p id="valid-until"></p>
  <p id="countdown">Next update in: 20:00</p>
  <p id="source">Source: <a href="https://open-meteo.com/" target="_blank">Open-Meteo</a></p>
  <p>MAAM THANK YOU MAAM SIR THANK YOU SIR</p>

  <script>
    // Convert degrees to compass direction
    function degToCompass(num) {
      const val = Math.floor((num / 22.5) + 0.5);
      const arr = ["N","NNE","NE","ENE","E","ESE","SE","SSE","S","SSW","SW","WSW","W","WNW","NW","NNW"];
      return arr[(val % 16)];
    }

    async function getWeather() {
      try {
        const lat = 14.5995; // Manila
        const lon = 120.9842;
        const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true&hourly=apparent_temperature,precipitation,uv_index`;

        const response = await fetch(url);
        const data = await response.json();
        const current = data.current_weather;

        // Temperature, Wind, Heat Index, POP, UV
        document.getElementById("temp").textContent = current.temperature;
        document.getElementById("wind").textContent = current.windspeed;
        document.getElementById("wind-dir").textContent = degToCompass(current.winddirection);
        document.getElementById("wind-arrow").style.transform = `rotate(${current.winddirection}deg)`;
        document.getElementById("heat").textContent = data.hourly.apparent_temperature[0];
        document.getElementById("pop").textContent = data.hourly.precipitation[0];
        document.getElementById("uv").textContent = data.hourly.uv_index[0];

        // Manila time
        const now = new Date().toLocaleString("en-PH", { timeZone: "Asia/Manila" });
        document.getElementById("time").textContent = "📅 Manila Time: " + now;

        // Valid until
        const validUntil = new Date(Date.now() + 20 * 60000);
        document.getElementById("valid-until").textContent =
          "Valid until: " + validUntil.toLocaleTimeString("en-PH", { hour: "2-digit", minute: "2-digit" });

        // Customizable summary
        const tempSummary = `Temperature is ${current.temperature}°C with a heat index of ${data.hourly.apparent_temperature[0]}°C.`;
        const windSummary = `Wind is blowing at ${current.windspeed} km/h from ${degToCompass(current.winddirection)}.`;
        const popSummary = `Chance of precipitation: ${data.hourly.precipitation[0]} mm.`;
        const uvSummary = `Max UV index is ${data.hourly.uv_index[0]}.`;
        const summary = `Weather Summary: ${tempSummary} ${windSummary} ${popSummary} ${uvSummary}`;
        document.getElementById("weather-summary").textContent = summary;

      } catch (err) {
        console.error("Error fetching weather:", err);
        document.getElementById("weather-box").textContent = "Error loading weather data.";
        document.getElementById("weather-summary").textContent = "Weather summary unavailable.";
      }
    }

    // Countdown timer
    let countdownSeconds = 20 * 60;
    function startCountdown() {
      const countdownEl = document.getElementById("countdown");

      const interval = setInterval(() => {
        const minutes = Math.floor(countdownSeconds / 60);
        const seconds = countdownSeconds % 60;
        countdownEl.textContent = `Next update in: ${minutes.toString().padStart(2,'0')}:${seconds.toString().padStart(2,'0')}`;

        if (countdownSeconds <= 0) {
          clearInterval(interval);
          countdownSeconds = 20 * 60;
          getWeather();
          startCountdown();
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
