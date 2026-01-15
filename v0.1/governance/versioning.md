# Versioning Policy

**Version**: 0.1.0  
**Status**: Draft  
**Last Updated**: 2025-01-15

## Overview

The MCP Server Security Standard (MSSS) follows Semantic Versioning (SemVer) principles with adaptations for a normative security standard.

## Version Format

Versions follow the pattern: `MAJOR.MINOR.PATCH`

Example: `0.1.0`, `1.2.3`

### Major Version (X.0.0)

Incremented when:
- Control requirements fundamentally change (e.g., a MUST becomes NOT_APPLICABLE)
- Verification level definitions are restructured
- Control IDs are renumbered or removed without deprecation period
- Breaking changes that invalidate prior certifications

**Impact**: Organizations certified under v1.x are NOT automatically compliant with v2.0. Re-evaluation required.

**Frequency**: Rare; expect 1-2 years between major versions.

### Minor Version (0.X.0)

Incremented when:
- New controls are added
- Clarifications to existing controls (without changing pass/fail criteria)
- New domains or profiles are introduced
- Non-breaking enhancements to schemas or documentation

**Impact**: Organizations certified under v1.2 should largely comply with v1.3, but new controls must be evaluated.

**Frequency**: 2-4 times per year based on community feedback and emerging threats.

### Patch Version (0.0.X)

Incremented when:
- Typos or grammatical corrections
- Link updates or reference corrections
- Schema formatting fixes (non-functional)
- Documentation improvements without requirement changes

**Impact**: No impact on certification status.

**Frequency**: As needed.

## Pre-1.0 Versions (0.x.x)

Versions before 1.0.0 are considered **draft** or **beta** and MAY include breaking changes in minor versions. Organizations using v0.x SHOULD expect frequent updates and are encouraged to provide feedback.

## Version Lifecycle

### Draft
- Versions 0.1.0 - 0.9.x
- Community review and feedback period
- Requirements may change based on real-world testing

### Stable
- Version 1.0.0 and above
- Breaking changes only in major versions
- Predictable update schedule

### Deprecated
- Controls marked for removal in next major version
- At least one minor version warning before deletion
- Deprecated controls remain valid for certification until next major version

## Control Stability

Each control MAY have a stability indicator:

| Indicator | Meaning | Lifecycle |
|-----------|---------|-----------|
| **Draft** | New control under evaluation | May change in minor versions (pre-1.0 only) |
| **Stable** | Mature control unlikely to change | Changes only in major versions |
| **Deprecated** | Scheduled for removal | Will be removed in next major version |

All controls in v0.1 are considered **Draft**.

## Deprecation Process

1. **Announcement** (Minor version N): Control marked as deprecated in documentation and control file
2. **Migration Period** (Minor versions N+1, N+2, ...): Control remains valid but warnings issued
3. **Removal** (Next major version): Control removed from standard

Example:
- v1.3.0: MCP-OLD-01 marked deprecated
- v1.4.0, v1.5.0: MCP-OLD-01 still valid but deprecated warnings
- v2.0.0: MCP-OLD-01 removed; replacement control (or rationale for removal) documented

## Backward Compatibility

- **Major versions**: No backward compatibility guarantee
- **Minor versions**: Additive only (new controls, clarifications)
- **Patch versions**: Fully backward compatible

## Version History

MSSS maintains a comprehensive changelog in `/CHANGELOG.md` following Keep a Changelog format.

## Release Schedule

- **Minor releases**: Quarterly (Jan, Apr, Jul, Oct) or as needed for critical updates
- **Patch releases**: As needed
- **Major releases**: When accumulated changes justify breaking compatibility (typically 1-2 years)

## Emergency Updates

In case of critical vulnerabilities or incidents requiring immediate standard updates:
- Emergency patch release issued within 7 days
- Expedited review process
- Announcement via GitHub releases and mailing list

## Community Input

Version planning happens transparently:
- Proposed changes discussed in GitHub Issues
- Draft changes reviewed in Pull Requests
- Community feedback period before finalizing minor/major versions (minimum 30 days for major, 14 days for minor)

## Notification

Users can subscribe to updates via:
- GitHub Watch (Releases only)
- RSS feed of releases
- Mailing list (to be established)

---

For questions about versioning, open an issue with the `versioning` label.
