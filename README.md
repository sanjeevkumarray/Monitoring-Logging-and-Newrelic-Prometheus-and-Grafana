

# 🚀 Exploring Open-Source Alternatives for Logging & Monitoring

## 💡 Why Move Away from New Relic

* Expensive at scale
* Limited data ownership and export options
* Complex data retrieval process

---

## 🧠 Open-Source Alternatives

* **Prometheus** → Metrics collection (time-series database)
* **Grafana** → Visualization and dashboarding
* **Jaeger** → Distributed tracing

---

## 📊 Prometheus Overview

* Prometheus is a **time-series database** used for monitoring numerical data.
* It follows a **pull-based architecture**, where the Prometheus server periodically scrapes metrics from application endpoints.
* Integrations:

  * **Node Exporter** → Host machine stats
  * **Push Gateway** → Short-lived jobs
  * **Consul** → Service discovery

### 🔄 Architecture Flow

1. App exposes metrics at `/metrics`
2. Prometheus scrapes the metrics periodically
3. Data stored in the time-series database
4. Visualized using Grafana

### ⚠️ Drawbacks

* Provides cumulative (not real-time) data
* Limited horizontal scalability
* Single-node dependency (possible data loss on crash)

---

## ⚙️ Express + Prometheus Setup

1. **Install dependencies**

   ```bash
   npm install
   npm install typescript --save-dev
   npx tsc --init
   ```

2. **Build & start the app**

   ```bash
   npm run start
   ```

3. **Test endpoints**

   * Visit [http://localhost:3000](http://localhost:3000) → should display “Hello from Express + TypeScript + Prometheus!”
   * Visit [http://localhost:3000/metrics](http://localhost:3000/metrics) → displays Prometheus metrics output

4. **Expose metrics**
   Use `prom-client` in `/metrics` endpoint.

---

## 📈 Prometheus Metric Types

| Type          | Description              | Example Use          |
| ------------- | ------------------------ | -------------------- |
| **Counter**   | Increases cumulatively   | Total requests       |
| **Gauge**     | Can increase or decrease | Active users, memory |
| **Histogram** | Shows data distribution  | Response times       |

### Example Metric Format

```
http_requests_total{method="POST", route="/user", code="200"}
```

---

## 🧩 Sample Middleware Snippets

**⏱ Request Duration**

```ts
res.on('finish', () => console.log(`Request took ${Date.now() - start}ms`));
```

**📊 Counter**

```ts
const requestCounter = new client.Counter({
  name: 'http_requests_total',
  help: 'Total number of HTTP requests',
  labelNames: ['method', 'route', 'status_code'],
});
```

**👥 Gauge**

```ts
const activeUsers = new client.Gauge({
  name: 'active_users',
  help: 'Number of active users',
});
```

**📉 Histogram**

```ts
const responseTime = new client.Histogram({
  name: 'http_request_duration_ms',
  help: 'Duration of HTTP requests in ms',
  buckets: [0.1, 5, 10, 100, 500],
});
```

---

## 🐳 Docker & Prometheus Integration

### `prometheus.yml`

```yml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'express-app'
    static_configs:
      - targets: ['node-app:3000']
```

### `docker-compose.yml`

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

**Run containers**

```bash
docker-compose up
```

### Access:

* **Prometheus UI** → [http://localhost:9090](http://localhost:9090)
* **App Metrics** → [http://localhost:3000/metrics](http://localhost:3000/metrics)

---

## 📊 Grafana Integration

* Add Prometheus as a data source in Grafana
* Create dashboards for:

  * Requests per second
  * Latency distribution
  * Error rates

---

## 🎯 Final Outcome

✅ End-to-end **open-source monitoring stack** using:

* **Prometheus + Grafana** → Metrics & visualization
* **Express.js + prom-client** → Application instrumentation

Efficient, scalable, and cost-effective observability — without New Relic.

---

