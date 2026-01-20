---
title: "MCP-AUTHZ-04 — Resource-Based Access Control"
weight: 44
---

# MCP-AUTHZ-04 — Resource-Based Access Control

**Level:** L2
**Domain:** AUTHZ

## Requirement

MCP servers MUST implement resource-based access control (RBAC) that validates user permissions against specific resource identities, ownership, and attributes before granting access, with mandatory enforcement of resource boundaries, hierarchical permission inheritance, and explicit deny rules taking precedence over allows.

## Rationale

Without resource-level access control, authorization decisions become binary (all-or-nothing), leading to privilege escalation through resource manipulation. Research shows 45% of MCP servers lack resource-specific authorization, allowing users to access any resource once authenticated. Documented attacks include accessing other users' data through ID enumeration (CVE-2025-53109), modifying system resources through user-level access, and bypassing tenant isolation in multi-tenant deployments.

## Scope

**Applies to:**
- All resource access operations (files, database records, API objects)
- Resource creation and ownership assignment
- Cross-resource references and relationships
- Tenant/workspace isolation boundaries
- Resource sharing and delegation
- All access methods (direct, indirect, bulk)

**Does NOT apply to:**
- Public/anonymous resources (explicitly marked)
- System-level resources without ownership
- Temporary/ephemeral resources with TTL

## Required Evidence

1. **RBAC policy definitions**: Resource access rules and ownership model
   ```json
   {
     "rbac_policies": {
       "file_access": {
         "rules": [
           {
             "resource": "/users/{userId}/files/**",
             "principal": "user:{userId}",
             "permissions": ["read", "write", "delete"]
           },
           {
             "resource": "/shared/files/**",
             "principal": "role:employee",
             "permissions": ["read"]
           },
           {
             "resource": "/admin/**",
             "principal": "role:admin",
             "permissions": ["*"]
           }
         ],
         "deny_rules": [
           {
             "resource": "**/.secrets",
             "principal": "*",
             "permissions": ["*"],
             "priority": 1000
           }
         ]
       },
       "database_access": {
         "rules": [
           {
             "resource": "table:users",
             "condition": "row.id == principal.userId",
             "permissions": ["select", "update"]
           },
           {
             "resource": "table:projects",
             "condition": "row.team_id IN principal.teams",
             "permissions": ["select"]
           }
         ]
       }
     }
   }
   ```

2. **Authorization enforcement code**: Resource-level permission checks
3. **Test results**:
   - User can access own resources
   - User cannot access other users' resources
   - Role-based access works correctly
   - Deny rules override allow rules
   - Resource hierarchy inheritance functions

## Verification Guidance

### Static (code/config review)

