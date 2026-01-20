---
title: "Control Level Mapping"
weight: 4
---

# MCP Security Standard — Control Level Mapping

**Version:** 1.0
**Status:** Official
**Effective Date:** 2026-01-20
**Last Updated:** 2026-01-20

---

## Overview

This document provides a comprehensive mapping of all 24 MCP Security Standard (MSSS) controls to their assigned compliance levels. It serves as a quick reference for implementers, auditors, and security teams.

For detailed level definitions and selection criteria, see [Compliance Levels](./compliance-levels.md).

---

## Summary Statistics

| Level | Control Count | Percentage | Cumulative % |
|-------|---------------|------------|--------------|
| **Level 1** | 7 | 29% | 29% |
| **Level 2** | 14 | 58% | 88% |
| **Level 3** | 3 | 12% | 100% |
| **Total** | 24 | 100% | 100% |

---

## Level 1 Controls (7 total)

**Target**: Personal / Development environments
**Philosophy**: Essential baseline protecting against the most common and severe vulnerabilities

| Control ID | Title | CWE Mapping | Rationale for L1 |
|------------|-------|-------------|------------------|
| **MCP-FS-01** | Path Allowlisting and Canonical Resolution | CWE-22, CWE-23 | Directory traversal is #3 CWE Top 25. Simple to implement, critical impact. |
| **MCP-FS-02** | Symlink Resolution Validation | CWE-59, CWE-61 | Common bypass for path validation. Low implementation cost. |
| **MCP-EXEC-01** | Prohibition of Shell Execution | CWE-78 | Command injection is #1 CWE Top 25. Single line fix (use arrays, not strings). |
| **MCP-NET-01** | URL Validation and SSRF Mitigation | CWE-918 | SSRF enables cloud metadata attacks, port scanning, internal service access. |
| **MCP-INPUT-01** | JSON Schema Validation | CWE-20, CWE-915 | Schema poisoning unique to MCP. Prevents injection and DoS. Library support excellent. |
| **MCP-LOG-02** | Secret Redaction in Logs | CWE-532, CWE-312 | Credential leakage to logs is easily automated. Critical for personal API keys. |
| **MCP-SUPPLY-02** | Trusted Package Sources | CWE-494, CWE-830 | Supply chain attacks increasing (postmark-mcp, playwright-mcp typosquatting). |

### Level 1 Coverage

**Attack Vectors Mitigated**:
- Path traversal and symlink attacks
- Command injection via shell metacharacters
- Server-Side Request Forgery (SSRF)
- Schema and tool poisoning
- Credential leakage
- Malicious package installation

**CWE Top 25 Coverage**: 5 of Top 25 (CWE-22, CWE-78, CWE-20, CWE-918, CWE-532)

**OWASP Top 10 Coverage**: A03 (Injection), A08 (Software Integrity Failures)

---

## Level 2 Controls (+14 additional, 21 total)

**Target**: Team / Enterprise environments with sensitive business data
**Philosophy**: Comprehensive protection with authorization, audit, and defense-in-depth

### Authorization Domain (4 controls)

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-AUTHZ-01** | OAuth Token Delegation | CWE-287, CWE-306 | Per-user auth essential for multi-tenant. HTTP/WebSocket transport requirement. |
| **MCP-AUTHZ-02** | Per-Tool Scope Definition | CWE-269, CWE-284 | Granular permissions prevent lateral movement. Requires architectural planning. |
| **MCP-AUTHZ-03** | Least Privilege Tool Design | CWE-250, CWE-269 | Tool design principle requiring upfront planning. Critical for enterprise. |
| **MCP-AUTHZ-04** | Resource-Based Access Control | CWE-639, CWE-863 | RBAC with resource isolation. Essential for customer data separation. |

### Logging Domain (1 control)

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-LOG-01** | Comprehensive Audit Logging | CWE-778, CWE-779 | Forensic capability required for incident response. Compliance requirement (SOC 2, ISO 27001). |

### Network Domain (1 control)

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-NET-03** | TLS Enforcement | CWE-319, CWE-326 | Transport encryption essential for remote deployments. Industry standard. |

### Input Validation Domain (2 controls)

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-INPUT-02** | Input Bounds Enforcement | CWE-400, CWE-770 | DoS prevention via resource exhaustion. Requires careful tuning for business needs. |
| **MCP-INPUT-03** | Timeout Enforcement | CWE-400, CWE-770 | Prevents long-running operations consuming resources. Operational overhead. |

### Supply Chain Domain (1 control)

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-SUPPLY-01** | Package Integrity Verification | CWE-494, CWE-506 | Cryptographic hash verification. CI/CD integration required. |

