---
title: "MCP-AUTHZ-03 — Least Privilege Tool Design"
weight: 43
---

# MCP-AUTHZ-03 — Least Privilege Tool Design

**Level:** L3
**Domain:** AUTHZ

## Requirement

MCP tools MUST be designed with the principle of least privilege, implementing minimal required permissions, avoiding permission aggregation, separating read from write operations, and providing granular operation-specific tools rather than general-purpose tools with broad capabilities.

## Rationale

Tools with excessive privileges create unnecessary risk surfaces. A single vulnerability in an over-privileged tool can compromise entire systems. Research shows 70% of MCP tools request more permissions than needed for their stated purpose, with common anti-patterns including combined read/write tools, tools that accept arbitrary commands, and tools with administrative capabilities enabled by default. Real incidents include ransomware deployment through file management tools (CVE-2025-61492) and data exfiltration through overly permissive query tools.

## Scope

**Applies to:**
- Tool design and architecture decisions
- Permission requests and capability declarations
- Default tool configurations
- Tool composition and chaining
- Error handling and fallback behaviors
- All tool types (filesystem, network, execution, database)

**Does NOT apply to:**
- Explicitly designated administrative tools (must be clearly marked)
- Emergency break-glass procedures (must be audited)
- Development/debugging tools (must not be in production)

## Required Evidence

1. **Tool permission analysis**: Documentation of minimal permissions per tool
   ```yaml
   tools:
     # Good: Separated, minimal permissions
     read_config:
       purpose: "Read application configuration"
       permissions:
         - fs:read:/config/*.json
       cannot:
         - write_files
         - execute_commands
         - network_access

     list_users:
       purpose: "List user accounts"
       permissions:
         - db:select:users:id,name,email
       cannot:
         - modify_data
         - access_passwords
         - cross_table_joins

     # Bad: Combined, excessive permissions
     file_manager:  # ANTI-PATTERN
       permissions:
         - fs:read:/**
         - fs:write:/**
         - fs:delete:/**
         - fs:execute:/**
   ```

2. **Privilege separation code**: Implementation showing separated operations
3. **Test results**:
   - Read tool cannot perform writes
   - Write tool cannot read arbitrary files
   - Query tool cannot modify data
   - Limited tool cannot escalate privileges
   - Tool chaining cannot bypass restrictions

## Verification Guidance

### Static (code/config review)

**Look for:**
```python
# Python example of least privilege tool design

# GOOD: Separated, specific tools
class ConfigReader:
    """Read-only access to configuration files."""

    ALLOWED_PATHS = ['/etc/app/config.json', '/etc/app/settings.yaml']

    def read_config(self, filename: str) -> dict:
        if filename not in self.ALLOWED_PATHS:
            raise PermissionError(f"Not allowed to read {filename}")

        # Can only read, cannot write
        with open(filename, 'r') as f:
            return json.load(f)

class LogWriter:
    """Write-only access to log directory."""

    LOG_DIR = '/var/log/app/'

    def append_log(self, message: str, level: str = 'info'):
        # Can only append to logs, cannot read or delete
        log_file = os.path.join(self.LOG_DIR, f"{date.today()}.log")

        # Validate we're writing to log directory
        if not os.path.abspath(log_file).startswith(self.LOG_DIR):
            raise PermissionError("Path traversal attempt")

        # Append-only mode
        with open(log_file, 'a') as f:
            f.write(f"[{level.upper()}] {datetime.now()}: {message}\n")

class UserLister:
    """Read-only access to user list (no sensitive data)."""

    def list_users(self, filters: dict = None) -> list:
        # Only SELECT, only specific columns
        query = "SELECT id, username, created_at FROM users WHERE active = true"

        # No raw SQL injection possible
        if filters:
            if 'department' in filters:
                query += " AND department = %s"
                params = [filters['department']]
            else:
                params = []

        # Cannot see passwords, tokens, or PII
        with self.get_readonly_connection() as conn:
            cursor = conn.cursor()
            cursor.execute(query, params)
            return cursor.fetchall()

# BAD: Overprivileged combined tool (ANTI-PATTERN)
class FileManager:  # DO NOT USE THIS PATTERN
    """Excessive privileges in single tool."""

    def execute_file_operation(self, operation: str, path: str, content: str = None):
        # Too many capabilities in one tool
        if operation == 'read':
            return open(path).read()  # Can read any file
        elif operation == 'write':
            open(path, 'w').write(content)  # Can write anywhere
        elif operation == 'delete':
            os.remove(path)  # Can delete anything
        elif operation == 'execute':
            subprocess.run(path)  # Can execute anything
```

