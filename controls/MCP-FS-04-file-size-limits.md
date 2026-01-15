# MCP-FS-04 — File Operation Size Limits

**Level:** L2
**Domain:** FS

## Requirement

Tools that read files SHOULD enforce maximum file size limits to prevent memory exhaustion and denial of service.

## Rationale

Reading arbitrarily large files can exhaust server memory, causing crashes or degraded performance. Malicious or coerced requests could target large files to cause denial of service.

## Scope

- **Tools:** Tools that read file contents into memory
- **Resources:** File-based resources that return content
- **Transport:** All transports
- **Profiles:** DEV-WORKSTATION, SHARED-INTERNAL, INTERNET-FACING, HIGH-RISK-TOOLS, REGULATED

## Required Evidence

- Configuration or code defines maximum file size for read operations
- File size is checked before reading content into memory
- Operations exceeding limit are rejected with appropriate error
- Limit is reasonable for use case (recommended: 10MB-100MB depending on context)

## Verification Guidance (non-tool-specific)

### Static (code/config review)

Look for:

- Configuration parameter for max file size (e.g., `max_file_size`, `read_limit`)
- Size check before `read()` operations
- Streaming or chunked reading for large files if needed
- Error handling when limit exceeded

Example pattern:

```python
MAX_FILE_SIZE = 50 * 1024 * 1024  # 50MB

def read_file(path):
    size = os.path.getsize(path)
    if size > MAX_FILE_SIZE:
        raise ValueError(f"File exceeds maximum size of {MAX_FILE_SIZE}")
    with open(path) as f:
        return f.read()
```

Red flags:

- Unbounded `read()` calls
- No size checking before memory allocation
- Size limits only in documentation, not enforced in code

### Dynamic (behavioral verification)

Test cases:

1. Read file within size limit → Should succeed
2. Read file exceeding size limit → Should fail with size error
3. Read file at exact limit boundary → Verify behavior is correct
4. Attempt to read very large file (if available) → Should fail quickly without memory spike

## Mappings

- **CWE:** CWE-400 (Uncontrolled Resource Consumption)
- **OWASP MCP Top 10:** Related to input validation
- **OWASP ASVS:** V11.1 (Business Logic Security)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**

- Server exposes no file reading tools or resources
- All file operations use streaming without full content loading

**Compensating controls:**

- Container memory limits (MCP-DEPLOY-03) prevent host impact but may cause container restart
- Streaming APIs that never load full file into memory
- Pagination for large file content

## References

- [MCP Security Best Practices](https://modelcontextprotocol.io/specification/draft/basic/security_best_practices)
