---
title: "MCP-INPUT-02 — Input Bounds Enforcement"
weight: 52
---

# MCP-INPUT-02 — Input Bounds Enforcement

**Level:** L2
**Domain:** INPUT

## Requirement

MCP servers MUST enforce explicit bounds on all input parameters including maximum string length (10KB default), maximum array size (1000 items default), maximum object nesting depth (10 levels default), and maximum total JSON payload size (1MB default).

## Rationale

Unbounded input enables multiple attack vectors: denial of service through memory exhaustion, ReDoS (Regular Expression Denial of Service) via catastrophic backtracking, stack overflow through deep recursion, and disk exhaustion. Research shows 40% of tested MCP servers lack input size validation, with documented cases of 10,000-level nested objects causing crashes.

## Scope

**Applies to:**
- All tool arguments and parameters
- Resource request/response payloads
- File upload/download operations
- Streaming data chunks
- All transports (stdio, HTTP, WebSocket)

**Does NOT apply to:**
- Internal server-to-server communication (though recommended)
- Static configuration files

## Required Evidence

1. **Bounds configuration**: Documented limits for each input type
   ```json
   {
     "limits": {
       "maxStringLength": 10240,
       "maxArrayItems": 1000,
       "maxObjectDepth": 10,
       "maxPayloadSize": 1048576,
       "maxFileSize": 104857600
     }
   }
   ```

2. **Enforcement code**: Implementation showing bounds checking
3. **Test results**:
   - String at limit (10KB) accepted
   - String over limit (10KB + 1) rejected
   - Array at limit (1000 items) accepted
   - Array over limit (1001 items) rejected
   - Deeply nested object (11 levels) rejected
   - Large payload (>1MB) rejected

## Verification Guidance

### Static (code/config review)

**Look for:**
```python
# Python example
import json
from typing import Any, Dict

class BoundsValidator:
    MAX_STRING_LENGTH = 10 * 1024  # 10KB
    MAX_ARRAY_ITEMS = 1000
    MAX_OBJECT_DEPTH = 10
    MAX_PAYLOAD_SIZE = 1024 * 1024  # 1MB

    def validate_bounds(self, data: Any, depth: int = 0) -> None:
        if depth > self.MAX_OBJECT_DEPTH:
            raise ValueError(f"Object nesting depth {depth} exceeds limit {self.MAX_OBJECT_DEPTH}")

        if isinstance(data, str):
            if len(data) > self.MAX_STRING_LENGTH:
                raise ValueError(f"String length {len(data)} exceeds limit {self.MAX_STRING_LENGTH}")

        elif isinstance(data, list):
            if len(data) > self.MAX_ARRAY_ITEMS:
                raise ValueError(f"Array size {len(data)} exceeds limit {self.MAX_ARRAY_ITEMS}")
            for item in data:
                self.validate_bounds(item, depth + 1)

        elif isinstance(data, dict):
            for value in data.values():
                self.validate_bounds(value, depth + 1)

    def validate_payload(self, raw_data: bytes) -> Dict:
        if len(raw_data) > self.MAX_PAYLOAD_SIZE:
            raise ValueError(f"Payload size {len(raw_data)} exceeds limit {self.MAX_PAYLOAD_SIZE}")

        data = json.loads(raw_data)
        self.validate_bounds(data)
        return data
```

**Red flags:**
- No size checks before processing
- Recursive functions without depth tracking
- Unlimited file reads (`file.read()` without size parameter)
- Regular expressions on unbounded input

### Dynamic (behavioral verification)

**Test cases:**
1. **Memory exhaustion test**: Send 1GB string → Expect rejection before OOM
2. **CPU exhaustion test**: Nested arrays `[[[[[[...]]]]]]` (1000 levels) → Expect rejection
3. **ReDoS test**: Pattern `(a+)+` with input `"a" * 10000 + "b"` → Expect timeout/rejection
4. **File size test**: Upload 1GB file when limit is 100MB → Expect rejection
5. **Streaming test**: Send continuous data stream → Expect cutoff at limit

**Expected behavior:**
- Fast rejection (< 100ms) when bounds exceeded
- Clear error message indicating which limit was exceeded
- No resource consumption proportional to attack input size
- Graceful handling without crashes

## Mappings

- **CWE**: CWE-400 (Uncontrolled Resource Consumption), CWE-776 (Improper Restriction of Recursive Entity References), CWE-1284 (Improper Validation of Specified Quantity in Input)
- **OWASP Top 10**: A05:2021 – Security Misconfiguration
- **OWASP MCP Top 10**: MCP-04 (Denial of Service via Resource Exhaustion)
- **OWASP ASVS**: V5.1.4 (Verify that data input validation is enforced on a trusted service layer)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Server handles only fixed-size inputs (rare)
- All inputs are generated internally (no external input)

**Compensating controls:**
- Resource limits at OS/container level (CPU, memory quotas) - provides last line of defense
- Rate limiting to prevent repeated attempts
- Circuit breakers that trip on resource exhaustion

**Risk acceptance:**
- May be relaxed for trusted internal environments at L1
- MUST be enforced for L2+ and any external-facing deployment

## References

- Empirical finding: 40% of MCP servers lack input validation (arXiv:2506.13538)
- ReDoS vulnerability patterns in MCP servers
- Stack overflow via 10,000-level nested JSON objects (MCPManager research)
- OWASP Denial of Service Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Denial_of_Service_Cheat_Sheet.html
- CWE-400: https://cwe.mitre.org/data/definitions/400.html