**Look for:**
```python
# Python example with resource-based access control
from dataclasses import dataclass
from typing import List, Set, Optional, Dict
import fnmatch
from enum import Enum

class Permission(Enum):
    READ = "read"
    WRITE = "write"
    DELETE = "delete"
    SHARE = "share"
    ADMIN = "admin"

@dataclass
class Resource:
    path: str
    owner: str
    tenant_id: Optional[str] = None
    attributes: Dict = None
    parent: Optional['Resource'] = None

@dataclass
class Principal:
    user_id: str
    roles: Set[str]
    teams: Set[str]
    tenant_id: Optional[str] = None

class ResourceAccessControl:
    def __init__(self):
        self.policies = []
        self.deny_policies = []  # Deny rules have precedence

    def add_policy(self, pattern: str, principal_match: str,
                  permissions: List[Permission], deny: bool = False):
        """Add an access policy rule."""
        policy = {
            "pattern": pattern,
            "principal": principal_match,
            "permissions": set(permissions),
            "deny": deny
        }

        if deny:
            self.deny_policies.append(policy)
        else:
            self.policies.append(policy)

    def check_access(self, principal: Principal, resource: Resource,
                    permission: Permission) -> tuple[bool, str]:
        """Check if principal has permission on resource."""

        # 1. Tenant isolation check (critical)
        if resource.tenant_id and resource.tenant_id != principal.tenant_id:
            return False, "Cross-tenant access denied"

        # 2. Check deny rules first (they override everything)
        for policy in self.deny_policies:
            if self._matches_policy(principal, resource, policy):
                if permission in policy["permissions"] or Permission.ADMIN in policy["permissions"]:
                    return False, f"Explicitly denied by policy: {policy['pattern']}"

        # 3. Check ownership
        if resource.owner == principal.user_id:
            # Owners have full access to their resources
            return True, "Owner access granted"

        # 4. Check allow policies
        for policy in self.policies:
            if self._matches_policy(principal, resource, policy):
                if permission in policy["permissions"] or Permission.ADMIN in policy["permissions"]:
                    return True, f"Allowed by policy: {policy['pattern']}"

        # 5. Check inherited permissions from parent
        if resource.parent:
            parent_access, reason = self.check_access(principal, resource.parent, permission)
            if parent_access:
                return True, f"Inherited from parent: {reason}"

        # 6. Default deny
        return False, "No matching policy found"

    def _matches_policy(self, principal: Principal, resource: Resource,
                       policy: dict) -> bool:
        """Check if policy matches principal and resource."""

        # Match resource pattern
        if not fnmatch.fnmatch(resource.path, policy["pattern"]):
            return False

        # Match principal
        principal_match = policy["principal"]

        # Check user match
        if principal_match.startswith("user:"):
            expected_user = principal_match[5:]
            if expected_user == "{userId}":
                # Dynamic matching
                return principal.user_id in resource.path
            return principal.user_id == expected_user

        # Check role match
        if principal_match.startswith("role:"):
            expected_role = principal_match[5:]
            return expected_role in principal.roles

        # Check team match
        if principal_match.startswith("team:"):
            expected_team = principal_match[5:]
            return expected_team in principal.teams

        # Wildcard
        if principal_match == "*":
            return True

        return False

    def filter_resources(self, principal: Principal, resources: List[Resource],
                        permission: Permission) -> List[Resource]:
        """Filter list of resources to those principal can access."""
        accessible = []
        for resource in resources:
            allowed, _ = self.check_access(principal, resource, permission)
            if allowed:
                accessible.append(resource)
        return accessible

    def get_resource_permissions(self, principal: Principal,
                                resource: Resource) -> Set[Permission]:
        """Get all permissions principal has on resource."""
        permissions = set()
        for perm in Permission:
            allowed, _ = self.check_access(principal, resource, perm)
            if allowed:
                permissions.add(perm)
        return permissions
```

