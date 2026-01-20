---
title: "MCP-LOG-02 — Secret Redaction in Logs"
weight: 62
---

# MCP-LOG-02 — Secret Redaction in Logs

**Level:** L1  
**Domain:** LOG  

## Requirement

MCP servers MUST NOT log secrets (API keys, passwords, tokens, credentials) in plaintext. Implement automated redaction or avoid logging sensitive parameters entirely.

## Rationale

Logs are often stored insecurely, aggregated in log management systems, and accessible to many personnel. Plaintext secrets in logs enable credential theft.

## Scope

All logging (application logs, access logs, error logs).

## Required Evidence

1. Logging configuration or code showing redaction of sensitive fields
2. Sample log output confirming secrets are redacted (e.g., `api_key=***REDACTED***`)
3. Test: tool invoked with API key → log does not contain actual key

## Verification Guidance

### Static
```python
# Example
import re
SENSITIVE_PATTERNS = [
    r'(api[_-]?key=)([^\s&]+)',
    r'(Authorization:\s*Bearer\s+)([^\s]+)',
    r'(password=)([^\s&]+)'
]

def redact_secrets(message):
    for pattern in SENSITIVE_PATTERNS:
        message = re.sub(pattern, r'\1***REDACTED***', message)
    return message

logger.info(redact_secrets(f"Calling API with {api_key}"))
```

### Dynamic
- Trigger error with API key in request
- Check logs: key should not appear

## Mappings

- CWE: CWE-532 (Insertion of Sensitive Information into Log File)
- OWASP Top 10: A09:2021
- OWASP MCP Top 10: MCP-01 (Token Mismanagement & Secret Exposure)

## Exceptions & Compensating Controls

NOT_APPLICABLE: If server never handles secrets (rare).

## References

- WorkOS: "Best Practices for MCP Secrets Management" (July 2024)
- Palo Alto Networks: "MCP Security Exposed" (May 2024)
