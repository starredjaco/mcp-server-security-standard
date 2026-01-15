# MCP-SUPPLY-02 — Trusted Package Sources and Registry Verification

**Level:** L1
**Domain:** SUPPLY

## Requirement

MCP servers MUST be installed only from official sources or verified publishers, with package names validated against known legitimate repositories and cryptographic signatures or checksums verified before installation.

## Rationale

Supply chain attacks targeting MCP servers have escalated dramatically. The "postmark-mcp" incident (September 2025) was the first documented malicious MCP package, masquerading as the legitimate Postmark email server while exfiltrating all emails to attackers. Typosquatting attacks like "playwright-mcp" vs "@playwright/mcp" garnered 17,000 downloads in one week. Research identified 143+ typosquats across PyPI, npm, and GitHub Actions targeting MCP packages. The Smithery.ai breach compromised 3,000+ servers through registry manipulation. "Slopsquatting" exploits AI hallucinations where LLMs recommend non-existent packages that attackers preemptively register. Without source verification, organizations unknowingly install backdoored servers granting attackers persistent access to internal systems.

## Scope

**Applies to:**
- All MCP server installations
- Package managers (npm, pip, cargo, go get)
- Container image pulls (Docker Hub, GitHub Container Registry)
- Manual downloads from GitHub releases
- CI/CD pipeline dependencies
- Development and production environments

**Does NOT apply to:**
- Internal packages from private registries (covered by different controls)
- Source code cloned for development (covered by code review controls)

## Required Evidence

1. **Allowlist of trusted sources**: Document listing official package names and sources
2. **Package verification logs**: Installation logs showing signature/checksum verification
3. **Registry configuration**: Package manager configured to use official registries only
4. **Name validation**: Verification that package names match official patterns
5. **Publisher verification**: Confirmation of verified publisher badges or signatures
6. **Typosquat detection**: Scanning results showing no similar malicious packages
7. **Installation audit**: Record of all installed MCP servers with sources

## Verification Guidance

### Static (code/config review)

**NPM configuration for trusted sources:**
```json
// .npmrc
registry=https://registry.npmjs.org/
@anthropic:registry=https://registry.npmjs.org/
@microsoft:registry=https://registry.npmjs.org/
audit-level=critical
```

**Package verification script (Node.js):**
```javascript
const crypto = require('crypto');
const fs = require('fs');

const TRUSTED_PACKAGES = {
  '@anthropic/mcp': {
    publisher: 'anthropic',
    minVersion: '1.0.0',
    checksums: {
      '1.0.0': 'sha256:abc123...',
      '1.0.1': 'sha256:def456...'
    }
  },
  '@modelcontextprotocol/server-memory': {
    publisher: 'mcp-team',
    namespace: '@modelcontextprotocol'
  }
};

function verifyPackage(packageName, version) {
  const trusted = TRUSTED_PACKAGES[packageName];
  if (!trusted) {
    throw new Error(`Unknown package: ${packageName}`);
  }

  // Verify namespace matches
  if (trusted.namespace && !packageName.startsWith(trusted.namespace)) {
    throw new Error(`Invalid namespace for ${packageName}`);
  }

  // Check against known checksums
  if (trusted.checksums && trusted.checksums[version]) {
    const packagePath = `node_modules/${packageName}/package.json`;
    const hash = crypto.createHash('sha256')
      .update(fs.readFileSync(packagePath))
      .digest('hex');

    if (hash !== trusted.checksums[version]) {
      throw new Error(`Checksum mismatch for ${packageName}@${version}`);
    }
  }
}
```

**Python pip with hash verification:**
```bash
# requirements.txt with hash verification
mcp-server-python==1.0.0 \
    --hash=sha256:abcdef1234567890abcdef1234567890abcdef12

# Install with verification
pip install --require-hashes -r requirements.txt
```

**Docker image verification:**
```bash
# Pull with content trust enabled
export DOCKER_CONTENT_TRUST=1
docker pull anthropic/mcp-server:latest

# Verify image signature
docker trust inspect anthropic/mcp-server:latest

# Use specific digests instead of tags
docker pull anthropic/mcp-server@sha256:abc123...
```

