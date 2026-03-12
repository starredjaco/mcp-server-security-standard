# MCP Server Security Standard (MSSS)

[![CC BY-SA 4.0][cc-by-sa-shield]][cc-by-sa]
[![GitHub release](https://img.shields.io/badge/release-v0.1.0-blue)](https://github.com/mcp-security-standard/mcp-server-security-standard/releases/latest)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen)](#how-to-contribute)

## About MSSS

The Model Context Protocol enables AI models to interact with external systems through tools, resources, and prompts. As adoption accelerates, critical vulnerabilities have emerged: command injection, path traversal, SSRF attacks, and supply chain compromises.

MSSS provides:
- **24 security controls** across 8 domains
- **4 compliance levels** (L1-Essential, L2-Development, L3-Production, L4-Maximum Assurance)
- **Risk-based level selection** framework inspired by NIST CSF, OWASP ASVS, and CIS Controls
- **6 deployment profiles** (Local Dev, Team Server, Internet-Facing, etc.)
- **Evidence-based verification** with clear acceptance criteria
- **Machine-readable reporting** through JSON schemas

## Compliant Platforms

The following platforms have adopted the MCP Server Security Standard:

| - | Platform | Description | Status |
|---|----------|-------------|--------|
| <a href="https://mcp-hub.info/"><img src="https://cdn.mcp-hub.info/logo.svg" height="24"></a> | [MCP Hub](https://mcp-hub.info/) | MCP server directory and marketplace — discover, publish, and manage MCP-compliant servers | ✅ Compliant |

> Are you implementing MSSS? [Open an issue](https://github.com/mcp-security-standard/mcp-server-security-standard/issues) or submit a PR to be listed here.

## Current Status - v0.1.0

Released: **January 15, 2026** (Community Review Draft)

### What's Included
- Core standard framework (msss.md)
- 6 deployment profiles defined
- Comprehensive threat model
- 23 fully documented security controls
- JSON reporting schemas
- i18n framework for translations

### Areas for Community Contribution
- Implementation examples for common frameworks
- Automated verification tools
- Reference assessment reports
- Translations to other languages
- Real-world testing and feedback

## Quick Start

### For Implementers
1. Review [deployment profiles](v0.1/standard/profiles.md) to find your scenario
2. Implement controls from [control catalog](v0.1/controls/)
3. Use [reporting schemas](v0.1/reporting/) for assessment
4. Share your experience via issues or discussions

### For Contributors
```bash
# Fork and clone
git clone https://github.com/YOUR-USERNAME/mcp-server-security-standard
cd mcp-server-security-standard

# Start a translation
mkdir -p v0.1/i18n/es/standard
```

### For Security Researchers
- Report vulnerabilities: security@mcp-security-standard.org
- Share attack patterns: Open an issue with `threat-research` label
- Propose new controls: See [contributing guide](v0.1/governance/contributing.md)

## Compliance Levels

MSSS defines **four compliance levels** using a **risk-based selection model** (not maturity progression). Organizations select their target level based on deployment context, data sensitivity, and potential impact.

### Level Selection Framework

| Level | Target Audience | Controls | Validation | Timeline |
|-------|----------------|----------|------------|----------|
| **L1: Essential** | Personal/Hobby | 6 (25%) | Self-assessment | 1-2 hours |
| **L2: Development** | Internal/Team | 12 (50%) | Self + scanning | 4-8 hours |
| **L3: Production** | Enterprise/Customers | 18 (75%) | Internal audit | 1-2 weeks |
| **L4: Maximum Assurance** | Critical/Regulated | 24 (100%) | Third-party pentest | 4-8 weeks |

### Quick Decision Guide

**Choose your level based on 4 key questions:**

1. **Who uses it?** Individual → L1 | Team → L2 | Organization/Customers → L3 | Public/Regulated → L4
2. **What data?** Public → L1 | Internal → L2 | Business/PII → L3 | Regulated (PHI/PCI) → L4
3. **Impact if compromised?** Inconvenience → L1 | Dev delays → L2 | Disruption → L3 | Severe harm → L4
4. **Threat model?** Opportunistic → L1 | Semi-targeted → L2 | Targeted → L3 | APT → L4

### Level Highlights

**Level 1 (Essential)**
- Essential protection for personal tools and hobby projects
- Prevents: Command injection, path traversal, SSRF, credential leaks
- Key controls: No shell execution, path allowlisting, URL validation, schema validation, secret redaction

**Level 2 (Development)**
- Security for development teams and internal tools
- Adds: TLS enforcement, input bounds, timeouts, command allowlisting, trusted sources
- Required for: Team projects, internal apps, pre-production environments

**Level 3 (Production)**
- Comprehensive security for enterprise and customer-facing applications
- Adds: OAuth authentication, RBAC, audit logging, container hardening
- Required for: SaaS products, customer data, business-confidential information

**Level 4 (Maximum Assurance)**
- Maximum hardening for critical infrastructure and regulated environments
- Adds: Filesystem sandboxing, egress filtering, seccomp/AppArmor, runtime monitoring
- Required for: HIPAA (healthcare), PCI DSS (payments), FedRAMP (government)

📖 **Full documentation**: See [Compliance Levels](v0.1/standard/compliance-levels.md) and [Control-Level Mapping](v0.1/standard/control-level-mapping.md)

### Regulatory Mappings

- **HIPAA** (Healthcare): Level 4 minimum for PHI access
- **PCI DSS** (Payments): Level 4 minimum for cardholder data
- **SOC 2** (SaaS): Level 3 minimum
- **ISO 27001**: Level 3 minimum for certification
- **FedRAMP**: Low→L3, Moderate/High→L4

## Control Catalog

### Filesystem (FS)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-FS-01](v0.1/controls/MCP-FS-01-path-allowlisting.md) | L1 | Path allowlisting to prevent unauthorized file access |
| [MCP-FS-02](v0.1/controls/MCP-FS-02-symlink-resolution.md) | L1 | Symlink resolution to prevent path traversal via symbolic links |
| [MCP-FS-03](v0.1/controls/MCP-FS-03-filesystem-sandboxing.md) | L4 | Filesystem sandboxing for complete isolation |

### Execution (EXEC)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-EXEC-01](v0.1/controls/MCP-EXEC-01-no-shell-execution.md) | L1 | Avoid shell execution to prevent command injection |
| [MCP-EXEC-02](v0.1/controls/MCP-EXEC-02-command-allowlisting.md) | L2 | Command allowlisting for permitted executables |
| [MCP-EXEC-03](v0.1/controls/MCP-EXEC-03-argument-separator.md) | L2 | Argument separation to prevent injection attacks |

### Network (NET)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-NET-01](v0.1/controls/MCP-NET-01-url-validation.md) | L1 | URL validation to prevent SSRF attacks |
| [MCP-NET-02](v0.1/controls/MCP-NET-02-egress-filtering.md) | L4 | Egress traffic filtering with destination allowlists |
| [MCP-NET-03](v0.1/controls/MCP-NET-03-tls-enforcement.md) | L2 | TLS 1.2+ enforcement for all remote connections |

### Authorization (AUTHZ)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-AUTHZ-01](v0.1/controls/MCP-AUTHZ-01-oauth-delegation.md) | L3 | OAuth 2.1 delegation for secure authentication |
| [MCP-AUTHZ-02](v0.1/controls/MCP-AUTHZ-02-tool-scopes.md) | L3 | Per-tool scope definition with granular permissions |
| [MCP-AUTHZ-03](v0.1/controls/MCP-AUTHZ-03-least-privilege.md) | L3 | Least privilege tool design principles |
| [MCP-AUTHZ-04](v0.1/controls/MCP-AUTHZ-04-rbac.md) | L3 | Resource-based access control (RBAC) |

### Input Validation (INPUT)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-INPUT-01](v0.1/controls/MCP-INPUT-01-schema-validation.md) | L1 | JSON Schema validation for all tool arguments |
| [MCP-INPUT-02](v0.1/controls/MCP-INPUT-02-bounds-checking.md) | L2 | Input bounds checking to prevent DoS attacks |
| [MCP-INPUT-03](v0.1/controls/MCP-INPUT-03-timeout-enforcement.md) | L2 | Timeout enforcement for resource exhaustion prevention |

### Logging (LOG)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-LOG-01](v0.1/controls/MCP-LOG-01-audit-logging.md) | L3 | Comprehensive audit logging for all tool invocations |
| [MCP-LOG-02](v0.1/controls/MCP-LOG-02-secret-redaction.md) | L1 | Automatic secret redaction in logs |

### Supply Chain (SUPPLY)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-SUPPLY-01](v0.1/controls/MCP-SUPPLY-01-package-integrity.md) | L4 | Package integrity verification with checksums |
| [MCP-SUPPLY-02](v0.1/controls/MCP-SUPPLY-02-trusted-sources.md) | L2 | Trusted package sources and registry verification |

### Deployment (DEPLOY)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-DEPLOY-01](v0.1/controls/MCP-DEPLOY-01-container-hardening.md) | L3 | Container hardening with security best practices |
| [MCP-DEPLOY-02](v0.1/controls/MCP-DEPLOY-02-seccomp-enforcement.md) | L4 | System call filtering via seccomp/AppArmor |
| [MCP-DEPLOY-03](v0.1/controls/MCP-DEPLOY-03-resource-limits.md) | L4 | Resource limits and rate limiting for DoS prevention |

**Total**: 24 controls across 8 security domains
- **Level 1**: 6 controls (25%) - Essential baseline
- **Level 2**: 12 controls (50%) - Development protection
- **Level 3**: 18 controls (75%) - Production security
- **Level 4**: 24 controls (100%) - Maximum assurance

## How to Contribute

We follow a simple process:

1. **Pick an area** - Check issues labeled `help-wanted` or `good-first-issue`
2. **Discuss** - Open an issue or join discussions before major work
3. **Submit** - Create a PR with clear description
4. **Iterate** - Address feedback from reviewers

### Priority Contributions

**HIGH PRIORITY**
- Add real-world implementation examples
- Create reference implementations for common frameworks
- Test controls against production deployments

**MEDIUM PRIORITY**
- Add profile-specific guidance
- Start Spanish, Portuguese, or other translations
- Develop automated verification tools

**ALWAYS WELCOME**
- Fix typos and improve clarity
- Add references to new CVEs or research
- Share implementation experiences

## Community

### Get Involved

- **Discussions**: [GitHub Discussions](https://github.com/mcp-security-standard/mcp-server-security-standard/discussions) - Ask questions, share ideas
- **Issues**: [GitHub Issues](https://github.com/mcp-security-standard/mcp-server-security-standard/issues) - Report bugs, request features

### Project Lead

- **Daniel García (cr0hn)** - [@cr0hn](https://github.com/cr0hn)
- **Dr. Alfonso Múñoz (Mindcrypt)** [Mindcrypt](https://github.com/mindcrypt)

**Looking for Co-Maintainers!** If you're passionate about MCP security and want to help shape this standard, please reach out.

### Recognition

All contributors will be recognized in:
- CHANGELOG.md for significant contributions
- Control documents you author or substantially improve

## Roadmap

### v0.1 (Current - Community Review)
- Gather feedback on 23 controls
- Validate against real-world deployments
- Collect implementation experiences

### v0.2 (Q2 2026)
- Incorporate community feedback
- Add controls for emerging threats
- Publish reference implementations
- Launch translation program

### v1.0 (Q4 2026)
- Stable specification
- Automated verification tools
- Certification program framework
- Training materials

## Related Standards

MSSS complements:
- [OWASP MCP Top 10](https://owasp.org/www-project-mcp-top-10/) - Risk categories
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) - Verification standard
- [CWE](https://cwe.mitre.org/) - Weakness enumeration
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework) - Risk management

## License

MSSS uses a multi-license approach:

| Component | License | Purpose |
|-----------|---------|---------|
| Standard Text | [CC BY-SA 4.0][cc-by-sa] | Free sharing with attribution |
| JSON Schemas | Apache 2.0 | Commercial tool integration |
| Code Examples | MIT | Maximum flexibility |

See the full license text in the [LICENSE](LICENSE) file.

## Support the Project

- **Star this repository** - Help others discover MSSS
- **Share with your network** - Spread awareness
- **Contribute** - Your expertise makes MSSS better

## Acknowledgments

MSSS builds upon:
- Security researchers who disclosed MCP vulnerabilities
- OWASP MCP Top 10 community
- Early adopters providing feedback
- Academic researchers (MCPLIB, Hou et al.)

---

**The MCP Server Security Standard is an open community project.** We provide this standard as-is without warranties. Use at your own discretion.

[cc-by-sa]: http://creativecommons.org/licenses/by-sa/4.0/
[cc-by-sa-shield]: https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg
