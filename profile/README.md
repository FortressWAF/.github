# FortressWAF

Security tooling for application-layer traffic inspection, log analysis, and threat detection. All projects are offline-capable, cross-platform, and distributed as single binaries with no runtime dependencies.

---

## Projects

### FortressWAF (core)

Application-layer firewall that inspects HTTP, WebSocket, and GraphQL traffic. Runs as a reverse proxy, API gateway, or sidecar. Configuration is YAML with live reload.

- Language: Go (engine), Python (ML sidecar), TypeScript (dashboard)
- Status: v1.2.0 — active development
- License: AGPL-3.0

### fortressctl

CLI client for the FortressWAF admin REST API. Supports status checks, configuration reload, rule listing, site management, and config summary display.

- Language: Go
- Status: v0.1.0 — initial release
- License: AGPL-3.0

### fortresshoneypot

Low-interaction honeypot for TCP port monitoring. Captures HTTP requests and SSH connection attempts, outputs structured JSON events for SIEM ingestion. Designed for deployment alongside production services.

- Language: Go
- Status: v0.1.0 — initial release
- License: AGPL-3.0

### fortress-trace

Multi-source log file parser that reads syslog, auth.log, nginx access/error, and Windows EVTX files. YAML rule engine with sliding-window aggregation and MITRE ATT&CK technique mapping. Outputs findings to terminal, JSON, or CSV.

- Language: Go (core), Rust (optional high-performance parser)
- Status: v0.1.0 — initial release
- License: MIT

---

## Capabilities (Core WAF)

Each module in the inspection pipeline can be enabled or disabled independently:

| Layer | Modules |
|---|---|
| Request inspection | SQL injection, XSS, RCE, LFI/RFI, XXE, SSRF, path traversal, protocol anomalies, HTTP method validation, body size limits |
| Authentication | JWT validation (JWKS), OAuth 2.0 introspection, mTLS, API key management, CAPTCHA verification |
| Content security | File upload validation (type, size, content), credential leak prevention, GraphQL depth and alias limits, SOAP/XML nesting depth, gRPC message size limits |
| Traffic management | Rate limiting (token bucket, leaky bucket, sliding window, fixed window), IP reputation with threat feeds, Tor/proxy/VPN detection, ASN/CIDR filtering, WebSocket inspection, bot detection |
| Credential protection | Brute force detection per source IP, login path discovery, configurable lockout thresholds |
| Response inspection | Response body capture and configurable inspection per route |
| Observability | Prometheus metrics, structured JSON logging, SIEM export (Elasticsearch, Splunk), Grafana dashboards, health and readiness probes |
| Management | REST API for runtime configuration, hot-reload, admin authentication |

The optional ML sidecar (Python) runs separate models for anomaly detection, attack classification, bot detection, and risk scoring via dedicated API endpoints.

---

## Deployment

```
docker run -v /path/to/config.yaml:/etc/fortresswaf/config.yaml -p 8080:8080 fortresswaf/proxy
```

Supported deployment methods:
- Docker and Docker Compose
- Kubernetes with Helm charts
- Ansible playbooks
- Terraform modules
- Systemd service files

Primary deployment mode is reverse proxy. Configuration is YAML, loaded at startup, and watched for runtime changes.

---

Maintained by [@zulfff](https://github.com/zulfff).
