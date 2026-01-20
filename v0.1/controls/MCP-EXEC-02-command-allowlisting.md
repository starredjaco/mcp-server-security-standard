---
title: "MCP-EXEC-02 — Command Allowlisting"
weight: 22
---

# MCP-EXEC-02 — Command Allowlisting

**Level:** L2
**Domain:** EXEC

## Requirement

Tools that execute commands SHOULD restrict execution to an explicit allowlist of permitted commands.

## Rationale

Even without shell injection, unrestricted command execution allows attackers to run arbitrary binaries. An allowlist limits exposure to only necessary commands, reducing the attack surface.

## Scope

- **Tools:** Tools that execute system commands
- **Resources:** N/A
- **Transport:** All transports
- **Profiles:** DEV-WORKSTATION, SHARED-INTERNAL, INTERNET-FACING, HIGH-RISK-TOOLS, REGULATED

## Required Evidence

- Configuration or code defines explicit list of allowed commands
- Command name is validated against allowlist before execution
- Attempts to execute commands not on allowlist are rejected
- Allowlist is minimal (only commands needed for functionality)

## Verification Guidance (non-tool-specific)

### Static (code/config review)

Look for:

- Allowlist definition (e.g., `ALLOWED_COMMANDS = ["git", "npm", "docker"]`)
- Validation of command name before `exec` or `spawn`
- Rejection path when command not in allowlist

Example pattern:

```python
ALLOWED_COMMANDS = {"git", "npm", "node"}

def execute_command(cmd_name, args):
    if cmd_name not in ALLOWED_COMMANDS:
        raise PermissionError(f"Command '{cmd_name}' not allowed")
    return subprocess.run([cmd_name] + args, shell=False)
```

Red flags:

- Empty or missing allowlist
- Allowlist containing dangerous commands that can invoke shells:
  - `find` (with `-exec`)
  - `xargs`
  - `awk`, `perl`, `python`, `ruby` (script interpreters)
  - `env` (can execute arbitrary commands)
- Dynamic allowlist populated from untrusted source

### Dynamic (behavioral verification)

Test cases:

1. Execute allowed command → Should succeed
2. Execute command not in allowlist → Should fail with permission error
3. Execute allowed command with path prefix (e.g., `/usr/bin/git`) → Verify behavior (should normalize)
4. Attempt to bypass via symlink to allowed command → Should validate actual command

## Mappings

- **CWE:** CWE-78 (OS Command Injection)
- **CWE:** CWE-250 (Execution with Unnecessary Privileges)
- **OWASP MCP Top 10:** MCP-07 (Excessive Permissions and Scope Creep)
- **OWASP ASVS:** V5.2.4 (OS Command Injection)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**

- Server has no tools that execute system commands
- Server is designed to execute arbitrary commands (must be HIGH-RISK-TOOLS profile with L3)

**Compensating controls:**

- Container isolation with minimal installed binaries
- Seccomp profile blocking dangerous syscalls
- AppArmor/SELinux profiles restricting executable paths

## References

- [OWASP MCP Top 10 — Excessive Permissions](https://modelcontextprotocol-security.io/top10/)
- [MCP Hardening Guide](https://modelcontextprotocol-security.io/hardening/)
