# MCP-EXEC-02 — Command Allowlisting

**Level:** L2  
**Domain:** EXEC  

## Requirement

MCP servers that execute system commands SHOULD maintain an explicit allowlist of permitted commands and reject all others.

## Rationale

Unrestricted command execution enables lateral movement and privilege escalation even without shell injection.

## Scope

Tools providing general command execution or shell access.

## Required Evidence

1. Configuration or code defining allowed commands (e.g., `["df", "uptime", "git"]`)
2. Validation logic checking command name against allowlist
3. Test: attempt to execute `nc` (netcat) → expect rejection

## Verification Guidance

### Static
- Locate allowlist definition in code/config
- Verify all exec calls check command name against list

### Dynamic
- Attempt disallowed command → expect error response

## Mappings

- OWASP MCP Top 10: MCP-02 (Scope Creep)
- Principle of Least Functionality

## Exceptions & Compensating Controls

NOT_APPLICABLE: If no general execution tool exists (only specific, non-shell tools like "restart_service").

## References

- StackHawk: MCP Security Best Practices (Sept 2024)