### Execution Domain (2 controls)

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-EXEC-02** | Command Allowlisting | CWE-78, CWE-88 | Reduces attack surface beyond shell execution ban. Requires explicit allowlist maintenance. |
| **MCP-EXEC-03** | Argument Separator Usage | CWE-88, CWE-88 | Prevents argument injection (`--`). Implementation overhead for all exec calls. |

### Filesystem Domain (1 control)

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-FS-03** | Filesystem Sandboxing | CWE-22, CWE-73 | OS-level defense-in-depth. Container/chroot requirement adds deployment complexity. |

### Deployment Domain (2 controls)

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-DEPLOY-01** | Container Hardening | CWE-250, CWE-269 | Non-root user, dropped capabilities. Standard container best practice. |
| **MCP-DEPLOY-03** | Resource Limits and Rate Limiting | CWE-400, CWE-770 | Prevents resource exhaustion. Essential for multi-tenant and public-facing. |

### Level 2 Coverage

**Additional Attack Vectors Mitigated**:
- Privilege escalation through authorization bypass
- Lateral movement via over-permissioned tools
- Resource exhaustion and DoS attacks
- Supply chain integrity violations
- Man-in-the-middle attacks (TLS)
- Container escape and privilege escalation

**CWE Top 25 Coverage**: +10 additional (CWE-287, CWE-269, CWE-400, CWE-319, CWE-863, CWE-778, CWE-506)

**OWASP Top 10 Coverage**: +A01 (Broken Access Control), A02 (Cryptographic Failures), A04 (Insecure Design), A05 (Security Misconfiguration), A09 (Security Logging Failures)

---

## Level 3 Controls (+3 additional, 24 total)

**Target**: Critical infrastructure, healthcare, financial services, public-facing gateways
**Philosophy**: Maximum assurance with kernel-level isolation and network hardening

| Control ID | Title | CWE Mapping | Rationale for L3 |
|------------|-------|-------------|------------------|
| **MCP-NET-02** | Egress Traffic Filtering | CWE-829, CWE-923 | Data exfiltration prevention. Requires extensive allowlist maintenance and testing. |
| **MCP-DEPLOY-02** | System Call Filtering (seccomp/AppArmor) | CWE-265, CWE-250 | Kernel attack surface reduction. Platform-specific, high complexity. |
| **MCP-DEPLOY-04** | Runtime Integrity Monitoring | TBD | Anomaly detection and behavioral analysis. Significant operational overhead. (Future) |

### Level 3 Coverage

**Additional Attack Vectors Mitigated**:
- Data exfiltration via unrestricted egress
- Container escape via syscall exploitation
- Kernel vulnerabilities and privilege escalation
- Zero-day exploitation (via reduced attack surface)
- Advanced persistent threats (APT) techniques

**Regulatory Alignment**: HIPAA, PCI DSS, FedRAMP High, ISO 27001, SOC 2 Type II

---

## Control Distribution by Domain

| Domain | L1 | L2 | L3 | Total | L1 % | L2 % | L3 % |
|--------|----|----|-------|-------|------|------|------|
| **FS** | 2 | +1 | 0 | 3 | 67% | 100% | 100% |
| **EXEC** | 1 | +2 | 0 | 3 | 33% | 100% | 100% |
| **NET** | 1 | +1 | +1 | 3 | 33% | 67% | 100% |
| **AUTHZ** | 0 | +4 | 0 | 4 | 0% | 100% | 100% |
| **INPUT** | 1 | +2 | 0 | 3 | 33% | 100% | 100% |
| **LOG** | 1 | +1 | 0 | 2 | 50% | 100% | 100% |
| **SUPPLY** | 1 | +1 | 0 | 2 | 50% | 100% | 100% |
| **DEPLOY** | 0 | +2 | +2 | 4 | 0% | 50% | 100% |

### Key Observations

1. **Authorization is L2+ only**: All 4 AUTHZ controls require Level 2 or higher, reflecting that personal/dev environments often don't need multi-user access control.

2. **Deployment hardening scales with level**: DEPLOY domain is the only one with controls at L2 and L3 but not L1, since container security is primarily relevant for server deployments.

3. **Filesystem has strongest L1 coverage**: 67% of FS controls are Level 1, reflecting the criticality of path traversal prevention.

4. **Network security spans all levels**: Each level adds network protections (L1: SSRF, L2: TLS, L3: Egress filtering).

---

## Implementation Priority

When implementing controls, the following order is recommended for maximum risk reduction per effort invested:

### Phase 1: Critical Baseline (Week 1)
1. **MCP-EXEC-01**: No shell execution (prevents RCE)
2. **MCP-FS-01**: Path allowlisting (prevents directory traversal)
3. **MCP-NET-01**: URL validation (prevents SSRF)
4. **MCP-INPUT-01**: Schema validation (prevents injection)

