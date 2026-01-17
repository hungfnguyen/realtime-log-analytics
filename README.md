# Real-time Log Analytics & Security Monitoring Pipeline

> **A scalable, end-to-end data engineering solution for real-time web server monitoring, anomaly detection, and log analysis.**

This project implements a robust **Real-time Big Data Pipeline** designed to ingest, process, and visualize high-throughput log data from distributed web servers. Leveraging the power of the **Hadoop ecosystem** and **Stream Processing** technologies, the system provides sub-minute insights into infrastructure health, traffic patterns, and potential security threats (DDoS, scanning).

---

## System Architecture

The architecture follows a decoupled, microservices-based design ensuring high availability, fault tolerance, and horizontal scalability.

<div align="center">
<img src="docs/img/architecture.jpeg" alt="System Architecture" width="820">




<em>Figure 1. High-level System Architecture</em>
</div>

### Key Technical Highlights

* **Decoupled Ingestion**: Uses **Apache Flume** with `TAILDIR` source for reliable log shipping and **Apache Kafka** as a durable message buffer.
* **Event-Time Processing**: Implements **Apache Spark Structured Streaming** with stateful aggregations, handling late data via **Watermarking** (2-minute threshold) and Windowing (10-second sliding windows).
* **TimeSeries Optimization**: Metrics are stored in **InfluxDB** with optimized schema design (Tags vs. Fields) for high-cardinality queries.
* **Infrastructure as Code (IaC)**: Fully containerized environment using **Docker** and **Docker Compose** for reproducible deployments.

---

## Data Pipeline Workflow

<div align="center">
<img src="docs/log-pipeline.jpg" alt="Log Data Pipeline" width="1000">




<em>Figure 2. End-to-end Data Flow: From Nginx to Grafana</em>
</div>

1. **Log Generation**: Distributed **Nginx** servers generate Access (JSON) and Error (Text) logs.
2. **Ingestion Layer**: **Flume Agents** tail logs and push structured events to a centralized Flume Collector via Avro.
3. **Buffering Layer**: **Kafka** partitions data into `web-logs` (access) and `web-errors` topics, decoupling producers from consumers.
4. **Processing Layer**: **Spark Streaming** jobs consume Kafka topics to:
* Parse and normalize raw logs.
* Compute metrics (RPS, Latency, Throughput).
* Detect anomalies using statistical thresholds.


5. **Serving & Visualization**: **InfluxDB** stores time-series metrics, while **Grafana** queries and visualizes actionable insights.

---

## Key Features

### 1. Real-time Traffic Monitoring

* **Performance Metrics**: Tracks **Requests Per Second (RPS)**, average/max **Latency**, and **Bandwidth** usage in real-time.
* **Status Code Analysis**: Granular breakdown of HTTP 2xx, 3xx, 4xx, and 5xx responses.

### 2. Cybersecurity Anomaly Detection

The system employs heuristic analysis to identify potential threats:

* **IP Spike Detection**: Identifies IPs exceeding request thresholds within a short window (Potential DDoS).
* **Error Surges**: Alerts when the 5xx error rate spikes abnormally.
* **Port/Path Scanning**: Detects IPs probing multiple distinct non-existent paths (Reconnaissance attacks).

### 3. Centralized Alerting

* Automated alerts configured in Grafana based on InfluxDB thresholds, notifying administrators of critical incidents immediately.

---

## Monitoring Dashboards

### HTTP Performance Stats

*Real-time visibility into server health, latency distribution, and error rates.*

<div align="center">
<img src="docs/img/grafana/HTTP_Stats.jpg" alt="HTTP Realtime Stats" width="1000">
</div>

### Error Analysis & Forensics

*Deep dive into system errors classified by hostname, severity level, and message patterns.*

<div align="center">
<img src="docs/img/grafana/ErrorAnalytics.jpg" alt="Error Events" width="1000">
</div>

### Security & Anomaly Detection

*Visualizing potential threats such as IP spikes and scanning activities with anomaly scores.*

<div align="center">
<img src="docs/img/grafana/AnomalyDetection.jpg" alt="Realtime Anomaly Detection" width="1000">
</div>

### Traffic Distribution (Top URLs)

*Analysis of most accessed endpoints and their respective status codes.*

<div align="center">
<img src="docs/img/grafana/TopURLs.jpg" alt="Top URLs" width="1000">
</div>

---

## Tech Stack

| Domain | Technology | Usage |
| --- | --- | --- |
| **Ingestion** | **Apache Flume** | Log collection (Taildir Source, Avro Sink) |
| **Messaging** | **Apache Kafka** | Distributed Event Streaming Platform |
| **Processing** | **Apache Spark** | Structured Streaming (Python/PySpark), Stateful Aggregation |
| **Storage** | **InfluxDB** | Time-Series Database (TSDB) for metrics |
| **Visualization** | **Grafana** | Operational Dashboards & Alerting (Flux query language) |
| **DevOps** | **Docker** | Containerization & Orchestration |

---

## Project Structure

```text
realtime-log-analytics/
├── docker-compose.yml       # Orchestration of the entire stack
├── .env                     # Environment variables configuration
├── flume/                   # Log ingestion configurations
│   ├── agents/              # Per-node agents (web1, web2, web3)
│   └── collector/           # Centralized aggregation logic
├── kafka/                   # Kafka provisioning scripts
├── spark/                   # ETL & Analytics Logic
│   ├── src/python/          # PySpark streaming jobs
│   │   ├── stream_access.py # Access log processing & metrics
│   │   ├── stream_error.py  # Error log parsing & classification
│   │   └── schemas/         # Data contracts & Schema definitions
│   └── Dockerfile           # Custom Spark image with dependencies
├── influxdb/                # DB initialization & retention policies
├── grafana/                 # Dashboard provisioning & Datasources
└── docs/                    # Architectural documentation

```

---

## Contributors & Roles

This project was built by a collaborative team of 5 engineers, simulating a real-world agile environment.

* **Hung (DevOps & Ingestion):**
* Designed the Flume topology (Agents & Collectors).
* Managed Docker deployment scripts and environment stability.


* **Hao (Data Pipeline):**
* Managed the Kafka cluster implementation.
* Optimized topic partitioning and retention policies for high throughput.


* **Hai (Data Processing):**
* Developed Spark Structured Streaming jobs.
* Implemented windowed aggregations and watermark logic for data consistency.


* **Don (Data Storage):**
* Designed InfluxDB schema (measurements, tags, fields) for query optimization.
* Handled data persistence layers.


* **Nhat (Visualization):**
* Built Grafana dashboards and configured alerting rules (Flux).
* Ensured data interpretability for end-users.



---

### Documentation

For detailed architectural decisions and runbooks, please refer to the internal documentation:

* [Architecture Design Document](https://www.google.com/search?q=docs/architecture.md)
* [Operational Runbook](https://www.google.com/search?q=docs/runbook.md)

