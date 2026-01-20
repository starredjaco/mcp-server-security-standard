# Compliance Levels & Maturity Models Research

**Date**: January 20, 2026
**Purpose**: Research major security standards and frameworks to inform the design of a compliance level system for the MCP Server Security Standard (MSSS).

---

## Executive Summary

This document analyzes how seven major security standards and frameworks implement compliance levels, maturity models, and implementation tiers. The research covers:

1. NIST Cybersecurity Framework (CSF) Implementation Tiers
2. CIS Controls Implementation Groups (IG1, IG2, IG3)
3. CMMI (Capability Maturity Model Integration)
4. ISO 27001 Maturity Models
5. PCI DSS Compliance Levels
6. OWASP ASVS (Application Security Verification Standard)
7. FedRAMP Authorization Levels

Key findings:
- **3-5 levels** is the industry standard (most use 3-4 levels)
- **Cumulative/staged approaches** are most common (each level builds on the previous)
- **Risk-based differentiation** is used to assign organizations to appropriate levels
- **Control distribution** typically follows 20-50-30 or similar progressive ratios
- **Implementation philosophy** varies: maturity progression vs. risk-based selection vs. transaction volume

---

## 1. NIST Cybersecurity Framework (CSF)

### Number of Levels
**4 Implementation Tiers**

### Level Definitions

| Tier | Name | Description |
|------|------|-------------|
| **Tier 1** | Partial | Cybersecurity risk management is reactive, with inconsistent or ad hoc approaches. No unified risk management across departments. |
| **Tier 2** | Risk-Informed | Organization is aware of cybersecurity risks and has some processes in place but lacks comprehensive and unified risk management across all departments. |
| **Tier 3** | Repeatable | Cybersecurity practices are consistent and repeatable across the organization, with established processes that are regularly updated and adapted to address emerging risks. |
| **Tier 4** | Adaptive | Organizations use advanced technology and real-time insights to proactively manage cybersecurity risks. Highest maturity level. |

### Philosophy & Rationale

- **Not a maturity model**: NIST explicitly states these are benchmarking tools and direction indicators, not maturity levels
- **Three evaluation dimensions**: Each tier is broken down into:
  - Risk Management Processes
  - Risk Management Program
  - External Participation
- **Self-selected**: Organizations choose their target tier based on risk profile, resources, and threat environment
- **No mandate for progression**: Organizations don't need to achieve Tier 4; should select the tier appropriate to their risk tolerance

### Control Distribution

The NIST CSF does not distribute controls across tiers. Instead, all organizations implement the same core functions (GOVERN, IDENTIFY, PROTECT, DETECT, RESPOND, RECOVER), but the **rigor and formality** of implementation differs by tier.

### Determination Criteria

Organizations determine their tier through self-assessment based on:
- Formalization of cybersecurity risk management processes
- Integration of cybersecurity into enterprise risk management
- External participation and information sharing
- Consistency and repeatability of practices

### Relevance to MCP Standard

**Applicable**: The tier concept could work for MCP servers where the same controls apply but with different implementation rigor (e.g., manual vs. automated, reactive vs. proactive).

**Not directly applicable**: MCP servers may need prescriptive control requirements rather than process maturity assessment.

---

## 2. CIS Controls Implementation Groups

### Number of Levels
**3 Implementation Groups (IG1, IG2, IG3)**

### Level Definitions

| Group | Safeguards | Description | Typical Organization |
|-------|-----------|-------------|---------------------|
| **IG1** | 56 safeguards | Essential cyber hygiene. Foundational set of cyber defense safeguards implementable with limited cybersecurity expertise. Aimed to thwart general, non-targeted attacks. | Small to medium-sized organizations with limited IT and cybersecurity expertise. Low sensitivity data (employee and financial information). |
| **IG2** | 130 safeguards (56 from IG1 + 74 new) | Helps security teams deal with increased operational complexity. Defends against more sophisticated attacks. | Medium-scale organizations with dedicated cybersecurity teams. Store sensitive client and organizational information. |
| **IG3** | 153 safeguards (all controls) | Comprehensive security implementation for mature organizations. Defends against targeted attacks from sophisticated adversaries and reduces zero-day impact. | Large organizations with highly sensitive data. Advanced cybersecurity services, penetration testing, security audits. |

### Philosophy & Rationale

- **Progressive layering**: IG2 builds upon IG1; IG3 comprises all controls
- **Risk-based selection**: Organizations self-select based on:
  - Size and/or complexity
  - Data types and sensitivity
  - Available resources and technology
  - Threat landscape
  - Risk tolerance
