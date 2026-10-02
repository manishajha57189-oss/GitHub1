# Practical: Monitoring Python Application using Prometheus and Grafana

## Aim

To monitor a Python Flask application using Prometheus and Grafana.

## Software Required

* Windows
* Python
* Flask
* Prometheus
* Grafana
* Windows Exporter

---

## Part A: Set up Prometheus

### Step 1: Download Prometheus

1. Download Prometheus for Windows.
2. Extract it to:

```text
C:\prometheus
```

3. Make sure these files are present:

```text
prometheus.exe
promtool.exe
prometheus.yml
```

### Step 2: Configure Prometheus

Open:

```text
C:\prometheus\prometheus.yml
```

Replace the contents with:

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "python-app"
    static_configs:
      - targets: ["localhost:8000"]

  - job_name: "windows"
    static_configs:
      - targets: ["localhost:9182"]
```

Save the file.

---

# Part B: Create Python Application

## Step 3: Create Project Folder

Open CMD:

```cmd
mkdir C:\monitoring-app
cd C:\monitoring-app
```

Create virtual environment:

```cmd
python -m venv venv
```

Activate it:

```cmd
venv\Scripts\activate
```

## Step 4: Install Flask and Prometheus Client

```cmd
pip install flask prometheus-client
```

## Step 5: Create app.py

Create:

```text
C:\monitoring-app\app.py
```

Add:

```python
from flask import Flask
from prometheus_client import Counter, start_http_server

app = Flask(__name__)

REQUEST_COUNT = Counter(
    "api_requests_total",
    "Total number of API requests"
)

@app.route("/")
def home():
    REQUEST_COUNT.inc()
    return "Hello! Flask application is running."

@app.route("/hello")
def hello():
    REQUEST_COUNT.inc()
    return "Hello from the monitoring application!"

if __name__ == "__main__":
    start_http_server(8000)
    app.run(host="0.0.0.0", port=5000)
```

## Step 6: Run Python Application

```cmd
cd C:\monitoring-app
venv\Scripts\activate
python app.py
```

Expected:

```text
* Running on http://127.0.0.1:5000
```

Keep this CMD open.

## Step 7: Test Flask

Open browser:

```text
http://localhost:5000
```

Output:

```text
Hello! Flask application is running.
```

Also open:

```text
http://localhost:5000/hello
```

## Step 8: Test Prometheus Metrics

Open:

```text
http://localhost:8000
```

Search for:

```text
api_requests_total
```

---

# Part C: Start Prometheus

## Step 9: Open Second CMD

Keep Python application running.

Open another CMD:

```cmd
cd C:\prometheus
prometheus.exe --config.file=prometheus.yml
```

Keep this CMD open.

## Step 10: Open Prometheus

Open:

```text
http://localhost:9090
```

Go to:

```text
Status → Targets
```

Check that:

```text
prometheus    UP
python-app    UP
windows       UP
```

---

# Part D: Install Windows Exporter

## Step 11: Install Windows Exporter

Download and install the 64-bit Windows Exporter.

It exposes Windows metrics at:

```text
http://localhost:9182/metrics
```

## Step 12: Test Windows Exporter

Open:

```text
http://localhost:9182/metrics
```

You should see Windows CPU and memory metrics.

## Step 13: Check Prometheus

Open:

```text
http://localhost:9090
```

Go to:

```text
Status → Targets
```

Check:

```text
prometheus    UP
python-app    UP
windows       UP
```

---

# Part E: Install Grafana

## Step 14: Install Grafana

Download and install **Grafana for Windows 64-bit**.

## Step 15: Start Grafana

Open:

```text
http://localhost:3000
```

Log in using the administrator account created during installation.

---

# Part F: Connect Grafana to Prometheus

## Step 16: Add Prometheus Data Source

In Grafana:

```text
Connections
↓
Data Sources
↓
Add Data Source
↓
Prometheus
```

Enter:

```text
http://localhost:9090
```

Click:

```text
Save & Test
```

---

# Part G: Create Dashboard

Go to:

```text
Dashboards
→ New Dashboard
→ Add Visualization
```

Select **Prometheus** as the data source.

### Panel 1: API Request Rate

PromQL:

```promql
rate(api_requests_total[1m])
```

Visualization:

```text
Time series
```

Title:

```text
API Request Rate
```

### Panel 2: CPU Usage

PromQL:

```promql
100 - (
  100 *
  avg by (instance) (
    rate(windows_cpu_time_total{mode="idle"}[5m])
  )
)
```

Visualization:

```text
Gauge
```

Title:

```text
CPU Usage %
```

### Panel 3: Memory Usage

PromQL:

```promql
100 * (
  1 -
  windows_memory_available_bytes
  /
  windows_memory_physical_total_bytes
)
```

Visualization:

```text
Gauge
```

Title:

```text
Memory Usage %
```

---

## Step 17: Generate API Requests

Open:

```text
http://localhost:5000
```

Refresh the page several times.

Also open:

```text
http://localhost:5000/hello
```

Refresh it several times.

The **API Request Rate** panel in Grafana will show the activity.

---

## Result

The Python Flask application was successfully monitored using **Prometheus and Grafana**, and CPU, memory, and API request metrics were displayed on the Grafana dashboard.
