---
title: "MCP-DEPLOY-01 — Container Hardening (Non-Root, Capabilities)"
weight: 81
---

# MCP-DEPLOY-01 — Container Hardening (Non-Root, Capabilities)

**Level:** L3
**Domain:** DEPLOY  

## Requirement

MCP servers deployed in containers MUST run as non-root users (UID != 0) and SHOULD drop all Linux capabilities unless specifically required.

## Rationale

Running as root amplifies impact of any vulnerability. Container escape exploits often require root privileges.

## Scope

Docker, Kubernetes, or other container deployments.

## Required Evidence

1. Dockerfile showing `USER 1000` or named user
2. Kubernetes pod spec: `runAsNonRoot: true`, `runAsUser: 1000`
3. Capabilities dropped: `drop: [ALL]`
4. Test: `whoami` inside container → not root

## Verification Guidance

### Static
```dockerfile
# Good
FROM node:20-slim
RUN useradd -u 1000 mcpuser
USER 1000
CMD ["node", "server.js"]
```

```yaml
# Good (Kubernetes)
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
  capabilities:
    drop: [ALL]
```

### Dynamic
- `docker exec <container> whoami` → should output non-root user
- Attempt to write to `/usr/bin` → should fail

## Mappings

- OWASP MCP Top 10: MCP-09 (Shadow MCP Servers - deployment security)
- Docker CIS Benchmark: 5.3 (Ensure containers do not run as root)
- NIST SP 800-190

## Exceptions & Compensating Controls

NOT_APPLICABLE: Non-containerized deployments (but principle of least privilege still applies via system users).

## References

- Docker security best practices
- Trend Micro: "Using Containers to Secure MCP Infrastructure" (Sept 2024)
