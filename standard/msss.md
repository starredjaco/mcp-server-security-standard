# MCP Server Security Standard (MSSS)

**Version:** 0.1.0
**Status:** Draft
**Effective Date:** 2025-01-15
**Authoritative Language:** English

> **Note:** The English version of this standard is authoritative. Translations are provided for convenience but do not supersede the English text in case of discrepancy.

---

## 1. Purpose and Scope

### 1.1 Purpose

The MCP Server Security Standard (MSSS) defines security requirements for servers implementing the Model Context Protocol (MCP). It provides a verifiable framework for assessing the security posture of MCP servers across multiple deployment contexts.

MSSS enables:

- **Developers** to build secure MCP servers from the start
- **Operators** to evaluate and harden deployed servers
- **Assessors** to verify compliance through reproducible evidence
- **Organizations** to establish security baselines for MCP adoption

### 1.2 Scope

This standard applies to software components that:

1. Implement the Model Context Protocol specification (version 1.0 or later)
2. Expose tools, resources, or prompts to MCP clients
3. Operate in any supported transport mode

**In Scope:**

- MCP server application code and configuration
- Tool implementations and their security properties
- Resource access controls and data handling
- Transport layer security requirements
- Deployment and runtime environment hardening
- Logging, monitoring, and auditability

**Out of Scope:**

- MCP client implementations (separate standard)
- LLM model security or prompt injection defenses within models
- Network infrastructure beyond the server boundary
- Business logic correctness (non-security functional requirements)

### 1.3 Transport Modes

MSSS recognizes the following transport modes:

| Transport | Description | Typical Use Case |
|-----------|-------------|------------------|
| **stdio** | Standard input/output communication with spawned process | Local development, IDE integrations |
| **HTTP/HTTPS** | JSON-RPC over HTTP with optional SSE streaming | Remote servers, multi-client scenarios |
| **WebSocket** | Bidirectional streaming over WebSocket connections | Real-time applications |
| **Custom/Proxy** | API gateways, reverse proxies, or custom transports | Enterprise deployments |

Controls apply across transports unless explicitly scoped. Transport-specific guidance appears in control scope sections.

---

## 2. Definitions

The following terms have specific meanings in this standard:

**MCP Server**
A software component implementing the Model Context Protocol that exposes capabilities to MCP clients.

**Tool**
An executable function exposed by an MCP server that accepts JSON-Schema-validated arguments and returns results. Tools perform actions on behalf of the LLM client.

**Resource**
A data source exposed by an MCP server that clients can read or write. Resources may be static (files, configurations) or dynamic (database queries, API responses).

**Prompt**
A predefined instruction template exposed by an MCP server to guide LLM behavior.

**Transport**
The communication mechanism between MCP client and server (stdio, HTTP, WebSocket, or custom).

**Control**
A specific, verifiable security requirement defined in this standard.

**Evidence**
Documentation, artifacts, or observations that demonstrate compliance with a control.

**Assessor**
A person or automated system evaluating an MCP server against this standard.

**Operator**
The entity responsible for deploying and maintaining an MCP server.

---

## 3. Normative Language

This standard uses the following terms as defined in RFC 2119:

| Term | Meaning |
|------|---------|
| **MUST** | Absolute requirement. Non-compliance results in FAIL. |
| **MUST NOT** | Absolute prohibition. Violation results in FAIL. |
| **SHOULD** | Recommended. Deviation requires documented justification. |
| **SHOULD NOT** | Discouraged. Usage requires documented justification. |
| **MAY** | Optional. No compliance impact if omitted. |

---

## 4. Verification Levels

MSSS defines three verification levels representing increasing security rigor:

### 4.1 Level 1 (L1) — Baseline

**Target:** MCP servers in development environments or low-risk contexts.

**Characteristics:**

- Protects against common, easily exploitable vulnerabilities
- Minimal operational overhead
- Achievable with standard development practices

**Requirements:** All L1 controls MUST pass.

### 4.2 Level 2 (L2) — Standard

**Target:** MCP servers handling sensitive data or exposed to untrusted clients.

**Characteristics:**

- Comprehensive protection against known attack patterns
- Defense-in-depth measures
- Suitable for production deployments

**Requirements:** All L1 and L2 controls MUST pass.

### 4.3 Level 3 (L3) — Advanced

**Target:** MCP servers in high-risk environments or processing regulated data.

**Characteristics:**

- Maximum security assurance
- Formal verification where applicable
- Continuous monitoring and response capabilities

**Requirements:** All L1, L2, and L3 controls MUST pass.

