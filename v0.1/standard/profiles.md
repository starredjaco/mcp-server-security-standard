# Deployment Profiles

**Version**: 0.1.0  
**Status**: Draft  
**Last Updated**: 2025-01-15

## Overview

This document defines common MCP server deployment profiles and maps them to recommended MSSS verification levels. Profiles represent typical deployment contexts with distinct threat models, trust assumptions, and risk tolerances.

Organizations SHOULD select the profile that best matches their deployment context and apply the recommended level as a baseline. Additional controls or higher levels MAY be required based on specific risk assessments.

---

## Profile 1: Local Development

### Description

MCP servers running on individual developer workstations as part of IDE integrations or local AI assistants.

### Characteristics

- **Transport**: Typically stdio (spawned process)
- **Client**: Single local user (developer)
- **Network**: Usually no direct network access
- **Data**: Developer's local files, credentials, source code
- **Lifetime**: Short-lived, frequent restarts

### Trust Assumptions

- Developer is trusted operator
- OS provides user-level isolation
- No remote or multi-user access

### Primary Threats

- **Accidental file access outside project directories**
- **Command injection via malformed tool arguments**
- **Credential leakage through logs**
- **Supply chain compromise (malicious packages)**

### Recommended Level

**L1** (Baseline)

### Critical Controls

| Control ID | Requirement |
|------------|-------------|
| MCP-FS-01 | Path allowlisting to project directory |
| MCP-EXEC-01 | No shell execution with user input |
| MCP-INPUT-01 | JSON schema validation |
| MCP-LOG-02 | Secret redaction in logs |
| MCP-SUPPLY-01 | Package integrity verification |

### Deployment Guidance

- Run server with developer's OS user privileges (no escalation)
- Configure explicit allowed directories (avoid home directory root)
- Use package managers with lockfiles
- Enable basic logging for troubleshooting

---

## Profile 2: Team Server (Internal)

### Description

MCP servers deployed on shared internal infrastructure serving multiple team members within an organization.

### Characteristics

- **Transport**: HTTP/HTTPS within corporate network
- **Client**: Authenticated employees (10-1000 users)
- **Network**: Behind firewall, access to internal resources
- **Data**: Shared team data, internal APIs, codebases
- **Lifetime**: Long-lived service

### Trust Assumptions

- Users are authenticated employees
- Network is trusted (internal only)
- Infrastructure is managed by IT/DevOps
- Some users may lack security awareness

### Primary Threats

- **Unauthorized access to team resources**
- **Cross-user data leakage**
- **Privilege escalation within org**
- **SSRF to internal services**
- **Insufficient audit trail for compliance**

### Recommended Level

**L2** (Standard)

### Critical Controls

| Control ID | Requirement |
|------------|-------------|
| MCP-AUTHZ-01 | OAuth token delegation (per-user) |
| MCP-AUTHZ-02 | Per-tool scope definition |
| MCP-AUTHZ-04 | Resource-based access control |
| MCP-NET-01 | SSRF mitigation (URL validation) |
| MCP-NET-03 | TLS enforcement |
| MCP-LOG-01 | Comprehensive audit logging |
| MCP-DEPLOY-01 | Container hardening (non-root) |

### Deployment Guidance

- Deploy in containerized environment (Docker/Kubernetes)
- Integrate with SSO/IAM for authentication
- Implement per-user token delegation
- Enable comprehensive audit logging with user attribution
- Regular security patching and updates

---

## Profile 3: Internet-Facing API

### Description

MCP servers exposed to the public internet, serving external clients or third-party integrations.

### Characteristics

- **Transport**: HTTPS (public endpoint)
- **Client**: Unknown external users/systems
- **Network**: Public internet access
- **Data**: User-submitted data, public APIs, potentially sensitive resources
- **Lifetime**: Continuous availability required

### Trust Assumptions

- **Zero trust**: All clients are potentially adversarial
- Network is hostile
- Rate limiting and DDoS protection required
- Regulatory compliance may apply (GDPR, CCPA, etc.)

### Primary Threats

- **Unauthenticated access**
- **Brute force attacks**
- **Data exfiltration**
- **SSRF to internal infrastructure**
- **Supply chain attacks with wide impact**
- **Insufficient monitoring allowing undetected breaches**

### Recommended Level

**L3** (Advanced)

### Critical Controls

**All L1 + L2 controls** PLUS:

| Control ID | Requirement |
|------------|-------------|
| MCP-NET-02 | Egress traffic filtering |
| MCP-DEPLOY-02 | Seccomp/AppArmor enforcement |
| MCP-DEPLOY-03 | Resource limits (CPU/memory) |
| MCP-SUPPLY-02 | Trusted registry requirement |

### Additional Requirements

- Penetration testing (annual minimum)
- Incident response procedures
- Security monitoring/SIEM integration
- DDoS mitigation
- Rate limiting per client/IP
- Regular third-party security audits

### Deployment Guidance

- Deploy behind WAF (Web Application Firewall)
- Use API gateway with rate limiting
- Implement defense-in-depth: multiple isolation layers
- Separate network zones (DMZ architecture)
- Automated vulnerability scanning in CI/CD
- 24/7 security monitoring

---

## Profile 4: High-Value Tool Server

### Description

MCP servers exposing tools with significant privileges, such as database access, cloud infrastructure management, or financial transactions.

### Characteristics

- **Transport**: Any (risk independent of transport)
- **Client**: Trusted operators, potentially AI agents
- **Network**: Varies
- **Data**: Critical business data, privileged credentials
- **Lifetime**: Mission-critical availability

### Trust Assumptions

