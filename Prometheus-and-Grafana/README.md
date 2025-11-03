
---

# 🚀 Exploring Open-Source Alternatives for Logging & Monitoring

## 💡 Why Move Away from New Relic

* High cost at scale
* Limited data ownership & export control
* Complex data extraction process

---

## 🧠 Open-Source Alternatives

* **Prometheus** — Time-series metrics collection
* **Grafana** — Visualization & dashboards
* **Jaeger** — Distributed tracing

---

## 📊 Prometheus Overview

* A **time-series database** for monitoring numerical data.
* Uses a **pull-based architecture** — Prometheus server scrapes metrics from app endpoints.
* Optionally integrates with:

  * **Node Exporter** – for host metrics
  * **Push Gateway** – for short-lived jobs
  * **Consul** – for service discovery

### Architecture Flow

1. App exposes metrics endpoint (e.g., `/metrics`)
2. Prometheus periodically scrapes metrics
3. Data is stored in a time-series DB
4. Visualized via Grafana

### Drawbacks

* Provides cumulative metrics, not real-time
* Limited horizontal scalability
* Single-node dependency (data loss if down)

---

## ⚙️ Express App Setup

1. Install dependencies:

   ```bash
   npm install
   ```
2. Start app:

   ```bash
   npm start
   ```
3. Expose metrics endpoint at `/metrics` using `prom-client`.

---

## 📈 Prometheus Metrics Types

| Type          | Description                        | Example Use          |
| ------------- | ---------------------------------- | -------------------- |
| **Counter**   | Cumulative metric (only increases) | Total requests       |
| **Gauge**     | Fluctuating value (up/down)        | Active users, memory |
| **Histogram** | Value distribution via buckets     | Response times       |

### Example Metric Format

```
http_requests_total{method="POST", route="/user", code="200"}
```

---

## 🧩 Middleware Samples

**Monitor request time**

```ts
res.on('finish', () => console.log(`Request took ${Date.now() - start}ms`));
```

**Counter Metric**

```ts
const requestCounter = new client.Counter({
  name: 'http_requests_total',
  help: 'Total HTTP requests',
  labelNames: ['method', 'route', 'status_code'],
});
```

**Gauge Metric**

```ts
const activeUsers = new client.Gauge({
  name: 'active_users',
  help: 'Number of active users',
});
```

**Histogram Metric**

```ts
const responseTime = new client.Histogram({
  name: 'http_request_duration_ms',
  help: 'Request duration in ms',
  buckets: [0.1, 5, 10, 100, 500],
});
```

---

## 🐳 Docker & Prometheus Integration

**prometheus.yml**

```yml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'express-app'
    static_configs:
      - targets: ['node-app:3000']
```

**docker-compose.yml**

```yml
version: '3.8'
services:
  node-app:
    build: .
    ports: ["3000:3000"]
    networks: [monitoring]
  prometheus:
    image: prom/prometheus
    volumes: [./prometheus.yml:/etc/prometheus/prometheus.yml]
    ports: ["9090:9090"]
    networks: [monitoring]
networks:
  monitoring:
```

Run:

```bash
docker-compose up
```

Access:

* **Prometheus UI** → [http://localhost:9090](http://localhost:9090)
* **App Metrics** → [http://localhost:3000/metrics](http://localhost:3000/metrics)

---

## 🧭 Grafana Integration

* Connect Grafana to Prometheus as a data source
* Build dashboards to visualize metrics (requests/sec, latency, errors, etc.)

---

## 🎉 Result

✅ Fully functional, open-source monitoring pipeline
using **Prometheus + Grafana** for metrics & visualization
and **Express.js + prom-client** for instrumentation.


