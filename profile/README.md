# FortressWAF

Web application firewalls have been around for decades, but most of them were designed when applications were simpler. Monolithic codebases, predictable request patterns, and a clear network perimeter. That world is gone. Applications today are distributed, APIs are public, and attackers have moved past simple signature-based exploits.

We built FortressWAF because the tooling had not caught up. Existing solutions were either expensive enterprise suites that required dedicated ops teams, or open source projects that covered one or two attack vectors and left the rest to chance. Neither approach fits the way modern teams ship software.

FortressWAF started as an internal project to solve a specific problem: we needed a security layer that could inspect traffic across HTTP, WebSocket, and GraphQL — without requiring separate rule sets for each protocol. The project grew as we added detection modules for the attacks we actually encountered in production, not the ones that looked good on a compliance checklist.

---

## Projects

There are currently three active repositories under this organization:

### FortressWAF (core)
The main WAF engine. Acts as a reverse proxy or API gateway with a configurable inspection pipeline. All detection and enforcement logic lives here.

**Status:** Stable. Active development on detection modules and protocol support.  
**Language:** Go (proxy engine), Python (ML sidecar), TypeScript (dashboard)  
**Latest:** v1.2.0

### fortressctl
CLI tool for managing running FortressWAF instances. Communicates with the admin REST API to check status, reload configuration, list rules and sites, and view config summaries.

**Status:** Initial release. Covers the most common admin operations.  
**Language:** Go  
**Latest:** v0.1.0

### fortresshoneypot
Lightweight, low-interaction honeypot that listens on configurable TCP ports, captures HTTP requests and SSH connection attempts, and logs structured events to JSON for SIEM ingestion. Designed to sit alongside production traffic and detect reconnaissance without exposing real services.

**Status:** Initial release. HTTP/1.x and SSH banner capture implemented.  
**Language:** Go  
**Latest:** v0.1.0

---

## Core Features

The main WAF engine provides the following capabilities. Each feature is self-contained in the inspection pipeline and can be enabled or disabled independently.

| Layer | Modules |
|---|---|
| **Request inspection** | SQL injection, cross-site scripting, remote code execution, local/remote file inclusion, XXE, SSRF, path traversal, protocol anomalies, HTTP method validation, body size limits |
| **Authentication** | JWT validation with JWKS, OAuth 2.0 introspection, mTLS with certificate validation, API key management, CAPTCHA verification (reCAPTCHA, hCaptcha) |
| **Content security** | File upload validation (type, size, content inspection), credential leak prevention, GraphQL depth and alias limits, SOAP/XML nesting depth validation, gRPC message size limits |
| **Traffic management** | DDoS protection, rate limiting (token bucket, leaky bucket, sliding window, fixed window), IP reputation with threat feeds, Tor/proxy/VPN detection, ASN and CIDR filtering, WebSocket inspection, bot detection with fingerprinting |
| **Credential protection** | Brute force and credential stuffing detection per IP, login path discovery, account lockout with configurable thresholds |
| **Response inspection** | Response body capture and inspection for data leakage, configurable per-route |
| **Observability** | Prometheus metrics endpoint, structured JSON logging, SIEM event export (Elasticsearch, Splunk), Grafana dashboards, Elasticsearch and Kibana configurations, live health and readiness probes |
| **Management** | REST API for runtime configuration, configuration hot-reload without restart, admin authentication with API keys |

The ML sidecar (optional, Python) provides anomaly scoring and attack classification via separate API endpoints. Multiple models run independently: anomaly detection, attack classification, bot detection, and risk scoring.

---

## Project Structure (Core Repo)

```
cmd/proxy/           — WAF server entry point
internal/
  engine/            — Core inspection pipeline
  api/               — REST API handlers
  config/            — YAML config with live reload
  reputation/        — IP reputation and threat feeds
  ratelimit/         — Rate limiting algorithms
  session/           — Session tracking
  siem/              — SIEM export
deploy/              — Docker, Ansible, Terraform, Helm, monitoring
docs/                — Documentation
dashboard/           — Web dashboard (Next.js)
ml-engine/           — ML sidecar (Python/FastAPI)
```

---

## Deployment

Run the core WAF as a reverse proxy behind your load balancer. Configuration is YAML, loaded at startup, and watched for changes at runtime.

```
docker run -v /path/to/config.yaml:/etc/fortresswaf/config.yaml -p 8080:8080 fortresswaf/proxy
```

Supported deployment methods:
- Docker and Docker Compose
- Kubernetes with Helm charts
- Ansible playbooks
- Terraform modules
- Systemd service files

The architecture supports reverse proxy mode as the primary deployment. Bridge, sidecar, and API gateway modes are documented as aspirational targets.

---

## Developer

Built and maintained by [@zulfff](https://github.com/zulfff).

## License

GNU Affero General Public License v3.0.