- **Practical implementation focus**: Each IG represents a complete, defensible security posture for that organization profile
- **Iterative approach**: Implementation is not one-time; it's continuous improvement

### Control Distribution

**Example from CIS Control: Inventory and Control of Software Assets (5 safeguards total)**
- **IG1**: Implements 2 of 5 safeguards (40%)
- **IG2**: Implements 4 of 5 safeguards (80%)
- **IG3**: Implements all 5 safeguards (100%)

**Overall distribution**:
- **IG1**: 56 safeguards (37% of total)
- **IG2**: 74 additional safeguards (48% increment)
- **IG3**: 23 additional safeguards (15% increment)

### Determination Criteria

Organizations select their IG based on:
1. **Size and resources**: IT staff count, security team size, budget
2. **Data sensitivity**: Type and classification of data handled
3. **Threat profile**: Likelihood of targeted vs. opportunistic attacks
4. **Regulatory requirements**: Industry-specific mandates
5. **Risk appetite**: Board/executive tolerance for risk

### Relevance to MCP Standard

**Highly applicable**: The IG model is excellent for MCP servers because:
- Clear organization profiles (e.g., personal/hobby servers vs. enterprise MCP infrastructure)
- Cumulative safeguards ensure IG1 is a complete baseline
- Control-by-control leveling allows fine-grained assignment

**Recommended approach**: Consider adapting the IG philosophy with 3 levels based on MCP server deployment context.

---

## 3. CMMI (Capability Maturity Model Integration)

### Number of Levels
**5 Maturity Levels**

### Level Definitions

| Level | Name | Description |
|-------|------|-------------|
| **Level 1** | Initial | Processes are unpredictable, poorly controlled, and reactive. Success relies on individual effort rather than defined processes. Work is often delayed and over budget. |
| **Level 2** | Managed | Processes are planned, documented, performed, monitored, and controlled at the project level. Basic project management practices established. |
| **Level 3** | Defined | Processes are well characterized and described using standards, procedures, and methods. Organization-wide standards provide guidance. Proactive rather than reactive. |
| **Level 4** | Quantitatively Managed | Organizations are data-driven with quantitative performance metrics. Processes are measured and controlled statistically. |
| **Level 5** | Optimizing | Focus on continuous process improvement through incremental enhancements and innovative breakthroughs. Culture of learning and adaptation. |

### Philosophy & Rationale

- **Process maturity focus**: CMMI measures organizational process capability, not technical controls
- **Staged path**: Each level builds on the previous, adding new functionality or rigor
- **Predefined practice areas**: Specific process areas must be satisfied at each level
- **From reactive to proactive to optimizing**: Natural evolution from ad-hoc to continuously improving

### Control Distribution

CMMI doesn't distribute technical security controls. Instead, it defines **process areas** that must be institutionalized:
- **Level 2**: 7 process areas (e.g., Requirements Management, Project Planning)
- **Level 3**: 11 additional process areas (e.g., Risk Management, Decision Analysis)
- **Level 4**: 2 additional process areas (Organizational Process Performance, Quantitative Project Management)
- **Level 5**: 2 additional process areas (Organizational Innovation, Causal Analysis and Resolution)

### Determination Criteria

Organizations are appraised against maturity levels through formal assessment:
- **Appraisal method**: SCAMPI (Standard CMMI Appraisal Method for Process Improvement)
- **Evidence collection**: Documentation, interviews, work products
- **Institutionalization**: Practices must be embedded in organizational culture

### Relevance to MCP Standard

**Limited direct applicability**: CMMI is about process maturity, not technical security controls.

**Indirect value**: The 5-level progression (reactive → managed → defined → measured → optimized) could inspire a maturity dimension for MCP server development practices:
- Level 1: Ad-hoc security
- Level 2: Documented security practices
- Level 3: Standardized secure development lifecycle
- Level 4: Security metrics and monitoring
- Level 5: Continuous security improvement

**Not recommended as primary model**: Too process-oriented for a technical security standard.

---

## 4. ISO 27001 Maturity Models

### Number of Levels
**5 Maturity Levels (various models)**

### Level Definitions

**Common maturity progression**:

