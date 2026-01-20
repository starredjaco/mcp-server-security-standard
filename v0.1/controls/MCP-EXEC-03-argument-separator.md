---
title: "MCP-EXEC-03 — Argument Separator Usage"
weight: 23
---

# MCP-EXEC-03 — Argument Separator Usage

**Level:** L2
**Domain:** EXEC

## Requirement

Tools that execute commands with user-provided arguments SHOULD use the `--` separator to prevent argument injection attacks.

## Rationale

User-controlled arguments starting with `-` can be interpreted as command options rather than data. The `--` separator signals end of options, ensuring subsequent arguments are treated as positional parameters.

## Scope

- **Tools:** Tools that pass user input as command arguments
- **Resources:** N/A
- **Transport:** All transports
- **Profiles:** DEV-WORKSTATION, SHARED-INTERNAL, INTERNET-FACING, HIGH-RISK-TOOLS, REGULATED

## Required Evidence

- Commands that accept user input include `--` separator before user arguments
- Pattern is consistently applied across all tools executing commands
- Documentation acknowledges argument injection risk and mitigation

## Verification Guidance (non-tool-specific)

### Static (code/config review)

Look for:

- `--` separator in command argument lists before user-provided values
- Consistent application across all command execution paths

Secure pattern:

```python
# User input goes after '--'
subprocess.run(["git", "log", "--", user_provided_path], shell=False)
subprocess.run(["grep", "-r", "pattern", "--", user_provided_dir], shell=False)
```

Red flags:

```python
# User input can be interpreted as flags
subprocess.run(["git", "log", user_provided_path], shell=False)
# If user_provided_path = "-p" or "--patch", behavior changes

subprocess.run(["rm", user_provided_file], shell=False)
# If user_provided_file = "-rf /", catastrophic outcome
```

### Dynamic (behavioral verification)

Test cases:

1. Input starting with `-` (e.g., `-v`, `--help`) → Should be treated as data, not option
2. Input of `--` itself → Should not break command parsing
3. Input with typical flag patterns → Should not alter command behavior

Example test:

```
Tool: "show file contents"
Input: "--version"
Expected: Error "file not found: --version" or similar
Fail: Shows program version instead of file
```

## Mappings

- **CWE:** CWE-88 (Improper Neutralization of Argument Delimiters in a Command)
- **OWASP MCP Top 10:** Related to input validation
- **OWASP ASVS:** V5.2 (Sanitization and Sandboxing)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**

- Server has no tools that execute system commands
- Commands used do not accept options (rare)

**Compensating controls:**

- Input validation rejecting strings starting with `-`
- Prefixing user input with `./` for file paths
- Using commands that support `--` and documenting those that don't

**Commands that support `--`:**

Most GNU/POSIX utilities including: `grep`, `git`, `ls`, `cat`, `rm`, `cp`, `mv`, `find`, `tar`

**Commands that may NOT support `--`:**

Some built-in shell commands and non-GNU utilities. Verify for each command.

## References

- [POSIX Utility Syntax Guidelines](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap12.html)
- [Git documentation on `--`](https://git-scm.com/docs/git)