```javascript
// JavaScript example with attribute-based access control (ABAC)
class ResourceAuthorizer {
    constructor() {
        this.policies = [];
        this.denyPolicies = [];
    }

    // Define policy using a DSL
    definePolicy(policy) {
        const compiled = this.compilePolicy(policy);
        if (policy.effect === 'DENY') {
            this.denyPolicies.push(compiled);
        } else {
            this.policies.push(compiled);
        }
    }

    async authorize(context) {
        const { principal, resource, action } = context;

        // Validate tenant boundary
        if (resource.tenantId && resource.tenantId !== principal.tenantId) {
            throw new AuthorizationError('Cross-tenant access forbidden');
        }

        // Check deny policies first
        for (const policy of this.denyPolicies) {
            if (await this.evaluatePolicy(policy, context)) {
                return {
                    allowed: false,
                    reason: `Denied by policy: ${policy.id}`,
                    policy: policy.id
                };
            }
        }

        // Check allow policies
        for (const policy of this.policies) {
            if (await this.evaluatePolicy(policy, context)) {
                return {
                    allowed: true,
                    reason: `Allowed by policy: ${policy.id}`,
                    policy: policy.id
                };
            }
        }

        // Default deny
        return {
            allowed: false,
            reason: 'No matching policy',
            policy: null
        };
    }

    async evaluatePolicy(policy, context) {
        const { principal, resource, action } = context;

        // Check action matches
        if (!policy.actions.includes(action) && !policy.actions.includes('*')) {
            return false;
        }

        // Check resource matches
        if (!this.matchesResource(policy.resources, resource)) {
            return false;
        }

        // Check principal matches
        if (!this.matchesPrincipal(policy.principals, principal)) {
            return false;
        }

        // Evaluate conditions (ABAC)
        if (policy.conditions) {
            return await this.evaluateConditions(policy.conditions, context);
        }

        return true;
    }

    async evaluateConditions(conditions, context) {
        for (const condition of conditions) {
            const result = await this.evaluateCondition(condition, context);
            if (!result) return false;
        }
        return true;
    }

    async evaluateCondition(condition, context) {
        const { operator, left, right } = condition;

        const leftValue = await this.resolveValue(left, context);
        const rightValue = await this.resolveValue(right, context);

        switch (operator) {
            case 'equals':
                return leftValue === rightValue;
            case 'in':
                return rightValue.includes(leftValue);
            case 'contains':
                return leftValue.includes(rightValue);
            case 'startsWith':
                return leftValue.startsWith(rightValue);
            case 'matches':
                return new RegExp(rightValue).test(leftValue);
            default:
                throw new Error(`Unknown operator: ${operator}`);
        }
    }

    async resolveValue(expr, context) {
        if (typeof expr === 'string' && expr.startsWith('${')) {
            // Variable resolution
            const path = expr.slice(2, -1);
            return this.getPath(context, path);
        }
        return expr;
    }
}

// Usage example
const authorizer = new ResourceAuthorizer();

// Define policies
authorizer.definePolicy({
    id: 'user-own-files',
    effect: 'ALLOW',
    principals: ['user:*'],
    resources: ['/users/${principal.id}/files/*'],
    actions: ['read', 'write', 'delete'],
    conditions: [
        {
            operator: 'equals',
            left: '${resource.owner}',
            right: '${principal.id}'
        }
    ]
});

authorizer.definePolicy({
    id: 'deny-sensitive',
    effect: 'DENY',
    principals: ['*'],
    resources: ['**/sensitive/**', '**/.env', '**/secrets.json'],
    actions: ['*']
});

// Check authorization
const decision = await authorizer.authorize({
    principal: { id: 'user123', roles: ['developer'], tenantId: 'tenant1' },
    resource: { path: '/users/user123/files/document.pdf', owner: 'user123', tenantId: 'tenant1' },
    action: 'read'
});
```

**Red flags:**
- Missing tenant isolation checks
- No deny rule support
- Authorization based only on roles (not resources)
- Missing ownership validation
- No audit logging of access decisions
- Wildcard permissions without restrictions

### Dynamic (behavioral verification)

**Test cases:**
1. **Ownership validation**: User A accessing User B's resource → Denied
2. **Tenant isolation**: Cross-tenant resource access attempt → Blocked
3. **Deny precedence**: Allow rule + deny rule on same resource → Denied
4. **Hierarchy inheritance**: Access to parent grants child access → Verified
5. **Bulk operations**: Filter 100 resources by permission → Only authorized shown
6. **Permission enumeration**: List user's permissions on resource → Accurate

**Expected behavior:**
- Clear authorization decisions with reasons
- Consistent enforcement across all access paths
- No information leakage through authorization errors
- Audit trail of all authorization decisions
- Performance under 10ms for authorization checks

## Mappings

- **CWE**: CWE-639 (Authorization Bypass Through User-Controlled Key), CWE-863 (Incorrect Authorization), CWE-284 (Improper Access Control)
- **OWASP Top 10**: A01:2021 – Broken Access Control
- **OWASP MCP Top 10**: MCP-03 (Authorization Bypass), MCP-06 (Insecure Direct Object References)
- **OWASP ASVS**: V4.2.1 (Verify resource-based access control), V4.1.5 (Verify deny by default)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- All resources are public/shared equally
- Single-user system with no sharing
- Stateless computation-only server

**Compensating controls:**
- Data encryption with per-user keys
- Network-level isolation (VLANs, firewalls)
- Application-level data filtering
- Row-level security in databases

**Risk acceptance:**
- Simple ownership model acceptable for L1
- MUST implement RBAC with deny rules for L2+
- L3 requires attribute-based access control (ABAC)

## References

- 45% of MCP servers lack resource-level authorization (arXiv:2506.13538)
- IDOR vulnerability in file access (CVE-2025-53109)
- NIST SP 800-162: Guide to Attribute Based Access Control
- AWS IAM policy evaluation logic
- Multi-tenant isolation failures (OWASP Cloud Top 10)