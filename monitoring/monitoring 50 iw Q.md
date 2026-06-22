Below is a **professional Top 50 Prometheus + Grafana interview guide** for Java/Spring Boot backend interviews.

Prometheus is mainly used for **metrics collection, time-series storage, querying, and alerting**, while Grafana is mainly used for **visualization, dashboards, exploration, and alerting UI**. Prometheus stores data as **time series with labels**, supports **PromQL**, and usually works on a **pull/scrape model**. Grafana can query Prometheus and many other data sources to build dashboards and alerts. ([Prometheus][1])

---

# 1. Prometheus and Grafana Basics

## 1. What is Prometheus?

**Prometheus** is an open-source monitoring and alerting system. It collects metrics from applications, servers, containers, Kubernetes, databases, and other systems.

In simple words:

> Prometheus answers: **“What is happening in my system numerically?”**

Example metrics:

```text
http_requests_total
jvm_memory_used_bytes
system_cpu_usage
order_created_total
database_connection_active
```

Prometheus stores all these values as **time-series data**, meaning every metric is stored with timestamped values. ([Prometheus][2])

---

## 2. What is Grafana?

**Grafana** is a visualization and dashboarding tool. It connects to data sources like Prometheus, Loki, Elasticsearch, PostgreSQL, CloudWatch, etc., and displays data in graphs, tables, gauges, heatmaps, and dashboards.

In simple words:

> Grafana answers: **“How can I clearly see and analyze what is happening?”**

Prometheus collects and stores metrics. Grafana displays those metrics beautifully.

---

## 3. Difference between Prometheus and Grafana

| Prometheus                 | Grafana                                     |
| -------------------------- | ------------------------------------------- |
| Collects metrics           | Visualizes metrics                          |
| Stores time-series data    | Creates dashboards                          |
| Uses PromQL                | Uses queries from connected data sources    |
| Has basic UI               | Has powerful dashboard UI                   |
| Handles alert rules        | Can also manage alerting visually           |
| Pulls metrics from targets | Reads data from Prometheus or other sources |

Interview line:

> Prometheus is the monitoring engine, and Grafana is the visualization layer.

---

## 4. Why do we use Prometheus?

We use Prometheus to monitor application health, performance, and infrastructure behavior.

Common use cases:

1. API response time monitoring
2. Error rate monitoring
3. JVM memory and GC monitoring
4. CPU and memory usage
5. Kubernetes pod/container metrics
6. Database connection pool monitoring
7. Alerting when something goes wrong

Example:

> If API latency goes above 2 seconds or error rate crosses 5%, Prometheus can detect it and trigger alerts.

---

## 5. What is a metric?

A **metric** is a numerical measurement of system behavior.

Example:

```text
http_server_requests_seconds_count
jvm_memory_used_bytes
process_cpu_usage
```

A metric generally tells:

> “How many?”, “How much?”, “How long?”, or “What is the current value?”

---

## 6. What is time-series data?

Time-series data means values stored over time.

Example:

```text
cpu_usage{instance="server1"}  60% at 10:00
cpu_usage{instance="server1"}  70% at 10:01
cpu_usage{instance="server1"}  55% at 10:02
```

Prometheus stores metrics as timestamped samples with metric name and labels. ([Prometheus][2])

---

## 7. What is a scrape in Prometheus?

A **scrape** means Prometheus calling an HTTP endpoint to collect metrics.

Example:

```text
Prometheus ---> http://localhost:8080/actuator/prometheus
```

Prometheus periodically scrapes this endpoint and stores the metrics.

---

## 8. What is the default Prometheus approach: push or pull?

Prometheus mainly uses a **pull model**.

That means Prometheus pulls/scrapes metrics from applications.

Example:

```yaml
scrape_configs:
  - job_name: 'spring-boot-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['localhost:8080']
```

Prometheus will call:

```text
http://localhost:8080/actuator/prometheus
```

---

