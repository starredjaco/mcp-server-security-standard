# MCP Server Security Standard (MSSS)

[![CC BY-SA 4.0][cc-by-sa-shield]][cc-by-sa]
[![GitHub release](https://img.shields.io/badge/release-v0.1.0-blue)](https://github.com/mcp-security-standard/mcp-server-security-standard/releases/latest)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen)](#how-to-contribute)

## About MSSS

The Model Context Protocol enables AI models to interact with external systems through tools, resources, and prompts. As adoption accelerates, critical vulnerabilities have emerged: command injection, path traversal, SSRF attacks, and supply chain compromises.

MSSS provides:
- **23 security controls** across 8 domains
- **3 maturity levels** (L1-Baseline, L2-Standard, L3-Advanced)
- **6 deployment profiles** (Local Dev, Team Server, Internet-Facing, etc.)
- **Evidence-based verification** with clear acceptance criteria
- **Machine-readable reporting** through JSON schemas

## Current Status - v0.1.0

Released: **January 15, 2025** (Community Review Draft)

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

## Control Catalog

### Filesystem (FS)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-FS-01](v0.1/controls/MCP-FS-01-path-allowlisting.md) | L1 | Path allowlisting to prevent unauthorized file access |
| [MCP-FS-02](v0.1/controls/MCP-FS-02-symlink-resolution.md) | L2 | Symlink resolution to prevent path traversal via symbolic links |
| [MCP-FS-03](v0.1/controls/MCP-FS-03-filesystem-sandboxing.md) | L3 | Filesystem sandboxing for complete isolation |

### Execution (EXEC)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-EXEC-01](v0.1/controls/MCP-EXEC-01-no-shell-execution.md) | L1 | Avoid shell execution to prevent command injection |
| [MCP-EXEC-02](v0.1/controls/MCP-EXEC-02-command-allowlisting.md) | L1 | Command allowlisting for permitted executables |
| [MCP-EXEC-03](v0.1/controls/MCP-EXEC-03-argument-separator.md) | L1 | Argument separation to prevent injection attacks |

### Network (NET)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-NET-01](v0.1/controls/MCP-NET-01-url-validation.md) | L1 | URL validation to prevent SSRF attacks |
| [MCP-NET-02](v0.1/controls/MCP-NET-02-egress-filtering.md) | L3 | Egress traffic filtering with destination allowlists |
| [MCP-NET-03](v0.1/controls/MCP-NET-03-tls-enforcement.md) | L2 | TLS 1.2+ enforcement for all remote connections |

### Authorization (AUTHZ)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-AUTHZ-01](v0.1/controls/MCP-AUTHZ-01-oauth-delegation.md) | L2 | OAuth 2.1 delegation for secure authentication |
| [MCP-AUTHZ-02](v0.1/controls/MCP-AUTHZ-02-tool-scopes.md) | L2 | Per-tool scope definition with granular permissions |
| [MCP-AUTHZ-03](v0.1/controls/MCP-AUTHZ-03-least-privilege.md) | L2 | Least privilege tool design principles |
| [MCP-AUTHZ-04](v0.1/controls/MCP-AUTHZ-04-rbac.md) | L2 | Resource-based access control (RBAC) |

### Input Validation (INPUT)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-INPUT-01](v0.1/controls/MCP-INPUT-01-schema-validation.md) | L1 | JSON Schema validation for all tool arguments |
| [MCP-INPUT-02](v0.1/controls/MCP-INPUT-02-bounds-checking.md) | L1 | Input bounds checking to prevent DoS attacks |
| [MCP-INPUT-03](v0.1/controls/MCP-INPUT-03-timeout-enforcement.md) | L2 | Timeout enforcement for resource exhaustion prevention |

### Logging (LOG)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-LOG-01](v0.1/controls/MCP-LOG-01-audit-logging.md) | L1 | Comprehensive audit logging for all tool invocations |
| [MCP-LOG-02](v0.1/controls/MCP-LOG-02-secret-redaction.md) | L1 | Automatic secret redaction in logs |

### Supply Chain (SUPPLY)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-SUPPLY-01](v0.1/controls/MCP-SUPPLY-01-package-integrity.md) | L1 | Package integrity verification with checksums |
| [MCP-SUPPLY-02](v0.1/controls/MCP-SUPPLY-02-trusted-sources.md) | L1 | Trusted package sources and registry verification |

### Deployment (DEPLOY)

| Control | Level | Description |
|---------|-------|-------------|
| [MCP-DEPLOY-01](v0.1/controls/MCP-DEPLOY-01-container-hardening.md) | L2 | Container hardening with security best practices |
| [MCP-DEPLOY-02](v0.1/controls/MCP-DEPLOY-02-seccomp-enforcement.md) | L3 | System call filtering via seccomp/AppArmor |
| [MCP-DEPLOY-03](v0.1/controls/MCP-DEPLOY-03-resource-limits.md) | L2 | Resource limits and rate limiting for DoS prevention |

**Total**: 23 controls across 8 security domains

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

### v0.2 (Q2 2025)
- Incorporate community feedback
- Add controls for emerging threats
- Publish reference implementations
- Launch translation program

### v1.0 (Q4 2025)
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
