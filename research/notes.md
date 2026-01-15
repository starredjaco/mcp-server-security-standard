# Research Notes — MCP Server Security Standard v0.1

This document summarizes the research sources, patterns, and claims used to inform the MSSS v0.1 controls.

## Sources

### Official MCP Documentation

| Source | Summary |
|--------|---------|
| [MCP Specification — Security Best Practices](https://modelcontextprotocol.io/specification/draft/basic/security_best_practices) | Official Anthropic guidance on confused deputy attacks, session management, local server risks |
| [MCP Authorization Tutorial](https://modelcontextprotocol.io/docs/tutorials/security/authorization) | OAuth 2.1 token delegation patterns for MCP servers |

### OWASP & Industry Frameworks

| Source | Summary |
|--------|---------|
| [OWASP MCP Top 10 (2025)](https://modelcontextprotocol-security.io/top10/) | Community-driven top 10 security risks for MCP servers and clients |
| [OWASP MCP Server Security Risks](https://modelcontextprotocol-security.io/top10/server/) | Server-specific risk categories: token mismanagement, scope creep, schema poisoning |
| [OWASP GenAI Security Project](https://genai.owasp.org/) | Hardening patterns: network segmentation, gateway controls, rate limiting |
| [Cloud Security Alliance MCP Resource Center](https://cloudsecurityalliance.org/blog/2025/08/20/) | Mapping to OWASP, MITRE ATLAS, NIST RMF, ISO 42001, SOC 2, EU AI Act |

### Academic Research

| Source | Summary |
|--------|---------|
| [arXiv:2508.12538 — MCPLIB](https://arxiv.org/abs/2508.12538) | Taxonomy of 31 attack methods across 4 classes (direct injection, indirect injection, malicious user, LLM inherent) |
| [HuggingFace:2506.02040](https://huggingface.co/papers/2506.02040) | Attack vector study: tool poisoning, puppet attacks, rug pulls, malicious external resources |
| [arXiv:2506.13538 — Empirical Study](https://arxiv.org/abs/2506.13538) | Analysis of 1,899 MCP servers; 7.2% with vulnerabilities, 5.5% with tool poisoning |
| [Hou et al. — MCP Landscape](https://xinyi-hou.github.io/files/hou2025mcp.pdf) | Lifecycle security analysis: creation, operation, update phase risks |

### Vulnerability Reports

| CVE/Report | Summary |
|------------|---------|
| [CVE-2025-53109](https://cymulate.com/blog/cve-2025-53109-53110-escaperoute-anthropic/) | Symlink bypass in Anthropic Filesystem MCP Server (CVSS 8.8) |
| [CVE-2025-53110](https://cymulate.com/blog/cve-2025-53109-53110-escaperoute-anthropic/) | Naive prefix matching bypass (CVSS 7.3) |
| [CVE-2025-61492](https://www.sentinelone.com/vulnerability-database/cve-2025-61492/) | Command injection in terminal-controller-mcp via unsanitized input |
| [CVE-2025-5276](https://labs.snyk.io/resources/prompt-injection-mcp/) | SSRF in markdownify-mcp allowing internal service access |
| [CVE-2025-5277](https://labs.snyk.io/resources/prompt-injection-mcp/) | Command injection in aws-mcp-server |
| [CVE-2025-6514](https://jfrog.com/blog/2025-6514-critical-mcp-remote-rce-vulnerability/) | RCE in mcp-remote via URL crafting |

### Security Research & Best Practices

| Source | Summary |
|--------|---------|
| [Trail of Bits — Prompt Injection to RCE](https://blog.trailofbits.com/2025/10/22/prompt-injection-to-rce-in-ai-agents/) | Demonstrated approval bypass in AI agents; one-shot prompt injection to RCE |
| [Kaspersky GERT — Supply Chain Attack](https://securelist.com/model-context-protocol-for-ai-integration-abused-in-supply-chain-attacks/) | PoC malicious MCP server stealing credentials (.env, SSH keys, AWS creds) |
| [Trend Micro — Exposed MCP Servers](https://www.trendmicro.com/vinfo/us/security/news/) | 492 MCP servers without authentication discovered; 74% on cloud providers |
| [WorkOS — MCP Security Guide](https://workos.com/blog/mcp-security-risks-best-practices) | Scope creep, privilege escalation, secrets management patterns |
| [Docker Blog — MCP Security](https://www.docker.com/blog/mcp-security-explained/) | Container security patterns for MCP deployments |

---

## Repeated Failure Patterns by Domain

### FS — Filesystem & Local Resources

- **Naive prefix matching**: Path validation using `startswith()` without canonical path resolution
- **Symlink bypass**: Validating requested path but not resolving symlink targets before access
- **Insecure glob patterns**: Unrestricted glob expansion allowing access outside intended directories
- **Missing size limits**: No bounds on file reads leading to memory exhaustion

### EXEC — Command Execution

- **Shell string concatenation**: Using `os.system()` or `shell=True` with user-controlled input
- **Missing argument separators**: Not using `--` to separate options from user-supplied arguments
- **Over-broad command allowlists**: Including commands that can invoke shells (git, find, etc.)

### NET — Network Access

- **SSRF via unrestricted fetch**: Tools fetching arbitrary URLs including internal services and cloud metadata
- **Blacklist-based validation**: Using deny lists instead of explicit allowlists for URL validation
- **Missing egress controls**: No network policy restricting outbound connections
- **DNS rebinding**: Validating hostname before fetch but not after resolution

### AUTHZ — Authorization

- **Missing OAuth delegation**: Using shared server credentials instead of per-user tokens
- **Over-permissioned tools**: Tools with blanket read/write access instead of scoped permissions
- **No RBAC implementation**: All users/clients get the same access level
- **Hardcoded credentials**: API keys and database passwords in source code or config files

### INPUT — Input Validation

- **Schema-less tools**: Accepting arbitrary JSON without schema validation
- **Unbounded arrays/strings**: No limits on collection sizes or string lengths
- **Missing timeouts**: Long-running operations without cancellation mechanisms
- **ReDoS patterns**: Regular expressions with catastrophic backtracking

### LOG — Logging & Audit

- **Secrets in logs**: Logging API keys, tokens, or passwords in plaintext
- **Missing attribution**: Unable to trace actions to specific users
- **Insufficient invocation logging**: Not recording tool calls, arguments, or results

### SUPPLY — Supply Chain

- **No package signing**: Distributing MCP servers without cryptographic signatures
- **Typosquatting**: Malicious packages with names similar to legitimate ones
- **Rug pulls**: Legitimate-appearing packages that change behavior after trust is established
- **Unpinned dependencies**: Using floating versions without hash verification

### DEPLOY — Deployment Hardening

- **Running as root**: Container processes with UID 0
- **Docker socket mounting**: Exposing host Docker control to container
- **Missing seccomp/AppArmor**: No syscall filtering or MAC policies
- **Writable root filesystem**: Allowing persistent modifications
- **No resource limits**: Unbounded CPU/memory consumption

---

## Claims to Verify

The following claims from research sources require independent verification before citing as fact:

### UNVERIFIED — Statistics

| Claim | Source | Status |
|-------|--------|--------|
| "43% of MCP servers tested allow command injection" | Practical DevOps, MCPManager | UNVERIFIED — Methodology and sample size not disclosed |
| "492 exposed MCP servers without authentication" | Trend Micro | UNVERIFIED — Discovery methodology unclear |
| "7.2% of 1,899 servers contain vulnerabilities" | arXiv:2506.13538 | REQUIRES REVIEW — Need to verify paper methodology |
| "5.5% exhibit tool poisoning" | arXiv:2506.13538 | REQUIRES REVIEW — Definition of "tool poisoning" unclear |
| "14.4% contain bug patterns" | arXiv:2506.13538 | REQUIRES REVIEW — "Bug pattern" definition needed |
| "74% of exposed servers hosted on cloud providers" | Trend Micro | UNVERIFIED — Based on limited scan data |
| "90% have read access to data sources" | Trend Micro | UNVERIFIED — Sample selection bias possible |

### UNVERIFIED — CVE Details

| Claim | Source | Status |
|-------|--------|--------|
| CVE-2025-53109 CVSS 8.8 | Cymulate | VERIFIED — NVD entry exists |
| CVE-2025-53110 CVSS 7.3 | Cymulate | VERIFIED — NVD entry exists |
| CVE-2025-61492 | SentinelOne | VERIFIED — Vulnerability database entry |
| CVE-2025-6514 | JFrog | REQUIRES REVIEW — Need NVD confirmation |

### UNVERIFIED — Future Claims

| Claim | Source | Status |
|-------|--------|--------|
| "Official MCP registry coming Q1-Q2 2025" | Community speculation | UNVERIFIED — No official Anthropic announcement |
| "Anthropic will require authentication" | Community discussion | UNVERIFIED — MCP spec makes auth optional |

---

## Notes for Control Development

1. **Do not cite unverified statistics** in control rationale. Use qualitative statements instead (e.g., "commonly observed" rather than "43% of servers").

2. **CVEs with NVD entries** may be referenced as concrete examples of vulnerability patterns.

3. **Academic papers** should be cited for taxonomy and attack methodology but not for prevalence statistics without verification.

4. **OWASP MCP Top 10** is a living document and may change; reference the specific version when citing.

5. **Transport-agnostic controls** are preferred; transport-specific guidance belongs in profiles or appendices.