## 9. Why does Prometheus prefer pull-based monitoring?

Pull-based monitoring is useful because:

1. Prometheus controls scraping frequency.
2. Target health can be checked easily.
3. Service discovery works well in Kubernetes/cloud environments.
4. Debugging is simple because each application exposes a metrics endpoint.
5. Prometheus can detect if a target is down.

Interview line:

> In pull model, Prometheus knows which target failed because it directly scrapes the target.

---

## 10. What is a Prometheus target?

A **target** is an endpoint from which Prometheus scrapes metrics.

Example target:

```text
localhost:8080
my-service:8080
payment-service.default.svc.cluster.local:8080
```

For Spring Boot:

```text
/actuator/prometheus
```

Spring Boot exposes application metrics in Prometheus format through `/actuator/prometheus` when Actuator and Prometheus registry are configured. ([Home][3])

---

# 2. Prometheus Architecture

## 11. Explain Prometheus architecture.

Prometheus architecture usually contains:

1. **Prometheus Server** — scrapes and stores metrics.
2. **Targets** — applications/services exposing metrics.
3. **Exporters** — expose third-party system metrics.
4. **PromQL** — query language.
5. **Alertmanager** — handles alert notifications.
6. **Grafana** — visualization dashboard.

Flow:

```text
Spring Boot App / Node Exporter / DB Exporter
              |
              v
        Prometheus Server
              |
       PromQL Queries / Rules
              |
       Grafana / Alertmanager
```

---

## 12. What is an exporter in Prometheus?

An **exporter** is a component that exposes metrics of a system in Prometheus format.

Examples:

| Exporter            | Purpose               |
| ------------------- | --------------------- |
| Node Exporter       | Linux server metrics  |
| MySQL Exporter      | MySQL metrics         |
| PostgreSQL Exporter | PostgreSQL metrics    |
| Blackbox Exporter   | HTTP/TCP/ICMP probing |
| JMX Exporter        | JVM/JMX metrics       |

Exporters are useful when the original system does not expose Prometheus metrics directly. ([Prometheus][4])

---

## 13. What is Node Exporter?

**Node Exporter** exposes Linux machine metrics like:

```text
CPU usage
Memory usage
Disk usage
Network usage
Filesystem metrics
```

Example metrics:

```text
node_cpu_seconds_total
node_memory_MemAvailable_bytes
node_filesystem_avail_bytes
```

Interview line:

> Node Exporter is commonly used to monitor host-level Linux infrastructure.

---

## 14. What is Prometheus service discovery?

Service discovery allows Prometheus to automatically discover targets instead of manually listing every IP.

Common service discovery mechanisms:

1. Kubernetes service discovery
2. Consul
3. EC2 discovery
4. Static config
5. File-based discovery

In Kubernetes, Prometheus can discover pods, services, endpoints, and nodes dynamically.

---

## 15. What is the Prometheus configuration file?

Prometheus configuration is usually written in `prometheus.yml`.

Example:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'spring-boot-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['localhost:8080']
```

Meaning:

| Property          | Meaning                              |
| ----------------- | ------------------------------------ |
| `scrape_interval` | How frequently metrics are collected |
| `job_name`        | Logical name of monitored system     |
| `metrics_path`    | Metrics endpoint path                |
| `targets`         | Host and port to scrape              |

---

## 16. What is scrape interval?

`scrape_interval` defines how often Prometheus collects metrics.

Example:

```yaml
global:
  scrape_interval: 15s
```

This means Prometheus scrapes metrics every 15 seconds.

Interview point:

> Lower interval gives more detail but increases storage and load. Higher interval saves resources but gives less granularity.

---

## 17. What is evaluation interval?

`evaluation_interval` defines how often Prometheus evaluates rules such as recording rules and alerting rules.

Example:

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 30s
```

Meaning:

```text
Scrape metrics every 15 seconds.
Evaluate alert/recording rules every 30 seconds.
```

---

## 18. What are labels in Prometheus?

Labels are key-value pairs attached to metrics.

Example:

```text
http_requests_total{method="GET", status="200", endpoint="/api/users"}
```

Here:

```text
method="GET"
status="200"
endpoint="/api/users"
```

Labels help filter, group, and aggregate metrics.

Example PromQL:

```promql
sum by (status) (http_requests_total)
```

---

## 19. What is high cardinality in Prometheus?

**Cardinality** means the number of unique time series.

High cardinality happens when labels create too many combinations.

Bad example:

```text
http_requests_total{userId="1001"}
http_requests_total{userId="1002"}
http_requests_total{userId="1003"}
```

If millions of users exist, Prometheus creates millions of time series.

Avoid labels like:

```text
userId
email
requestId
sessionId
orderId
jwtToken
```

Good labels:

```text
method
status
endpoint
service
instance
environment
```

Interview line:

> High cardinality increases memory, storage, and query cost.

---

## 20. What is the difference between metric name and label?

Example:

```text
http_requests_total{method="GET", status="200"}
```

| Part        | Example               | Meaning                |
| ----------- | --------------------- | ---------------------- |
| Metric name | `http_requests_total` | What is being measured |
| Label       | `method="GET"`        | Dimension/filter       |
| Label       | `status="200"`        | Another dimension      |

Metric name tells **what**. Labels tell **where/how/by whom category-wise**.

---

# 3. Prometheus Metric Types

Prometheus supports common metric types such as **Counter, Gauge, Histogram, and Summary**. ([Prometheus][5])

## 21. What is a Counter?

A **Counter** is a metric that only increases or resets to zero.

Use Counter for:

```text
Total requests
Total errors
Total orders created
Total messages consumed
```

Example:

```text
http_requests_total
order_created_total
login_failed_total
```

Counter should not be used for values that go up and down.

---

## 22. What is a Gauge?

A **Gauge** is a metric that can go up and down.

Use Gauge for:

```text
Current memory usage
Current CPU usage
Active DB connections
Queue size
Thread count
```

Example:

```text
jvm_memory_used_bytes
hikaricp_connections_active
system_cpu_usage
```

Interview line:

> Counter is for cumulative increasing values. Gauge is for current state.

---

## 23. What is a Histogram?

A **Histogram** measures distribution of values using buckets.

Common use case:

```text
Request duration
Response size
Job execution time
```

Example metric:

```text
http_server_requests_seconds_bucket
http_server_requests_seconds_count
http_server_requests_seconds_sum
```

Histogram is very important for latency percentiles.

Example PromQL for 95th percentile latency:

```promql
histogram_quantile(
  0.95,
  sum(rate(http_server_requests_seconds_bucket[5m])) by (le)
)
```

---

## 24. What is a Summary?

A **Summary** also measures distributions and can calculate quantiles.

Difference:

| Histogram                      | Summary                                   |
| ------------------------------ | ----------------------------------------- |
| Bucket-based                   | Client-side quantile calculation          |
| Aggregatable across instances  | Usually not safely aggregatable           |
| Better for distributed systems | Useful for local instance-level quantiles |

Interview preference:

> In microservices and Kubernetes, Histogram is generally preferred because it can be aggregated across instances.

---

## 25. Counter vs Gauge

| Counter                | Gauge                   |
| ---------------------- | ----------------------- |
| Only increases         | Increases and decreases |
| Can reset              | Can change freely       |
| Used with `rate()`     | Usually used directly   |
| Example: request count | Example: memory usage   |

Example Counter query:

```promql
rate(http_requests_total[5m])
```

Example Gauge query:

```promql
jvm_memory_used_bytes
```

---

# 4. PromQL Interview Questions

## 26. What is PromQL?

**PromQL** is Prometheus Query Language. It is used to query, filter, aggregate, and calculate metrics stored in Prometheus.

Example:

```promql
up
```

Shows whether targets are up.

```promql
rate(http_requests_total[5m])
```

