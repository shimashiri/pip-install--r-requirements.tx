# pip-install--r-requirements.tx
from flask import Flask, render_template_string, request
import requests

app = Flask(__name__)

API_KEY = "https://wttr.in"  # Free weather source (no API key needed)

HTML_TEMPLATE = """
<!DOCTYPE html>
<html>
<head>
    <title>🌦 Weather Dashboard</title>
    <style>
        body { font-family: Arial; background: #f0f2f5; text-align: center; padding: 50px; }
        .box { background: white; padding: 20px; border-radius: 10px; display: inline-block; }
        input { padding: 10px; width: 250px; }
        button { padding: 10px; background: #007bff; color: white; border: none; border-radius: 5px; }
    </style>
</head>
<body>
    <h1>🌦 Weather Dashboard</h1>
    <form method="POST">
        <input type="text" name="city" placeholder="Enter city name" required>
        <button type="submit">Check</button>
    </form>

    {% if weather %}
        <div class="box">
            <h2>{{ weather.city }}</h2>
            <p><strong>Condition:</strong> {{ weather.condition }}</p>
            <p><strong>Temperature:</strong> {{ weather.temp }}°C</p>
        </div>
    {% endif %}
</body>
</html>
"""

def get_weather(city):
    url = f"{API_KEY}/{city}?format=j1"
    res = requests.get(url)
    if res.status_code == 200:
        data = res.json()
        condition = data["current_condition"][0]["weatherDesc"][0]["value"]
        temp = data["current_condition"][0]["temp_C"]
        return {"city": city.title(), "condition": condition, "temp": temp}
    return None

@app.route("/", methods=["GET", "POST"])
def index():
    weather = None
    if request.method == "POST":
        city = request.form["city"]
        weather = get_weather(city)
    return render_template_string(HTML_TEMPLATE, weather=weather)

if __name__ == "__main__":
    app.run(debug=True)