### 4.4 Level Selection

Organizations SHOULD select levels based on:

- Data sensitivity classification
- Deployment environment (see [profiles.md](profiles.md))
- Regulatory requirements
- Threat model considerations (see [threat-model.md](threat-model.md))

---

## 5. Control Domains

Controls are organized into eight domains reflecting the primary attack surfaces:

| Domain | Code | Focus Area |
|--------|------|------------|
| Filesystem | FS | Path validation, sandboxing, symlink handling |
| Execution | EXEC | Command injection, process spawning, shell usage |
| Network | NET | SSRF, egress control, transport security |
| Authorization | AUTHZ | Access control, scoping, privilege management |
| Input Validation | INPUT | Schema enforcement, bounds checking, timeouts |
| Logging | LOG | Audit trails, secret redaction, monitoring |
| Supply Chain | SUPPLY | Dependency integrity, package verification |
| Deployment | DEPLOY | Container hardening, runtime isolation |

---

## 6. Evaluation Status

Each control evaluation results in one of four statuses:

### 6.1 PASS

The control requirements are fully satisfied. Required evidence is present and valid.

### 6.2 FAIL

The control requirements are not satisfied. Evidence is missing, invalid, or contradicts requirements.

### 6.3 NOT_APPLICABLE

The control does not apply to this server. Valid only when:

- The server does not implement the relevant capability (e.g., no filesystem tools)
- A compensating control is documented and approved
- The scope explicitly excludes this control for the deployment profile

NOT_APPLICABLE MUST be justified in the assessment report.

### 6.4 MANUAL_REVIEW

Automated verification is insufficient. Human assessor review required.

Applies when:

- Evidence requires contextual interpretation
- Complex compensating controls need evaluation
- Novel implementation patterns require expert judgment

MANUAL_REVIEW MUST be resolved to PASS, FAIL, or NOT_APPLICABLE before level certification.

---

## 7. Evidence Requirements

### 7.1 Minimal Acceptable Evidence

For a control to receive PASS status, the assessor MUST obtain evidence demonstrating compliance. Evidence types include:

| Type | Description | Example |
|------|-------------|---------|
| **Code** | Source code patterns matching requirements | Path validation using canonical paths |
| **Configuration** | Settings files with required values | Seccomp profile enabled |
| **Artifact** | Build outputs demonstrating properties | Container image with non-root user |
| **Observation** | Runtime behavior matching expectations | Request blocked when path escapes sandbox |
| **Documentation** | Written policies or procedures | Incident response plan |

### 7.2 Evidence Quality

Evidence MUST be:

- **Specific**: Directly addresses the control requirement
- **Current**: Reflects the assessed version
- **Verifiable**: Can be independently confirmed
- **Complete**: Covers all aspects of the requirement

### 7.3 Evidence Retention

Operators SHOULD retain evidence for a minimum of:

- 12 months for L1 assessments
- 24 months for L2 assessments
- 36 months for L3 assessments

---

## 8. Level Certification

### 8.1 Achieving a Level

An MCP server achieves a verification level when:

1. All MUST requirements for that level and lower levels receive PASS or justified NOT_APPLICABLE
2. All SHOULD requirements for that level are addressed (PASS, justified deviation, or NOT_APPLICABLE)
3. All MANUAL_REVIEW statuses are resolved
4. The assessment report documents the scope, evidence, and any exceptions

### 8.2 Certification Period

Certifications are valid until:

- The next MSSS version with breaking changes is released
- Significant changes are made to the assessed server
- The defined certification period expires (recommended: 12 months)

### 8.3 Partial Compliance

Servers that do not achieve a level MAY still claim partial compliance by documenting:

- Total controls at each status
- Specific controls passed per domain
- Remediation timeline for FAIL controls

---

## 9. Versioning

This standard follows semantic versioning as defined in [governance/versioning.md](../governance/versioning.md).

- **Major versions** indicate breaking changes that may invalidate prior certifications
- **Minor versions** add controls or clarify requirements without breaking compatibility
- **Patch versions** fix errors or improve wording without changing requirements

---

## 10. References

### Normative References

- Model Context Protocol Specification, Version 1.0+
- RFC 2119: Key words for use in RFCs to Indicate Requirement Levels

### Informative References

- OWASP MCP Top 10 (2025)
- OWASP Application Security Verification Standard (ASVS) v4.0
- CWE/SANS Top 25
- NIST Cybersecurity Framework

---

## Document History

| Version | Date | Changes |
|---------|------|---------|
| 0.1.0 | 2025-01-15 | Initial draft |
