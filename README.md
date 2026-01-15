# MCP Server Security Standard (MSSS)

**Version**: 0.1.0  
**Status**: Draft for Community Review  
**License**: CC BY 4.0 (standard text), Apache 2.0 (schemas), MIT (repository)

## Overview

The **MCP Server Security Standard (MSSS)** is an open, community-driven standard for securing implementations of the Model Context Protocol (MCP). It provides verifiable security controls across three maturity levels (L1, L2, L3) to help developers build, operators deploy, and assessors evaluate MCP servers.

MCP servers act as intermediaries between AI models and external systems, exposing tools, resources, and prompts. Without proper security controls, they can become vectors for code execution, data exfiltration, and privilege escalation.

## Why This Standard?

- **Real threats**: Multiple CVEs and incidents in 2024-2025 (command injection, path traversal, SSRF, supply chain attacks)
- **Rapid adoption**: MCP gaining traction but security guidance is fragmented
- **Verifiable controls**: Move beyond checklists to concrete, testable requirements
- **Risk-based levels**: Choose appropriate security rigor for your deployment context

## Quick Start

1. **Identify your deployment profile**: See `/standard/profiles.md`
   - Local Development → L1
   - Team Server → L2
   - Internet-Facing or High-Value Tools → L3

2. **Review the threat model**: `/standard/threat-model.md`

3. **Read the core standard**: `/standard/msss.md`

4. **Evaluate controls**: Browse `/controls/` and assess your implementation

5. **Generate a report**: Use `/reporting/report.schema.json` to document compliance

## Repository Structure

```
.
├── README.md                 # This file
├── CHANGELOG.md              # Version history
├── standard/
│   ├── msss.md               # Core normative standard (START HERE)
│   ├── profiles.md           # Deployment profiles and recommendations
│   └── threat-model.md       # Threat analysis and assumptions
├── controls/                 # Individual control documents (23 controls)
│   ├── MCP-FS-01-path-allowlisting.md
│   ├── MCP-EXEC-01-no-shell-execution.md
│   ├── MCP-NET-01-url-validation.md
│   ├── MCP-AUTHZ-01-oauth-delegation.md
│   ├── MCP-INPUT-01-schema-validation.md
│   ├── MCP-LOG-01-audit-logging.md
│   ├── MCP-SUPPLY-01-package-integrity.md
│   ├── MCP-DEPLOY-01-container-hardening.md
│   └── ... (and 15 more)
├── reporting/                # JSON schemas for assessment reports
│   ├── definitions.schema.json
│   └── report.schema.json
├── governance/               # Policies and procedures
│   ├── versioning.md
│   ├── contributing.md
│   └── trademark-and-certification-policy.md
├── i18n/                     # Internationalization
│   ├── README.md             # Translation guidelines
│   └── languages.json        # Supported languages
└── research/                 # Source research and notes
    └── notes.md
```

## Control Domains

MSSS organizes controls into 8 security domains:

| Domain | Code | Controls | Focus |
|--------|------|----------|-------|
| Filesystem | FS | 3 | Path traversal, symlinks, sandboxing |
| Execution | EXEC | 3 | Command injection, shell usage |
| Network | NET | 3 | SSRF, egress filtering, TLS |
| Authorization | AUTHZ | 4 | OAuth, scoping, least privilege, RBAC |
| Input Validation | INPUT | 3 | Schema validation, bounds, timeouts |
| Logging | LOG | 2 | Audit trails, secret redaction |
| Supply Chain | SUPPLY | 2 | Package integrity, trusted sources |
| Deployment | DEPLOY | 3 | Container hardening, isolation, resource limits |

**Total**: 23 controls (L1: 12, L2: 8, L3: 3)

## Verification Levels

