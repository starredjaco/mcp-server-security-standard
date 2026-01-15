# MCP-SUPPLY-01 — Package Integrity Verification

**Level:** L2  
**Domain:** SUPPLY  

## Requirement

MCP server deployments SHOULD use dependency lockfiles with cryptographic hashes and SHOULD verify package signatures where available.

## Rationale

Supply chain attacks via malicious packages (typosquatting, compromised maintainer accounts) can inject backdoors or exfiltrate credentials.

## Scope

Package installation and build processes.

## Required Evidence

1. Lockfile present: `package-lock.json`, `poetry.lock`, `Cargo.lock`, `go.sum`
2. Hashes verified during install: `npm ci`, `pip install --require-hashes`
3. Documented process for reviewing dependency updates

## Verification Guidance

### Static
- Presence of lockfile in repository
- CI/CD uses `npm ci` or equivalent (not `npm install`)
- Dependabot/Renovate configured with manual review

### Dynamic
- Modify lockfile hash → install should fail

## Mappings

- OWASP Top 10: A06:2021 – Vulnerable and Outdated Components, A08:2021 – Software and Data Integrity Failures
- OWASP MCP Top 10: MCP-06 (Supply Chain & Dependency Tampering)

## Exceptions & Compensating Controls

NOT_APPLICABLE: Fully vendored dependencies with audited source.

## References

- Kaspersky: "Malicious MCP Servers Supply Chain Attack" (Sept 2024)
- Snyk advisory on dependency risks
