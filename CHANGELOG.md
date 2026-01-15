# Changelog

All notable changes to the MCP Server Security Standard will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed
- Reorganized repository structure with versioned directories (v0.1/)
- Enhanced README following OWASP ASVS professional format
- Added badges and improved visual presentation

### Planned for v0.2
- Community feedback integration
- Additional controls based on emerging threats
- Tool-specific guidance appendices
- Sample assessment report examples
- Automated compliance checker tool

## [0.1.0] - 2025-01-15

### Added

**Standard Documents**
- Core normative standard (`msss.md`) defining verification levels, domains, and evaluation framework
- Deployment profiles document defining 6 common deployment contexts with recommended levels
- Threat model document with assets, actors, attack surfaces, and threat scenarios

**Controls** (23 total)
- **FS (Filesystem)**: 3 controls
  - MCP-FS-01: Path allowlisting and canonical resolution (L1)
  - MCP-FS-02: Symlink resolution validation (L1)
  - MCP-FS-03: Filesystem sandboxing (L2)
  
- **EXEC (Execution)**: 3 controls
  - MCP-EXEC-01: Prohibition of shell execution (L1)
  - MCP-EXEC-02: Command allowlisting (L2)
  - MCP-EXEC-03: Argument separation with delimiters (L2)
  
- **NET (Network)**: 3 controls
  - MCP-NET-01: URL validation and SSRF mitigation (L1)
  - MCP-NET-02: Egress traffic filtering (L3)
  - MCP-NET-03: TLS enforcement (L2)
  
- **AUTHZ (Authorization)**: 4 controls
  - MCP-AUTHZ-01: OAuth token delegation (L2)
  - MCP-AUTHZ-02: Per-tool scope definition (L2)
  - MCP-AUTHZ-03: Least privilege tool design (L2)
  - MCP-AUTHZ-04: Resource-based access control (L2)
  
- **INPUT (Input Validation)**: 3 controls
  - MCP-INPUT-01: JSON schema validation (L1)
  - MCP-INPUT-02: Bounds checking (L2)
  - MCP-INPUT-03: Timeout enforcement (L2)
  
- **LOG (Logging)**: 2 controls
  - MCP-LOG-01: Comprehensive audit logging (L2)
  - MCP-LOG-02: Secret redaction in logs (L1)
  
- **SUPPLY (Supply Chain)**: 2 controls
  - MCP-SUPPLY-01: Package integrity verification (L2)
  - MCP-SUPPLY-02: Trusted registry requirement (L3)
  
- **DEPLOY (Deployment)**: 3 controls
  - MCP-DEPLOY-01: Container hardening (non-root, capabilities) (L2)
  - MCP-DEPLOY-02: Seccomp/AppArmor enforcement (L3)
  - MCP-DEPLOY-03: Resource limits (L3)

**Reporting Framework**
- JSON schema for assessment reports (`report.schema.json`)
- Common definitions schema (`definitions.schema.json`)
- Evidence types and control result structures

**Governance**
- Versioning policy following SemVer
- Contributing guidelines for community participation
- Trademark and certification policy
- Code of conduct (referenced, to be added)

**Internationalization**
- i18n framework and translation guidelines
- Language tracking (`languages.json`)
- English as authoritative language
- Planned support for ES, PT-BR, FR, DE, ZH-CN, JA

**Research Foundation**
- Research notes consolidating sources, failure patterns, and claims verification
- References to 40+ sources (CVEs, academic papers, blog posts, OWASP)
- Documented verification status of quantitative claims

### Context

This initial release is based on analysis of:
- 8 documented CVEs (CVE-2025-53109, CVE-2025-53110, CVE-2025-61492, CVE-2025-6514, etc.)
- OWASP MCP Top 10 (2025)
- 4 academic papers (MCPLIB, empirical MCP server study, attack vector research)
- 20+ security blog posts and incident reports
- Real-world security incidents from 2024-2025

### Known Limitations

As a v0.1 draft release:
- Controls are marked as "Draft" and may evolve based on community feedback
- No official certification program established yet
- Some controls have placeholder content and require community refinement
- Automated scanning tools not yet available
- Sample assessment reports pending

### Notes for Adopters

- This is a **community review draft**
- Feedback is actively sought on control requirements, verification guidance, and deployment profiles
- Organizations may self-assess but should not claim "certified" status until official program exists
- Report issues and suggestions via GitHub

---

## Release Notes Format

Future releases will follow this structure:

### Added
- New controls, features, or documentation

### Changed
- Updates to existing controls (clarifications, scope adjustments)

### Deprecated
- Controls or features marked for removal in next major version

### Removed
- Controls or features deleted (major version only)

### Fixed
- Corrections to errors or broken links

### Security
- Security-relevant changes or advisories

---

**Note**: Pre-1.0 versions (0.x.x) are subject to breaking changes in minor versions. After 1.0, semantic versioning will be strictly followed.

**Contributors**: MSSS v0.1 was developed through analysis of community research and security incidents. Individual contributors will be recognized as they engage with the project.

---
