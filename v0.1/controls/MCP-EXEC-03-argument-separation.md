# MCP-EXEC-03 — Argument Separation with Delimiters

**Level:** L2  
**Domain:** EXEC  

## Requirement

When invoking commands with user-supplied arguments, MCP servers SHOULD use argument separators (e.g., `--`) to prevent user input from being interpreted as flags.

## Rationale

Even with parameterized execution, attackers can inject malicious flags (e.g., `git --upload-pack='evil'`).

## Scope

Commands that accept flags and user data (git, grep, find, etc.).

## Required Evidence

Code showing: `subprocess.run(["git", "show", "--", user_branch])`

## Verification Guidance

### Static
- Check for `--` in command arrays before user input

### Dynamic
- Submit branch name like `--help` or `-v` → expect literal treatment

## Mappings

- CWE: CWE-88 (Argument Injection)
- OWASP ASVS: V5.3.10

## Exceptions & Compensating Controls

NOT_APPLICABLE: Commands that don't accept flags.

## References

- Trail of Bits research on argument injection