Shows per-second request rate over the last 5 minutes.

---

## 27. What is the `up` metric?

`up` is a built-in Prometheus metric that tells whether a target scrape was successful.

```promql
up
```

Values:

| Value | Meaning        |
| ----- | -------------- |
| `1`   | Target is up   |
| `0`   | Target is down |

Example:

```promql
up{job="spring-boot-app"} == 0
```

This can be used for alerting.

---

## 28. What is `rate()` in PromQL?

`rate()` calculates the per-second average increase of a counter over a time range.

Example:

```promql
rate(http_requests_total[5m])
```

Meaning:

> Average requests per second during the last 5 minutes.

Use `rate()` with Counters.

---

## 29. What is `irate()`?

`irate()` calculates the instant rate using the last two samples.

Example:

```promql
irate(http_requests_total[1m])
```

Difference:

| `rate()`          | `irate()`                       |
| ----------------- | ------------------------------- |
| Smoother          | More sensitive                  |
| Better for alerts | Better for fast-changing graphs |
| Uses full range   | Uses last two samples           |

Interview answer:

> Use `rate()` for alerting and stable dashboards. Use `irate()` for highly volatile real-time graphs.

---

## 30. What is `increase()`?

`increase()` shows total increase over a time range.

Example:

```promql
increase(http_requests_total[1h])
```

Meaning:

> Total number of requests in the last 1 hour.

Difference:

```promql
rate(http_requests_total[5m])
```

Requests per second.

```promql
increase(http_requests_total[1h])
```

Total requests in 1 hour.

---

## 31. What is the difference between instant vector and range vector?

Instant vector:

```promql
http_requests_total
```

Gives latest value at one point in time.

Range vector:

```promql
http_requests_total[5m]
```

Gives values over the last 5 minutes.

Functions like `rate()` need range vectors:

```promql
rate(http_requests_total[5m])
```

---

## 32. What is aggregation in PromQL?

Aggregation combines multiple time series.

Examples:

```promql
sum(http_requests_total)
```

```promql
sum by (status) (http_requests_total)
```

```promql
avg by (instance) (system_cpu_usage)
```

Common aggregation operators:

```text
sum
avg
min
max
count
topk
bottomk
```

---

## 33. Explain `sum by` and `sum without`.

`sum by` keeps selected labels.

```promql
sum by (status) (http_requests_total)
```

Output grouped by status:

```text
status=200
status=404
status=500
```

`sum without` removes selected labels.

```promql
sum without (instance) (http_requests_total)
```

This sums across instances but keeps other labels.

---

## 34. How do you calculate API error rate?

Example:

```promql
sum(rate(http_server_requests_seconds_count{status=~"5.."}[5m]))
/
sum(rate(http_server_requests_seconds_count[5m]))
* 100
```

Meaning:

> Percentage of 5xx responses out of total requests in the last 5 minutes.

Interview line:

> Error rate is usually calculated using 5xx requests divided by total requests.

---

## 35. How do you calculate API latency percentile?

For 95th percentile latency:

```promql
histogram_quantile(
  0.95,
  sum(rate(http_server_requests_seconds_bucket[5m])) by (le)
)
```

For endpoint-wise latency:

```promql
histogram_quantile(
  0.95,
  sum(rate(http_server_requests_seconds_bucket[5m])) by (le, uri)
)
```

Meaning:

> 95% of requests are completed within this latency value.

---

## 36. How do you find top 5 slow APIs?

Example:

```promql
topk(
  5,
  histogram_quantile(
    0.95,
    sum(rate(http_server_requests_seconds_bucket[5m])) by (le, uri)
  )
)
```

This gives the top 5 APIs with highest p95 latency.

---

## 37. How do you calculate request throughput?

Example:

```promql
sum(rate(http_server_requests_seconds_count[1m]))
```

Endpoint-wise:

```promql
sum by (uri) (rate(http_server_requests_seconds_count[1m]))
```