| Level | Name | Description |
|-------|------|-------------|
| **Level 1** | Initial/Ad-hoc | Information security is reactive, inconsistent, and undocumented. No formal ISMS. |
| **Level 2** | Repeatable/Managed | Basic security processes exist but may not be standardized across the organization. Some documentation. |
| **Level 3** | Defined/Established | Processes are documented, standardized, and integrated. ISMS is implemented using defined processes capable of achieving expected outcomes. |
| **Level 4** | Managed/Predictable | Processes operate within defined limits. Quantitative metrics used to measure performance. |
| **Level 5** | Optimized/Continuous Improvement | Continuous improvement culture. Processes are enhanced to meet current and projected goals. |

**Alternative research model** (Information Security Management System focus):
- **Level 1**: Initial Stage
- **Level 2**: Planning Stage
- **Level 3**: Implementation Stage
- **Level 4**: Monitoring Stage
- **Level 5**: Improvement Stage

### Philosophy & Rationale

- **Maturity models are supplementary**: ISO 27001 itself does not define maturity levels; various organizations have created maturity models to complement the standard
- **Stages approach**: To move from level X to X+1, organization must comply with all criteria from level X
- **Assessment tool**: Helps organizations determine current maturity and create improvement roadmaps
- **Integration with ISO 27001**: ISO 27001 compliance represents Level 5 maturity in some models
- **Structured improvement pathway**: Provides a roadmap for organizations to enhance their security posture systematically

### Control Distribution

ISO 27001 Annex A contains 93 controls (as of ISO 27001:2022) that apply to all certified organizations. Maturity models don't distribute these controls across levels; instead, they assess:
- **Completeness** of control implementation
- **Consistency** of application
- **Documentation** quality
- **Integration** with business processes
- **Measurement and optimization**

### Determination Criteria

Organizations assess their maturity through:
- **Gap assessments**: Comparing current state to target maturity level
- **Control maturity evaluation**: Assessing each control's implementation maturity
- **Process maturity**: Evaluating the ISMS processes (plan-do-check-act cycle)
- **Self-assessment or external audit**: Internal reviews or third-party assessments

### Relevance to MCP Standard

**Partially applicable**: The staged lifecycle approach (Plan → Implement → Monitor → Improve) could map to MCP server security practices.

**Not recommended as primary model**: ISO 27001 maturity models are designed for enterprise ISMS programs, not individual software components or services.

**Potential adaptation**: Could use a simplified 3-level model based on security lifecycle maturity:
- Level 1: Basic security controls implemented
- Level 2: Security monitoring and testing in place
- Level 3: Continuous security improvement and threat response

---

## 5. PCI DSS Compliance Levels

### Number of Levels
**4 Merchant Levels** (based on transaction volume)

### Level Definitions

| Level | Transaction Volume (Annual) | Validation Requirements |
|-------|----------------------------|------------------------|
| **Level 1** | Over 6 million transactions | - Annual on-site audit by Qualified Security Assessor (QSA)<br>- Report on Compliance (ROC)<br>- Quarterly network scans by Approved Scanning Vendor (ASV)<br>- Attestation of Compliance |
| **Level 2** | 1-6 million transactions | - Annual Self-Assessment Questionnaire (SAQ)<br>- Quarterly network scans by ASV<br>- For SAQ A, A-EP, or D: must engage QSA or ISA for validation |
| **Level 3** | 20,000-1 million transactions | - Annual SAQ<br>- Quarterly external vulnerability scans by ASV<br>- Attestation of Compliance<br>- No external audit or ROC required |
| **Level 4** | Fewer than 20,000 transactions | - May not require SAQ, ROC, or AOC (varies by acquirer)<br>- Mastercard does not require validation, but acquirer must have risk management program |

### Philosophy & Rationale

- **Volume-based risk assessment**: Higher transaction volumes = greater potential impact of breach
- **Proportional validation burden**: Larger merchants face more rigorous audits
- **Economic pragmatism**: Small merchants can't afford QSA audits; self-assessment is acceptable
- **Acquirer discretion**: Payment processors may impose stricter requirements

### Control Distribution

**All merchants must comply with all 12 PCI DSS requirements**, regardless of level:
1. Install and maintain network security controls
2. Apply secure configurations
3. Protect stored account data
4. Protect cardholder data with strong cryptography during transmission
5. Protect all systems and networks from malicious software
6. Develop and maintain secure systems and software
7. Restrict access to system components and cardholder data
8. Identify users and authenticate access
9. Restrict physical access to cardholder data
10. Log and monitor all access
11. Test security systems and processes regularly
12. Support information security with organizational policies

**Levels differ only in validation rigor**, not in required controls.

### Determination Criteria

**Automatic assignment** based on:
- Annual transaction volume (counted by acquiring bank)
- Card brand (Visa, Mastercard, etc.) may have slightly different level definitions
- E-commerce vs. brick-and-mortar may affect categorization

### Relevance to MCP Standard

**Limited applicability**: Transaction volume is not relevant to MCP servers.

**Key lesson**: Consider **validation method** (self-assessment vs. third-party audit) as a dimension separate from control requirements.

**Potential adaptation for MCP**:
- All MCP servers implement the same controls
- Validation rigor depends on deployment context:
  - Personal/development: Self-attestation
  - Enterprise: Internal audit
  - Critical/public: Third-party assessment

---

## 6. OWASP ASVS (Application Security Verification Standard)

### Number of Levels
**3 Verification Levels**

### Level Definitions

| Level | Name | Description | Use Case | Testing Approach |
|-------|------|-------------|----------|-----------------|
| **Level 1** | Opportunistic / Low Assurance | Focuses on easily detectable vulnerabilities that can often be checked automatically (e.g., basic penetration testing). Defends against OWASP Top 10 and similar checklists. | Low-risk applications, or as a first step. Every application should aim for at least Level 1. | Automated tools, basic manual testing |
| **Level 2** | Standard Assurance | More in-depth checks covering protection against common application security risks. Includes design/code review elements. | Most applications, especially those handling sensitive data or performing important transactions. | Automated + manual testing, code review |
| **Level 3** | High / Advanced Assurance | Comprehensive security for critical applications. Defends against advanced threats and demonstrates good security design principles. | Critical applications with highly sensitive data (financial, health, government). | In-depth manual analysis, architecture review, penetration testing, code auditing |

### Philosophy & Rationale

- **Risk-based selection**: Choose the level based on the application's risk profile
- **Cumulative levels**: Level 2 includes all Level 1 requirements; Level 3 includes Levels 1 and 2
- **Flexibility**: Can mix levels per component (e.g., public API at Level 2, identity service at Level 3)
- **Three selection factors**:
  1. **Risk exposure**: Criticality and sensitivity of data/functionality
  2. **User expectations**: Trust placed in the application
  3. **Regulatory requirements**: Industry or legal obligations

### Control Distribution

**Approximate distribution** (OWASP community estimates):
- **Level 1**: ~20% of total requirements
- **Level 2**: ~50% of total requirements
- **Level 3**: ~30% of total requirements

**Example from ASVS** (illustrative):
- Total requirements: ~286 (varies by ASVS version)
- **Level 1**: ~57 requirements (20%)
- **Level 2**: ~143 requirements (50% cumulative to ~200 total)
- **Level 3**: ~86 requirements (30% cumulative to 286 total)

### Determination Criteria

Organizations select their target ASVS level based on:

1. **Risk exposure**:
   - Low: Public-facing informational sites
   - Medium: E-commerce, SaaS applications
   - High: Banking, healthcare, government systems

2. **Data sensitivity**:
   - Level 1: Public data
   - Level 2: Controlled Unclassified Information (CUI), PII
   - Level 3: PHI, financial data, classified information

3. **Regulatory requirements**:
   - Level 1: No specific compliance mandates
   - Level 2: PCI DSS, GDPR, CCPA
   - Level 3: HIPAA, SOX, national security

4. **User trust expectations**:
   - Level 1: Minimal trust required
   - Level 2: Users expect security
   - Level 3: Users depend on security for safety/livelihood

### Relevance to MCP Standard

**Highly applicable**: The ASVS model is excellent for MCP servers because:
- **3 levels** is manageable and well-understood in the security community
- **Cumulative approach** ensures Level 1 is a complete baseline
- **Risk-based selection** fits MCP server diversity (personal tools vs. enterprise infrastructure)
- **Control distribution** (20-50-30) provides a proven ratio
- **Flexibility** to mix levels by component aligns with MCP's modular architecture

**Recommended approach**: Adopt a 3-level model inspired by ASVS for MCP standard.

---

## 7. FedRAMP Authorization Levels

### Number of Levels
**3 Impact Levels (Low, Moderate, High)**

### Level Definitions

| Level | Description | Impact of Compromise | Control Count |
|-------|-------------|---------------------|---------------|
| **Low** | Loss of confidentiality, integrity, or availability would have **limited adverse effects** on federal operations, assets, or individuals. | Limited harm | ~125 controls |
| **Moderate** | Loss of confidentiality, integrity, or availability would have **serious adverse effects** on agency operations or individuals. Handles Controlled Unclassified Information (CUI). | Serious harm | ~325 controls |
| **High** | Security incident could result in **severe or catastrophic adverse effects**, such as loss of life, mission failure, or financial ruin. | Severe/catastrophic harm | ~425 controls |

