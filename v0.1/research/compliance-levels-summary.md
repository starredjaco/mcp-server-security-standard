# Compliance Levels Research - Executive Summary

**Quick Reference Guide for MSSS Level Design**

---

## Key Findings at a Glance

### Industry Standard: 3-4 Levels
- **Most common**: 3 levels (CIS Controls, OWASP ASVS, FedRAMP)
- **Alternative**: 4 levels (NIST CSF, PCI DSS)
- **Less common**: 5 levels (CMMI, ISO maturity models) - typically for process maturity

### Recommended Approach: Risk-Based Selection
- **Best fit**: Choose level based on deployment context and risk profile
- **Avoid**: Maturity progression models (not all servers need to "mature")
- **Precedent**: CIS Controls and OWASP ASVS both use this approach successfully

### Control Distribution: Cumulative with 20-50-30 Pattern
| Level | Controls | Cumulative |
|-------|----------|------------|
| Level 1 | ~20-30% | ~25% |
| Level 2 | +50-60% | ~80% |
| Level 3 | +15-20% | 100% |

---

## Framework Comparison Matrix

| Framework | Levels | Philosophy | Distribution | Best Feature |
|-----------|--------|------------|--------------|--------------|
| **CIS Controls** | 3 (IG1/IG2/IG3) | Risk-based selection | 37% / 85% / 100% | Clear organization profiles |
| **OWASP ASVS** | 3 (L1/L2/L3) | Risk-based selection | 20% / 70% / 100% | Proven 20-50-30 split |
| **FedRAMP** | 3 (Low/Mod/High) | Impact-based | 30% / 76% / 100% | Clear harm-based criteria |
| **NIST CSF** | 4 Tiers | Process rigor | Same controls, different rigor | Separates controls from implementation |
| **PCI DSS** | 4 Levels | Transaction volume | All controls at all levels | Separates validation from controls |
| **CMMI** | 5 Levels | Process maturity | Not applicable | 5-stage evolution model |
| **ISO 27001** | 5 Levels (various) | Lifecycle maturity | All controls at all levels | ISMS lifecycle approach |

---

## Recommended MSSS Structure

### Three Levels with Clear Deployment Context

```
Level 1: Personal / Development
├─ Target: Individual developers, hobbyists, local environments
├─ Controls: ~6-8 controls (~25-30%)
├─ Validation: Self-assessment
└─ Use Cases: Personal tools, dev/test, non-sensitive data

Level 2: Team / Enterprise
├─ Target: Teams, departments, internal business apps
├─ Controls: ~17-20 controls (~80-85% cumulative)
├─ Validation: Internal audit + automated testing
└─ Use Cases: Collaboration servers, sensitive business data

Level 3: Critical / Public
├─ Target: Enterprise infrastructure, public services, regulated
├─ Controls: All ~23 controls (100%)
├─ Validation: Third-party assessment + penetration testing
└─ Use Cases: Public-facing, regulated data, high-value targets
```

---

## Selection Criteria (Quick Decision Tree)

### Ask These Questions:

1. **Who uses this MCP server?**
   - Just me → Level 1
   - My team/department → Level 2
   - External users or whole organization → Level 3

2. **What data does it access?**
   - Non-sensitive/public → Level 1
   - Business-confidential/PII → Level 2
   - Regulated data (PHI/PCI/classified) → Level 3

3. **What happens if compromised?**
   - Personal inconvenience → Level 1
   - Business disruption, reputation damage → Level 2
   - Financial loss, legal liability, safety risk → Level 3

4. **What's the threat model?**
   - Opportunistic attacks → Level 1
   - Targeted attacks → Level 2
   - Advanced persistent threats → Level 3

---

## Key Design Principles (Dos and Don'ts)

### DO:
- Make Level 1 a complete, defensible baseline for its context
- Use cumulative control distribution (each level builds on previous)
- Provide clear, objective criteria for level selection
- Allow flexibility for mixed-level implementations
- Ensure each level is a complete security posture

### DON'T:
- Create a maturity model requiring progression through levels
- Use transaction volume or org size as sole criterion
- Make Level 1 so minimal it's not a real baseline
- Distribute controls so Level 3 becomes impractical
- Name levels to stigmatize lower tiers ("Immature", "Incomplete")

---

## Control Distribution Examples

### From CIS Controls (3 levels, 18 controls)
**Example Control**: "Inventory and Control of Software Assets" (5 safeguards)
- **IG1**: 2 safeguards (40%)
- **IG2**: 4 safeguards (80%)
- **IG3**: 5 safeguards (100%)

### From OWASP ASVS (3 levels, ~286 requirements)
- **Level 1**: ~57 requirements (20%)
- **Level 2**: ~143 requirements (50%, cumulative ~200)
- **Level 3**: ~86 requirements (30%, cumulative 286)

### From FedRAMP (3 levels, NIST SP 800-53)
- **Low**: ~125 controls (29%)
- **Moderate**: ~325 controls (76%)
- **High**: ~425 controls (100%)

---

## Validation Methods (Separate from Control Requirements)

| Level | Validation Approach | Cost | Rigor |
|-------|---------------------|------|-------|
| Level 1 | Self-assessment questionnaire (SAQ) | Free | Basic |
| Level 2 | Internal audit + automated tools | Low | Moderate |
| Level 3 | Third-party assessment + pen testing | High | Comprehensive |

**Key Insight from PCI DSS**: All merchants implement the same 12 requirements; levels differ only in validation rigor. This keeps costs down for small deployments while ensuring credibility for critical ones.

---

## Flexibility Option (From OWASP ASVS)

**Allow mixed-level implementations**:
- Different MCP server components can target different levels
- Example: Filesystem tools at Level 3, read-only tools at Level 2
- Requires clear documentation of component-level compliance

---

## Proposed MSSS Level 1 Controls (Draft)

**Essential baseline for personal/development MCP servers** (~6-8 controls):

1. **MCP-FS-01**: Path Allowlisting
2. **MCP-EXEC-01**: No Shell Execution
3. **MCP-NET-01**: URL Validation
4. **MCP-INPUT-01**: Schema Validation
5. **MCP-INPUT-03**: Timeout Enforcement
6. **MCP-LOG-02**: Secret Redaction

**Rationale**: These 6 controls defend against the most common and severe vulnerabilities (command injection, path traversal, SSRF, DoS, credential leaks) with minimal implementation burden.

---

## Next Steps

1. Validate this approach with MSSS stakeholders
2. Map all 23 existing MSSS controls to 3 levels
3. Define detailed selection criteria and decision tree
4. Create self-assessment questionnaires (SAQs) for each level
5. Draft validation methodologies
6. Consider certification/badging program

---

## Quick Reference Links

- Full research document: `compliance-levels-research.md`
- Existing MSSS controls: `/controls/`
- MSSS threat model: `/standard/threat-model.md`
- MSSS profiles: `/standard/profiles.md`

---

**Document Status**: Draft Recommendation
**Date**: January 20, 2026
