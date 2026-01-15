# MCP-AUTHZ-01 — OAuth Token Delegation

**Level:** L2  
**Domain:** AUTHZ  

## Requirement

MCP servers exposed via HTTP/WebSocket transports MUST implement per-user authentication using OAuth 2.1, JWT, or equivalent token-based systems with user-specific scopes.

## Rationale

Shared credentials prevent per-user authorization, audit attribution, and principle of least privilege. OAuth delegation ensures each client uses their own identity.

## Scope

Remote MCP servers (HTTP/WebSocket). stdio transport typically inherits OS user identity.

## Required Evidence

1. Authentication middleware validating Bearer tokens
2. Token introspection showing user identity and scopes
3. Backend operations using user's credentials (not server's admin credentials)
4. Test: two different users cannot access each other's resources

## Verification Guidance

### Static
- OAuth provider integration (Auth0, Okta, custom)
- Code extracting `user_id` from token claims
- Database queries filtered by `user_id`

### Dynamic
- User A authenticates → receives token
- User A accesses own resource → SUCCESS
- User A attempts access to User B's resource → DENIED (403)

## Mappings

- OWASP Top 10: A01:2021 – Broken Access Control
- OWASP MCP Top 10: MCP-01 (Token Mismanagement), MCP-07 (Insufficient Auth)
- OWASP ASVS: V2.1 (Password Security), V3.1 (Session Management)

## Exceptions & Compensating Controls

NOT_APPLICABLE: stdio transport with OS-level user separation may use filesystem permissions instead.
Compensating: API key with embedded scopes (JWTs) acceptable at L2 if properly validated.

## References

- Trend Micro: "492 Exposed MCP Servers" (Jan 2025)
- WorkOS: "MCP Security Risks & Best Practices" (Aug 2024)
- MCP Authorization Tutorial (modelcontextprotocol.io)
