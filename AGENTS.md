# AGENTS.md

## Repo purpose and scope

- This is a **documentation-only** repository for the Prometheus + Grafana monitoring challenge. `README.md` is the deliverable.
- There is no application code, Compose file, dashboard, or config in Git. Do not add implementation files (compose, YAML, scripts, JSON dashboards) unless the user explicitly asks for them.
- Safe default when asked to improve the project: update `README.md` and this file only.

## Current state

- Fresh Git repo on `main`, remote `https://github.com/javierjuanJJ/Prometheus-and-Grafana.git`; it may have no commits yet.
- `opencode.json` configures the Context7 MCP server (`context7`). Use it to verify upstream docs, flags, and image versions before changing technical claims.
- No build, test, lint, or typecheck scripts exist. Do not invent or run them.

## README conventions

- Write in **Spanish**; keep standard technical terms in English (scrape, target, dashboard, receiver).
- Target stack: Ubuntu/Debian + Docker Compose v2, with Prometheus, Grafana, Alertmanager, Loki, Alloy, and service exporters.
- Pin exact image tags, never `latest`. Versions are declared in the README badges and `.env` snippet; update both together and note the verification date.
- Assume the reader creates the files on a server. Snippets must be copy-pasteable and self-consistent: service names, ports, paths, and versions must match across sections.
- Keep the requirement-to-section mapping and acceptance checklist aligned with the challenge brief.
- Do not expose admin services publicly: Prometheus/Grafana/Alertmanager/Loki default to `127.0.0.1`; TLS is via a host reverse proxy.

## Non-obvious technical constraints

- `node_exporter` needs `pid: host`, `network_mode: host`, and `/:/host:ro,rslave` with `--path.rootfs=/host`. Container-only metrics are not host metrics.
- Host-network exporters are scraped through `host.docker.internal`; Prometheus needs `extra_hosts: ["host.docker.internal:host-gateway"]`.
- Promtail is removed as of Loki 3.7.3; use **Grafana Alloy** for logs.
- Prometheus 3.x retention is configured in `prometheus.yml` under `storage.tsdb.retention` (`time` + `size`); the first limit reached applies. The old `--storage.tsdb.retention.*` flags are deprecated. Default time retention is 15 days.
- `--web.enable-admin-api` is only for TSDB snapshots; it must stay disabled otherwise.
- Do not put MongoDB credentials in `--mongodb.uri`; use `MONGODB_USER`/`MONGODB_PASSWORD`. Keep MySQL exporter credentials in the Docker secret, not in Compose.
- Grafana OSS has basic roles and folder/dashboard permissions; fine-grained RBAC is Enterprise. Do not claim otherwise.

## Validation

- Check every internal TOC link and fenced code block after editing.
- If a Compose snippet changes, validate it in a scratch directory (not this repo) with `docker compose config -q`.
- If Prometheus config/rules snippets change, validate them with `promtool check config` and `promtool check rules`.
- Verify readiness commands against the current ports: `9090/-/ready`, `9093/-/ready`, `3000/api/health`, `3100/ready`, `12345/-/ready`.
- Do not run destructive Docker commands against the repo; there is no stack here.

## Source of truth

- `README.md` for scope, versions, and step-by-step instructions.
- Context7/official upstream docs for flags and APIs when a claim may be outdated.
- Preserve verified guidance; delete stale instructions rather than layering contradictions.
