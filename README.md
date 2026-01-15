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

## Control Domains

| Domain | Code | Controls | Description |
|--------|------|----------|-------------|
| **Filesystem** | FS | 3 | Path traversal prevention, allowlists, symlink handling |
| **Execution** | EXEC | 3 | Command injection prevention, sandboxing, allowlists |
| **Network** | NET | 3 | SSRF prevention, egress filtering, TLS enforcement |
| **Authorization** | AUTHZ | 4 | Authentication, tool scopes, least privilege, RBAC |
| **Input Validation** | INPUT | 3 | Schema validation, bounds checking, timeout enforcement |
| **Logging** | LOG | 2 | Audit logging, sensitive data redaction |
| **Supply Chain** | SUPPLY | 2 | Dependency pinning, trusted sources |
| **Deployment** | DEPLOY | 3 | Container hardening, seccomp, resource limits |

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