### Philosophy & Rationale

- **Based on FIPS 199**: Federal Information Processing Standard for security categorization
- **Impact-driven**: Levels determined by potential harm, not transaction volume or organization size
- **CIA triad assessment**: Evaluates impact across Confidentiality, Integrity, and Availability
- **Majority of authorizations at Moderate**: Nearly 80% of FedRAMP-authorized systems are Moderate impact
- **Not a maturity model**: Organizations don't "progress" through levels; they select based on the system's impact categorization

### Control Distribution

Controls are based on NIST SP 800-53 security control baselines:
- **Low**: ~125 controls
- **Moderate**: ~325 controls (~200 additional beyond Low)
- **High**: ~425 controls (~100 additional beyond Moderate)

Controls are cumulative; higher levels include all controls from lower levels plus additional ones.

### Determination Criteria

**FIPS 199 categorization** process:
1. **Identify information types** handled by the system
2. **Assess impact** of compromise for each information type across C-I-A
3. **Determine system security category**: Highest impact rating becomes the system level
4. **Apply corresponding control baseline**

**Example**: A system that handles public information (Low confidentiality) but whose compromise would disrupt critical operations (High availability) would be categorized as **High**.

### 2026 Developments: FedRAMP 20X

- **Modernization initiative**: FedRAMP 20X represents a significant update to the authorization process
- **Low pilot complete**: FedRAMP 20X Low is open for new submissions
- **Moderate and High expected early 2026**: Updated authorization process for higher impact levels

### Relevance to MCP Standard

**Applicable with modifications**: The impact-based approach could work for MCP servers:
- **Low Impact**: Personal MCP servers, development/testing environments
- **Moderate Impact**: Team collaboration servers, internal business tools
- **High Impact**: Enterprise-wide MCP infrastructure, servers handling sensitive data

**Key lesson**: Basing levels on **potential harm** rather than organizational size creates clear decision criteria.

**Challenge**: Determining "impact" for MCP servers requires threat modeling specific to MCP deployment contexts.

---

## Comparative Analysis

### Level Count Distribution

| Framework | Levels | Type |
|-----------|--------|------|
| NIST CSF | 4 | Implementation Tiers |
| CIS Controls | 3 | Implementation Groups |
| CMMI | 5 | Maturity Levels |
| ISO 27001 (maturity models) | 5 | Maturity Stages |
| PCI DSS | 4 | Merchant Validation Levels |
| OWASP ASVS | 3 | Verification Levels |
| FedRAMP | 3 | Impact Levels |

**Most common**: **3-4 levels**

### Philosophical Approaches

| Approach | Frameworks | Description |
|----------|-----------|-------------|
| **Maturity Progression** | CMMI, ISO 27001 | Organizations evolve from ad-hoc to optimized over time |
| **Risk-Based Selection** | CIS Controls, OWASP ASVS, FedRAMP | Organizations select level based on risk profile, not maturity |
| **Volume/Scale-Based** | PCI DSS | Level determined by transaction volume or business size |
| **Process Rigor Tiers** | NIST CSF | Same controls, different implementation formality |

### Control Distribution Patterns

| Framework | Distribution Pattern | Cumulative? |
|-----------|---------------------|-------------|
| NIST CSF | Same controls, different rigor | N/A |
| CIS Controls | 56 / 130 / 153 (37% / 85% / 100%) | Yes |
| CMMI | Process areas, not controls | Yes |
| ISO 27001 | All 93 controls at all levels | N/A |
| PCI DSS | All 12 requirements at all levels | N/A |
| OWASP ASVS | ~20% / ~70% / ~100% | Yes |
| FedRAMP | 125 / 325 / 425 (~30% / ~76% / ~100%) | Yes |

**Common pattern**: **Cumulative distribution** with each level building on the previous.

**Typical ratios** (for cumulative models):
- **Level 1**: 20-40% of controls
- **Level 2**: 70-85% of controls (cumulative)
- **Level 3**: 100% of controls

---

## Recommendations for MCP Server Security Standard

Based on the research, here are recommendations for designing a compliance level system for MSSS:

### 1. Number of Levels: 3 Levels

