---
title: "Control Level Mapping"
weight: 4
---

# MCP Security Standard — Control Level Mapping

**Version:** 2.0
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
| **Level 1** | 6 | 25% | 25% |
| **Level 2** | 6 | 25% | 50% |
| **Level 3** | 6 | 25% | 75% |
| **Level 4** | 6 | 25% | 100% |
| **Total** | 24 | 100% | 100% |

### Distribution Benefits

The 4-level system provides balanced progression:

| Transition | Controls Added | Jump Size |
|------------|---------------|-----------|
| — → L1 | 6 | Baseline |
| L1 → L2 | +6 | **100% increase** |
| L2 → L3 | +6 | **50% increase** |
| L3 → L4 | +6 | **33% increase** |

This compares favorably to the previous 3-level system which had 200% and 14% jumps.

---

## Level 1 Controls (6 total)

**Target**: Personal / Hobby projects
**Philosophy**: Essential baseline protecting against the most common and severe vulnerabilities
**Timeline**: 1-2 hours implementation

| Control ID | Title | CWE Mapping | Rationale for L1 |
|------------|-------|-------------|------------------|
| **MCP-EXEC-01** | Prohibition of Shell Execution | CWE-78 | Command injection is #1 CWE Top 25. Single line fix (use arrays, not strings). |
| **MCP-FS-01** | Path Allowlisting and Canonical Resolution | CWE-22, CWE-23 | Directory traversal is #3 CWE Top 25. Simple to implement, critical impact. |
| **MCP-FS-02** | Symlink Resolution Validation | CWE-59, CWE-61 | Common bypass for path validation. Low implementation cost. |
| **MCP-NET-01** | URL Validation and SSRF Mitigation | CWE-918 | SSRF enables cloud metadata attacks, port scanning, internal service access. |
| **MCP-INPUT-01** | JSON Schema Validation | CWE-20, CWE-915 | Schema poisoning unique to MCP. Prevents injection and DoS. Library support excellent. |
| **MCP-LOG-02** | Secret Redaction in Logs | CWE-532, CWE-312 | Credential leakage to logs is easily automated. Critical for personal API keys. |

### Level 1 Coverage

**Attack Vectors Mitigated**:
- Command injection via shell metacharacters
- Path traversal and symlink attacks
- Server-Side Request Forgery (SSRF)
- Schema and tool poisoning
- Credential leakage in logs

**CWE Top 25 Coverage**: 5 of Top 25 (CWE-78, CWE-22, CWE-20, CWE-918, CWE-532)

**OWASP Top 10 Coverage**: A03 (Injection)

---

## Level 2 Controls (+6 additional, 12 total)

**Target**: Development teams / Internal tools
**Philosophy**: Input hardening, transport security, supply chain basics, execution safety
**Timeline**: 4-8 hours implementation

| Control ID | Title | CWE Mapping | Rationale for L2 |
|------------|-------|-------------|------------------|
| **MCP-SUPPLY-02** | Trusted Package Sources | CWE-494, CWE-830 | Supply chain attacks increasing. First line of defense. |
| **MCP-INPUT-02** | Input Bounds Enforcement | CWE-400, CWE-770 | DoS prevention via resource exhaustion. Requires careful tuning. |
| **MCP-INPUT-03** | Timeout Enforcement | CWE-400, CWE-770 | Prevents long-running operations consuming resources. |
| **MCP-NET-03** | TLS Enforcement | CWE-319, CWE-326 | Transport encryption essential for remote deployments. Industry standard. |
| **MCP-EXEC-02** | Command Allowlisting | CWE-78, CWE-88 | Reduces attack surface beyond shell execution ban. |
| **MCP-EXEC-03** | Argument Separator Usage | CWE-88 | Prevents argument injection (`--`). Defense-in-depth for execution. |

### Level 2 Coverage

**Additional Attack Vectors Mitigated**:
- Malicious package installation (supply chain)
- Resource exhaustion and DoS attacks
- Man-in-the-middle attacks (TLS)
- Argument injection attacks
- Unauthorized binary execution

