---
title: "MCP-LOG-01 — Comprehensive Audit Logging"
weight: 61
---

# MCP-LOG-01 — Comprehensive Audit Logging

**Level:** L3
**Domain:** LOG  

## Requirement

MCP servers MUST log security-relevant events including tool invocations, authentication attempts, authorization decisions, and errors with sufficient detail for forensic analysis.

## Rationale

Audit logs enable detection of misuse, support incident response, and satisfy compliance requirements.

## Scope

All production deployments (L2+).

## Required Evidence

1. Log entries showing: timestamp, user/client ID, tool name, result (success/failure)
2. Logs stored in immutable or tamper-evident storage
3. Sample log query demonstrating ability to answer: "Who accessed what resource at what time?"

## Verification Guidance

### Static
```python
# Example
logger.info({
    "timestamp": datetime.utcnow().isoformat(),
    "user_id": request.user_id,
    "tool": "read_file",
    "args_hash": hashlib.sha256(str(args).encode()).hexdigest()[:16],
    "result": "success",
    "ip": request.remote_addr
})
```

### Dynamic
- Invoke tool → verify log entry appears
- Check log includes user attribution
- Verify sensitive args are hashed/redacted (see MCP-LOG-02)

## Mappings

- OWASP Top 10: A09:2021 – Security Logging and Monitoring Failures
- OWASP MCP Top 10: MCP-08 (Lack of Audit & Telemetry)
- OWASP ASVS: V7.1 (Log Content Requirements)

## Exceptions & Compensating Controls

NOT_APPLICABLE: L1 development environments may use minimal logging.

## References

- OWASP Logging Cheat Sheet
- Trend Micro / WorkOS articles on audit requirements