Meaning:

> Number of requests per second.

---

## 38. How do you monitor JVM memory usage?

Example:

```promql
jvm_memory_used_bytes
```

Heap memory:

```promql
jvm_memory_used_bytes{area="heap"}
```

Percentage usage can be calculated as:

```promql
sum(jvm_memory_used_bytes{area="heap"})
/
sum(jvm_memory_max_bytes{area="heap"})
* 100
```

---

# 5. Alerting Questions

## 39. What is Alertmanager?

**Alertmanager** receives alerts from Prometheus and manages notifications.

It handles:

1. Grouping
2. Deduplication
3. Routing
4. Silencing
5. Inhibition
6. Sending notifications to email, Slack, PagerDuty, Opsgenie, etc. ([Prometheus][6])

---

## 40. What is an alerting rule?

An alerting rule defines a condition that should trigger an alert.

Example:

```yaml
groups:
  - name: application-alerts
    rules:
      - alert: ApplicationDown
        expr: up{job="spring-boot-app"} == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Spring Boot application is down"
          description: "The application has been down for more than 1 minute."
```

Meaning:

> If the Spring Boot app is down for 1 minute, fire a critical alert.

Prometheus supports alerting rules and sends firing alerts to Alertmanager. ([Prometheus][7])

---

## 41. What is the meaning of `for` in Prometheus alert?

Example:

```yaml
for: 5m
```

This means the condition must be true continuously for 5 minutes before the alert fires.

Why useful?

> It prevents false alerts due to temporary spikes.

---

## 42. What is alert severity?

Severity is a label used to classify alert priority.

Example:

```yaml
labels:
  severity: critical
```

Common levels:

| Severity | Meaning                   |
| -------- | ------------------------- |
| info     | Low priority              |
| warning  | Needs attention           |
| critical | Immediate action required |

---

## 43. What is alert silencing?

Silencing means temporarily muting alerts.

Use case:

```text
During planned deployment
During maintenance window
During database migration
```

Interview line:

> Silencing prevents unnecessary notifications during known maintenance activities.

---

## 44. What is alert inhibition?

Inhibition suppresses lower-level alerts when a higher-level alert is already firing.

Example:

If `DatabaseDown` is firing, then alerts like `HighDBLatency` can be inhibited.

Reason:

> No need to send 20 alerts when the root cause is already known.

---

## 45. Difference between Prometheus alerting and Grafana alerting

| Prometheus Alerting             | Grafana Alerting                             |
| ------------------------------- | -------------------------------------------- |
| Rule-based using PromQL         | UI-based alerting experience                 |
| Works closely with Alertmanager | Can alert from multiple data sources         |
| Common in infra/K8s setups      | Good for centralized visual alert management |
| YAML/rule file based            | Dashboard/panel-oriented experience          |

Interview answer:

> Prometheus alerting is powerful for metrics-based rules, while Grafana alerting is useful when teams want alerting from multiple data sources with a visual interface.

---

# 6. Grafana Questions

## 46. How does Grafana connect to Prometheus?

Grafana connects to Prometheus as a **data source**.

Basic flow:

```text
Grafana Dashboard
      |
      v
Prometheus Data Source
      |
      v
PromQL Query
      |
      v
Graph / Table / Gauge / Alert
```

Grafana supports data sources such as Prometheus, CloudWatch, Loki, Elasticsearch, PostgreSQL, and more. ([Grafana Labs][8])

---

## 47. What is a Grafana dashboard?

A Grafana dashboard is a collection of panels used to visualize and analyze data.

A dashboard can contain:

1. Time-series graphs
2. Tables
3. Gauges
4. Stat panels
5. Heatmaps
6. Logs panels
7. Variables
8. Alerts

Grafana dashboards help query, transform, visualize, and understand data from connected sources. ([Grafana Labs][9])

---

## 48. What is a panel in Grafana?

A **panel** is a single visualization inside a dashboard.