**CWE Top 25 Coverage**: +3 additional (CWE-400, CWE-319, CWE-494)

**OWASP Top 10 Coverage**: +A08 (Software Integrity Failures)

---

## Level 3 Controls (+6 additional, 18 total)

**Target**: Enterprise / Customer-facing production
**Philosophy**: Authentication, authorization, audit logging, container security
**Timeline**: 1-2 weeks implementation

| Control ID | Title | CWE Mapping | Rationale for L3 |
|------------|-------|-------------|------------------|
| **MCP-AUTHZ-01** | OAuth Token Delegation | CWE-287, CWE-306 | Per-user auth essential for multi-tenant. HTTP/WebSocket transport requirement. |
| **MCP-AUTHZ-02** | Per-Tool Scope Definition | CWE-269, CWE-284 | Granular permissions prevent lateral movement. Requires architectural planning. |
| **MCP-AUTHZ-03** | Least Privilege Tool Design | CWE-250, CWE-269 | Tool design principle requiring upfront planning. Critical for enterprise. |
| **MCP-AUTHZ-04** | Resource-Based Access Control | CWE-639, CWE-863 | RBAC with resource isolation. Essential for customer data separation. |
| **MCP-LOG-01** | Comprehensive Audit Logging | CWE-778, CWE-779 | Forensic capability required for incident response. Compliance requirement. |
| **MCP-DEPLOY-01** | Container Hardening | CWE-250, CWE-269 | Non-root user, dropped capabilities. Standard container best practice. |

### Level 3 Coverage

**Additional Attack Vectors Mitigated**:
- Authentication bypass
- Privilege escalation through authorization bypass
- Lateral movement via over-permissioned tools
- Missing forensic evidence (audit logs)
- Container escape via excessive privileges

**CWE Top 25 Coverage**: +5 additional (CWE-287, CWE-269, CWE-863, CWE-778, CWE-250)

**OWASP Top 10 Coverage**: +A01 (Broken Access Control), A05 (Security Misconfiguration), A09 (Security Logging Failures)

---

## Level 4 Controls (+6 additional, 24 total)

**Target**: Critical infrastructure, healthcare (HIPAA), financial (PCI DSS), government
**Philosophy**: Maximum isolation, supply chain integrity, network hardening, runtime monitoring
**Timeline**: 4-8 weeks implementation

| Control ID | Title | CWE Mapping | Rationale for L4 |
|------------|-------|-------------|------------------|
| **MCP-FS-03** | Filesystem Sandboxing | CWE-22, CWE-73 | OS-level defense-in-depth. Container/chroot adds deployment complexity. |
| **MCP-SUPPLY-01** | Package Integrity Verification | CWE-494, CWE-506 | Cryptographic hash verification. CI/CD integration required. |
| **MCP-DEPLOY-03** | Resource Limits and Rate Limiting | CWE-400, CWE-770 | Prevents resource exhaustion. Essential for public-facing services. |
| **MCP-NET-02** | Egress Traffic Filtering | CWE-829, CWE-923 | Data exfiltration prevention. Requires extensive allowlist maintenance. |
| **MCP-DEPLOY-02** | System Call Filtering (seccomp/AppArmor) | CWE-265, CWE-250 | Kernel attack surface reduction. Platform-specific, high complexity. |
| **MCP-DEPLOY-04** | Runtime Integrity Monitoring | TBD | Anomaly detection and behavioral analysis. Significant operational overhead. |

### Level 4 Coverage

**Additional Attack Vectors Mitigated**:
- Filesystem escape from application layer
- Supply chain integrity violations (cryptographic)
- Data exfiltration via unrestricted egress
- Container escape via syscall exploitation
- Zero-day exploitation (via reduced attack surface)
- Advanced persistent threats (APT) techniques

**Regulatory Alignment**: HIPAA, PCI DSS, FedRAMP High, ISO 27001, SOC 2 Type II