**Risk Reduction**: Prevents ~70% of reported MCP server vulnerabilities

### Phase 2: Defense-in-Depth (Week 2-3)
5. **MCP-FS-02**: Symlink resolution (closes path validation bypass)
6. **MCP-LOG-02**: Secret redaction (prevents credential leaks)
7. **MCP-SUPPLY-02**: Trusted package sources (prevents supply chain attacks)

**Risk Reduction**: Completes Level 1, addresses supply chain and credential exposure

### Phase 3: Multi-User Deployment (Month 2-3, if applicable)
8. **MCP-AUTHZ-01**: OAuth token delegation
9. **MCP-AUTHZ-02**: Per-tool scopes
10. **MCP-LOG-01**: Audit logging
11. **MCP-NET-03**: TLS enforcement

**Risk Reduction**: Enables secure team/enterprise deployment

### Phase 4: Hardening (Month 4-6)
12-21. Remaining Level 2 controls (execution, input, deployment)

### Phase 5: Maximum Assurance (Year 2+)
22-24. Level 3 controls for critical deployments

---

## Exceptions and Flexibility

### Common Exceptions by Level

#### Level 1 Exceptions

- **MCP-EXEC-01 (No Shell)**: NOT ACCEPTABLE. This is a non-negotiable baseline control.
- **MCP-FS-01 (Path Allowlisting)**: NOT ACCEPTABLE for tools accessing filesystems.
- **MCP-SUPPLY-02 (Trusted Sources)**: Acceptable for experimental/research tools in isolated environments.

#### Level 2 Exceptions

- **MCP-AUTHZ-01 (OAuth)**: N/A if using stdio transport with OS-level user isolation.
- **MCP-LOG-01 (Audit Logging)**: May use minimal logging in development environments.
- **MCP-DEPLOY-01 (Container Hardening)**: N/A for bare-metal deployments.

#### Level 3 Exceptions

- **MCP-NET-02 (Egress Filtering)**: May use network-level firewall rules instead of application-level filtering.
- **MCP-DEPLOY-02 (Seccomp)**: N/A for Windows (use Windows Sandbox) or macOS (use sandbox-exec).

### Mixed-Level Deployments

Servers may implement different levels for different tools:

**Example**:
- Tool A (file write) → Level 3
- Tool B (database read) → Level 2
- Tool C (public API call) → Level 1

**Requirement**: Server infrastructure must meet the highest declared tool level.

---

## Verification Checklist

### Level 1 Self-Assessment

- [ ] MCP-FS-01: All file operations use canonical paths + prefix matching
- [ ] MCP-FS-02: Symlinks resolved before path validation
- [ ] MCP-EXEC-01: Zero instances of `os.system()`, `shell=True`, or string-based exec
- [ ] MCP-NET-01: URL validation blocks RFC 1918 private ranges + localhost
- [ ] MCP-INPUT-01: All tool args validated against JSON Schema
- [ ] MCP-LOG-02: Automated secret redaction for API keys, passwords, tokens
- [ ] MCP-SUPPLY-02: Packages installed from official registries only, checksums verified

### Level 2 Internal Audit

- [ ] All Level 1 controls verified
- [ ] MCP-AUTHZ-01: Per-user OAuth/JWT authentication for HTTP/WS transports
- [ ] MCP-AUTHZ-02: Tool-level scope validation before execution
- [ ] MCP-AUTHZ-03: Tools follow least privilege (read/write separated)
- [ ] MCP-AUTHZ-04: Resource-based access control with deny rules
- [ ] MCP-LOG-01: Structured audit logs with user attribution
- [ ] MCP-NET-03: TLS 1.2+ enforced, certificates validated
- [ ] MCP-INPUT-02: Max string length, array size, nesting depth enforced
- [ ] MCP-INPUT-03: Timeouts configured (30s tools, 60s requests)
- [ ] MCP-SUPPLY-01: Lockfiles with hashes, signature verification where available
- [ ] MCP-EXEC-02: Command allowlist defined and enforced
- [ ] MCP-EXEC-03: Argument separator (`--`) used for all commands
- [ ] MCP-FS-03: Filesystem sandbox configured (chroot, container volumes)
- [ ] MCP-DEPLOY-01: Container runs as non-root (UID != 0), capabilities dropped
- [ ] MCP-DEPLOY-03: Memory caps, CPU quotas, rate limits enforced

### Level 3 Third-Party Assessment