- **L1 (Baseline)**: Essential protections against common, high-impact vulnerabilities. Suitable for development and low-risk deployments.
- **L2 (Standard)**: Comprehensive production-grade security with defense-in-depth. Recommended for team servers and internal deployments.
- **L3 (Advanced)**: Maximum assurance for high-risk environments, including penetration testing and formal audits. Required for internet-facing and high-value tool servers.

## Example Controls

### MCP-FS-01 — Path Allowlisting (L1)
> MCP servers that expose filesystem access tools MUST restrict file operations to explicitly allowed directories using canonical path resolution.

Prevents: Path traversal attacks (CVE-2025-53110)

### MCP-EXEC-01 — No Shell Execution (L1)
> MCP servers MUST NOT use shell invocation with user-controlled input. Use parameterized execution instead.

Prevents: Command injection (CVE-2025-61492, CVE-2025-5277)

### MCP-AUTHZ-01 — OAuth Token Delegation (L2)
> MCP servers exposed via HTTP/WebSocket MUST implement per-user authentication using OAuth 2.1, JWT, or equivalent.

Prevents: Shared credentials, cross-user data leakage

## Roadmap

### v0.1 (Current - Community Review)
- 23 core controls across 8 domains
- 3 verification levels
- Deployment profiles and threat model
- JSON reporting schemas

### v0.2 (Planned - Q2 2026)
- Community feedback integration
- Additional controls based on new incidents
- Tool-specific guidance (filesystem, database, cloud API tools)
- Sample assessment reports

### v1.0 (Target - Q3 2026)
- Stable specification
- Official certification program design
- Assessor training materials
- Automated scanning tool reference implementation

## Contributing

We welcome contributions! See [governance/contributing.md](governance/contributing.md) for:
- How to propose new controls
- Documentation improvements
- Translation efforts
- Reporting issues

## Community

- **Issues**: Report bugs, suggest improvements
- **Discussions**: Ask questions, share experiences
- **Pull Requests**: Submit changes (see contributing guide)

## Related Standards and Frameworks

MSSS aligns with and references:
- **OWASP MCP Top 10** (2025): High-level risk categories
- **OWASP ASVS**: Application Security Verification Standard
- **CWE**: Common Weakness Enumeration
- **NIST Cybersecurity Framework**: Informative references

## Translations

The English version is authoritative. Translations are welcomed and tracked in `/i18n/`.

**Available**: English  
**Planned**: Spanish, Portuguese (BR), French, German, Chinese (Simplified), Japanese

See [i18n/README.md](i18n/README.md) for translation guidelines.

## Citing MSSS

```bibtex
@techreport{msss2026,
  title = {MCP Server Security Standard (MSSS)},
  author = {{MSSS Community}},
  year = {2026},
  version = {0.1.0},
  url = {https://github.com/YOUR-ORG/mcp-security-standard},
  note = {Draft for community review}
}
```

## License

- **Standard text** (`/standard`, `/controls`, `/governance`): [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **JSON schemas** (`/reporting`): [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0)
- **Repository code**: [MIT](https://opensource.org/licenses/MIT)

See [governance/trademark-and-certification-policy.md](governance/trademark-and-certification-policy.md) for details.

## Disclaimer

This is v0.1 - a **draft** standard for community review. Requirements may change based on feedback. It is not yet suitable for formal certification claims.

Organizations using this standard do so at their own discretion. The maintainers provide no warranties or guarantees.

## Acknowledgments

This standard draws from:
- OWASP MCP Top 10 community contributors
- Security researchers who disclosed MCP vulnerabilities
- Academic papers on MCP security (Hou et al., MCPLIB, and others)
- Incident reports from Cymulate, Kaspersky, Trail of Bits, Snyk, and others

## Contact

- **Issues**: https://github.com/YOUR-ORG/mcp-security-standard/issues
- **Discussions**: https://github.com/YOUR-ORG/mcp-security-standard/discussions
- **Email**: (To be established)

---

**Built with** [Claude Code](https://claude.ai/code) **via** [Happy](https://happy.engineering)
