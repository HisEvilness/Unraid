# 📊Prometheus Configuration Reference (Unraid)

This document captures the **baseline Prometheus + exporters configuration** used in this repository for Unraid, including:

- Unraid Docker template fields (**Extra Parameters** vs **Post Arguments**)
- Prometheus runtime flags (TSDB storage and retention)
- `prometheus.yml` scrape configuration (sanitized)
- Scraparr exporter configuration (sanitized)

> **Sanitization policy:** Internal IPs, hostnames, and API keys/tokens are replaced with placeholders.

---

## 🧠1. Unraid Docker Template: Extra Parameters vs Post Arguments

Unraid’s Docker template separates runtime options into **two distinct layers**.  
Understanding this separation is critical to avoid misconfiguration.

### 1.1 Extra Parameters (Docker-level)

These parameters are passed **to Docker itself** and apply at the container runtime level.  
They are **not Prometheus flags**.

**Example:**
```markdown
--user 99:100
```
### 1.2 Post Arguments (Prometheus runtime flags)

These arguments are passed to the **Prometheus process inside the container** (entrypoint arguments).  
They directly control Prometheus runtime behavior.
Using runtime behaviour for Prometheus requires that paths be set.

**Baseline flags used:**
```markdown
--config.file=/etc/prometheus/prometheus.yml
--storage.tsdb.path=/prometheus/data
```
## 🎯2. Prometheus `prometheus.yml`

This section documents the baseline Prometheus scrape configuration.

### 2.1 Global settings
```ymal
global:
  scrape_interval: 15s
  evaluation_interval: 15s
  scrape_timeout: 10s
```
### 2.2 Scrape configurations

Replace placeholders with your actual hostnames, IP addresses, and ports.

scrape_configs:
```yaml
   ### PROMETHEUS SELF + UNRAID NODE EXPORTER
   
   - job_name: "prometheus"
     scrape_interval: 10s
     static_configs:
       - targets:
           - "localhost:9090"
           - "<UNRAID_HOST>:9100"

  ### ADGUARD HOME EXPORTER
  
  - job_name: "adguard"
    scrape_interval: 15s
    static_configs:
      - targets:
          - "<ADGUARD_EXPORTER_HOST>:9617"

  ### SCRAPARR (Arr stack exporter)
  #### OPERATIONAL MODE – NO PER-MEDIA METRICS
 
  - job_name: "scraparr"
    scrape_interval: 30s
    static_configs:
      - targets:
          - "<SCRAPARR_HOST>:7100"

  ### STATPING (AUTHENTICATED)
 
  - job_name: "statping"
    scrape_interval: 15s
    metrics_path: "/metrics"
    bearer_token: "<REDACTED_TOKEN>"
    static_configs:
      - targets:
          - "<STATPING_HOST>:8366"

  ### CLOUDFLARE EXPORTER (DISABLED / PARKED)
 
  - job_name: "cloudflare"
    scrape_interval: 30s
    static_configs:
      - targets:
          - "<CLOUDFLARE_EXPORTER_HOST>:<PORT>"
    honor_labels: true
```
## ⚙️3. Scraparr configuration

This Scraparr configuration is used to query Arr applications and expose a single `/metrics`
endpoint for Prometheus.

> **Important:** Setting `detailed: true` significantly increases metric cardinality and TSDB growth.
```yaml
- sonarr:
  url: http://<SONARR_HOST>:8989
  api_key: <REDACTED_API_KEY>
  api_version: v3
  interval: 30
  detailed: true

- radarr:
  url: http://<RADARR_HOST>:7878
  api_key: <REDACTED_API_KEY>
  api_version: v3
  interval: 30
  detailed: true

- prowlarr:
  url: http://<PROWLARR_HOST>:9696
  api_key: <REDACTED_API_KEY>
  api_version: v1
  interval: 30
  detailed: true

- lidarr:
  url: http://<LIDARR_HOST>:8686
  api_key: <REDACTED_API_KEY>
  api_version: v3
  interval: 30
  detailed: true

- overseerr:
  url: http://<OVERSEERR_HOST>:5055
  api_key: <REDACTED_API_KEY>
  api_version: v3
  interval: 30
  detailed: true
```
## ⚡4. Baseline runtime arguments

This section provides the baseline arguments as used in Unraid, ready for direct reuse.

### 4.1 Extra Parameters (Unraid)

These parameters are applied at the Docker runtime level.
--user 99:100 Run the container as Unraid’s standard nobody:users to avoid permission issues on mounted volumes
```markdown
--user 99:100
```
### 4.2 Post Arguments (Unraid)

These arguments are passed to the Prometheus process inside the container.
--config.file Path to Prometheus config file Unraid template typically mounts /etc/prometheus/.
--storage.tsdb.path TSDB data directory must match your mounted data volume path.
--storage.tsdb.retention.time Retention duration: Old data is deleted once it exceeds this.
--storage.tsdb.retention.size Retention size cap TSDB is capped to this size.
--storage.tsdb.wal-compression WAL compression reduces disk writes / IO amplification.
```markdown
--config.file=/etc/prometheus/prometheus.yml
--storage.tsdb.path=/prometheus/data
--storage.tsdb.retention.time=180d
--storage.tsdb.retention.size=40GB
--storage.tsdb.wal-compression
```
## 🔍5. Practical retention guidance (fit-for-purpose)

Prometheus TSDB growth is primarily driven by:

- Scrape intervals (for example: 10s vs 15s vs 30s)
- Number of active scrape targets and exporters
- Metric cardinality (notably “detailed” exporters and histogram metrics)

**Recommended operational posture:**

1. Start with conservative retention limits (time **and** size).
2. Observe TSDB growth over a minimum of **7–14 days**.
3. Right-size `--storage.tsdb.retention.size` only after observation.
4. Maintain **25–40% headroom** to prevent aggressive churn and WAL pressure.

---

## 📜6. Security and hygiene

- Never commit real API keys or bearer tokens to version control.
- Replace internal IP addresses and hostnames with placeholders before publishing.
- Keep exporter endpoints on a trusted or segmented network.
- Avoid exposing `/metrics` endpoints directly to the public internet.

---

## 🧩7. Scope boundary

This document covers **baseline Prometheus and exporter configuration only**.

Items **explicitly out of scope** for this reference:

- Grafana dashboards and panel JSON
- Alertmanager configuration
- Recording rules and alert rules
- Long-term remote storage backends (e.g. Thanos, Cortex, Mimir)

Those components are documented separately.