**Rationale**:
- **Industry standard**: Both CIS Controls and OWASP ASVS (most relevant comparables) use 3 levels
- **Cognitive simplicity**: 3 levels are easier to understand and communicate than 4-5
- **Sufficient differentiation**: Can clearly distinguish basic/standard/advanced implementations
- **Avoids over-complexity**: 5 levels (CMMI, ISO) are more suited to process maturity than technical controls

### 2. Philosophy: Risk-Based Selection (Not Maturity Progression)

**Rationale**:
- **MCP server diversity**: Hobbyist servers vs. enterprise infrastructure require different security postures
- **No universal progression**: Not all MCP servers need to "mature" to the highest level
- **CIS/ASVS precedent**: Both successful frameworks use risk-based selection
- **Clear decision criteria**: Organizations can select based on deployment context and threat model

**Avoid**:
- Maturity models (CMMI, ISO) that assume organizational evolution
- Transaction volume (PCI DSS) which doesn't apply to MCP servers

### 3. Control Distribution: Cumulative with 20-50-30 Split

**Recommended distribution**:
- **Level 1**: ~20-30% of controls (foundational baseline)
- **Level 2**: ~50-60% additional controls (cumulative 70-85%)
- **Level 3**: ~15-30% additional controls (cumulative 100%)

**Rationale**:
- **OWASP ASVS pattern**: Proven 20-50-30 split
- **CIS Controls pattern**: 37-48-15 split shows similar progression
- **Ensures Level 1 completeness**: 20-30% must be a defensible baseline
- **Avoids top-heavy distribution**: Most security value in Levels 1-2

### 4. Level Naming Convention

**Option A: Deployment Context (Recommended)**
- **Level 1**: Personal / Development
- **Level 2**: Team / Enterprise
- **Level 3**: Critical / Public

**Option B: Assurance Level (ASVS-style)**
- **Level 1**: Basic Assurance
- **Level 2**: Standard Assurance
- **Level 3**: High Assurance

**Option C: Implementation Group (CIS-style)**
- **IG1**: Essential MCP Security
- **IG2**: Standard MCP Security
- **IG3**: Advanced MCP Security

**Rationale for Option A**:
- **Clear use case mapping**: Makes selection criteria obvious
- **Self-explanatory**: Users immediately understand which level applies
- **Avoids "maturity" connotation**: Doesn't imply Level 1 is "immature"

### 5. Determination Criteria

Organizations should select their level based on:

| Criterion | Level 1 | Level 2 | Level 3 |
|-----------|---------|---------|---------|
| **Deployment Context** | Personal, development, testing | Team collaboration, internal business | Enterprise-wide, public-facing, critical infrastructure |
| **Data Sensitivity** | Non-sensitive, public data | Business-confidential, PII | Highly sensitive, regulated data (PHI, financial) |
| **User Base** | Single user or small team | Department or organization | Multi-tenant, external users |
| **Threat Model** | Opportunistic attacks | Targeted attacks on organization | Advanced persistent threats, nation-state actors |
| **Regulatory Requirements** | None | Industry best practices (e.g., SOC 2) | Regulatory compliance (HIPAA, PCI DSS, FedRAMP) |
| **Impact of Compromise** | Limited (personal inconvenience) | Moderate (business disruption, reputation) | Severe (financial loss, legal liability, safety) |

### 6. Validation Methods (Inspired by PCI DSS)

| Level | Validation Approach |
|-------|---------------------|
| **Level 1** | Self-assessment questionnaire (SAQ) |
| **Level 2** | Internal audit + automated testing |
| **Level 3** | Third-party assessment + penetration testing |

**Rationale**: Separates control requirements from validation rigor, making higher levels more credible without adding control burden.

### 7. Flexibility (Inspired by OWASP ASVS)

Allow **mixed-level implementation**:
- Different MCP server components may target different levels
- Example: Filesystem tools at Level 3, read-only tools at Level 2
- Requires clear documentation of which components meet which levels

### 8. Avoid These Pitfalls

**Don't**:
- Create a maturity model that requires "progression" through levels
- Use transaction volume or organization size as sole criterion
- Make Level 1 so minimal it's not a complete security baseline
- Distribute controls so unevenly that Level 3 is impractical
- Name levels in ways that stigmatize lower levels ("Immature", "Incomplete")

**Do**:
- Make Level 1 a complete, defensible baseline for its use case
- Use cumulative control distribution
- Provide clear, objective criteria for level selection
- Allow flexibility for mixed-level implementations
- Ensure each level represents a complete security posture for its context

---

## Proposed MSSS Level Structure (Draft)