Examples:

```text
CPU usage graph
Memory usage gauge
API latency time-series graph
Error rate stat panel
Database connection table
```

One dashboard can have many panels.

---

## 49. What are Grafana variables?

Variables make dashboards dynamic.

Example variable:

```text
service = payment-service, order-service, user-service
environment = dev, qa, prod
instance = app1, app2, app3
```

PromQL with variable:

```promql
up{job="$job"}
```

Grafana variables are helpful when one dashboard needs to work for multiple services, servers, or environments. ([Grafana Labs][10])

---

## 50. What are Grafana annotations?

Annotations mark important events on graphs.

Examples:

```text
Deployment happened at 10:30 AM
Database migration completed
Production incident started
New version released
```

In Grafana, annotations appear as markers or vertical lines on visualizations, helping correlate events with metric changes. ([Grafana Labs][11])

---

# Java/Spring Boot Practical Setup

For Java backend interviews, the interviewer may ask:

> “How did you expose Spring Boot metrics to Prometheus?”

Use this explanation.

## Step 1: Add dependencies

For Maven:

```xml
<dependencies>
    <!-- Spring Boot Actuator -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>

    <!-- Prometheus Registry for Micrometer -->
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-prometheus</artifactId>
    </dependency>
</dependencies>
```

Spring Boot uses Micrometer for application metrics, and the Prometheus registry exposes those metrics in Prometheus-compatible format. ([Home][3])

---

## Step 2: Expose Prometheus endpoint

`application.yml`:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      show-details: always
```

Now Spring Boot exposes:

```text
http://localhost:8080/actuator/prometheus
```

---

## Step 3: Configure Prometheus

`prometheus.yml`:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'spring-boot-blog-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['localhost:8080']
```

Prometheus will scrape your Spring Boot application every 15 seconds.

---

## Step 4: Create custom metric in Java

Example: count how many orders are created.

```java
import io.micrometer.core.instrument.Counter;
import io.micrometer.core.instrument.MeterRegistry;
import org.springframework.stereotype.Service;

@Service
public class OrderService {

    private final Counter orderCreatedCounter;

    public OrderService(MeterRegistry meterRegistry) {
        this.orderCreatedCounter = Counter.builder("order_created_total")
                .description("Total number of orders created")
                .tag("service", "order-service")
                .register(meterRegistry);
    }

    public void createOrder() {
        // Business logic to create order
        orderCreatedCounter.increment();
    }
}
```

Prometheus metric:

```text
order_created_total{service="order-service"}
```

PromQL:

```promql
rate(order_created_total[5m])
```

---

## Step 5: Create custom timer for API latency

```java
import io.micrometer.core.instrument.MeterRegistry;
import io.micrometer.core.instrument.Timer;
import org.springframework.stereotype.Service;

@Service
public class PaymentService {

    private final Timer paymentTimer;

    public PaymentService(MeterRegistry meterRegistry) {
        this.paymentTimer = Timer.builder("payment_processing_seconds")
                .description("Time taken to process payment")
                .tag("service", "payment-service")
                .register(meterRegistry);
    }

    public void processPayment() {
        paymentTimer.record(() -> {
            // payment processing logic
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
    }
}
```

PromQL for average latency:

```promql
rate(payment_processing_seconds_sum[5m])
/
rate(payment_processing_seconds_count[5m])
```

---

# Important PromQL Queries for Interview

## Application availability

```promql
up{job="spring-boot-app"}
```

## Application down alert condition

```promql
up{job="spring-boot-app"} == 0
```

## Request per second

```promql
sum(rate(http_server_requests_seconds_count[1m]))
```

## Error rate percentage

```promql
sum(rate(http_server_requests_seconds_count{status=~"5.."}[5m]))
/
sum(rate(http_server_requests_seconds_count[5m]))
* 100
```

## p95 latency

```promql
histogram_quantile(
  0.95,
  sum(rate(http_server_requests_seconds_bucket[5m])) by (le)
)
```