---

## Complete Control Matrix

| Control ID | Title | Level | Domain |
|------------|-------|:-----:|--------|
| MCP-EXEC-01 | Prohibition of Shell Execution | **L1** | EXEC |
| MCP-FS-01 | Path Allowlisting and Canonical Resolution | **L1** | FS |
| MCP-FS-02 | Symlink Resolution Validation | **L1** | FS |
| MCP-NET-01 | URL Validation and SSRF Mitigation | **L1** | NET |
| MCP-INPUT-01 | JSON Schema Validation | **L1** | INPUT |
| MCP-LOG-02 | Secret Redaction in Logs | **L1** | LOG |
| MCP-SUPPLY-02 | Trusted Package Sources | **L2** | SUPPLY |
| MCP-INPUT-02 | Input Bounds Enforcement | **L2** | INPUT |
| MCP-INPUT-03 | Timeout Enforcement | **L2** | INPUT |
| MCP-NET-03 | TLS Enforcement | **L2** | NET |
| MCP-EXEC-02 | Command Allowlisting | **L2** | EXEC |
| MCP-EXEC-03 | Argument Separator Usage | **L2** | EXEC |
| MCP-AUTHZ-01 | OAuth Token Delegation | **L3** | AUTHZ |
| MCP-AUTHZ-02 | Per-Tool Scope Definition | **L3** | AUTHZ |
| MCP-AUTHZ-03 | Least Privilege Tool Design | **L3** | AUTHZ |
| MCP-AUTHZ-04 | Resource-Based Access Control | **L3** | AUTHZ |
| MCP-LOG-01 | Comprehensive Audit Logging | **L3** | LOG |
| MCP-DEPLOY-01 | Container Hardening | **L3** | DEPLOY |
| MCP-FS-03 | Filesystem Sandboxing | **L4** | FS |
| MCP-SUPPLY-01 | Package Integrity Verification | **L4** | SUPPLY |
| MCP-DEPLOY-03 | Resource Limits and Rate Limiting | **L4** | DEPLOY |
| MCP-NET-02 | Egress Traffic Filtering | **L4** | NET |
| MCP-DEPLOY-02 | System Call Filtering (seccomp/AppArmor) | **L4** | DEPLOY |
| MCP-DEPLOY-04 | Runtime Integrity Monitoring | **L4** | DEPLOY |

---

## Control Distribution by Domain

| Domain | L1 | L2 | L3 | L4 | Total |
|--------|:--:|:--:|:--:|:--:|:-----:|
| **FS (Filesystem)** | 2 | 0 | 0 | 1 | 3 |
| **EXEC (Execution)** | 1 | 2 | 0 | 0 | 3 |
| **NET (Network)** | 1 | 1 | 0 | 1 | 3 |
| **AUTHZ (Authorization)** | 0 | 0 | 4 | 0 | 4 |
| **INPUT (Validation)** | 1 | 2 | 0 | 0 | 3 |
| **LOG (Logging)** | 1 | 0 | 1 | 0 | 2 |
| **SUPPLY (Supply Chain)** | 0 | 1 | 0 | 1 | 2 |
| **DEPLOY (Deployment)** | 0 | 0 | 1 | 3 | 4 |

### Key Observations

1. **Filesystem controls span L1 and L4**: Essential path security at L1; advanced sandboxing at L4.

2. **Execution controls complete at L2**: All 3 EXEC controls are in L1-L2, providing full command injection protection early.

3. **Authorization is exclusively L3**: All 4 AUTHZ controls require Level 3, reflecting that personal/dev environments often don't need multi-user access control.

4. **Deployment hardening scales with level**: DEPLOY domain has 1 control at L3 (container basics) and 3 at L4 (advanced isolation).

5. **Input validation completes at L2**: All INPUT controls are in L1-L2, providing comprehensive input protection for team environments.

6. **Network security spans L1, L2, L4**: URL validation (L1), TLS (L2), egress filtering (L4).

