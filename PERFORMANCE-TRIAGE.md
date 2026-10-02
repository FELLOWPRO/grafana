# DocBits performance diagnosis

Dashboard: `docbits-performance-triage.json`, UID `docbits-performance-triage`.
Live EU view: https://grafana.docbits.com/d/docbits-performance-triage

Start with measurement quality, then select an endpoint and compare phases, worker events and resource pressure on the shared time axis. Request ranking and latency alerts are withheld until all reachable API pods advertise `docbits_api_metrics_multiprocess_enabled=1` and histogram count/+Inf agree, with target identities and full-window scrape coverage checked. The current healthy target count must also equal Kubernetes desired API replicas. The 3-second threshold additionally requires the new bucket. p95 requires at least 200 observations per endpoint in the five-minute window. No missing metric is presented as a zero duration.

Resource panels already use existing Kubernetes/Executor metrics. Executor values cover the scraped worker, not every worker. Worker import completion is not ASGI readiness. Component averages cover requests that measured that component; overlapping phases must not be subtracted to infer CPU. HTTP durations stop at response headers and do not measure browser readiness.

Dependencies: DocBits_API PRs 11919/11920 and central DevOps PR677. Auth, upstream and SQL pool-wait instrumentation remain gaps until individually implemented and verified. The dashboard cannot create those measurements. Existing old production histogram data must not be interpreted as reliable performance evidence.

The automatic warning table highlights endpoints with more than 5% responses over one second and at least 200 observations in five minutes. It does not send messages to external recipients. Explore links select the same time window for diagnostic logs and API traces; EU Loki/Tempo datasource health checks pass; trace coverage remains a separate gate. They are searches, not proof that an individual slow request has a recorded trace.

EU provisioning currently syncs this repository's main branch. A manually created EU dashboard was verified by API readback; this PR makes its source durable. No API rollout, production load test or measured speedup is implied.

Independent static review found and corrected missing target identity checks, bidirectional histogram completeness, rollout lookback coverage, and low-sample Redis/EventLoop quantiles. Final 24 PromQL targets parsed successfully against the EU datasource. Dev/US existing cluster-secret authentication returned 401; their Grafana views were not modified. No posted access tokens were copied into files or logs.