```typescript
// TypeScript example with privilege separation

// GOOD: Minimal, specific interfaces
interface ReadOnlyFileAccess {
    readFile(path: string): Promise<Buffer>;
    listDirectory(path: string): Promise<string[]>;
    // No write, delete, or execute methods
}

interface AppendOnlyLogger {
    appendLog(message: string, level: LogLevel): Promise<void>;
    // No read, update, or delete methods
}

interface QueryOnlyDatabase {
    select<T>(table: string, columns: string[], where?: Conditions): Promise<T[]>;
    // No insert, update, delete, or DDL methods
}

// Implementation with strict validation
class SecureConfigReader implements ReadOnlyFileAccess {
    private readonly allowedPaths = new Set([
        '/app/config/settings.json',
        '/app/config/features.json'
    ]);

    async readFile(path: string): Promise<Buffer> {
        const normalized = path.normalize(path);

        if (!this.allowedPaths.has(normalized)) {
            throw new UnauthorizedError(`Cannot read ${path}`);
        }

        // Read-only file handle
        const handle = await fs.open(normalized, 'r');
        try {
            return await handle.readFile();
        } finally {
            await handle.close();
        }
    }

    async listDirectory(path: string): Promise<string[]> {
        // Can only list config directory
        if (!path.startsWith('/app/config/')) {
            throw new UnauthorizedError(`Cannot list ${path}`);
        }

        return await fs.readdir(path);
    }
}

// GOOD: Separate tools for different privilege levels
class ToolRegistry {
    // Public read operations
    @RequireScope('public:read')
    getPublicInfo(): PublicInfoReader {
        return new PublicInfoReader();
    }

    // Authenticated read operations
    @RequireScope('user:read')
    getUserData(userId: string): UserDataReader {
        return new UserDataReader(userId); // Scoped to single user
    }

    // Write operations require higher privilege
    @RequireScope('user:write')
    getUserUpdater(userId: string): UserUpdater {
        return new UserUpdater(userId); // Can only update, not delete
    }

    // Admin operations clearly separated
    @RequireScope('admin:write')
    @RequireAuditLog
    @RequireMFA
    getAdminTool(): AdminTool {
        return new AdminTool();
    }
}
```

**Red flags:**
- Tools that combine read and write operations
- Generic "execute command" or "run query" tools
- Tools with default admin/root privileges
- Fallback to higher privileges on error
- Tools that accept raw/unvalidated input paths
- Missing operation-specific access controls

### Dynamic (behavioral verification)

**Test cases:**
1. **Operation separation**: Read tool attempting write → Denied
2. **Privilege escalation**: Chaining tools to gain admin access → Blocked
3. **Scope creep**: Tool accessing resources outside declared scope → Failed
4. **Default deny**: Tool accessing undeclared resource type → Rejected
5. **Minimal exposure**: Enumerate tool capabilities → Only necessary operations listed
6. **Privilege downgrade**: Admin tool used for read → Uses minimal privileges

**Expected behavior:**
- Each tool performs exactly one type of operation
- No privilege escalation through tool combination
- Clear error when exceeding tool capabilities
- Tools fail safely without elevated fallbacks
- Audit logs show privilege checks

## Mappings

- **CWE**: CWE-250 (Execution with Unnecessary Privileges), CWE-269 (Improper Privilege Management), CWE-271 (Privilege Dropping/Lowering Errors)
- **OWASP Top 10**: A01:2021 – Broken Access Control, A04:2021 – Insecure Design
- **OWASP MCP Top 10**: MCP-03 (Authorization Bypass), MCP-07 (Excessive Tool Permissions)
- **OWASP ASVS**: V4.1.1 (Verify principle of least privilege), V1.4.1 (Verify all components run with least privilege)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Single-purpose server with one operation type
- All operations require identical privilege level (rare)
- Purely computational tools with no I/O

**Compensating controls:**
- Runtime privilege dropping after authentication
- Mandatory access control (MAC) systems (SELinux, AppArmor)
- Just-in-time (JIT) privilege elevation with MFA
- Time-boxed privilege grants with automatic expiration

**Risk acceptance:**
- May combine related read operations in L1
- MUST separate read/write/execute in L2+
- L3 requires granular operation-specific tools

## References

- 70% of MCP tools over-privileged by design (arXiv:2506.13538)
- Ransomware via file management tool (CVE-2025-61492)
- Principle of Least Privilege: Saltzer & Schroeder, 1975
- NIST SP 800-53: AC-6 Least Privilege
- Tool privilege escalation chains (OWASP MCP Security Guide)