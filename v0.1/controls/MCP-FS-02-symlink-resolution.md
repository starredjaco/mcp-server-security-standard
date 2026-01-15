# MCP-FS-02 — Symlink Resolution Validation

**Level:** L1  
**Domain:** FS  

## Requirement

MCP servers MUST resolve symbolic links before validating file paths and MUST NOT follow symlinks that target paths outside allowed directories.

## Rationale

Symlinks can bypass path validation by pointing to files outside the sandbox. Attackers can create symlinks within allowed directories that reference sensitive system files.

## Scope

Applies to all filesystem operations including read, write, stat, and directory listing.

## Required Evidence

1. Code showing symlink resolution before access (e.g., `os.path.realpath()` before open)
2. Validation that resolved target is within allowed directory
3. Test showing symlink to `/etc/passwd` is rejected

## Verification Guidance

### Static
- Look for `os.path.realpath()`, `fs.realpathSync()`, or `filepath.EvalSymlinks()`
- Verify resolved path is checked against allowlist before file access

### Dynamic
- Create symlink in allowed dir pointing to `/etc/passwd`
- Attempt to read via tool → expect rejection

## Mappings

- CWE: CWE-59 (Improper Link Resolution Before File Access), CWE-61 (UNIX Symbolic Link Following)
- OWASP MCP Top 10: MCP-02 (Privilege Escalation via Scope Creep)

## Exceptions & Compensating Controls

NOT_APPLICABLE: Same as MCP-FS-01

## References

- CVE-2025-53109: Symlink bypass in Anthropic Filesystem MCP Server (CVSS 8.8)
- Cymulate research (August 2025)