---

## Implementation Priority

When implementing controls, the following order is recommended for maximum risk reduction per effort invested:

### Phase 1: Critical Baseline (Day 1)

**Level 1 - Essential (6 controls)**

1. **MCP-EXEC-01**: No shell execution (prevents RCE)
2. **MCP-FS-01**: Path allowlisting (prevents directory traversal)
3. **MCP-FS-02**: Symlink resolution (closes bypass)
4. **MCP-NET-01**: URL validation (prevents SSRF)
5. **MCP-INPUT-01**: Schema validation (prevents injection)
6. **MCP-LOG-02**: Secret redaction (prevents credential leaks)

**Risk Reduction**: Prevents ~70% of reported MCP server vulnerabilities

### Phase 2: Development Hardening (Week 1-2)

**Level 2 - Development (+6 controls)**

7. **MCP-SUPPLY-02**: Trusted package sources
8. **MCP-INPUT-02**: Input bounds enforcement
9. **MCP-INPUT-03**: Timeout enforcement
10. **MCP-NET-03**: TLS enforcement
11. **MCP-EXEC-02**: Command allowlisting
12. **MCP-EXEC-03**: Argument separator usage

**Risk Reduction**: Adds supply chain protection, DoS prevention, transport security

### Phase 3: Production Deployment (Month 1-2)

**Level 3 - Production (+6 controls)**

13. **MCP-AUTHZ-01**: OAuth token delegation
14. **MCP-AUTHZ-02**: Per-tool scopes
15. **MCP-AUTHZ-03**: Least privilege design
16. **MCP-AUTHZ-04**: Resource-based access control
17. **MCP-LOG-01**: Comprehensive audit logging
18. **MCP-DEPLOY-01**: Container hardening

**Risk Reduction**: Enables secure multi-user deployment with audit trail

### Phase 4: Maximum Assurance (Quarter 1-2)

**Level 4 - Maximum (+6 controls)**

19. **MCP-FS-03**: Filesystem sandboxing
20. **MCP-SUPPLY-01**: Package integrity verification
21. **MCP-DEPLOY-03**: Resource limits and rate limiting
22. **MCP-NET-02**: Egress traffic filtering
23. **MCP-DEPLOY-02**: System call filtering (seccomp)
24. **MCP-DEPLOY-04**: Runtime integrity monitoring

**Risk Reduction**: Maximum hardening for critical/regulated environments

---

## Exceptions and Flexibility

### Common Exceptions by Level

#### Level 1 Exceptions

- **MCP-EXEC-01 (No Shell)**: **NOT ACCEPTABLE**. This is a non-negotiable baseline control.
- **MCP-FS-01 (Path Allowlisting)**: **NOT ACCEPTABLE** for tools accessing filesystems.
- **MCP-LOG-02 (Secret Redaction)**: Acceptable exception if no secrets are ever logged (must prove negative).

#### Level 2 Exceptions

- **MCP-NET-03 (TLS)**: N/A if using stdio transport only with no network access.
- **MCP-EXEC-02/03 (Command controls)**: N/A if no command execution capability.
- **MCP-SUPPLY-02 (Trusted Sources)**: Acceptable for experimental/research tools in isolated environments.

#### Level 3 Exceptions

- **MCP-AUTHZ-01 (OAuth)**: N/A if using stdio transport with OS-level user isolation.
- **MCP-LOG-01 (Audit Logging)**: May use minimal logging in development-only environments.
- **MCP-DEPLOY-01 (Container Hardening)**: N/A for bare-metal deployments with other isolation.

#### Level 4 Exceptions

- **MCP-NET-02 (Egress Filtering)**: May use network-level firewall rules instead of application-level.
- **MCP-DEPLOY-02 (Seccomp)**: N/A for Windows (use Windows Sandbox) or macOS (use sandbox-exec).
- **MCP-DEPLOY-04 (Runtime Monitoring)**: Future control; implementation details TBD.

