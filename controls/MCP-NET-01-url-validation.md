# MCP-NET-01 — URL Validation and SSRF Mitigation

**Level:** L1  
**Domain:** NET  

## Requirement

MCP servers that fetch URLs MUST validate target addresses against an allowlist or MUST block private IP ranges, localhost, and cloud metadata endpoints.

## Rationale

Server-Side Request Forgery (SSRF) allows attackers to use the server as a proxy to access internal services, cloud metadata (AWS/GCP/Azure credentials), or perform port scanning.

## Scope

Tools that make HTTP/HTTPS requests, fetch web content, or resolve DNS.

## Required Evidence

1. Code showing URL validation before fetch
2. Blocked addresses include: `127.0.0.1`, `localhost`, `169.254.169.254`, `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`
3. Test: attempt to fetch `http://169.254.169.254/latest/meta-data` → expect rejection

## Verification Guidance

### Static
```python
# Example validation
import ipaddress
from urllib.parse import urlparse

def is_safe_url(url):
    parsed = urlparse(url)
    # Resolve to IP
    ip = socket.gethostbyname(parsed.hostname)
    ip_obj = ipaddress.ip_address(ip)
    
    # Block private/loopback/metadata
    if ip_obj.is_private or ip_obj.is_loopback or ip_obj.is_link_local:
        raise ValueError("Private IP access denied")
    
    # Additional: check against allowlist if applicable
    return True
```

### Dynamic
- Request `http://localhost:8080` → BLOCKED
- Request `http://169.254.169.254` → BLOCKED
- Request legitimate external URL → ALLOWED

## Mappings

- CWE: CWE-918 (Server-Side Request Forgery)
- OWASP Top 10: A10:2021 – Server-Side Request Forgery
- OWASP MCP Top 10: Implied in MCP-07 (Insufficient AuthN/AuthZ)

## Exceptions & Compensating Controls

NOT_APPLICABLE: Server has no network fetch capabilities.
Compensating: Network-level egress filtering (firewall rules) provides additional protection but does not replace application-level validation.

## References

- CVE-2025-5276: SSRF in markdownify-mcp
- Snyk Labs: "Prompt Injection Meets MCP" (July 2025)
- OWASP SSRF Prevention Cheat Sheet
