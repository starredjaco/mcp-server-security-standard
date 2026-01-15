# MCP-NET-03 — TLS Enforcement for Remote Connections

**Level:** L2
**Domain:** NET

## Requirement

MCP servers exposed via HTTP/WebSocket transports MUST enforce TLS 1.2 or higher for all connections, with proper certificate validation and secure cipher suites. Plaintext HTTP MUST be disabled in production environments.

## Rationale

Unencrypted MCP communication exposes sensitive data including authentication tokens, tool parameters, and response data to network interception. Research has shown MCP servers transmitting API keys, database credentials, and user data over plaintext HTTP. The "NeighborJack" attacks demonstrated that servers binding to 0.0.0.0 without TLS enabled session hijacking from any device on the same network (cafés, coworking spaces). Microsoft's security framework mandates TLS for all remote MCP connections, and the MCP specification explicitly requires HTTPS for production deployments.

## Scope

**Applies to:**
- HTTP/WebSocket transport servers
- Remote MCP servers (non-localhost)
- Proxy/gateway MCP servers
- MCP servers handling any sensitive data

**Does NOT apply to:**
- stdio transport (local process communication)
- Localhost-only development servers (127.0.0.1)
- Test/CI environments with no sensitive data

## Required Evidence

1. **TLS Configuration**: Server config showing TLS enabled with minimum version 1.2
2. **Certificate validation**: Valid certificate chain from trusted CA (or pinned cert for internal)
3. **Cipher suite configuration**: Strong ciphers only (no NULL, export, or weak ciphers)
4. **HTTP redirect or blocking**: Plaintext HTTP requests rejected or redirected to HTTPS
5. **Test results**:
   - HTTPS connection succeeds with valid certificate
   - HTTP connection fails or redirects
   - TLS 1.0/1.1 connections rejected
   - Self-signed certificate rejected (unless explicitly trusted)

## Verification Guidance

### Static (code/config review)

**Look for:**
```javascript
// Node.js example with proper TLS
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('private-key.pem'),
  cert: fs.readFileSync('certificate.pem'),
  // Enforce TLS 1.2 minimum
  secureOptions: constants.SSL_OP_NO_TLSv1 | constants.SSL_OP_NO_TLSv1_1,
  // Strong ciphers only
  ciphers: 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384',
  honorCipherOrder: true
};

https.createServer(options, app).listen(443);
```

```python
# Python example with secure TLS
import ssl
from aiohttp import web

ssl_context = ssl.create_default_context(ssl.Purpose.CLIENT_AUTH)
ssl_context.load_cert_chain('certificate.pem', 'private-key.pem')
ssl_context.minimum_version = ssl.TLSVersion.TLSv1_2
ssl_context.set_ciphers('ECDHE+AESGCM:ECDHE+CHACHA20:!aNULL:!MD5:!DSS')

web.run_app(app, ssl_context=ssl_context, port=443)
```

**Red flags:**
- `http://` URLs in production configs
- Missing SSL/TLS configuration
- `verify=False` or `rejectUnauthorized: false`
- Weak cipher suites or old TLS versions allowed
- Self-signed certificates without proper trust store

### Dynamic (behavioral verification)

**Test with SSL Labs or testssl.sh:**
```bash
# Check TLS configuration
testssl.sh https://mcp-server.example.com

# Verify minimum TLS version
openssl s_client -connect server:443 -tls1_1  # Should fail
openssl s_client -connect server:443 -tls1_2  # Should succeed

# Check certificate chain
openssl s_client -connect server:443 -showcerts

# Attempt plaintext connection
curl http://mcp-server.example.com  # Should fail or redirect
```

**Expected results:**
- Grade A or better from SSL Labs
- No weak ciphers or protocols
- Valid certificate chain to trusted root
- HTTP requests blocked or redirected

## Mappings

- **CWE**: CWE-319 (Cleartext Transmission of Sensitive Information), CWE-326 (Inadequate Encryption Strength)
- **OWASP Top 10**: A02:2021 – Cryptographic Failures
- **OWASP MCP Top 10**: MCP-01 (Token Mismanagement), MCP-07 (Insufficient Authentication)
- **OWASP ASVS**: V9.1 (Communications Security Requirements)
- **NIST**: SP 800-52r2 (Guidelines for TLS Implementations)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- stdio transport only (no network exposure)
- Localhost-only binding (127.0.0.1) in development
- Air-gapped networks with no external access

**Compensating controls:**
- VPN or SSH tunnel providing transport encryption (partial mitigation)
- Network-level TLS termination at load balancer (must validate end-to-end)
- Mutual TLS (mTLS) for additional client authentication

**Risk acceptance:**
- Development/test environments MAY use self-signed certificates with explicit trust
- Internal networks MAY use private CA with proper certificate distribution

## References

- Backslash Security: "NeighborJack - Network Exposure via 0.0.0.0 Binding" (Jan 2025)
- Microsoft: "Securing the Model Context Protocol" requiring TLS for remote connections
- MCP Specification: "Servers SHOULD use HTTPS in production" (modelcontextprotocol.io)
- Mozilla SSL Configuration Generator: https://ssl-config.mozilla.org/
- OWASP TLS Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Security_Cheat_Sheet.html