### Mixed-Level Deployments

Servers may implement different levels for different tools:

**Example**:
- Tool A (file write) → Level 4
- Tool B (database read) → Level 3
- Tool C (public API call) → Level 1

**Requirement**: Server infrastructure must meet the highest declared tool level.

---

## Verification Checklist

### Level 1 Self-Assessment

- [ ] MCP-EXEC-01: Zero instances of `os.system()`, `shell=True`, or string-based exec
- [ ] MCP-FS-01: All file operations use canonical paths + prefix matching
- [ ] MCP-FS-02: Symlinks resolved before path validation
- [ ] MCP-NET-01: URL validation blocks RFC 1918 private ranges + localhost
- [ ] MCP-INPUT-01: All tool args validated against JSON Schema
- [ ] MCP-LOG-02: Automated secret redaction for API keys, passwords, tokens

### Level 2 Self-Assessment + Scanning

- [ ] All Level 1 controls verified
- [ ] MCP-SUPPLY-02: Packages installed from official registries only
- [ ] MCP-INPUT-02: Max string length, array size, nesting depth enforced
- [ ] MCP-INPUT-03: Timeouts configured (30s tools, 60s requests)
- [ ] MCP-NET-03: TLS 1.2+ enforced for all remote connections
- [ ] MCP-EXEC-02: Command allowlist defined and enforced
- [ ] MCP-EXEC-03: Argument separator (`--`) used for all commands

### Level 3 Internal Audit

- [ ] All Level 1 and Level 2 controls verified
- [ ] MCP-AUTHZ-01: Per-user OAuth/JWT authentication for HTTP/WS transports
- [ ] MCP-AUTHZ-02: Tool-level scope validation before execution
- [ ] MCP-AUTHZ-03: Tools follow least privilege (read/write separated)
- [ ] MCP-AUTHZ-04: Resource-based access control with deny rules
- [ ] MCP-LOG-01: Structured audit logs with user attribution
- [ ] MCP-DEPLOY-01: Container runs as non-root (UID != 0), capabilities dropped

### Level 4 Third-Party Assessment

- [ ] All Level 1, 2, and 3 controls verified
- [ ] MCP-FS-03: Filesystem sandbox configured (chroot, container volumes, read-only)
- [ ] MCP-SUPPLY-01: Lockfiles with hashes, signature verification where available
- [ ] MCP-DEPLOY-03: Memory caps, CPU quotas, rate limits enforced
- [ ] MCP-NET-02: Egress allowlist defined, DNS control, blocked attempts logged
- [ ] MCP-DEPLOY-02: Seccomp/AppArmor profile applied, syscall allowlist documented
- [ ] MCP-DEPLOY-04: Runtime monitoring deployed (Falco, Sysdig, etc.)
- [ ] Penetration test report reviewed, all high/critical findings remediated
- [ ] Incident response plan documented and tested
- [ ] Compliance mapping completed (HIPAA/PCI DSS/SOC 2)

---

## Regulatory and Framework Mappings

### HIPAA (Healthcare)

**Minimum Level**: Level 4 (for servers accessing PHI)

**Key Controls**:
- MCP-AUTHZ-01/04: Unique user identification (§164.312(a)(2)(i))
- MCP-LOG-01: Audit controls (§164.312(b))
- MCP-NET-03: Transmission security (§164.312(e)(1))
- MCP-NET-02: Access controls preventing unauthorized egress (§164.312(a)(1))

### PCI DSS (Payment Card Industry)

**Minimum Level**: Level 4 (for servers accessing cardholder data)

**Key Controls**:
- MCP-AUTHZ-01/02: Restrict access by business need-to-know (Req 7)
- MCP-NET-03: Encrypt transmission of cardholder data (Req 4)
- MCP-LOG-01: Track and monitor all access to network resources (Req 10)
- MCP-DEPLOY-02: Security controls on production systems (Req 6)

### SOC 2 Type II