- Even authenticated users may make mistakes
- AI agents may be coerced via prompt injection
- Compromise has severe business impact
- Regulatory/compliance obligations

### Primary Threats

- **Privilege escalation (AI or user)**
- **Data destruction or tampering**
- **Financial fraud**
- **Compliance violations**
- **Insider threat (malicious or negligent)**

### Recommended Level

**L3** (Advanced)

### Critical Controls

**All L1 + L2 + L3 controls** PLUS:

- Multi-factor authentication for sensitive operations
- Human-in-the-loop approval for destructive actions
- Fine-grained RBAC with least privilege
- Change management with audit trails
- Anomaly detection on tool invocations

### Deployment Guidance

- Implement additional approval workflows for high-risk operations
- Use short-lived credentials with automatic rotation
- Separate read-only vs write tools
- Enable real-time alerting on anomalous behavior
- Regular compliance audits (SOC 2, ISO 27001, etc.)
- Formal incident response plan with defined SLAs

---

## Profile 5: Research/Sandbox Environment

### Description

MCP servers used for security research, testing, or controlled experimentation with untrusted tools or data.

### Characteristics

- **Transport**: Any
- **Client**: Researchers or security testers
- **Network**: Isolated test network
- **Data**: Non-production test data
- **Lifetime**: Ephemeral

### Trust Assumptions

- Tools and data may be intentionally malicious
- Goal is to observe behavior safely
- No production system connectivity

### Primary Threats

- **Sandbox escape**
- **Lateral movement to other research systems**
- **Accidental exposure of real credentials**

### Recommended Level

**L1 minimum** (with specialized isolation)

### Critical Controls

| Control ID | Requirement |
|------------|-------------|
| MCP-DEPLOY-01 | Container hardening |
| MCP-DEPLOY-02 | Seccomp/AppArmor enforcement |
| MCP-NET-02 | Egress traffic filtering (block all) |

### Additional Requirements

- Network isolation (no internet or production access)
- Ephemeral containers (destroyed after each test)
- Snapshot/rollback capabilities
- Monitoring for escape attempts

### Deployment Guidance

- Use dedicated hardware or air-gapped VMs
- Implement network policies blocking all egress
- Use forensic logging for behavioral analysis
- Regularly reset environment to known good state

---

## Profile 6: MCP Gateway / Proxy

### Description

Centralized gateway or proxy that mediates access to multiple backend MCP servers, providing unified authentication, authorization, and monitoring.

### Characteristics

- **Transport**: HTTP/HTTPS (frontend), any (backend)
- **Client**: Multiple users/services
- **Network**: Acts as trust boundary
- **Data**: Aggregates access to many tools/resources
- **Lifetime**: Critical infrastructure component

### Trust Assumptions

- Gateway is single point of control and failure
- Backend servers may vary in security posture
- Centralized logging and monitoring critical

### Primary Threats

- **Gateway compromise = broad access**
- **Insufficient authorization at gateway level**
- **Bypass via direct backend access**
- **Logging gaps in distributed system**

### Recommended Level

**L3** (Advanced)

### Critical Controls

**All L1 + L2 + L3 controls** PLUS:

- Gateway-level authentication and authorization
- Backend server identity verification
- Centralized audit logging across all servers
- Circuit breaker patterns for backend failures
- Security policy enforcement at gateway

### Deployment Guidance

- Harden gateway as critical infrastructure
- Implement mTLS for gateway-to-backend communication
- Use service mesh or API gateway patterns
- Centralized observability (logs, metrics, traces)
- Automated backend health checks
- Failover and redundancy

---

## Cross-Profile Considerations

### Compliance Requirements

Profiles MAY need to align with regulatory frameworks:

| Compliance Framework | Minimum Level | Applicable Profiles |
|---------------------|---------------|---------------------|
| GDPR (personal data) | L2 | Team Server, Internet-Facing |
| HIPAA (health data) | L3 | High-Value Tool Server |
| SOC 2 Type II | L2 | Team Server, Internet-Facing |
| PCI DSS (payment data) | L3 | High-Value Tool Server |
| ISO 27001 | L2 | All Production Profiles |

### Threat Model Alignment

Each profile assumes a specific threat model. Organizations SHOULD:

1. Review `/standard/threat-model.md` for detailed threat analysis
2. Customize threat assumptions based on specific deployment
3. Perform formal threat modeling for L3 profiles
4. Document deviations from profile assumptions

### Level Upgrade Triggers

Consider upgrading to a higher level if:

- Threat landscape changes (e.g., targeted attacks observed)
- Data sensitivity increases
- User base expands beyond initial assumptions
- Regulatory requirements change
- Security incidents occur

---

## Selecting a Profile

Use the following decision tree:

```
Is the server publicly accessible?
├─ YES → Profile 3: Internet-Facing API (L3)
└─ NO → Does it manage highly privileged tools?
    ├─ YES → Profile 4: High-Value Tool Server (L3)
    └─ NO → Is it used by multiple users?
        ├─ YES → Profile 2: Team Server (L2)
        └─ NO → Is it for local development only?
            ├─ YES → Profile 1: Local Development (L1)
            └─ NO → Is it acting as a gateway?
                ├─ YES → Profile 6: MCP Gateway (L3)
                └─ NO → Is it for security research?
                    ├─ YES → Profile 5: Research/Sandbox (L1+isolation)
                    └─ NO → Default to Profile 2 (L2)
```

---

## Custom Profiles

Organizations MAY define custom profiles by:

1. Starting with the closest standard profile
2. Documenting differences in assumptions or threats
3. Adjusting control requirements with justification
4. Submitting to the community for potential inclusion in future versions

See `/governance/contributing.md` for contribution procedures.

---

**End of Profiles Document**
