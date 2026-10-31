# Deprecated: api.cjs

**Status: NOT RUNNING as of 2026-10-31** (verified via `ps -ef` — no match).
Not referenced by `package.json` scripts. Not the target of any configured
reverse proxy (`Caddyfile` proxies to 127.0.0.1:5173, the Vite dev server,
not to 9999). Port 9999 is not in use.

## Historical concerns (do not re-enable without remediation)

This file, if run, would expose:
- `Access-Control-Allow-Origin: *`
- Unauthenticated `/api/health` executing `bash $HOME/twgt-git-hitl/scripts/check-drift.sh`
- Unauthenticated `/api/repos` executing `gh repo list` (uses host GitHub token)
- Unauthenticated `/api/pipelines` executing `gh pr list`
- Unauthenticated `/api/device` executing 5 system commands (`up time -p` is broken; correct is `uptime -p`)

## If a device API is rebuilt

Use the four-operation fixed interface per EPR-021:
  - GET /api/health         → static liveness, no execution
  - GET /api/device-info    → approved device metadata
  - GET /api/repo-status    → read-only Git status, fixed path set
  - GET /api/pipeline-status → approved pipeline metadata
Each route: per-route auth, CORS allowlist, request size/timeout/output caps.
Bind loopback unless explicitly scoped otherwise. Never re-use this file as a base.