**Minimum Level**: Level 3 (for SaaS providers)

**Key Controls**:
- MCP-AUTHZ-01/02/04: Logical access controls (CC6.1, CC6.2)
- MCP-LOG-01: System operations logging (CC7.2)
- MCP-INPUT-02/03: System boundaries and resource limits (CC6.6)
- MCP-DEPLOY-01: Change management and capacity (CC8.1)

### ISO 27001

**Minimum Level**: Level 3 (for certified deployments)

**Annex A Control Mappings**:
- MCP-AUTHZ-* → A.9 (Access Control)
- MCP-LOG-* → A.12.4 (Logging and Monitoring)
- MCP-NET-03 → A.13.2 (Information Transfer)
- MCP-DEPLOY-* → A.14.2 (Security in Development and Support)

### FedRAMP

**Level Mapping**:
- FedRAMP Low → MSSS Level 3 minimum
- FedRAMP Moderate → MSSS Level 3 or Level 4
- FedRAMP High → MSSS Level 4 required

**NIST SP 800-53 Mappings**: See individual control documentation

---

## Frequently Asked Questions

### Q: Can I skip levels and go directly to Level 3?

**A:** Yes. Level 3 includes all Level 1 and Level 2 controls (cumulative model), so implementing Level 3 automatically satisfies earlier levels. However, we recommend implementing controls in order for incremental validation.

### Q: My server is just for personal use. Do I really need Level 1?

**A:** Yes. Even personal servers face automated attacks (botnets, CVE scanners). Level 1 controls take 1-2 hours to implement and prevent 70% of reported vulnerabilities. The cost/benefit ratio is highly favorable.

### Q: Can I use Level 1 or Level 2 for production?

**A:** Generally no. Level 1 is for personal/hobby use. Level 2 is for development teams. Production servers typically handle customer data or have external users, requiring Level 3 minimum. Exceptions: Internal read-only tools with non-sensitive data may qualify for Level 2.

### Q: How often should I recertify?

**A:**
- **Level 1**: Annual self-assessment or when significant changes occur
- **Level 2**: Annual self-assessment + scans
- **Level 3**: Annual internal audit + continuous monitoring recommended
- **Level 4**: Annual penetration test + quarterly vulnerability scans

### Q: What if my deployment context changes?

**A:** Re-evaluate your level selection immediately. If your personal project becomes a team tool (L1→L2), or your internal server becomes customer-facing (L2→L3), or you start handling regulated data (L3→L4), upgrade your compliance level to match the new risk profile.

### Q: Are there automated tools for compliance verification?

**A:** Partial automation is possible:
- Static analysis can verify MCP-EXEC-01, MCP-FS-01, MCP-INPUT-01
- Container scanning can verify MCP-DEPLOY-01/02
- SAST/DAST can detect many violations

However, architectural controls (AUTHZ, LOG) require manual review. We provide `msss-scanner` CLI tool for automated checks.

### Q: Why did the standard change from 3 levels to 4?

**A:** The original 3-level system had poor granularity:
- L1→L2 jump was 200% (7 to 21 controls)
- L2→L3 jump was only 14% (21 to 24 controls)

The new 4-level system provides balanced progression (100%, 50%, 33% jumps), aligning with NIST CSF and PCI DSS which also use 4 levels.

---

## Conclusion

This mapping provides a clear, objective assignment of all 24 MSSS controls to four compliance levels. The balanced distribution (~6 controls per level) ensures smooth progression and aligns with industry best practices (NIST CSF, PCI DSS).

For implementation guidance, see [Compliance Levels](./compliance-levels.md).
For individual control requirements, see `/controls/` directory.

---

**Document Status**: Official
**Maintained By**: MCP Security Standard Working Group
**Questions**: https://github.com/mcp-security-standard/msss/issues

## Revision History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | 2026-01-20 | Initial release (3 levels) | Dani |
| 2.0 | 2026-01-20 | Expanded to 4 levels for improved granularity | Dani |
