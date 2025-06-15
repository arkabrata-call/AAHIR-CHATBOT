from flask import Flask, request, jsonify, render_template_string
from datetime import datetime, timedelta

app = Flask(__name__)

# Frontend Interface
html_code = '''
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AAHIR Chatbot</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #eef2f7;
            padding: 20px;
        }
        .container {
            max-width: 800px;
            margin: auto;
            background: #fff;
            padding: 25px;
            border-radius: 12px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        input, button {
            width: 100%;
            padding: 10px;
            margin-top: 8px;
            border-radius: 8px;
            border: 1px solid #ccc;
        }
        button {
            background-color: #007bff;
            color: white;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        pre {
            background: #f4f4f4;
            padding: 10px;
            border-radius: 6px;
            white-space: pre-wrap;
        }
    </style>
</head>
<body>
    <h1 style="text-align:center;">🤖 AAHIR Chatbot</h1>
    <div class="container">

        <h3>🌤️ Weather</h3>
        <input id="weatherCity" placeholder="Enter city (e.g. kolkata)">
        <button onclick="getWeather()">Get Weather</button>
        <pre id="weatherResult"></pre>

        <h3>🕒 Current Time</h3>
        <button onclick="getTime()">Get IST Time</button>
        <pre id="timeResult"></pre>

        <h3>🔁 Unit Conversion</h3>
        <input id="value" placeholder="Value (e.g. 10)">
        <input id="from" placeholder="From (e.g. kg)">
        <input id="to" placeholder="To (e.g. lb)">
        <button onclick="convert()">Convert</button>
        <pre id="convertResult"></pre>

        <h3>🏙️ Capital Lookup</h3>
        <input id="country" placeholder="Enter country (e.g. india)">
        <button onclick="getCapital()">Get Capital</button>
        <pre id="capitalResult"></pre>
    </div>

    <script>
        async function getWeather() {
            const city = document.getElementById("weatherCity").value;
            const res = await fetch(`/weather?city=${city}`);
            const data = await res.json();
            document.getElementById("weatherResult").textContent = JSON.stringify(data, null, 2);
        }

        async function getTime() {
            const res = await fetch("/time");
            const data = await res.json();
            document.getElementById("timeResult").textContent = JSON.stringify(data, null, 2);
        }

        async function convert() {
            const v = document.getElementById("value").value;
            const f = document.getElementById("from").value;
            const t = document.getElementById("to").value;
            const res = await fetch(`/convert?value=${v}&from=${f}&to=${t}`);
            const data = await res.json();
            document.getElementById("convertResult").textContent = JSON.stringify(data, null, 2);
        }

        async function getCapital() {
            const country = document.getElementById("country").value;
            const res = await fetch(`/capital?country=${country}`);
            const data = await res.json();
            document.getElementById("capitalResult").textContent = JSON.stringify(data, null, 2);
        }
    </script>
</body>
</html>
'''

# Routes
@app.route('/')
def home():
    return render_template_string(html_code)

@app.route('/weather')
def get_weather():
    city = request.args.get('city', '').lower()
    sample_weather = {
        "kolkata": "cloudy", "mumbai": "rainy", "delhi": "sunny",
        "new york": "sunny", "london": "rainy", "tokyo": "cloudy"
    }
    weather_icons = {
        "sunny": "☀️", "cloudy": "☁️", "rainy": "🌧️",
        "stormy": "⛈️", "snowy": "❄️"
    }

    if city in sample_weather:
        condition = sample_weather[city]
        return jsonify({
            "city": city.title(),
            "condition": condition,
            "icon": weather_icons.get(condition, "🌍")
        })
    return jsonify({"error": f"Weather data not found for '{city}'"}), 404

@app.route('/time')
def get_time():
    ist = datetime.utcnow() + timedelta(hours=5, minutes=30)
    return jsonify({
        "Indian Time": ist.strftime("%Y-%m-%d %H:%M:%S"),
        "emoji": "🕒"
    })

@app.route('/convert')
def convert_units():
    value = request.args.get('value')
    from_unit = request.args.get('from', '').lower()
    to_unit = request.args.get('to', '').lower()

    try:
        value = float(value)
        conversions = {
            "kg": {"lb": 2.20462}, "lb": {"kg": 0.453592},
            "cm": {"inch": 0.393701}, "inch": {"cm": 2.54},
            "c": {"f": lambda x: (x * 9/5) + 32},
            "f": {"c": lambda x: (x - 32) * 5/9}
        }

        if from_unit in conversions and to_unit in conversions[from_unit]:
            factor = conversions[from_unit][to_unit]
            result = factor(value) if callable(factor) else value * factor
            return jsonify({
                "input": f"{value} {from_unit}",
                "output": f"{round(result, 2)} {to_unit}"
            })
        return jsonify({"error": "Unsupported conversion!"}), 400

    except:
        return jsonify({"error": "Invalid value!"}), 400

@app.route('/capital')
def get_capital():
    country = request.args.get('country', '').lower()
    capitals = {
        "india": "New Delhi", "usa": "Washington, D.C.", "uk": "London",
        "japan": "Tokyo", "france": "Paris", "germany": "Berlin",
        "china": "Beijing", "canada": "Ottawa", "australia": "Canberra"
    }

    if country in capitals:
        return jsonify({
            "country": country.title(),
            "capital": capitals[country]
        })
    return jsonify({"error": f"Capital not found for '{country}'"}), 404

if __name__ == '__main__':
    app.run(debug=True)