### Level 1: Personal / Development MCP Servers

**Target Audience**: Individual developers, hobbyists, local development environments

**Control Distribution**: ~25-30% of all MSSS controls (~6-8 controls)

**Typical Controls**:
- MCP-FS-01: Path Allowlisting
- MCP-EXEC-01: No Shell Execution
- MCP-NET-01: URL Validation
- MCP-INPUT-01: Schema Validation
- MCP-INPUT-03: Timeout Enforcement
- MCP-LOG-02: Secret Redaction

**Validation**: Self-assessment

**Use Cases**:
- Personal productivity MCP servers
- Development and testing environments
- Single-user tools with non-sensitive data

---

### Level 2: Team / Enterprise MCP Servers

**Target Audience**: Teams, departments, internal business applications

**Control Distribution**: ~55-60% additional controls (cumulative ~80-85%, ~17-20 controls)

**Additional Controls Beyond Level 1**:
- MCP-FS-02: Symlink Resolution
- MCP-FS-03: Filesystem Sandboxing
- MCP-EXEC-02: Command Allowlisting
- MCP-EXEC-03: Argument Separator
- MCP-NET-02: Egress Filtering
- MCP-AUTHZ-01: OAuth Delegation
- MCP-AUTHZ-02: Tool Scopes
- MCP-AUTHZ-03: Least Privilege
- MCP-INPUT-02: Bounds Checking
- MCP-LOG-01: Audit Logging
- MCP-SUPPLY-01: Package Integrity

**Validation**: Internal audit + automated testing

**Use Cases**:
- Team collaboration servers
- Internal business tools accessing sensitive data
- Multi-user MCP deployments

---

### Level 3: Critical / Public MCP Servers

**Target Audience**: Enterprise infrastructure, public services, regulated environments

**Control Distribution**: ~15-20% additional controls (cumulative 100%, all ~23 controls)

**Additional Controls Beyond Level 2**:
- MCP-NET-03: TLS Enforcement
- MCP-AUTHZ-04: RBAC
- MCP-SUPPLY-02: Trusted Sources
- MCP-DEPLOY-01: Container Hardening
- MCP-DEPLOY-02: Seccomp Enforcement
- MCP-DEPLOY-03: Resource Limits

**Validation**: Third-party assessment + penetration testing

**Use Cases**:
- Enterprise-wide MCP infrastructure
- Public-facing MCP servers
- Servers handling regulated data (PHI, PCI, CUI)
- High-value target environments

---

## References & Sources