## JVM heap usage percentage

```promql
sum(jvm_memory_used_bytes{area="heap"})
/
sum(jvm_memory_max_bytes{area="heap"})
* 100
```

## CPU usage

```promql
system_cpu_usage
```

## Active database connections

```promql
hikaricp_connections_active
```

---

# Strong Interview Answer: End-to-End Flow

You can say this in interview:

> In my Spring Boot application, I use Spring Boot Actuator and Micrometer Prometheus registry. Actuator exposes application metrics at `/actuator/prometheus`. Prometheus scrapes this endpoint at a configured interval using `prometheus.yml`. The collected metrics are stored as time-series data with labels. Then Grafana is connected to Prometheus as a data source, where I create dashboards for API latency, request throughput, error rate, JVM memory, CPU usage, and database connections. For alerting, Prometheus alerting rules can detect conditions like high error rate or application down, and Alertmanager can route notifications to email, Slack, or PagerDuty.

---

# Final Revision Cheat Sheet

| Concept                | Remember Like This                             |
| ---------------------- | ---------------------------------------------- |
| Prometheus             | Metrics collection + time-series DB + alerting |
| Grafana                | Visualization + dashboards                     |
| Scrape                 | Prometheus pulling metrics                     |
| Target                 | Endpoint Prometheus scrapes                    |
| Exporter               | Converts system metrics into Prometheus format |
| Counter                | Only increases                                 |
| Gauge                  | Goes up and down                               |
| Histogram              | Bucket-based latency distribution              |
| Summary                | Client-side quantiles                          |
| PromQL                 | Query language for Prometheus                  |
| `rate()`               | Per-second increase                            |
| `increase()`           | Total increase over range                      |
| `up`                   | Target health                                  |
| Alertmanager           | Routes and manages alerts                      |
| Grafana variables      | Dynamic dashboards                             |
| Annotations            | Mark deployments/incidents on graphs           |
| Micrometer             | Java metrics facade                            |
| Actuator               | Spring Boot production monitoring endpoints    |
| `/actuator/prometheus` | Spring Boot Prometheus metrics endpoint        |

Best preparation strategy: focus heavily on **Counter vs Gauge vs Histogram**, **rate/increase/histogram_quantile**, **Spring Boot Actuator integration**, **Grafana dashboard creation**, and **alerting flow**. These are the most interview-relevant areas for Java backend and microservices roles.

[1]: https://prometheus.io/docs/introduction/overview/?utm_source=chatgpt.com "Overview | Prometheus"
[2]: https://prometheus.io/docs/concepts/data_model/?utm_source=chatgpt.com "Data model"
[3]: https://docs.spring.io/spring-boot/reference/actuator/metrics.html?utm_source=chatgpt.com "Metrics :: Spring Boot"
[4]: https://prometheus.io/docs/instrumenting/exporters/?utm_source=chatgpt.com "Exporters and integrations"
[5]: https://prometheus.io/docs/tutorials/understanding_metric_types/?utm_source=chatgpt.com "Understanding metric types"
[6]: https://prometheus.io/docs/alerting/latest/alertmanager/?utm_source=chatgpt.com "Alertmanager"
[7]: https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/?utm_source=chatgpt.com "Alerting rules"
[8]: https://grafana.com/docs/grafana/latest/datasources/?utm_source=chatgpt.com "Data sources | Grafana documentation"
[9]: https://grafana.com/docs/grafana/latest/visualizations/dashboards/?utm_source=chatgpt.com "Dashboards | Grafana documentation"
[10]: https://grafana.com/docs/grafana/latest/visualizations/dashboards/variables/?utm_source=chatgpt.com "Variables | Grafana documentation"
[11]: https://grafana.com/docs/grafana/latest/visualizations/dashboards/build-dashboards/annotate-visualizations/?utm_source=chatgpt.com "Annotate visualizations | Grafana documentation"
