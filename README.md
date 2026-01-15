# MCP Server Security Standard (MSSS)

[![CC BY-SA 4.0][cc-by-sa-shield]][cc-by-sa]
[![GitHub release](https://img.shields.io/badge/release-v0.1.0-blue)](https://github.com/mcp-security-standard/mcp-server-security-standard/releases/latest)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen)](#help-us-build-msss)
[![Discord Community](https://img.shields.io/badge/Discord-Join%20Community-7289da)](https://discord.gg/mcp-security)

## 🚀 Help Us Build the MCP Security Standard!

**The MCP ecosystem is growing rapidly, but security guidance is fragmented. We need YOUR expertise to build the definitive security standard for MCP servers.**

MSSS is an open, community-driven initiative to establish security baselines for Model Context Protocol implementations. With recent vulnerabilities (CVE-2025-53109, CVE-2025-53110, and others) demonstrating real risks, we're creating verifiable controls to protect MCP deployments.

### 🎯 Why We Need You

- **11 controls need completion** - Help us finish draft controls for INPUT, AUTHZ, and DEPLOY domains
- **Real-world testing needed** - Apply MSSS to your MCP servers and share feedback
- **Translations wanted** - Make MSSS accessible globally (Spanish, Portuguese, French, German, Chinese, Japanese)
- **Security research** - Share vulnerabilities and attack patterns you've discovered
- **Implementation examples** - Contribute secure code patterns and configurations

## About MSSS

The Model Context Protocol enables AI models to interact with external systems through tools, resources, and prompts. As adoption accelerates, critical vulnerabilities have emerged: command injection, path traversal, SSRF attacks, and supply chain compromises.

MSSS provides:
- **23 security controls** across 8 domains (12 complete, 11 need community input)
- **3 maturity levels** (L1-Baseline, L2-Standard, L3-Advanced)
- **6 deployment profiles** (Local Dev, Team Server, Internet-Facing, etc.)
- **Evidence-based verification** with clear acceptance criteria
- **Machine-readable reporting** through JSON schemas

## 🔥 Critical Areas Needing Help

### 1. Complete Draft Controls
These controls have placeholders and need expert input:
- **INPUT domain**: Schema validation, bounds checking, timeout enforcement
- **AUTHZ domain**: Tool scopes, least privilege, RBAC
- **DEPLOY domain**: Seccomp enforcement, resource limits
- **NET domain**: Egress filtering, TLS enforcement

### 2. Profile Standardization
Help us map deployment profiles to control requirements:
- Define which controls are mandatory vs. optional per profile
- Add profile-specific implementation guidance
- Share your deployment patterns

### 3. Threat Model Validation
- Review our threat scenarios against real attacks
- Add missing attack vectors
- Validate our risk ratings

## Current Status - v0.1.0

Released: **January 15, 2025** (Community Review Draft)

### What's Ready
✅ Core standard framework (msss.md)
✅ 6 deployment profiles defined
✅ Comprehensive threat model
✅ 12 fully documented controls
✅ JSON reporting schemas
✅ i18n framework

### What Needs Work
🚧 11 control specifications (have structure, need content)
🚧 Implementation examples
🚧 Automated verification tools
🚧 Reference assessment reports
🚧 Translations to other languages

## Quick Start

### For Contributors
```bash
# Fork and clone
git clone https://github.com/YOUR-USERNAME/mcp-server-security-standard
cd mcp-server-security-standard

# Pick a draft control to complete
ls v0.1/controls/*placeholder*

# Or start a translation
mkdir -p v0.1/i18n/es/standard
```

### For Implementers
1. Review [deployment profiles](v0.1/standard/profiles.md) to find your scenario
2. Implement controls from [control catalog](v0.1/controls/)
3. Use [reporting schemas](v0.1/reporting/) for assessment
4. Share your experience via issues or discussions

### For Security Researchers
- Report vulnerabilities: security@mcp-security-standard.org
- Share attack patterns: Open an issue with `threat-research` label
- Propose new controls: See [contributing guide](v0.1/governance/contributing.md)

## Control Domains

| Domain | Code | Controls | Status | Help Needed |
|--------|------|----------|--------|-------------|
| **Filesystem** | FS | 3 | ✅ Complete | Review & test |
| **Execution** | EXEC | 3 | ✅ Complete | Implementation examples |
| **Network** | NET | 3 | ⚠️ 1 complete, 2 draft | Complete NET-02, NET-03 |
| **Authorization** | AUTHZ | 4 | ⚠️ 1 complete, 3 draft | Complete AUTHZ-02/03/04 |
| **Input Validation** | INPUT | 3 | 🚧 All draft | Write all 3 controls |
| **Logging** | LOG | 2 | ✅ Complete | Review & test |
| **Supply Chain** | SUPPLY | 2 | ⚠️ 1 complete, 1 draft | Complete SUPPLY-02 |
| **Deployment** | DEPLOY | 3 | ⚠️ 1 complete, 2 draft | Complete DEPLOY-02/03 |

**Total**: 23 controls (12 complete, 11 need your help!)

## How to Contribute

We follow a simple process:

1. **Pick an area** - Check issues labeled `help-wanted` or `good-first-issue`
2. **Discuss** - Open an issue or join discussions before major work
3. **Submit** - Create a PR with clear description
4. **Iterate** - Address feedback from reviewers

### Priority Contributions

🔴 **HIGH PRIORITY**
- Complete INPUT-01, INPUT-02, INPUT-03 controls
- Add real-world examples to existing controls
- Create reference implementation for common frameworks

🟡 **MEDIUM PRIORITY**
- Complete remaining AUTHZ and DEPLOY controls
- Add profile-specific guidance
- Start Spanish or Portuguese translations

🟢 **ALWAYS WELCOME**
- Fix typos and improve clarity
- Add references to new CVEs or research
- Share implementation experiences

## Community

### Get Involved

- 💬 **Discussions**: [GitHub Discussions](https://github.com/mcp-security-standard/mcp-server-security-standard/discussions) - Ask questions, share ideas
- 🐛 **Issues**: [GitHub Issues](https://github.com/mcp-security-standard/mcp-server-security-standard/issues) - Report bugs, request features
- 🎮 **Discord**: [Join our Discord](https://discord.gg/mcp-security) (Coming soon) - Real-time chat
- 📧 **Email**: community@mcp-security-standard.org (Coming soon)

### Contributors

**Project Lead**
- **Daniel García (cr0hn)** - [@cr0hn](https://github.com/cr0hn)

**Looking for Co-Maintainers!** If you're passionate about MCP security and want to help shape this standard, please reach out.

### Recognition

All contributors will be recognized in:
- CHANGELOG.md for significant contributions
- Control documents you author or substantially improve
- Annual contributor report (planned)

## Roadmap

### v0.1 (Current - Community Review)
- Gather feedback on initial 23 controls
- Complete draft controls with community input
- Validate against real-world deployments

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

- ⭐ **Star this repository** - Help others discover MSSS
- 📣 **Share with your network** - Spread awareness
- 💼 **Corporate sponsorship** - Contact us for sponsorship opportunities
- 🤝 **Contribute** - Your expertise makes MSSS better

## Acknowledgments

MSSS builds upon:
- Security researchers who disclosed MCP vulnerabilities
- OWASP MCP Top 10 community
- Early adopters providing feedback
- Academic researchers (MCPLIB, Hou et al.)

---

**Join us in securing the MCP ecosystem!** Every contribution, no matter how small, helps make MCP servers more secure for everyone.

**The MCP Server Security Standard is an open community project.** We provide this standard as-is without warranties. Use at your own discretion.

[cc-by-sa]: http://creativecommons.org/licenses/by-sa/4.0/
[cc-by-sa-shield]: https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg