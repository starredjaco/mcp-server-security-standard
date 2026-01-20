---
title: "Compliance Levels"
weight: 3
---

# MCP Security Standard — Compliance Levels

**Version:** 2.0
**Status:** Official
**Effective Date:** 2026-01-20
**Last Updated:** 2026-01-20

---

## Table of Contents

1. [Introduction](#introduction)
2. [Philosophy and Design Rationale](#philosophy-and-design-rationale)
3. [Why Four Levels](#why-four-levels)
4. [Level Definitions](#level-definitions)
5. [Selection Criteria](#selection-criteria)
6. [Control Distribution](#control-distribution)
7. [Validation and Certification](#validation-and-certification)
8. [Implementation Guidance](#implementation-guidance)
9. [Mixed-Level Deployments](#mixed-level-deployments)
10. [Level Comparison Matrix](#level-comparison-matrix)
11. [References and Research](#references-and-research)

---

## Introduction

### Purpose

The MCP Security Standard (MSSS) defines **four compliance levels** to accommodate the diverse deployment contexts of Model Context Protocol (MCP) servers — from personal hobby projects to enterprise-critical infrastructure processing regulated data.

This document establishes the framework for selecting, implementing, and validating compliance at each level.

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
- Require all MCP servers to eventually reach Level 4

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
- **NIST CSF** Implementation Tiers (Partial/Risk Informed/Repeatable/Adaptive)

#### 2. Cumulative Control Distribution

**Principle**: Each level builds upon the previous level's controls. Level 2 includes all Level 1 controls plus additional protections. Level 3 includes all Level 1 and 2 controls plus more. Level 4 includes all controls.

**Rationale**:
- Ensures consistent baseline security across all deployments
- Simplifies compliance assessment (you either meet a level fully, or you don't)
- Prevents dangerous gaps where critical controls are skipped
- Facilitates upgrades when deployment context changes

**Distribution Pattern**: Balanced 25-50-75-100 distribution:
- **Level 1**: 25% of total controls (essential baseline)
- **Level 2**: 50% of total controls (development/team)
- **Level 3**: 75% of total controls (production/enterprise)
- **Level 4**: 100% of total controls (maximum assurance)

This **gradual progression** ensures each level-up adds approximately equal effort (~6 controls per level).

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

**Principle**: Control requirements are separate from validation rigor. All Level 2 servers implement the same controls, but validation may differ (self-assessment vs. internal audit).

**Rationale**: Inspired by PCI DSS, which requires all merchants to implement the same 12 requirements but varies audit rigor by transaction volume. This keeps costs manageable for small deployments while ensuring credibility for critical ones.

**Application**:
- Level 1: Self-assessment questionnaire (SAQ) acceptable
- Level 2: Self-assessment + automated scanning
- Level 3: Internal security audit required
- Level 4: Third-party penetration testing required

---

## Why Four Levels

### Problem with Three Levels

The original 3-level system had significant granularity issues:

| Transition | Controls Added | Percentage Jump |
|------------|---------------|-----------------|
| L1 → L2 | 7 → 21 | **200% increase** |
| L2 → L3 | 21 → 24 | **14% increase** |

This created two problems:
1. **L1 → L2 gap too large**: Teams faced a daunting jump from 7 to 21 controls
2. **L2 → L3 gap too small**: Only 3 additional controls didn't justify a separate level

### Industry Precedents for Four Levels

Several major security frameworks use four levels:

| Framework | Levels | Distribution Logic |
|-----------|--------|-------------------|
| **NIST CSF** | 4 Tiers | Partial → Risk Informed → Repeatable → Adaptive |
| **PCI DSS** | 4 Merchant Levels | Based on transaction volume |
| **ISO 27001** | 4 Maturity Levels | Ad-hoc → Defined → Managed → Optimized |
| **COBIT** | 4 Process Levels | Initial → Managed → Established → Predictable |

### Benefits of Four-Level System

| Aspect | 3 Levels | 4 Levels |
|--------|----------|----------|
| Level jumps | 200%, 14% | 100%, 50%, 33% |
| Granularity | Poor | Good |
| Implementation effort | Uneven | Balanced |
| Industry alignment | OWASP ASVS | NIST CSF, PCI DSS |

### Distribution Comparison

| Level | 3-Level System | 4-Level System |
|-------|---------------|----------------|
| L1 | 7 controls (29%) | 6 controls (25%) |
| L2 | 21 controls (88%) | 12 controls (50%) |
| L3 | 24 controls (100%) | 18 controls (75%) |
| L4 | — | 24 controls (100%) |

The 4-level system provides a smoother progression:
- Each level adds ~6 controls
- Each jump represents ~25% of total controls
- Teams can incrementally improve without overwhelming changes

---

## Level Definitions

### Level 1: Essential

**Official Name**: Level 1 (L1) — Essential
**Informal Name**: Personal / Hobby

#### Target Audience
- Individual developers building personal MCP tools
- Hobbyists and open-source contributors
- Local development and testing environments
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
**6 controls** (25% of total 24 controls)

#### Controls Included
| Control | Description |
|---------|-------------|
| MCP-EXEC-01 | Prohibition of Shell Execution (prevents RCE) |
| MCP-FS-01 | Path Allowlisting and Canonical Resolution (prevents traversal) |
| MCP-FS-02 | Symlink Resolution Validation (closes bypass) |
| MCP-NET-01 | URL Validation and SSRF Mitigation (prevents SSRF) |
| MCP-INPUT-01 | JSON Schema Validation (prevents injection) |
| MCP-LOG-02 | Secret Redaction in Logs (prevents credential leaks) |

#### Validation Method
- **Self-Assessment Questionnaire (SAQ)**: Structured checklist format
- **Automated Tooling**: Static analysis and configuration scanning
- **Peer Review**: Optional community code review
- **Timeline**: Can be completed in **1-2 hours** for small projects

#### Certification
- **Badge**: "MSSS Level 1 Compliant" (self-certified)
- **No External Audit Required**
- **Renewal**: Annual self-attestation

---

### Level 2: Development

**Official Name**: Level 2 (L2) — Development
**Informal Name**: Internal / Team

#### Target Audience
- Development teams building internal tools
- Internal productivity applications
- Early-stage startups with small teams
- Pre-production staging environments
- Open-source projects with contributors

#### Data Profile
- Internal business data (non-critical)
- Test data that may include sanitized PII
- Source code and development artifacts
- Configuration and secrets (in development environments)

#### Impact if Compromised
- **Confidentiality**: Exposure of source code or internal processes
- **Integrity**: Corruption of development artifacts
- **Availability**: Development team productivity loss

**Concrete Examples**:
- Compromised MCP server exposes internal documentation → Limited business impact
- Attacker modifies test data → Rework required, no production impact
- DoS attack on development server → Team delays, no customer impact

#### Threat Model
- **Primary**: Opportunistic attacks + insider mistakes
- **Secondary**: Semi-targeted attacks (disgruntled employees, curious researchers)
- **Not modeled**: Advanced persistent threats

#### Control Count
**12 controls** (50% of total 24 controls, including all L1 controls)

#### Additional Controls (beyond L1)
| Control | Description |
|---------|-------------|
| MCP-SUPPLY-02 | Trusted Package Sources and Registry Verification |
| MCP-INPUT-02 | Input Bounds Enforcement (DoS prevention) |
| MCP-INPUT-03 | Timeout Enforcement (resource exhaustion) |
| MCP-NET-03 | TLS Enforcement for Remote Connections |
| MCP-EXEC-02 | Command Allowlisting (restrict binary execution) |
| MCP-EXEC-03 | Argument Separator Usage (prevent injection) |

#### Validation Method
- **Self-Assessment**: Structured checklist with evidence
- **Automated Scanning**: SAST tools, dependency scanning
- **Peer Review**: Recommended for security-critical components
- **Timeline**: **4-8 hours** for initial assessment

#### Certification
- **Badge**: "MSSS Level 2 Compliant" (self-certified with evidence)
- **No External Audit Required** (recommended)
- **Renewal**: Annual self-assessment

---

### Level 3: Production

**Official Name**: Level 3 (L3) — Production
**Informal Name**: Enterprise / Customer-Facing

#### Target Audience
- Customer-facing SaaS applications
- Multi-tenant enterprise deployments
- Business-critical internal applications
- Production environments with real customer data
- Servers accessing business-confidential information

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
- Command injection allows lateral movement → System compromise potential

#### Threat Model
- **Primary**: Targeted attacks by moderately skilled attackers (cybercriminals, insiders)
- **Secondary**: Sophisticated automated attacks (credential stuffing, CVE exploitation)
- **Not modeled**: Nation-state APTs with multi-year campaigns

#### Control Count
**18 controls** (75% of total 24 controls, including all L1 and L2 controls)

#### Additional Controls (beyond L2)
| Control | Description |
|---------|-------------|
| MCP-AUTHZ-01 | OAuth Token Delegation (per-user authentication) |
| MCP-AUTHZ-02 | Per-Tool Scope Definition (granular permissions) |
| MCP-AUTHZ-03 | Least Privilege Tool Design (principle of least privilege) |
| MCP-AUTHZ-04 | Resource-Based Access Control (RBAC with deny rules) |
| MCP-LOG-01 | Comprehensive Audit Logging (forensic capability) |
| MCP-DEPLOY-01 | Container Hardening (non-root, dropped capabilities) |

#### Validation Method
- **Internal Security Audit**: Performed by security team or qualified personnel
- **Automated Scanning**: SAST/DAST tools, container vulnerability scanning
- **Penetration Testing**: Recommended for internet-facing servers
- **Timeline**: **1-2 weeks** for initial assessment, **2-4 hours** for annual recertification

#### Certification
- **Badge**: "MSSS Level 3 Certified" (requires documented evidence)
- **Auditor**: Internal security team or qualified third party
- **Renewal**: Annual audit with continuous monitoring recommended

---

### Level 4: Maximum Assurance

**Official Name**: Level 4 (L4) — Maximum Assurance
**Informal Name**: Critical / Regulated

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
- Government MCP tool exploited → Exfiltration of classified data, national security implications

#### Threat Model
- **Primary**: Advanced Persistent Threats (nation-states, organized cybercrime)
- **Secondary**: Insider threats with privileged access
- **Modeled**: Multi-stage attacks, zero-days, supply chain compromise

#### Control Count
**24 controls** (100% of all MSSS controls)

#### Additional Controls (beyond L3)
| Control | Description |
|---------|-------------|
| MCP-FS-03 | Filesystem Sandboxing (OS-level isolation) |
| MCP-SUPPLY-01 | Package Integrity Verification (cryptographic hashes) |
| MCP-DEPLOY-03 | Resource Limits and Rate Limiting (DoS mitigation) |
| MCP-NET-02 | Egress Traffic Filtering (data exfiltration prevention) |
| MCP-DEPLOY-02 | System Call Filtering (seccomp/AppArmor) |
| MCP-DEPLOY-04 | Runtime Integrity Monitoring (anomaly detection) |

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
- **Timeline**: **4-8 weeks** initial assessment, ongoing continuous monitoring

#### Certification
- **Badge**: "MSSS Level 4 Certified" (requires independent verification)
- **Auditor**: Qualified third-party security assessor (QTSA)
- **Renewal**: Annual penetration test + quarterly vulnerability scans

---

## Selection Criteria

### Decision Framework

Organizations should select their target compliance level using the following decision tree:

#### Question 1: User Scope

**Who uses or accesses this MCP server?**

- **Just me (individual developer)** → Consider Level 1
- **My team (2-10 users)** → Consider Level 2
- **My organization or external customers** → Consider Level 3
- **Public users or regulated industry** → Consider Level 4

#### Question 2: Data Sensitivity

**What is the most sensitive data this MCP server can access?**

- **Public information or synthetic test data** → Consider Level 1
- **Internal business data (non-critical)** → Consider Level 2
- **Business-confidential or PII (GDPR/CCPA scope)** → Consider Level 3
- **Regulated data (PHI, PCI, classified)** → **Require Level 4**

#### Question 3: Impact Analysis

**What is the worst-case impact if this MCP server is fully compromised?**

- **Personal inconvenience, lost productivity** → Consider Level 1
- **Development delays, internal rework** → Consider Level 2
- **Business disruption, customer notification, reputational damage** → Consider Level 3
- **Regulatory penalties, legal liability, safety risk** → **Require Level 4**

#### Question 4: Threat Model

**What threat actors are likely to target this deployment?**

- **Opportunistic automated attacks (botnets, scanners)** → Consider Level 1
- **Semi-targeted attacks (curious researchers, mistakes)** → Consider Level 2
- **Targeted attacks by moderately skilled adversaries** → Consider Level 3
- **Advanced persistent threats, insider threats, nation-states** → **Require Level 4**

### Decision Matrix

| Criterion | Level 1 | Level 2 | Level 3 | Level 4 |
|-----------|---------|---------|---------|---------|
| **Users** | Individual | Team (2-10) | Organization / Customers | Public / Regulated |
| **Data** | Public / Test | Internal | Business / PII | Regulated / Critical |
| **Impact** | Inconvenience | Dev delays | Disruption / Breach | Severe Harm / Liability |
| **Threats** | Opportunistic | Semi-targeted | Targeted | APT / Insider |
| **Examples** | Personal tools | Dev servers | SaaS, team apps | Healthcare, finance, gov |

### Automatic Level Requirements

Certain deployment contexts **automatically require** a minimum compliance level:

#### Mandatory Level 2
- Servers with multiple developers accessing shared resources
- Pre-production environments mirroring production data structures
- Open-source projects accepting external contributions

#### Mandatory Level 3
- Servers accessing customer PII (email addresses, names, phone numbers)
- Multi-tenant systems with customer isolation requirements
- Servers exposed to untrusted networks (even behind VPN)
- Processing business-confidential data (financial records, strategic plans)

#### Mandatory Level 4
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

The 24 MSSS controls are distributed across four levels using a **balanced cumulative model**:

- **Level 1**: 6 controls (25%)
- **Level 2**: +6 controls (50%, cumulative 12)
- **Level 3**: +6 controls (75%, cumulative 18)
- **Level 4**: +6 controls (100%, cumulative 24)

### Distribution by Security Domain

| Domain | L1 | L2 | L3 | L4 | Total |
|--------|:--:|:--:|:--:|:--:|:-----:|
| **FS (Filesystem)** | 2 | 0 | 0 | +1 | 3 |
| **EXEC (Execution)** | 1 | +2 | 0 | 0 | 3 |
| **NET (Network)** | 1 | +1 | 0 | +1 | 3 |
| **AUTHZ (Authorization)** | 0 | 0 | +4 | 0 | 4 |
| **INPUT (Validation)** | 1 | +2 | 0 | 0 | 3 |
| **LOG (Logging)** | 1 | 0 | +1 | 0 | 2 |
| **SUPPLY (Supply Chain)** | 0 | +1 | 0 | +1 | 2 |
| **DEPLOY (Deployment)** | 0 | 0 | +1 | +3 | 4 |

### Level 1 Controls (6 total)

These controls defend against the most common and severe vulnerabilities with minimal implementation complexity:

1. **MCP-EXEC-01**: Prohibition of Shell Execution
2. **MCP-FS-01**: Path Allowlisting and Canonical Resolution
3. **MCP-FS-02**: Symlink Resolution Validation
4. **MCP-NET-01**: URL Validation and SSRF Mitigation
5. **MCP-INPUT-01**: JSON Schema Validation
6. **MCP-LOG-02**: Secret Redaction in Logs

**Rationale**: These 6 controls prevent:
- Command injection (EXEC-01)
- Directory traversal (FS-01, FS-02)
- Server-Side Request Forgery (NET-01)
- Injection attacks and schema poisoning (INPUT-01)
- Credential leakage (LOG-02)

Together, they address **CWE Top 25** vulnerabilities #1, #3, #8, #11.

### Level 2 Controls (+6 additional, 12 total)

Level 2 adds input hardening, transport security, supply chain basics, and execution safety:

7. **MCP-SUPPLY-02**: Trusted Package Sources and Registry Verification
8. **MCP-INPUT-02**: Input Bounds Enforcement
9. **MCP-INPUT-03**: Timeout Enforcement
10. **MCP-NET-03**: TLS Enforcement for Remote Connections
11. **MCP-EXEC-02**: Command Allowlisting
12. **MCP-EXEC-03**: Argument Separator Usage

**Rationale**: These controls add:
- Supply chain basic protection (SUPPLY-02)
- DoS prevention (INPUT-02/03)
- Transport encryption (NET-03)
- Additional execution controls (EXEC-02/03)

### Level 3 Controls (+6 additional, 18 total)

Level 3 adds comprehensive authorization, audit logging, and container hardening:

13. **MCP-AUTHZ-01**: OAuth Token Delegation
14. **MCP-AUTHZ-02**: Per-Tool Scope Definition
15. **MCP-AUTHZ-03**: Least Privilege Tool Design
16. **MCP-AUTHZ-04**: Resource-Based Access Control
17. **MCP-LOG-01**: Comprehensive Audit Logging
18. **MCP-DEPLOY-01**: Container Hardening (Non-Root, Capabilities)

**Rationale**: These controls add:
- Per-user authentication and authorization (AUTHZ-01/02/03/04)
- Forensic capabilities (LOG-01)
- Container security baseline (DEPLOY-01)

### Level 4 Controls (+6 additional, 24 total)

Level 4 adds maximum hardening for high-risk environments:

19. **MCP-FS-03**: Filesystem Sandboxing
20. **MCP-SUPPLY-01**: Package Integrity Verification
21. **MCP-DEPLOY-03**: Resource Limits and Rate Limiting
22. **MCP-NET-02**: Egress Traffic Filtering
23. **MCP-DEPLOY-02**: System Call Filtering (seccomp/AppArmor)
24. **MCP-DEPLOY-04**: Runtime Integrity Monitoring

**Rationale**: These controls provide:
- Complete filesystem isolation (FS-03)
- Cryptographic supply chain verification (SUPPLY-01)
- DoS mitigation at infrastructure level (DEPLOY-03)
- Data exfiltration prevention (NET-02)
- Kernel-level attack surface reduction (DEPLOY-02)
- Anomaly detection and response (DEPLOY-04)

---

## Validation and Certification

### Validation Methods by Level

| Level | Method | Frequency | Cost | Rigor |
|-------|--------|-----------|------|-------|
| **L1** | Self-Assessment Questionnaire (SAQ) | Annual | Free | Basic |
| **L2** | Self-Assessment + Automated Tools | Annual | Low | Moderate |
| **L3** | Internal Security Audit | Annual | Moderate | Comprehensive |
| **L4** | Third-Party Penetration Test | Annual | High | Maximum |

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

**Self-Assessment + Automated Scanning**

- Structured self-assessment with documented evidence
- Automated scanning requirements:
  - SAST: Static analysis for security vulnerabilities
  - SCA: Software composition analysis for dependencies
- Evidence documentation required for each control
- Peer review recommended for security-critical components

**Automated Tools**:
- SAST: Semgrep, Snyk Code, CodeQL
- SCA: Dependabot, Snyk Open Source, npm audit

**Certification Package**:
- Self-assessment questionnaire
- Automated scan results
- Evidence repository (code snippets, configs)

### Level 3 Validation

**Internal Security Audit**

- Performed by qualified internal security personnel or external consultant
- Includes:
  - Code review of security-critical components
  - Configuration audit (container settings, network policies)
  - Automated vulnerability scanning (SAST/DAST, container scanning)
  - Manual testing of authentication and authorization
- Evidence documentation required for each control
- Sign-off by security lead or CISO

**Automated Tools**:
- SAST: Semgrep Pro, Snyk Code, CodeQL
- DAST: OWASP ZAP, Burp Suite
- SCA: Dependabot, Snyk Open Source
- Container Scanning: Trivy, Grype, Clair

**Certification Package**:
- Audit report with findings
- Remediation plan for any exceptions
- Evidence repository (code, configs, scan results)
- Attestation letter from security team

### Level 4 Validation

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

**Timeline**: 1-2 hours for small projects

**Steps**:
1. **Read control requirements**: Review all 6 Level 1 controls
2. **Assess current state**: Run automated scanners (e.g., Semgrep with MSSS ruleset)
3. **Implement gaps**: Address identified deficiencies
4. **Document evidence**: Create SAQ with evidence references
5. **Display badge**: Add "MSSS Level 1 Compliant" to README

**Common Implementation Patterns**:
- No shell execution: Always use `subprocess.run([cmd, arg1, arg2], shell=False)`
- Path allowlisting: Use `realpath()` + prefix matching
- Symlink resolution: Resolve symlinks before path validation
- URL validation: Block private IP ranges (10.0.0.0/8, 169.254.0.0/16, 127.0.0.0/8)
- Schema validation: Use `jsonschema` library with strict schemas
- Secret redaction: Use regex patterns to detect and redact secrets before logging

**Tools**:
- `msss-scanner` (CLI tool for automated control verification)
- `msss-saq-generator` (generates SAQ template)

#### For Level 2

**Timeline**: 4-8 hours initial implementation

**Steps**:
1. **Achieve Level 1**: Ensure all L1 controls implemented first
2. **Add input hardening**: Implement bounds checking and timeouts
3. **Enable TLS**: Configure TLS 1.2+ for all remote connections
4. **Execution safety**: Add command allowlisting and argument separators
5. **Supply chain**: Verify packages come from trusted sources
6. **Document evidence**: Create comprehensive SAQ with scan results

**Common Implementation Patterns**:
- TLS: Use `ssl` module with modern cipher suites
- Input bounds: Max string length 64KB, array size 1000, nesting depth 10
- Timeouts: Tool execution 30s, total request 60s
- Command allowlist: Explicit list of permitted executables
- Argument separator: Use `--` between command and user-provided arguments

#### For Level 3

**Timeline**: 1-2 weeks initial implementation

**Steps**:
1. **Achieve Level 2**: Ensure all L2 controls implemented first
2. **Architecture review**: Design authentication and authorization
3. **Implement AUTHZ**: OAuth 2.1 + RBAC with deny rules
4. **Deploy logging**: Comprehensive audit logs to SIEM
5. **Harden containers**: Non-root user, dropped capabilities
6. **Internal audit**: Security team review with SAST/DAST scans
7. **Certification**: Obtain sign-off from security lead

**Common Architectural Decisions**:
- **Authentication**: OAuth 2.1 with OIDC for user delegation
- **Authorization**: Casbin or OPA for policy-based access control
- **Logging**: Structured JSON logs to SIEM (Splunk, Elasticsearch, Datadog)
- **Container Base**: Distroless or Alpine Linux, non-root user

**Tools**:
- OAuth: Auth0, Okta, Keycloak
- RBAC: Casbin, Open Policy Agent (OPA)
- Container Scanning: Trivy, Anchore, Snyk Container
- SAST: Semgrep Pro, CodeQL, Checkmarx

#### For Level 4

**Timeline**: 4-8 weeks initial assessment

**Steps**:
1. **Achieve Level 3**: Complete all L3 controls and validation
2. **Maximum isolation**: Filesystem sandboxing, seccomp profiles
3. **Supply chain integrity**: Cryptographic verification of all packages
4. **Network hardening**: Egress filtering with explicit allowlists
5. **Resource protection**: Rate limiting and resource quotas
6. **Runtime monitoring**: Deploy anomaly detection
7. **Engage penetration tester**: Select qualified third-party (OSCP, GPEN, CEH)
8. **Compliance mapping**: Align to industry regulations (HIPAA, PCI DSS, SOC 2)

**Advanced Security Measures**:
- **Filesystem Sandboxing**: chroot, containers with read-only root
- **Seccomp**: Custom profiles blocking 95% of syscalls
- **Egress Filtering**: Allowlist-based network policy (Kubernetes NetworkPolicy, iptables)
- **Runtime Monitoring**: Falco rules for detecting anomalous behavior

**Tools**:
- Filesystem Isolation: Docker --read-only, chroot, bubblewrap
- Egress Control: Kubernetes NetworkPolicy, Cilium, AWS Security Groups
- Seccomp Profiles: docker-slim, oci-seccomp-bpf-hook
- Runtime Security: Falco, Sysdig Secure, Aqua Security
- SIEM: Splunk Enterprise Security, Elastic Security, QRadar

### Incremental Adoption

**Start with Level 1, upgrade as deployment context changes:**

```
Personal Project → Team Project → Customer-Facing → Regulated Industry
     Level 1     →   Level 2   →    Level 3     →      Level 4
```

**Progressive Compliance**:
- **Week 1**: Implement Level 1 (6 controls)
- **Month 1**: Add Level 2 controls (6 more)
- **Quarter 1**: Implement Level 3 authorization and logging (6 more)
- **Year 1+**: Upgrade to Level 4 for regulated deployments (6 more)

---

## Mixed-Level Deployments

### Motivation

Not all MCP tools within a server require the same security rigor. A server may have:
- High-risk write tools (filesystem modification) → Level 4
- Moderate-risk read tools (database queries) → Level 3
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
    "overall_level": "L3",
    "tools": [
      {
        "name": "read_file",
        "level": "L3",
        "rationale": "Accesses business-confidential documents"
      },
      {
        "name": "write_file",
        "level": "L4",
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
- `read_file` and `write_file` undergo Level 3/4 validation respectively
- `get_weather` validated to Level 1
- Server infrastructure must meet Level 4 (highest tool level)

---

## Level Comparison Matrix

### Quick Reference

| Aspect | Level 1 | Level 2 | Level 3 | Level 4 |
|--------|---------|---------|---------|---------|
| **Name** | Essential | Development | Production | Maximum Assurance |
| **Target** | Personal / Hobby | Internal / Team | Enterprise / Customers | Critical / Regulated |
| **Users** | Individual | Team (2-10) | Organization | Public / Regulated |
| **Data** | Public / Test | Internal | Business / PII | Regulated / PHI |
| **Impact** | Inconvenience | Dev delays | Disruption | Severe Harm |
| **Threats** | Opportunistic | Semi-targeted | Targeted | APT / Insider |
| **Controls** | 6 (25%) | 12 (50%) | 18 (75%) | 24 (100%) |
| **Validation** | Self-Assessment | Self + Scanning | Internal Audit | Third-Party Pentest |
| **Frequency** | Annual | Annual | Annual | Annual + Quarterly |
| **Cost** | Free | Low | Moderate | High |
| **Timeline** | 1-2 hours | 4-8 hours | 1-2 weeks | 4-8 weeks |

### Decision Tree Visual

```
START: Need to secure MCP server
         |
         v
  [ Who uses it? ]
         |
    +----+----+----+
    |    |    |    |
   Me   Team  Org  Public
    |    |    |    |
    v    v    v    v
  [L1]  [L2] [L3] [L4]
    |    |    |    |
    v    v    v    v
  [ What data? ]
    |
  (Upgrade if more sensitive than expected)
```

---

## References and Research

### Industry Standards Analyzed

This compliance level framework is informed by analysis of eight major security standards:

1. **NIST Cybersecurity Framework** (4 Implementation Tiers)
2. **CIS Controls v8** (3 Implementation Groups: IG1/IG2/IG3)
3. **CMMI** (5 Maturity Levels)
4. **ISO 27001** (Maturity models)
5. **PCI DSS v4.0** (4 Merchant Levels)
6. **OWASP ASVS v4.0** (3 Verification Levels: L1/L2/L3)
7. **FedRAMP** (3 Authorization Levels: Low/Moderate/High)
8. **COBIT** (4 Process Capability Levels)

### Key Insights Applied

- **NIST CSF**: 4-tier model provides good granularity
- **PCI DSS**: 4-level merchant categorization balances practicality with rigor
- **CIS Controls**: Clear organizational profiles (small/medium/large enterprises)
- **OWASP ASVS**: Cumulative control distribution pattern
- **FedRAMP**: Impact-based categorization (FIPS 199 framework)

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
- NIST CSF: https://www.nist.gov/cyberframework

---

## Revision History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | 2026-01-20 | Initial release (3 levels) | Dani |
| 2.0 | 2026-01-20 | Expanded to 4 levels for improved granularity | Dani |

---

**Document Status**: Official
**Maintained By**: MCP Security Standard Working Group
**Questions**: https://github.com/mcp-security-standard/msss/issues
