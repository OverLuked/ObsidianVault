## Monitoring & Observability
*Understanding system health and behavior in production*

### Monitoring vs Observability
- Monitoring -> watching known metrics/thresholds and alerting when they're crossed
- Observability -> being able to ask new questions about system behavior without shipping new code, using the data already being collected

### The Three Pillars of Observability

| Pillar   | What It Captures                        | Example Tools           |
| --------- | ------------------------------------------ | -------------------------- |
| Metrics    | Numeric measurements over time             | Prometheus, Datadog        |
| Logs       | Discrete, timestamped event records        | ELK Stack, Splunk          |
| Traces     | The path of a request across services      | Jaeger, Zipkin             |

### Common Tools
- Prometheus - time-series metrics collection and alerting
- Grafana - dashboards/visualization, often paired with Prometheus
- ELK Stack (Elasticsearch, Logstash, Kibana) - log aggregation and search
- Datadog / New Relic - commercial, all-in-one observability platforms

### SRE Concepts
*Site Reliability Engineering - applying software engineering to operations problems*

##### SLI, SLO, SLA
- SLI (Service Level Indicator) -> a measured metric (e.g. request latency, error rate)
- SLO (Service Level Objective) -> the internal target for an SLI (e.g. 99.9% of requests < 200ms)
- SLA (Service Level Agreement) -> the external, often contractual, commitment to a customer

##### Error Budget
- The allowed amount of unreliability within an SLO (e.g. 0.1% downtime allowed per month)
- Once the budget is spent, feature work pauses in favor of reliability work

### Incident Management
- Alerting -> notify the right people when a threshold is breached
- On-call rotation -> a scheduled responsibility for responding to incidents
- Postmortem -> blameless review after an incident - focuses on process/system fixes, not blame
- Runbook -> step-by-step documented response to a known failure scenario

### Common Interview Questions
- What's the difference between monitoring and observability?
- Explain SLI, SLO, and SLA and how they relate
- What is an error budget and how does it change team behavior?
- Walk through how you'd investigate a service that's suddenly slow
