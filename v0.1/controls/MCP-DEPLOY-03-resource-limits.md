---
title: "MCP-DEPLOY-03 — Resource Limits and Rate Limiting"
weight: 83
---

# MCP-DEPLOY-03 — Resource Limits and Rate Limiting

**Level:** L2
**Domain:** DEPLOY

## Requirement

MCP servers MUST enforce resource limits including memory caps, CPU quotas, file descriptor limits, process limits, and request rate limiting to prevent denial of service and resource exhaustion attacks.

## Rationale

MCP servers processing untrusted input or LLM-generated requests are vulnerable to resource exhaustion attacks. The OWASP LLM Top 10 identifies "Unbounded Consumption" (LLM04) as a critical risk where recursive tool calls, large file operations, or malicious prompts cause CPU/memory exhaustion. Research documented MCP servers crashing from 10MB JSON payloads, infinite loop tool executions, and fork bombs via command injection. The "Billion Laughs" XML attack pattern applies to MCP's JSON-RPC protocol when processing deeply nested or exponentially expanding tool parameters. Without resource limits, a single malicious request can render an MCP server unresponsive, impacting all connected clients and potentially triggering cascading failures in distributed deployments.

## Scope

**Applies to:**
- All production MCP server deployments
- Containerized deployments (Docker, Kubernetes)
- Multi-tenant MCP environments
- Internet-facing MCP servers
- MCP servers processing untrusted input

**Does NOT apply to:**
- Local development servers with trusted input only
- Single-user deployments with controlled workloads

## Required Evidence

1. **Memory limits configured**: Container/process memory cap defined
2. **CPU limits configured**: CPU quota or shares limitation set
3. **Rate limiting implemented**: Request throttling per client/IP
4. **File descriptor limits**: ulimit or container limits configured
5. **Timeout configuration**: Request and execution timeouts defined
6. **Load test results**: Server remains responsive under stress
7. **Resource exhaustion test**: DoS attempt properly mitigated

## Verification Guidance

### Static (code/config review)

**Docker Compose configuration:**
```yaml
services:
  mcp_server:
    image: mcp-server:latest
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: '1.0'
        reservations:
          memory: 256M
          cpus: '0.5'
    ulimits:
      nofile:
        soft: 1024
        hard: 2048
      nproc:
        soft: 32
        hard: 64
    environment:
      - MAX_REQUEST_SIZE=1048576  # 1MB
      - REQUEST_TIMEOUT=30000      # 30 seconds
      - MAX_CONCURRENT_TOOLS=5
```

**Kubernetes resource limits:**
```yaml
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: mcp-server
    resources:
      limits:
        memory: "512Mi"
        cpu: "1000m"
        ephemeral-storage: "1Gi"
      requests:
        memory: "256Mi"
        cpu: "500m"
```

**Application-level rate limiting (Node.js):**
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 1 * 60 * 1000,  // 1 minute
  max: 100,                   // 100 requests per minute
  message: 'Too many requests',
  standardHeaders: true,
  legacyHeaders: false,
});

// Per-tool rate limiting
const toolLimiter = rateLimit({
  windowMs: 1 * 60 * 1000,
  max: 10,  // 10 tool executions per minute
  keyGenerator: (req) => req.user?.id || req.ip,
});

app.use('/mcp/tools/:tool/execute', toolLimiter);
```

**Python with asyncio timeouts:**
```python
import asyncio
from aiohttp import web
import resource

# Set process limits
resource.setrlimit(resource.RLIMIT_AS, (512 * 1024 * 1024, -1))  # 512MB
resource.setrlimit(resource.RLIMIT_NPROC, (32, 64))  # Max 32 processes

async def execute_tool(request):
    try:
        # Enforce execution timeout
        result = await asyncio.wait_for(
            tool.execute(params),
            timeout=30.0  # 30 second timeout
        )
        return web.json_response(result)
    except asyncio.TimeoutError:
        return web.json_response(
            {'error': 'Tool execution timeout'},
            status=408
        )
```

**Request size limiting (nginx proxy):**
```nginx
http {
    client_max_body_size 1M;
    client_body_timeout 30s;
    client_header_timeout 30s;

    limit_req_zone $binary_remote_addr zone=mcp:10m rate=10r/s;
    limit_conn_zone $binary_remote_addr zone=addr:10m;

    server {
        location /mcp {
            limit_req zone=mcp burst=20 nodelay;
            limit_conn addr 10;

            proxy_pass http://mcp-backend;
            proxy_read_timeout 30s;
            proxy_send_timeout 30s;
        }
    }
}
```

### Dynamic (behavioral verification)

**Resource exhaustion tests:**
```bash
# Memory bomb test
curl -X POST http://mcp-server/tools/execute \
  -d '{"params": "A" * 100000000}'  # 100MB payload
# Expected: Request rejected or server remains responsive

# CPU exhaustion test (stress test)
ab -n 10000 -c 100 http://mcp-server/tools/compute
# Monitor: CPU should be capped, server responsive

# Fork bomb simulation
curl -X POST http://mcp-server/tools/shell \
  -d '{"command": ":(){ :|:& };:"}'
# Expected: Process limit prevents fork bomb

# Check container limits
docker stats mcp-container --no-stream
# Verify: Memory and CPU within defined limits

# Kubernetes resource monitoring
kubectl top pod mcp-server-pod
```

**Rate limiting validation:**
```python
# Test script
import requests
import time

# Attempt rapid requests
for i in range(200):
    response = requests.post('http://mcp-server/tools/execute')
    if response.status_code == 429:
        print(f"Rate limited after {i} requests")
        break
    time.sleep(0.01)
```

## Mappings

- **CWE**: CWE-400 (Uncontrolled Resource Consumption), CWE-770 (Allocation Without Limits)
- **OWASP Top 10**: A05:2021 – Security Misconfiguration
- **OWASP LLM Top 10**: LLM04:2025 – Unbounded Consumption
- **OWASP MCP Top 10**: MCP-10 (Context Injection & Over-Sharing)
- **OWASP ASVS**: V11.1.2 (Verify application prevents against DoS attacks)
- **CIS Docker Benchmark**: 5.10 (Ensure memory usage for container is limited)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Single-user local development servers
- Batch processing servers with predictable workloads
- Internal tools with fully trusted users only

**Compensating controls:**
- Cloud provider auto-scaling (handles load but not prevents DoS)
- CDN/WAF rate limiting (network layer protection)
- Circuit breakers preventing cascade failures
- Monitoring and alerting on resource spikes

**Risk acceptance:**
- Development environments MAY use relaxed limits
- Batch processing MAY require higher limits (document and monitor)
- Some ML/data processing tools MAY need exemptions (isolate these)

## References

- OWASP LLM Top 10: "LLM04 - Unbounded Consumption" (2025)
- MCPSecBench: Resource exhaustion taxonomy and attack patterns
- "MCP Server DoS via Recursive Tool Calls" - Security research (April 2025)
- Docker Best Practices: Resource constraints - https://docs.docker.com/config/containers/resource_constraints/
- Kubernetes Resource Management: https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/
- NIST SP 800-204B: Attribute-based Access Control for Microservices