- [ ] All Level 1 and Level 2 controls verified
- [ ] MCP-NET-02: Egress allowlist defined, DNS control, rate limiting, blocked attempts logged
- [ ] MCP-DEPLOY-02: Seccomp/AppArmor profile applied, syscall allowlist documented
- [ ] MCP-DEPLOY-04: Runtime monitoring deployed (Falco, Sysdig, etc.) [Future]
- [ ] Penetration test report reviewed, all high/critical findings remediated
- [ ] Incident response plan documented and tested
- [ ] Compliance mapping completed (HIPAA/PCI DSS/SOC 2)

---

## Regulatory and Framework Mappings

### HIPAA (Healthcare)

**Minimum Level**: Level 3 (for servers accessing PHI)

**Key Controls**:
- MCP-AUTHZ-01/04: Unique user identification (§164.312(a)(2)(i))
- MCP-LOG-01: Audit controls (§164.312(b))
- MCP-NET-03: Transmission security (§164.312(e)(1))
- MCP-NET-02: Access controls preventing unauthorized egress (§164.312(a)(1))

### PCI DSS (Payment Card Industry)

**Minimum Level**: Level 3 (for servers accessing cardholder data)

**Key Controls**:
- MCP-AUTHZ-01/02: Restrict access by business need-to-know (Req 7)
- MCP-NET-03: Encrypt transmission of cardholder data (Req 4)
- MCP-LOG-01: Track and monitor all access to network resources (Req 10)
- MCP-DEPLOY-02: Security controls on production systems (Req 6)

### SOC 2 Type II

**Minimum Level**: Level 2 (for SaaS providers)

**Key Controls**:
- MCP-AUTHZ-01/02/04: Logical access controls (CC6.1, CC6.2)
- MCP-LOG-01: System operations logging (CC7.2)
- MCP-INPUT-02/03: System boundaries and resource limits (CC6.6)
- MCP-DEPLOY-01/03: Change management and capacity (CC8.1)

### ISO 27001

**Minimum Level**: Level 2 (for certified deployments)

**Annex A Control Mappings**:
- MCP-AUTHZ-* → A.9 (Access Control)
- MCP-LOG-* → A.12.4 (Logging and Monitoring)
- MCP-NET-03 → A.13.2 (Information Transfer)
- MCP-DEPLOY-* → A.14.2 (Security in Development and Support)

### FedRAMP

**Level Mapping**:
- FedRAMP Low → MSSS Level 2 minimum
- FedRAMP Moderate → MSSS Level 2 or Level 3
- FedRAMP High → MSSS Level 3 required

**NIST SP 800-53 Mappings**: See individual control documentation

---

## Frequently Asked Questions

### Q: Can I skip Level 1 and go directly to Level 2?

**A:** Yes. Level 2 includes all Level 1 controls (cumulative model), so implementing Level 2 automatically satisfies Level 1. However, we recommend implementing controls in order for incremental validation.

### Q: My server is just for personal use. Do I really need Level 1?

**A:** Yes. Even personal servers face automated attacks (botnets, CVE scanners). Level 1 controls take 1-4 hours to implement and prevent 70% of reported vulnerabilities. The cost/benefit ratio is highly favorable.

### Q: Can I use Level 1 for production?

**A:** Generally no. Level 1 is designed for personal/development contexts. Production servers typically handle business-confidential data or have external users, requiring Level 2 minimum. Exceptions: Internal read-only tools with public data may qualify for Level 1.

### Q: How often should I recertify?

**A:**
- **Level 1**: Annual self-assessment or when significant changes occur
- **Level 2**: Annual internal audit + continuous monitoring recommended
- **Level 3**: Annual penetration test + quarterly vulnerability scans

### Q: What if my deployment context changes?

**A:** Re-evaluate your level selection immediately. If your personal project becomes a team tool, or your internal server becomes customer-facing, upgrade your compliance level to match the new risk profile.

### Q: Are there automated tools for compliance verification?

**A:** Partial automation is possible:
- Static analysis can verify MCP-EXEC-01, MCP-FS-01, MCP-INPUT-01
- Container scanning can verify MCP-DEPLOY-01/02
- SAST/DAST can detect many violations

However, architectural controls (AUTHZ, LOG) require manual review. We provide `msss-scanner` CLI tool for automated checks.

---

## Conclusion

This mapping provides a clear, objective assignment of all 24 MSSS controls to three compliance levels. The distribution follows industry best practices (OWASP ASVS pattern) and ensures each level represents a complete security posture for its intended deployment context.

For implementation guidance, see [Compliance Levels](./compliance-levels.md).
For individual control requirements, see `/controls/` directory.

---

**Document Status**: Official
**Maintained By**: MCP Security Standard Working Group
**Questions**: https://github.com/mcp-security-standard/msss/issues

## Revision History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | 2026-01-20 | Initial release | Dani |
