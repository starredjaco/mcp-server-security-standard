# MCP Server Security Standard (MSSS)

[![CC BY-SA 4.0][cc-by-sa-shield]][cc-by-sa]
[![GitHub release](https://img.shields.io/badge/release-v0.1.0-blue)](https://github.com/mcp-security-standard/mcp-server-security-standard/releases/latest)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen)](#contributing)

## 🎯 Welcome to the MCP Server Security Standard

The MCP Server Security Standard (MSSS) provides an open security standard for Model Context Protocol (MCP) servers. Born from real-world security incidents and community collaboration, MSSS offers developers, security teams, and assessors a comprehensive framework for building and evaluating secure MCP implementations.

## About MSSS

The Model Context Protocol enables AI models to interact with external systems through tools, resources, and prompts. As MCP adoption accelerates, the security landscape has revealed critical vulnerabilities: command injection, path traversal, SSRF attacks, and supply chain compromises. MSSS addresses these challenges through verifiable, risk-based security controls.

MSSS provides:
- **23 verifiable controls** across 8 security domains
- **3 maturity levels** (L1-Baseline, L2-Standard, L3-Advanced)
- **6 deployment profiles** tailored to common use cases
- **Evidence-based verification** with clear acceptance criteria
- **Machine-readable reporting** through JSON schemas

## Project Leaders

- **Daniel García (cr0hn)** - [@cr0hn](https://github.com/cr0hn) - Project Lead

## Current Stable Release - 0.1.0

The latest stable version is **v0.1.0** (January 2026). This is a community review draft.

### Downloads

- 📘 [Browse Online](v0.1/)
- 📄 PDF Version (Coming Soon)
- 📊 Control Matrix CSV (Coming Soon)
- 🔧 [JSON Schemas](v0.1/reporting/)

Development takes place in the `main` branch.

## Standard Structure

MSSS is organized by version for stability and clarity:

```
v0.1/
├── standard/
│   ├── msss.md               # Core normative standard
│   ├── profiles.md           # Deployment profiles
│   └── threat-model.md       # Threat analysis
├── controls/                 # Individual control specifications
├── reporting/                # Assessment report schemas
├── governance/               # Policies and procedures
└── i18n/                     # Internationalization
```

## Using the Standard

### For Developers
Start with the [deployment profiles](v0.1/standard/profiles.md) to identify your security level, then implement controls from the [control catalog](v0.1/controls/).

### For Security Teams
Use the [threat model](v0.1/standard/threat-model.md) to understand attack surfaces, then leverage the [reporting schemas](v0.1/reporting/) for assessments.

### For Assessors
Follow the [core standard](v0.1/standard/msss.md) for evaluation methodology and use evidence requirements from individual controls.

## Control Domains

| Domain | Code | # Controls | Primary Focus |
|--------|------|------------|---------------|
| **Filesystem** | FS | 3 | Path traversal, symlink bypass, sandboxing |
| **Execution** | EXEC | 3 | Command injection, shell security |
| **Network** | NET | 3 | SSRF, egress control, transport security |
| **Authorization** | AUTHZ | 4 | Authentication, scoping, RBAC |
| **Input Validation** | INPUT | 3 | Schema enforcement, bounds checking |
| **Logging** | LOG | 2 | Audit trails, secret redaction |
| **Supply Chain** | SUPPLY | 2 | Package integrity, dependency security |
| **Deployment** | DEPLOY | 3 | Container hardening, isolation |

## Verification Levels

### Level 1 (L1) - Baseline
Essential security for development environments and low-risk deployments. Focus on preventing common, high-impact vulnerabilities.

### Level 2 (L2) - Standard
Production-grade security with defense-in-depth. Required for team servers, internal deployments, and moderate-risk scenarios.

### Level 3 (L3) - Advanced
Maximum assurance for internet-facing services, high-value tools, and regulated environments. Includes formal verification and continuous monitoring.

## How to Reference MSSS

When referencing MSSS requirements, use the format:

```
MSSS-v0.1-{CONTROL-ID}
```

For example:
- `MSSS-v0.1-MCP-FS-01` (Path allowlisting requirement)
- `MSSS-v0.1-MCP-EXEC-01` (No shell execution requirement)

This format ensures clarity across versions and prevents ambiguity in compliance documentation.

## Translations

MSSS maintains English as the authoritative language. Community translations expand accessibility:

| Language | Status | Contributors |
|----------|--------|--------------|
| English | ✅ Complete | Core Team |
| Spanish | 🔄 Planned | - |
| Portuguese (BR) | 🔄 Planned | - |
| French | 🔄 Planned | - |
| German | 🔄 Planned | - |
| Chinese (Simplified) | 🔄 Planned | - |
| Japanese | 🔄 Planned | - |

See [translation guidelines](v0.1/i18n/README.md) to contribute.

## Contributing

MSSS thrives on community contributions. We welcome:

- 🐛 **Bug Reports** - Identify errors or ambiguities
- 💡 **Feature Requests** - Propose new controls or improvements
- 📝 **Documentation** - Enhance clarity and examples
- 🌍 **Translations** - Expand language accessibility
- 🔍 **Security Research** - Share vulnerability findings

See [CONTRIBUTING.md](v0.1/governance/contributing.md) for guidelines.

## Related Projects

MSSS complements existing security frameworks:

- [OWASP MCP Top 10](https://owasp.org/www-project-mcp-top-10/) - Risk categories for MCP systems
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) - Application security verification
- [CWE](https://cwe.mitre.org/) - Common weakness enumeration
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework) - Risk management guidance

## Roadmap

### Version 0.1 (Current)
- ✅ Initial 23 controls
- ✅ 3 verification levels
- ✅ 6 deployment profiles
- ✅ JSON reporting schemas

### Version 0.2 (Q2 2026)
- 🔄 Community feedback integration
- 🔄 Additional controls from emerging threats
- 🔄 Tool-specific implementation guidance
- 🔄 Reference assessment reports

### Version 1.0 (Q3 2026)
- 📋 Stable specification
- 📋 Certification program framework
- 📋 Automated verification tools
- 📋 Training materials

## License

MSSS uses a multi-license approach for maximum compatibility:

| Component | License | Usage |
|-----------|---------|-------|
| Standard Text | [CC BY-SA 4.0][cc-by-sa] | Share and adapt with attribution |
| JSON Schemas | [Apache 2.0](LICENSE-APACHE) | Use in commercial tools |
| Repository Code | [MIT](LICENSE-MIT) | Maximum flexibility |

## Acknowledgments

MSSS builds upon the work of security researchers, incident responders, and the MCP community:

- CVE researchers (Cymulate, Kaspersky, Trail of Bits, Snyk)
- OWASP MCP Top 10 contributors
- Academic researchers (Hou et al., MCPLIB team)
- Early adopters providing feedback

## Contact

- 📬 **Issues**: [GitHub Issues](https://github.com/mcp-security-standard/mcp-server-security-standard/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/mcp-security-standard/mcp-server-security-standard/discussions)
- 📧 **Security**: security@mcp-security-standard.org (Coming Soon)

---

**The MCP Server Security Standard is an open community project. Neither the contributors, nor their employers, nor any other party involved in creating, producing, or delivering MSSS shall be liable for any damages arising from use of this standard.**

[cc-by-sa]: http://creativecommons.org/licenses/by-sa/4.0/
[cc-by-sa-shield]: https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg