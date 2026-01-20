---
title: "MCP-AUTHZ-02 — Per-Tool Scope Definition"
weight: 42
---

# MCP-AUTHZ-02 — Per-Tool Scope Definition

**Level:** L3
**Domain:** AUTHZ

## Requirement

MCP servers MUST implement granular per-tool authorization scopes that explicitly define allowed operations, resource patterns, and parameter constraints for each tool, with mandatory scope validation before tool execution and clear scope documentation in tool manifests.

## Rationale

Without granular tool scopes, a single compromised credential or malicious client gains unrestricted access to all server capabilities. Research shows 60% of MCP servers expose all tools with identical permissions, enabling lateral movement attacks where compromise of one tool leads to full system access. Real incidents include file deletion through read-only tools (CVE-2025-53110) and privilege escalation through tool chaining.

## Scope

**Applies to:**
- All tool definitions and registrations
- Tool invocation authorization checks
- Dynamic tool discovery mechanisms
- Resource access patterns
- Parameter validation boundaries
- All authentication methods (OAuth, API key, mTLS)

**Does NOT apply to:**
- Internal server-to-server tool calls
- Emergency administrative overrides (must be logged)
- Tool development/debugging modes (must be clearly marked)

## Required Evidence

1. **Tool scope definitions**: Manifest showing per-tool permissions
   ```json
   {
     "tools": {
       "read_file": {
         "scopes": ["fs:read"],
         "resource_patterns": ["/data/**", "/config/*.json"],
         "parameter_constraints": {
           "path": "^/data/|^/config/.*\\.json$",
           "max_size": 10485760
         }
       },
       "execute_query": {
         "scopes": ["db:read"],
         "allowed_operations": ["SELECT"],
         "denied_patterns": ["DROP", "DELETE", "UPDATE"],
         "max_rows": 1000
       },
       "send_notification": {
         "scopes": ["notify:send"],
         "rate_limit": "10/hour",
         "allowed_channels": ["email", "slack"],
         "recipient_domains": ["@company.com"]
       }
     }
   }
   ```

2. **Scope enforcement code**: Authorization checks before tool execution
3. **Test results**:
   - Tool with correct scope executes successfully
   - Tool without required scope is denied
   - Scope escalation attempt is blocked
   - Parameter outside constraints is rejected
   - Resource pattern violation is denied

## Verification Guidance

### Static (code/config review)

**Look for:**
```python
# Python example with scope-based authorization
from typing import Dict, List, Set, Optional
import re
import fnmatch

class ToolAuthorizationManager:
    def __init__(self):
        self.tool_scopes = {}
        self.scope_hierarchy = {
            "fs:admin": ["fs:write", "fs:read"],
            "fs:write": ["fs:read"],
            "db:admin": ["db:write", "db:read"],
            "db:write": ["db:read"]
        }

    def register_tool(self, tool_name: str, config: Dict):
        """Register tool with its required scopes and constraints."""
        self.tool_scopes[tool_name] = {
            "required_scopes": set(config.get("scopes", [])),
            "resource_patterns": config.get("resource_patterns", ["*"]),
            "parameter_constraints": config.get("parameter_constraints", {}),
            "rate_limit": config.get("rate_limit"),
            "denied_operations": set(config.get("denied_patterns", []))
        }

    def authorize_tool_execution(self, tool_name: str, user_scopes: Set[str],
                                parameters: Dict) -> tuple[bool, Optional[str]]:
        """Check if user can execute tool with given parameters."""

        if tool_name not in self.tool_scopes:
            return False, f"Unknown tool: {tool_name}"

        tool_config = self.tool_scopes[tool_name]

        # Check scope authorization
        effective_scopes = self._expand_scopes(user_scopes)
        if not tool_config["required_scopes"].issubset(effective_scopes):
            missing = tool_config["required_scopes"] - effective_scopes
            return False, f"Missing scopes: {missing}"

        # Check parameter constraints
        for param, constraint in tool_config["parameter_constraints"].items():
            if param in parameters:
                if isinstance(constraint, str):  # Regex pattern
                    if not re.match(constraint, str(parameters[param])):
                        return False, f"Parameter {param} violates constraint"
                elif isinstance(constraint, (int, float)):  # Max value
                    if parameters[param] > constraint:
                        return False, f"Parameter {param} exceeds maximum"

        # Check resource patterns for file operations
        if "path" in parameters:
            allowed = False
            for pattern in tool_config["resource_patterns"]:
                if fnmatch.fnmatch(parameters["path"], pattern):
                    allowed = True
                    break
            if not allowed:
                return False, f"Resource path not allowed: {parameters['path']}"

        # Check for denied operations in query tools
        if "query" in parameters:
            query_upper = parameters["query"].upper()
            for denied in tool_config["denied_operations"]:
                if denied in query_upper:
                    return False, f"Operation not allowed: {denied}"

        return True, None

    def _expand_scopes(self, user_scopes: Set[str]) -> Set[str]:
        """Expand scopes based on hierarchy."""
        expanded = set(user_scopes)
        for scope in user_scopes:
            if scope in self.scope_hierarchy:
                expanded.update(self.scope_hierarchy[scope])
        return expanded

    def get_available_tools(self, user_scopes: Set[str]) -> List[str]:
        """Return list of tools available to user based on scopes."""
        effective_scopes = self._expand_scopes(user_scopes)
        available = []
        for tool_name, config in self.tool_scopes.items():
            if config["required_scopes"].issubset(effective_scopes):
                available.append(tool_name)
        return available
```

```typescript
// TypeScript example with decorators for scope enforcement
interface ToolScope {
    scopes: string[];
    resourcePatterns?: string[];
    parameterConstraints?: Record<string, any>;
    rateLimit?: string;
}

// Decorator for tool scope definition
function RequireScopes(scopeConfig: ToolScope) {
    return function(target: any, propertyName: string, descriptor: PropertyDescriptor) {
        const originalMethod = descriptor.value;

        descriptor.value = async function(...args: any[]) {
            const context = args[0]; // Assume first arg is context with user info

            // Validate scopes
            const userScopes = context.user?.scopes || [];
            const hasRequiredScopes = scopeConfig.scopes.every(
                scope => userScopes.includes(scope)
            );

            if (!hasRequiredScopes) {
                throw new AuthorizationError(
                    `Missing required scopes: ${scopeConfig.scopes.join(', ')}`
                );
            }

            // Validate parameters
            const params = args[1];
            if (scopeConfig.parameterConstraints) {
                for (const [param, constraint] of Object.entries(
                    scopeConfig.parameterConstraints
                )) {
                    if (!validateParameter(params[param], constraint)) {
                        throw new ValidationError(
                            `Parameter ${param} violates constraints`
                        );
                    }
                }
            }

            // Check rate limits
            if (scopeConfig.rateLimit) {
                const limited = await checkRateLimit(
                    context.user.id,
                    propertyName,
                    scopeConfig.rateLimit
                );
                if (limited) {
                    throw new RateLimitError('Rate limit exceeded');
                }
            }

            return originalMethod.apply(this, args);
        };

        return descriptor;
    };
}

// Usage example
class FileTools {
    @RequireScopes({
        scopes: ['fs:read'],
        resourcePatterns: ['/public/**', '/shared/**'],
        parameterConstraints: {
            path: /^\/public\/|^\/shared\//,
            maxSize: 10 * 1024 * 1024
        }
    })
    async readFile(context: Context, params: { path: string }): Promise<string> {
        // Implementation
    }

    @RequireScopes({
        scopes: ['fs:write', 'fs:admin'],
        resourcePatterns: ['/uploads/**'],
        rateLimit: '100/hour'
    })
    async writeFile(context: Context, params: { path: string, content: string }) {
        // Implementation
    }
}
```

**Red flags:**
- Single scope granting access to all tools
- Missing scope validation before tool execution
- Hardcoded authorization bypasses
- Scope checks only in client code
- Tools with overly broad default permissions
- No differentiation between read/write operations

### Dynamic (behavioral verification)

**Test cases:**
1. **Scope isolation**: User with `fs:read` cannot execute `fs:write` tools
2. **Scope hierarchy**: User with `fs:admin` can execute both read and write tools
3. **Parameter constraints**: File path outside allowed pattern is rejected
4. **Operation filtering**: SQL query with DROP statement blocked for read-only scope
5. **Rate limiting**: 11th notification in hour is blocked when limit is 10/hour
6. **Tool discovery**: User sees only tools matching their scopes

**Expected behavior:**
- Clear error messages indicating missing scopes
- Audit logs showing authorization decisions
- No scope escalation through tool chaining
- Consistent scope enforcement across all transports
- Dynamic tool list based on user permissions

## Mappings

- **CWE**: CWE-285 (Improper Authorization), CWE-862 (Missing Authorization), CWE-266 (Incorrect Privilege Assignment)
- **OWASP Top 10**: A01:2021 – Broken Access Control
- **OWASP MCP Top 10**: MCP-03 (Authorization Bypass), MCP-07 (Excessive Tool Permissions)
- **OWASP ASVS**: V4.1.3 (Verify that the application enforces access controls), V4.2.1 (Verify that sensitive data access is logged)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Server exposes only a single tool (rare)
- All users require identical full access (development only)
- Tools are purely computational with no side effects

**Compensating controls:**
- Network segmentation limiting tool reach
- Mandatory audit logging of all tool executions
- Time-based access windows for sensitive tools
- Multi-factor authentication for high-privilege tools

**Risk acceptance:**
- May use coarse-grained scopes for L1 environments
- MUST implement fine-grained scopes for L2+ deployments
- Internet-facing servers require mandatory scope enforcement

## References

- 60% of MCP servers lack granular tool permissions (arXiv:2506.13538)
- Privilege escalation through tool chaining (CVE-2025-53110)
- OAuth 2.0 scope best practices: RFC 8252
- RBAC design patterns for API gateways
- Tool permission bypass in MCPManager (Issue #2341)