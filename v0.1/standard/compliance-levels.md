---
title: "Compliance Levels"
weight: 3
---

# MCP Security Standard — Compliance Levels

**Version:** 1.0
**Status:** Official
**Effective Date:** 2026-01-20
**Last Updated:** 2026-01-20

---

## Table of Contents

1. [Introduction](#introduction)
2. [Philosophy and Design Rationale](#philosophy-and-design-rationale)
3. [Level Definitions](#level-definitions)
4. [Selection Criteria](#selection-criteria)
5. [Control Distribution](#control-distribution)
6. [Validation and Certification](#validation-and-certification)
7. [Implementation Guidance](#implementation-guidance)
8. [Mixed-Level Deployments](#mixed-level-deployments)
9. [Level Comparison Matrix](#level-comparison-matrix)
10. [References and Research](#references-and-research)

---

## Introduction

### Purpose

The MCP Security Standard (MSSS) defines three compliance levels to accommodate the diverse deployment contexts of Model Context Protocol (MCP) servers — from personal hobby projects to enterprise-critical infrastructure. This document establishes the framework for selecting, implementing, and validating compliance at each level.

### Scope

These compliance levels apply to:
- MCP server implementations (all transports: stdio, HTTP, WebSocket)
- MCP gateways and proxy services
- MCP-enabled applications integrating server functionality
- Third-party MCP server distributions and packages

### Non-Goals

This framework explicitly does NOT:
- Create a maturity progression model requiring advancement through levels
- Suggest that lower levels are "incomplete" or "immature" security
- Base levels solely on organization size or transaction volume
- Require all MCP servers to eventually reach Level 3

---

## Philosophy and Design Rationale

### Core Principles

#### 1. Risk-Based Selection

**Principle**: Organizations select their target compliance level based on deployment context, data sensitivity, and potential impact — not based on organizational maturity or aspiration to "progress" through levels.

**Rationale**: An individual developer's personal MCP tool accessing public data has fundamentally different security requirements than an enterprise MCP gateway processing regulated health records. Forcing both into the same compliance framework wastes resources and provides false assurance.

**Precedent**: This approach mirrors:
- **CIS Controls** Implementation Groups (IG1/IG2/IG3) for organization profiles
- **OWASP ASVS** Verification Levels (L1/L2/L3) for application risk tiers
- **FedRAMP** authorization levels (Low/Moderate/High) for impact categories

#### 2. Cumulative Control Distribution

**Principle**: Each level builds upon the previous level's controls. Level 2 includes all Level 1 controls plus additional protections. Level 3 includes all Level 1 and 2 controls plus advanced hardening.

**Rationale**:
- Ensures consistent baseline security across all deployments
- Simplifies compliance assessment (you either meet a level fully, or you don't)
- Prevents dangerous gaps where critical controls are skipped
- Facilitates upgrades when deployment context changes

**Distribution Pattern**: Based on OWASP ASVS proven model:
- **Level 1**: 25-30% of total controls (essential baseline)
- **Level 2**: +55-60% additional controls (cumulative 80-85%)
- **Level 3**: +15-20% additional controls (cumulative 100%)

This **20-50-30 split** balances practicality for simple deployments with comprehensive coverage for critical systems.

#### 3. Complete Security Postures

**Principle**: Each level represents a complete, defensible security posture for its intended use case. Level 1 is not "partial security" — it is full protection appropriate for personal/development contexts.

**Rationale**:
- Prevents stigmatization of lower levels
- Ensures all deployments have meaningful security
- Aligns expectations: a hobby project doesn't need the same controls as a hospital system
- Encourages adoption rather than non-compliance

**Counter-example**: Avoid CMMI-style maturity levels (Initial → Managed → Defined → Quantitatively Managed → Optimizing) which imply lower levels are inadequate.

#### 4. Objective Selection Criteria

**Principle**: Level selection is driven by clear, measurable criteria — not subjective assessment or organizational aspiration.

**Criteria Dimensions**:
1. **User Scope**: Individual / Team / Organization / Public
2. **Data Sensitivity**: Public / Business-Confidential / Regulated
3. **Impact if Compromised**: Inconvenience / Disruption / Severe Harm
4. **Threat Model**: Opportunistic / Targeted / Advanced Persistent Threats

**Rationale**: Objective criteria ensure consistent application across organizations and enable automated tooling for level recommendation.

#### 5. Validation Rigor Independence

**Principle**: Control requirements are separate from validation rigor. All Level 2 servers implement the same controls, but validation may differ (self-assessment vs. third-party audit).

**Rationale**: Inspired by PCI DSS, which requires all merchants to implement the same 12 requirements but varies audit rigor by transaction volume. This keeps costs manageable for small deployments while ensuring credibility for critical ones.

**Application**:
- Level 1: Self-assessment questionnaire (SAQ) acceptable
- Level 2: Internal audit + automated testing recommended
- Level 3: Third-party penetration testing required

---

## Level Definitions

### Level 1: Foundational Baseline

**Official Name**: Level 1 (L1) — Foundational Baseline
**Informal Name**: Personal / Development

#### Target Audience
- Individual developers building personal MCP tools
- Hobbyists and open-source contributors
- Development and testing environments
- Internal prototypes and proof-of-concepts
- Educational and research projects

#### Data Profile
- Public information or synthetic test data
- Non-sensitive personal data (e.g., public GitHub repos)
- Localhost-only deployments with no external exposure
- Data that can be fully restored from backups without business impact

#### Impact if Compromised
- **Confidentiality**: Exposure of non-sensitive information
- **Integrity**: Corruption of personal files or test data
- **Availability**: Personal inconvenience, lost work requiring rework

**Concrete Examples**:
- Compromised MCP server deletes personal notes → User restores from backup
- Attacker accesses public blog posts through MCP tool → No incremental harm
- Command injection executes `whoami` → Limited local information disclosure

#### Threat Model
- **Primary**: Opportunistic automated attacks (botnets, vulnerability scanners)
- **Secondary**: Accidental misconfiguration or user error
- **Not modeled**: Targeted attacks by sophisticated adversaries

#### Control Count
**7 controls** (~29% of total 24 controls)

#### Key Controls
- MCP-FS-01: Path Allowlisting (prevents directory traversal)
- MCP-EXEC-01: No Shell Execution (prevents command injection)
- MCP-NET-01: URL Validation (prevents SSRF)
- MCP-INPUT-01: Schema Validation (prevents injection attacks)
- MCP-LOG-02: Secret Redaction (prevents credential leaks)
- MCP-SUPPLY-02: Trusted Package Sources (prevents supply chain attacks)
- MCP-FS-02: Symlink Resolution (prevents symlink bypass)

#### Validation Method
- **Self-Assessment Questionnaire (SAQ)**: Structured checklist format
- **Automated Tooling**: Static analysis and configuration scanning
- **Peer Review**: Optional community code review
- **Timeline**: Can be completed in 1-4 hours for small projects

#### Certification
- **Badge**: "MSSS Level 1 Compliant" (self-certified)
- **No External Audit Required**
- **Renewal**: Annual self-attestation

---

### Level 2: Standard Protection

**Official Name**: Level 2 (L2) — Standard Protection
**Informal Name**: Team / Enterprise

#### Target Audience
- Internal team collaboration servers
- Departmental and business unit applications
- SaaS products serving customers
- SME and mid-market enterprise deployments
- Servers accessing business-critical data sources

#### Data Profile
- Business-confidential information (financial data, strategic plans)
- Personal Identifiable Information (PII) under GDPR/CCPA
- Customer data with contractual protection obligations
- Intellectual property and trade secrets
- Multi-tenant systems with customer separation requirements

#### Impact if Compromised
- **Confidentiality**: Data breach requiring notification under privacy regulations
- **Integrity**: Corruption of business records affecting operations or decisions
- **Availability**: Service disruption impacting customers or revenue

**Concrete Examples**:
- Compromised MCP server exfiltrates customer email addresses → GDPR breach notification, reputational damage, potential fines
- Attacker modifies database records → Data integrity issues, operational disruption, customer trust erosion
- Command injection allows lateral movement → Full system compromise, potential ransomware deployment

#### Threat Model
- **Primary**: Targeted attacks by moderately skilled attackers (cybercriminals, disgruntled insiders)
- **Secondary**: Sophisticated automated attacks (credential stuffing, known CVE exploitation)
- **Not modeled**: Nation-state APTs with multi-year persistence campaigns

#### Control Count
**21 controls** (~88% of total 24 controls, including all L1 controls)

#### Key Additional Controls (beyond L1)
- MCP-AUTHZ-01: OAuth Token Delegation (per-user authentication)
- MCP-AUTHZ-02: Per-Tool Scope Definition (granular permissions)
- MCP-AUTHZ-03: Least Privilege Tool Design (principle of least privilege)
- MCP-AUTHZ-04: Resource-Based Access Control (RBAC with deny rules)
- MCP-LOG-01: Comprehensive Audit Logging (forensic capability)
- MCP-DEPLOY-01: Container Hardening (non-root, dropped capabilities)
- MCP-NET-03: TLS Enforcement (encrypted transport)
- MCP-INPUT-02: Input Bounds Enforcement (DoS prevention)
- MCP-INPUT-03: Timeout Enforcement (resource exhaustion prevention)
- MCP-SUPPLY-01: Package Integrity Verification (dependency locking)
- MCP-EXEC-02: Command Allowlisting (restrict binary execution)
- MCP-EXEC-03: Argument Separator Usage (prevent argument injection)
- MCP-FS-03: Filesystem Sandboxing (OS-level isolation)
- MCP-DEPLOY-03: Resource Limits and Rate Limiting (DoS mitigation)

#### Validation Method
- **Internal Security Audit**: Performed by security team or qualified personnel
- **Automated Scanning**: SAST/DAST tools, container vulnerability scanning
- **Penetration Testing**: Optional but recommended for internet-facing servers
- **Timeline**: 1-2 weeks for initial assessment, 2-4 hours for annual recertification

#### Certification
- **Badge**: "MSSS Level 2 Certified" (requires documented evidence)
- **Auditor**: Internal security team or qualified third party
- **Renewal**: Annual audit with continuous monitoring recommended

---

### Level 3: Maximum Assurance

**Official Name**: Level 3 (L3) — Maximum Assurance
**Informal Name**: Critical / Public

#### Target Audience
- Public-facing MCP gateways and API services
- Healthcare systems accessing PHI (HIPAA)
- Financial services with PCI DSS requirements
- Government and defense applications
- Critical infrastructure control systems
- Enterprise MCP gateways serving 1000+ users

#### Data Profile
- Protected Health Information (PHI) under HIPAA
- Payment Card Industry (PCI) data
- Classified or controlled unclassified information (CUI)
- Personally Identifiable Information (PII) at scale (100k+ records)
- Data subject to regulatory compliance (SOX, GLBA, ITAR)

#### Impact if Compromised
- **Confidentiality**: Massive data breach with regulatory penalties (e.g., HIPAA fines up to $1.5M per violation category)
- **Integrity**: Manipulation of critical records affecting safety or financial systems
- **Availability**: Service outage affecting essential operations or public safety

**Concrete Examples**:
- Hospital MCP gateway compromised → PHI of 100,000 patients exposed, HIPAA penalties, class-action lawsuits, loss of accreditation
- Financial MCP server breached → Manipulation of transaction records, SEC investigation, criminal charges
- Government MCP tool exploited → Exfiltration of classified data, national security implications, Congressional inquiry

#### Threat Model
- **Primary**: Advanced Persistent Threats (nation-states, organized cybercrime)
- **Secondary**: Insider threats with privileged access
- **Modeled**: Multi-stage attacks, zero-days, supply chain compromise

#### Control Count
**24 controls** (100% of all MSSS controls)

#### Key Additional Controls (beyond L2)
- MCP-NET-02: Egress Traffic Filtering (comprehensive allowlisting, DNS control, rate limiting)
- MCP-DEPLOY-02: System Call Filtering (seccomp/AppArmor enforcement)
- MCP-DEPLOY-04: Runtime Integrity Monitoring (future control, TBD)

#### Additional Requirements
- **Multi-Factor Authentication (MFA)**: Mandatory for administrative access
- **Security Operations Center (SOC)**: 24/7 monitoring or equivalent
- **Incident Response Plan**: Documented and tested procedures
- **Business Continuity**: Backup/DR with <4hr RTO
- **Security Training**: Annual training for all personnel with server access

#### Validation Method
- **Third-Party Penetration Testing**: Annual full-scope assessment by qualified firm
- **Continuous Monitoring**: SIEM, EDR, and behavioral analytics
- **Compliance Audit**: Industry-specific (HIPAA, PCI DSS, FedRAMP, SOC 2)
- **Timeline**: 4-8 weeks initial assessment, ongoing continuous monitoring

#### Certification
- **Badge**: "MSSS Level 3 Certified" (requires independent verification)
- **Auditor**: Qualified third-party security assessor (QTSA)
- **Renewal**: Annual penetration test + quarterly vulnerability scans

---

## Selection Criteria

### Decision Framework

Organizations should select their target compliance level using the following decision tree:

#### Question 1: User Scope

**Who uses or accesses this MCP server?**

- **Just me (individual developer)** → Consider Level 1
- **My team or department (2-50 users)** → Consider Level 2
- **My organization (50+ users) or external customers** → Consider Level 3

#### Question 2: Data Sensitivity

**What is the most sensitive data this MCP server can access?**

- **Public information or synthetic test data** → Consider Level 1
- **Business-confidential or PII (GDPR/CCPA scope)** → Consider Level 2
- **Regulated data (PHI, PCI, classified)** → **Require Level 3**

#### Question 3: Impact Analysis

**What is the worst-case impact if this MCP server is fully compromised?**

- **Personal inconvenience, lost productivity** → Consider Level 1
- **Business disruption, customer notification, reputational damage** → Consider Level 2
- **Regulatory penalties, legal liability, safety risk, national security** → **Require Level 3**

#### Question 4: Threat Model

**What threat actors are likely to target this deployment?**

- **Opportunistic automated attacks (botnets, scanners)** → Consider Level 1
- **Targeted attacks by moderately skilled adversaries** → Consider Level 2
- **Advanced persistent threats, insider threats, nation-states** → **Require Level 3**

### Decision Matrix

| Criterion | Level 1 | Level 2 | Level 3 |
|-----------|---------|---------|---------|
| **Users** | Individual | Team / Department | Organization / Public |
| **Data** | Public / Test | Business / PII | Regulated / Critical |
| **Impact** | Inconvenience | Disruption / Breach | Severe Harm / Liability |
| **Threats** | Opportunistic | Targeted | APT / Insider |
| **Examples** | Personal tools, dev/test | Team servers, SaaS | Healthcare, finance, gov |

### Automatic Level Requirements

Certain deployment contexts **automatically require** a minimum compliance level:

#### Mandatory Level 2
- Servers accessing customer PII (email addresses, names, phone numbers)
- Multi-tenant systems with customer isolation requirements
- Servers exposed to untrusted networks (even behind VPN)
- Processing business-confidential data (financial records, strategic plans)

#### Mandatory Level 3
- Healthcare applications accessing PHI (HIPAA requirement)
- Payment processing systems (PCI DSS requirement)
- Government/defense systems with classified or CUI data
- Critical infrastructure (energy, utilities, transportation control systems)
- Public-facing MCP gateways serving external users at scale

### Conservative Selection Guidance

**When in doubt, select the higher level.**

- If you're uncertain about data sensitivity → go up one level
- If threat model assessment is unclear → go up one level
- If deployment context may expand in future → plan for higher level now

**Rationale**: Retrofitting security controls after deployment is 10-100x more expensive than building them in from the start.

---

## Control Distribution

### Overview

The 24 MSSS controls are distributed across three levels using a **cumulative model** inspired by OWASP ASVS:

- **Level 1**: 7 controls (29%)
- **Level 2**: +14 controls (59%, cumulative 88%)
- **Level 3**: +3 controls (12%, cumulative 100%)

### Distribution by Security Domain

| Domain | L1 Controls | L2 Controls | L3 Controls | Total |
|--------|-------------|-------------|-------------|-------|
| **FS (Filesystem)** | 2 | +1 | 0 | 3 |
| **EXEC (Execution)** | 1 | +2 | 0 | 3 |
| **NET (Network)** | 1 | +1 | +1 | 3 |
| **AUTHZ (Authorization)** | 0 | +4 | 0 | 4 |
| **INPUT (Validation)** | 1 | +2 | 0 | 3 |
| **LOG (Logging)** | 1 | +1 | 0 | 2 |
| **SUPPLY (Supply Chain)** | 1 | +1 | 0 | 2 |
| **DEPLOY (Deployment)** | 0 | +2 | +2 | 4 |

### Level 1 Controls (7 total)

These controls defend against the most common and severe vulnerabilities with minimal implementation complexity:

1. **MCP-FS-01**: Path Allowlisting and Canonical Resolution
2. **MCP-FS-02**: Symlink Resolution Validation
3. **MCP-EXEC-01**: Prohibition of Shell Execution
4. **MCP-NET-01**: URL Validation and SSRF Mitigation
5. **MCP-INPUT-01**: JSON Schema Validation
6. **MCP-LOG-02**: Secret Redaction in Logs
7. **MCP-SUPPLY-02**: Trusted Package Sources and Registry Verification

**Rationale**: These 7 controls prevent:
- Directory traversal (FS-01, FS-02)
- Command injection (EXEC-01)
- Server-Side Request Forgery (NET-01)
- Injection attacks and schema poisoning (INPUT-01)
- Credential leakage (LOG-02)
- Supply chain attacks (SUPPLY-02)

Together, they address **CWE Top 25** vulnerabilities #1, #3, #8, #11, and #19.

### Level 2 Controls (+14 additional, 21 total)

Level 2 adds comprehensive authorization, audit logging, deployment hardening, and defense-in-depth:

8. **MCP-AUTHZ-01**: OAuth Token Delegation
9. **MCP-AUTHZ-02**: Per-Tool Scope Definition
10. **MCP-AUTHZ-03**: Least Privilege Tool Design
11. **MCP-AUTHZ-04**: Resource-Based Access Control
12. **MCP-LOG-01**: Comprehensive Audit Logging
13. **MCP-NET-03**: TLS Enforcement for Remote Connections
14. **MCP-INPUT-02**: Input Bounds Enforcement
15. **MCP-INPUT-03**: Timeout Enforcement
16. **MCP-SUPPLY-01**: Package Integrity Verification
17. **MCP-EXEC-02**: Command Allowlisting
18. **MCP-EXEC-03**: Argument Separator Usage
19. **MCP-FS-03**: Filesystem Sandboxing
20. **MCP-DEPLOY-01**: Container Hardening (Non-Root, Capabilities)
21. **MCP-DEPLOY-03**: Resource Limits and Rate Limiting

**Rationale**: These controls add:
- Per-user authentication and authorization (AUTHZ-01/02/03/04)
- Forensic capabilities (LOG-01)
- Transport security (NET-03)
- DoS prevention (INPUT-02/03, DEPLOY-03)
- Supply chain verification (SUPPLY-01)
- Additional execution controls (EXEC-02/03)
- OS-level sandboxing (FS-03, DEPLOY-01)

### Level 3 Controls (+3 additional, 24 total)

Level 3 adds maximum hardening for high-risk environments:

22. **MCP-NET-02**: Egress Traffic Filtering
23. **MCP-DEPLOY-02**: System Call Filtering (seccomp/AppArmor)
24. **MCP-DEPLOY-04**: Runtime Integrity Monitoring (future)

**Rationale**: These controls provide:
- Data exfiltration prevention (NET-02)
- Kernel-level attack surface reduction (DEPLOY-02)
- Anomaly detection and response (DEPLOY-04, future)

---

## Validation and Certification

### Validation Methods by Level

| Level | Method | Frequency | Cost | Rigor |
|-------|--------|-----------|------|-------|
| **L1** | Self-Assessment Questionnaire (SAQ) | Annual | Free | Basic |
| **L2** | Internal Audit + Automated Tools | Annual | Low-Moderate | Moderate |
| **L3** | Third-Party Penetration Test | Annual | High | Comprehensive |

### Level 1 Validation

**Self-Assessment Questionnaire (SAQ)**

- Structured checklist in machine-readable format (JSON/YAML)
- Each control includes:
  - Binary yes/no compliance indicator
  - Evidence artifact reference (file path, screenshot, config snippet)
  - Optional notes explaining implementation
- Automated tooling validates technical controls where possible
- Community peer review encouraged but not required

**Example SAQ Entry**:
```yaml
control: MCP-EXEC-01
compliant: true
evidence: "src/executor.py:45-67"
notes: "Using subprocess.run() with shell=False and argument array"
validated_by: "static-analysis-tool-v1.2.3"
validation_date: "2026-01-15"
```

**Badging**: Projects meeting all L1 controls can display "MSSS Level 1 Compliant" badge on documentation.

### Level 2 Validation

**Internal Security Audit**

- Performed by qualified internal security personnel or external consultant
- Includes:
  - Code review of security-critical components
  - Configuration audit (container settings, network policies)
  - Automated vulnerability scanning (SAST, DAST, SCA)
  - Manual testing of authentication and authorization
- Evidence documentation required for each control
- Sign-off by security lead or CISO

**Automated Tools**:
- SAST: Semgrep, Snyk Code, CodeQL
- DAST: OWASP ZAP, Burp Suite
- SCA: Dependabot, Snyk Open Source
- Container Scanning: Trivy, Grype, Clair

**Certification Package**:
- Audit report with findings
- Remediation plan for any exceptions
- Evidence repository (code, configs, scan results)
- Attestation letter from security team

### Level 3 Validation

**Third-Party Penetration Testing**

- Performed by qualified independent security assessor
- Includes:
  - Black-box penetration testing (external attacker perspective)
  - Gray-box testing (authenticated user privilege escalation)
  - White-box code audit (full source access)
  - Supply chain review
- Report includes:
  - Executive summary
  - Technical findings with CVSS scores
  - Evidence (screenshots, proof-of-concept code)
  - Remediation recommendations
- Remediation verification testing required

**Additional Requirements**:
- Penetration test every 12 months
- Vulnerability scans every 90 days
- Incident response tabletop exercise annually
- Security training for all personnel with server access

**Compliance Alignment**:
- HIPAA Security Rule
- PCI DSS v4.0
- SOC 2 Type II
- FedRAMP (Low/Moderate/High)
- ISO 27001

**Certification Package**:
- Penetration test report from QTSA
- Vulnerability scan results (quarterly)
- Incident response plan
- Business continuity/disaster recovery plan
- Security training records

---

## Implementation Guidance

### Getting Started

#### For Level 1

**Timeline**: 1-4 hours for small projects

**Steps**:
1. **Read control requirements**: Review all 7 Level 1 controls
2. **Assess current state**: Run automated scanners (e.g., Semgrep with MSSS ruleset)
3. **Implement gaps**: Address identified deficiencies
4. **Document evidence**: Create SAQ with evidence references
5. **Display badge**: Add "MSSS Level 1 Compliant" to README

**Common Implementation Patterns**:
- Path allowlisting: Use `realpath()` + prefix matching
- Shell execution: Always use `subprocess.run([cmd, arg1, arg2], shell=False)`
- URL validation: Block private IP ranges (10.0.0.0/8, 169.254.0.0/16, 127.0.0.0/8)
- Schema validation: Use `jsonschema` library with strict schemas
- Secret redaction: Use regex patterns to detect and redact secrets before logging

**Tools**:
- `msss-scanner` (CLI tool for automated control verification)
- `msss-saq-generator` (generates SAQ template)

#### For Level 2

**Timeline**: 1-2 weeks initial implementation, 2-4 hours annual recertification

**Steps**:
1. **Achieve Level 1**: Ensure all L1 controls implemented first
2. **Architecture review**: Assess need for OAuth, RBAC, container hardening
3. **Implement L2 controls**: Focus on AUTHZ domain (4 controls) first
4. **Deploy logging and monitoring**: Implement MCP-LOG-01 comprehensive audit logs
5. **Harden deployment**: Container non-root, dropped capabilities, resource limits
6. **Internal audit**: Security team review with SAST/DAST scans
7. **Remediation**: Fix findings, document exceptions
8. **Certification**: Obtain sign-off from security lead

**Common Architectural Decisions**:
- **Authentication**: OAuth 2.1 with OIDC for user delegation
- **Authorization**: Casbin or OPA for policy-based access control
- **Logging**: Structured JSON logs to SIEM (Splunk, Elasticsearch, Datadog)
- **Container Base**: Distroless or Alpine Linux, non-root user
- **Rate Limiting**: Token bucket algorithm (e.g., 100 req/min per user)

**Tools**:
- OAuth: Auth0, Okta, Keycloak
- RBAC: Casbin, Open Policy Agent (OPA)
- Container Scanning: Trivy, Anchore, Snyk Container
- SAST: Semgrep Pro, CodeQL, Checkmarx

#### For Level 3

**Timeline**: 4-8 weeks initial assessment, ongoing continuous monitoring

**Steps**:
1. **Achieve Level 2**: Complete all L2 controls and validation
2. **Implement L3 controls**: Egress filtering, seccomp/AppArmor profiles
3. **Threat modeling**: Formal STRIDE or PASTA analysis
4. **Engage penetration tester**: Select qualified third-party (OSCP, GPEN, CEH)
5. **Remediation**: Address all high/critical findings from pentest
6. **Compliance mapping**: Align to industry regulations (HIPAA, PCI DSS, SOC 2)
7. **Continuous monitoring**: Deploy SIEM, EDR, behavioral analytics
8. **Certification**: Obtain third-party attestation

**Advanced Security Measures**:
- **Egress Filtering**: Allowlist-based network policy (Kubernetes NetworkPolicy, iptables)
- **Seccomp**: Custom seccomp profiles blocking 95% of syscalls
- **Runtime Monitoring**: Falco rules for detecting anomalous behavior
- **Incident Response**: PagerDuty/Splunk On-Call integration for alerts

**Tools**:
- Egress Control: Kubernetes NetworkPolicy, Cilium, AWS Security Groups
- Seccomp Profiles: docker-slim, oci-seccomp-bpf-hook
- Runtime Security: Falco, Sysdig Secure, Aqua Security
- SIEM: Splunk Enterprise Security, Elastic Security, QRadar

### Incremental Adoption

**Start with Level 1, upgrade as deployment context changes:**

```
Personal Project → Deploy Internally → Customer-Facing → Regulated Industry
     Level 1     →     Level 2      →    Level 2     →     Level 3
```

**Progressive Compliance**:
- **Month 1-2**: Implement Level 1 (7 controls)
- **Month 3-6**: Add Level 2 authorization and logging (6 controls)
- **Month 7-12**: Complete Level 2 deployment hardening (8 controls)
- **Year 2+**: Upgrade to Level 3 as business criticality increases (3 controls)

---

## Mixed-Level Deployments

### Motivation

Not all MCP tools within a server require the same security rigor. A server may have:
- High-risk write tools (filesystem modification) → Level 3
- Moderate-risk read tools (database queries) → Level 2
- Low-risk informational tools (weather lookup) → Level 1

**Precedent**: OWASP ASVS allows mixed-level implementations with clear documentation.

### Requirements for Mixed-Level

1. **Tool-Level Declaration**: Each tool must declare its compliance level in metadata
2. **Minimum Server Level**: Server as a whole must meet the highest declared tool level
3. **Documentation**: Clearly document which tools are at which levels
4. **Isolation**: Higher-level tools must not depend on lower-level tools for security

### Example Mixed-Level Configuration

```json
{
  "server": {
    "name": "example-mcp-server",
    "overall_level": "L2",
    "tools": [
      {
        "name": "read_file",
        "level": "L2",
        "rationale": "Accesses business-confidential documents"
      },
      {
        "name": "write_file",
        "level": "L3",
        "rationale": "Can modify critical configuration files"
      },
      {
        "name": "get_weather",
        "level": "L1",
        "rationale": "Calls public API with non-sensitive data"
      }
    ]
  }
}
```

**Validation**:
- `read_file` and `write_file` undergo Level 2/3 validation respectively
- `get_weather` validated to Level 1
- Server infrastructure must meet Level 3 (highest tool level)

---

## Level Comparison Matrix

### Quick Reference

| Aspect | Level 1 | Level 2 | Level 3 |
|--------|---------|---------|---------|
| **Target** | Personal / Dev | Team / Enterprise | Critical / Public |
| **Users** | Individual | Team/Dept | Org / External |
| **Data** | Public / Test | Business / PII | Regulated / PHI |
| **Impact** | Inconvenience | Disruption | Severe Harm |
| **Threats** | Opportunistic | Targeted | APT / Insider |
| **Controls** | 7 (29%) | 21 (88%) | 24 (100%) |
| **Validation** | Self-Assessment | Internal Audit | Third-Party Pentest |
| **Frequency** | Annual | Annual | Annual + Quarterly Scans |
| **Cost** | Free | Low-Moderate | High |
| **Timeline** | 1-4 hours | 1-2 weeks | 4-8 weeks |
| **Examples** | Hobby projects, dev/test | SaaS, team servers | Healthcare, finance, gov |

### Decision Tree Visual

```
START: Need to secure MCP server
         |
         v
  [ Who uses it? ]
         |
    +----+----+
    |         |
   Me       Team/Org
    |         |
    v         v
[Level 1]  [ What data? ]
               |
          +----+----+
          |         |
       Business  Regulated
          |         |
          v         v
      [Level 2] [Level 3]
```

---

## References and Research

### Industry Standards Analyzed

This compliance level framework is informed by analysis of seven major security standards:

1. **NIST Cybersecurity Framework** (4 Implementation Tiers)
2. **CIS Controls v8** (3 Implementation Groups: IG1/IG2/IG3)
3. **CMMI** (5 Maturity Levels)
4. **ISO 27001** (Maturity models)
5. **PCI DSS v4.0** (4 Merchant Levels)
6. **OWASP ASVS v4.0** (3 Verification Levels: L1/L2/L3)
7. **FedRAMP** (3 Authorization Levels: Low/Moderate/High)

### Key Insights Applied

- **CIS Controls**: Clear organizational profiles (small/medium/large enterprises)
- **OWASP ASVS**: Proven 20-50-30 control distribution pattern
- **FedRAMP**: Impact-based categorization (FIPS 199 framework)
- **PCI DSS**: Separation of control requirements from validation rigor
- **NIST CSF**: Process maturity independent from control selection

### Research Documents

Full analysis available in:
- `/research/compliance-levels-research.md` (comprehensive 17,000-word analysis)
- `/research/compliance-levels-summary.md` (quick reference guide)

### External Resources

- CIS Controls v8: https://www.cisecurity.org/controls/v8
- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/
- FedRAMP Baselines: https://www.fedramp.gov/understanding-baselines-and-impact-levels/
- NIST SP 800-53: https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final
- PCI DSS: https://www.pcisecuritystandards.org/

---

## Revision History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | 2026-01-20 | Initial release | Dani |

---

**Document Status**: Official
**Maintained By**: MCP Security Standard Working Group
**Questions**: https://github.com/mcp-security-standard/msss/issues
