# MCP-FS-01 — Path Allowlisting and Canonical Resolution

**Level:** L1  
**Domain:** FS  

## Requirement

MCP servers that expose filesystem access tools MUST restrict file operations to explicitly allowed directories using canonical path resolution.

## Rationale

Path traversal vulnerabilities (CWE-22) allow attackers to access files outside intended directories by manipulating paths with sequences like `../` or by exploiting naive prefix validation. Canonical path resolution prevents these bypasses.

## Scope

**Applies to:**
- Tools that read, write, list, or otherwise access files
- Resource endpoints serving file content
- All transports (stdio, HTTP, WebSocket)

**Does NOT apply to:**
- Servers with no filesystem access capabilities

## Required Evidence

1. **Configuration**: Documented allowed base directories (e.g., `/data/project`, `/var/mcp/uploads`)
2. **Code**: Path validation logic showing canonical resolution (e.g., `os.path.realpath()`, `fs.realpathSync()`)
3. **Test Result**: Demonstrated rejection of path traversal attempts:
   - Input: `../../../etc/passwd` → REJECT
   - Input: `/allowed/subdir/../../outside/file.txt` → REJECT

## Verification Guidance

### Static (code/config review)

**Look for:**
- Explicit allowlist of base directories in configuration
- Use of canonical path resolution functions before validation:
  - Python: `os.path.realpath()`, `pathlib.Path.resolve()`
  - Node.js: `fs.realpathSync()`, `path.resolve()` + exists check
  - Go: `filepath.EvalSymlinks()`, `filepath.Clean()` + prefix check
- Validation logic comparing resolved path against allowed base paths

**Red flags:**
- String prefix matching without resolution: `if path.startswith('/allowed')`
- Blacklist approaches: checking for `../` in string (easily bypassed)
- Missing validation on every file operation

### Dynamic (behavioral verification)

**Test cases:**
1. Request file with `../` sequences → expect rejection with error
2. Request file with absolute path outside allowed dirs → expect rejection
3. Request legitimate file within allowed dir → expect success
4. Create symlink pointing outside allowed dir, request via symlink → expect rejection (see MCP-FS-02)

**Expected behavior:**
- Server returns error (4xx status for HTTP, error response for JSON-RPC)
- Error message does not reveal filesystem structure
- Audit log records rejected access attempt (see MCP-LOG-01)

## Mappings

- **CWE**: CWE-22 (Improper Limitation of a Pathname to a Restricted Directory)
- **OWASP Top 10**: A01:2021 – Broken Access Control
- **OWASP ASVS**: V5.2.1 (Verify that the application uses OS-level APIs for file access and does not allow user-controlled file paths)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Server does not expose any filesystem access (document which tools/resources exist and confirm none touch filesystem)

**Compensating controls:**
- Running in container with read-only root filesystem and specific volume mounts can limit blast radius but does not replace path validation
- Filesystem sandboxing (chroot, LXC) provides additional layer but is not sufficient alone

**Risk acceptance:**
- For L1 in pure development environments, accepting risk with documented justification MAY be acceptable if filesystem access is to developer's own files only

## References

- CVE-2025-53110: Directory traversal in Anthropic Filesystem MCP Server (naive prefix validation)
- Cymulate blog: "EscapeRoute: Breaking the Scope of Anthropic's Filesystem MCP" (August 2025)
- OWASP Path Traversal Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Path_Traversal_Cheat_Sheet.html