**Go module verification:**
```go
// go.mod with checksums
module github.com/mycompany/mcp-client

require (
    github.com/anthropic-ai/mcp-go v1.0.0
)

// go.sum automatically maintains checksums
// Verify with: go mod verify
```

**Typosquat detection script:**
```python
import difflib
from packaging import version

OFFICIAL_PACKAGES = [
    'mcp-server-anthropic',
    '@anthropic/mcp',
    '@microsoft/playwright-mcp'
]

def check_typosquats(package_name):
    # Check similarity to official packages
    for official in OFFICIAL_PACKAGES:
        similarity = difflib.SequenceMatcher(
            None, package_name, official
        ).ratio()

        if 0.8 < similarity < 1.0:  # Similar but not exact
            return f"WARNING: '{package_name}' similar to '{official}'"

    # Check common typosquat patterns
    suspicious_patterns = [
        'mcpp-',     # Extra 'p'
        'mpc-',      # Transposed letters
        'ncp-',      # Adjacent key
        '-mcps',     # Plural
        'test-mcp',  # Test prefix
    ]

    for pattern in suspicious_patterns:
        if pattern in package_name:
            return f"WARNING: Suspicious pattern '{pattern}' in name"

    return None
```

### Dynamic (behavioral verification)

**Installation verification commands:**
```bash
# NPM: Verify publisher
npm view @anthropic/mcp maintainers
npm audit signatures  # Requires npm 8.13.0+

# Check for typosquats
npm search mcp-server --json | jq '.[].name' | grep -E '(mcpp|mpc-|ncp)'

# Python: Verify package metadata
pip show mcp-server-python
pip list --format json | jq '.[] | select(.name | contains("mcp"))'

# Go: Verify module checksums
go mod verify
go list -m all | grep mcp

# Container: Verify signatures
cosign verify anthropic/mcp-server:latest \
  --certificate-identity=anthropic@example.com \
  --certificate-oidc-issuer=https://github.com/login/oauth
```

**Automated allowlist enforcement:**
```yaml
# GitHub Actions workflow
name: Verify MCP Dependencies
on: [pull_request]

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Check package.json
        run: |
          ALLOWED_MCP="@anthropic/mcp @microsoft/playwright-mcp"
          for pkg in $(jq -r '.dependencies | keys[]' package.json | grep mcp); do
            if ! echo "$ALLOWED_MCP" | grep -q "$pkg"; then
              echo "ERROR: Unauthorized MCP package: $pkg"
              exit 1
            fi
          done

      - name: Verify checksums
        run: |
          npm ci
          npm audit signatures
```

## Mappings

- **CWE**: CWE-494 (Download of Code Without Integrity Check), CWE-830 (Inclusion of Web Functionality from Untrusted Source)
- **OWASP Top 10**: A08:2021 – Software and Data Integrity Failures
- **OWASP MCP Top 10**: MCP-04 (Supply Chain Attacks & Dependency Tampering)
- **OWASP ASVS**: V10.2.1 (Source code dependencies are identified and checked)
- **SLSA**: Level 2 (Source and build platform requirements)
- **NIST SSDF**: PO.3.2 (Verify software matches expectations before use)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Building MCP servers from source with reviewed code
- Using only internal/private packages with separate verification

**Compensating controls:**
- Private registry mirror with pre-vetted packages
- Vendor review process with security assessment
- Runtime behavior monitoring for anomalous activity
- Network segmentation limiting impact of compromise

**Risk acceptance:**
- Prototypes MAY use unverified packages in isolated environments
- Research MAY require testing suspicious packages (use sandboxes)
- Emergency patches MAY bypass verification (document and review after)

## References

- Acuvity AI: "One Line of Code, Thousands of Stolen Emails: The First Malicious MCP Server Exposed" (September 2025)
- Noma Security: "Top Five MCP Security Blindspots" documenting playwright typosquatting (November 2025)
- Kaspersky: "Supply Chain PoC - Full Kill Chain from Packaging to Exfiltration" (December 2025)
- Smithery.ai Breach: Path traversal compromising 3,000+ servers (October 2025)
- CISA: "Defending Against Software Supply Chain Attacks" (April 2021)
- OpenSSF Scorecard: https://securityscorecards.dev/
- NPM Best Practices: https://docs.npmjs.com/best-practices-for-supply-chain-security