### NIST Cybersecurity Framework
- [The NIST Cybersecurity Framework Implementation Tiers Explained](https://www.cybersaint.io/blog/the-nist-cybersecurity-framework-implementation-tiers-explained)
- [NIST Cybersecurity Framework Guide 2026 | TrustNet](https://trustnetinc.com/resources/nist-cybersecurity-framework/)
- [The NIST Cybersecurity Framework (CSF) 2.0](https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf)
- [What are the NIST CSF implementation tiers?](https://www.cybersaint.io/glossary/what-are-the-nist-csf-implementation-tiers)
- [Ultimate Guide to NIST CSF Maturity Levels [2025]](https://www.metricstream.com/learn/nist-csf-maturity-levels.html)

### CIS Controls
- [CIS Critical Security Controls Implementation Groups](https://www.cisecurity.org/controls/implementation-groups)
- [Guide to Implementation Groups (IG): CIS Critical Security Controls v8.1](https://www.cisecurity.org/insights/white-papers/guide-implementation-groups-ig-cis-critical-security-controls-v8-1)
- [What are the CIS Implementation Groups?](https://www.cybersaint.io/blog/cis-critical-security-controls)
- [3 Types of CIS Implementation Groups](https://cybersierra.co/blog/cis-groups/)
- [CIS Implementation Group 1 (IG1): Essential Cyber Hygiene](https://netwrix.com/en/resources/blog/understanding-basic-cyber-hygiene-controls-implementation-group-1-cis-ig1/)

### CMMI
- [CMMI Institute - CMMI Levels of Capability and Performance](https://cmmiinstitute.com/learning/appraisals/levels)
- [Capability Maturity Model Integration - Wikipedia](https://en.wikipedia.org/wiki/Capability_Maturity_Model_Integration)
- [Understanding CMMI Maturity Levels 2 to 5](https://www.biztransform.net/cmmi-maturity-levels/)
- [Capability Maturity Model Integration (CMMI) - GeeksforGeeks](https://www.geeksforgeeks.org/software-engineering/capability-maturity-model-integration-cmmi/)
- [The 5 Capability Maturity Model Integration (CMMI) Levels](https://innovation.world/capability-maturity-model-integration/)

### ISO 27001
- [ISO 27001 User Guide – Compliance Maturity Model](https://docs.logrhythm.com/kbmodules/docs/iso-27001-user-guide-compliance-maturity-model-a-f)
- [ISO 27001: Maturity models driving continual improvement](https://advisera.com/27001academy/blog/2015/04/13/achieving-continual-improvement-through-the-use-of-maturity-models/)
- [Information Security Management Systems - A Maturity Model Based on ISO/IEC 27001](https://www.researchgate.net/publication/325786419_Information_Security_Management_Systems_-_A_Maturity_Model_Based_on_ISOIEC_27001)
- [Risk Management Maturity Models ISO 27001:2022](https://www.isms.online/iso-27001/risk-management/maturity-models/)

### PCI DSS
- [PCI Security Standards Council – Merchants](https://www.pcisecuritystandards.org/merchants/)
- [PCI DSS Merchant Compliance Levels](https://www.mastercard.us/en-us/business/overview/safety-and-security/security-recommendations/site-data-protection-PCI/merchants-need-to-know.html)
- [The 4 PCI Compliance Levels Explained](https://www.exabeam.com/explainers/pci-compliance/the-4-pci-compliance-levels-explained/)
- [PCI DSS Compliance Levels: Everything You Need to Know](https://www.sisainfosec.com/blogs/pci-dss-compliance-levels/)
- [PCI DSS Compliance Levels Explained (Merchant & Service Providers – 2025)](https://vistainfosec.com/blog/pci-compliance-levels-for-merchants-service-providers/)

### OWASP ASVS
- [Understanding the OWASP Application Security Verification Standard](https://blog.secureflag.com/2024/11/15/understanding-owasp-asvs/)
- [OWASP ASVS Explained: Web App Security Verification Standard](https://www.aikido.dev/learn/compliance/compliance-frameworks/owasp-asvs)
- [OWASP ASVS: A Comprehensive Overview - Codific](https://codific.com/owasp-asvs-a-comprehensive-overview/)
- [OWASP Application Security Verification Standard (ASVS)](https://owasp.org/www-project-application-security-verification-standard/)
- [GitHub - OWASP/ASVS: Application Security Verification Standard](https://github.com/OWASP/ASVS)

### FedRAMP
- [FedRAMP Impact Levels: High vs Moderate vs Low](https://sprinto.com/blog/fedramp-levels/)
- [FedRAMP Levels & Baselines, Explained](https://secureframe.com/hub/fedramp/impact-levels)
- [Understanding Baselines and Impact Levels in FedRAMP](https://www.fedramp.gov/understanding-baselines-and-impact-levels/)
- [FedRAMP Levels Explained: Low, Moderate and High](https://www.pivotpointsecurity.com/fedramp-levels-explained-low-moderate-and-high/)
- [What is FedRAMP 20X and How Will it Affect Your Business in 2026?](https://www.paramify.com/blog/fedramp20x)

### Security Maturity Models (General)
- [Security Maturity Model - Microsoft Azure Well-Architected Framework](https://learn.microsoft.com/en-us/azure/well-architected/security/maturity-model)
- [Cybersecurity Maturity Model Certification (CMMC) v2.0](https://dodcio.defense.gov/Portals/0/Documents/CMMC/ModelOverview_V2.0_FINAL2_20211202_508.pdf)
- [Security Maturity Models: Levels, Assessment, and Benefits](https://linfordco.com/blog/security-maturity-models/)
- [How to Build Information Security Maturity: Models + Best Practices Explained](https://secureframe.com/blog/information-security-maturity)
- [Cybersecurity Capability Maturity Model (C2M2)](https://www.energy.gov/ceser/cybersecurity-capability-maturity-model-c2m2)

---

## Next Steps

1. **Validate proposed MSSS level structure** with stakeholders
2. **Map existing MSSS controls** to the 3-level model
3. **Define selection criteria** for MCP server deployers
4. **Create decision tree** or questionnaire for level selection
5. **Develop validation methodologies** for each level
6. **Consider certification/badging program** (inspired by OWASP ASVS)
7. **Draft implementation guidance** for each level
8. **Create compliance checklist templates** (SAQs) for self-assessment

---

**Document Status**: Draft for Discussion
**Last Updated**: January 20, 2026
**Next Review**: TBD
