# Contributing to MSSS

Thank you for your interest in improving the MCP Server Security Standard!

## Ways to Contribute

1. **Report issues**: Found an error, ambiguity, or security gap? Open an issue.
2. **Propose new controls**: Identify a missing threat? Suggest a control.
3. **Improve documentation**: Clarify wording, add examples, fix typos.
4. **Translate**: Help make MSSS accessible in other languages (see `/i18n/README.md`).
5. **Share experience**: Comment on how controls work in practice.

## Before You Start

- Review existing issues and PRs to avoid duplicates
- For major changes, open an issue first to discuss the approach
- Read the Code of Conduct (TBD - standard open source CoC)

## Proposing a New Control

New controls MUST meet these criteria:

1. **Addresses a verified threat**: Reference real-world incidents, CVEs, or research
2. **Is verifiable**: Provide clear, testable evidence requirements
3. **Non-duplicative**: Doesn't overlap significantly with existing controls
4. **Proportional**: Benefit justifies implementation cost
5. **Transport-agnostic** (preferred): Applies across stdio, HTTP, WebSocket unless inherently specific

### New Control Proposal Template

```markdown
## Proposed Control

**ID**: MCP-{DOMAIN}-{NEXT_NUMBER}-{slug}  
**Level**: L1/L2/L3  
**Domain**: FS/EXEC/NET/AUTHZ/INPUT/LOG/SUPPLY/DEPLOY

### Requirement
(One sentence using MUST/SHOULD/MAY)

### Rationale
(What threat does this mitigate? Why is it important?)

### References
- [Incident/CVE/Research]: URL
- [Supporting documentation]: URL

### Verification Approach
(How can this be tested?)

### Estimated Adoption Impact
(Low/Medium/High - how difficult for existing servers?)
```

Submit as a GitHub issue with the label `new-control`.

## Modifying Existing Controls

Changes to control requirements follow versioning rules:
- **Clarifications** (same pass/fail outcome): Minor version
- **Requirement changes** (affects pass/fail): Major version
- **Severity reduction** (e.g., MUST → SHOULD): Major version

Before proposing changes:
1. Explain why current control is inadequate
2. Demonstrate how change improves security
3. Consider impact on certified implementations

## Documentation Improvements

For typos, broken links, or clarity improvements:
1. Fork the repository
2. Make changes
3. Submit PR with clear description

Small fixes (< 10 lines) can go directly to PR. Larger rewrites should be discussed in an issue first.

## Translation Contributions

See `/i18n/README.md` for translation guidelines. Translations are always welcome and highly valued!

## Pull Request Process

1. **Fork** and create a branch: `git checkout -b feature/my-improvement`
2. **Make changes**: Follow existing document structure and style
3. **Test** (if applicable): Ensure JSON schemas validate, markdown renders correctly
4. **Commit**: Use clear commit messages
5. **Push**: `git push origin feature/my-improvement`
6. **Open PR**: Provide context and link related issues

### PR Description Template

```markdown
## Summary
(Brief description of changes)

## Motivation
(Why is this change needed?)

## Impact
(Does this affect existing controls? Certification requirements? Versioning?)

## Checklist
- [ ] Follows versioning policy
- [ ] Updated CHANGELOG.md (if applicable)
- [ ] No breaking changes (or justified in major version)
- [ ] Documentation updated
- [ ] Spell-checked and proofread
```

## Review Process

- All PRs require at least one approval from a maintainer
- For control changes: security domain expert review preferred
- For major changes: community feedback period (14-30 days)
- Maintainers may request changes or clarification

## Style Guide

- **Language**: Clear, concise, professional English
- **Normative language**: Use RFC 2119 keywords correctly
- **Headers**: Title case for main headers
- **Lists**: Use `-` for bullet lists
- **Code examples**: Use language-specific syntax highlighting
- **Links**: Use reference-style links for readability

## Recognition

Contributors are recognized in:
- CHANGELOG.md (for significant contributions)
- Control documents (if you authored/substantially revised)
- Translation documents (translator credit)

## Questions?

- Open an issue with the `question` label
- Join GitHub Discussions

---

**Version**: 0.1.0  
**Last Updated**: 2025-01-15
