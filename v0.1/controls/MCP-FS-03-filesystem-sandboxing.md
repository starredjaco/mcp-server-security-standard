---
title: "MCP-FS-03 — Filesystem Sandboxing"
weight: 13
---

# MCP-FS-03 — Filesystem Sandboxing

**Level:** L2
**Domain:** FS

## Requirement

MCP servers SHOULD run within a filesystem sandbox that restricts access to only required paths at the operating system or container level.

## Rationale

Application-level path validation can contain bugs. OS-level sandboxing provides defense-in-depth by enforcing filesystem restrictions regardless of application logic flaws.

## Scope

- **Tools:** All tools
- **Resources:** All resources
- **Transport:** All transports
- **Profiles:** DEV-WORKSTATION, SHARED-INTERNAL, INTERNET-FACING, HIGH-RISK-TOOLS, REGULATED

## Required Evidence

- Container configuration shows restricted volume mounts (only required paths)
- No host filesystem mounts beyond necessary directories
- Docker socket is not mounted into the container
- Mounts use read-only mode where write access is not required
- Sandbox configuration documented and reviewed

## Verification Guidance (non-tool-specific)

### Static (code/config review)

Look for in container configuration (Dockerfile, docker-compose.yml, K8s manifests):

- Explicit volume mounts limited to required paths
- Read-only mounts where appropriate (`:ro` flag)
- Absence of dangerous mounts:
  - `/var/run/docker.sock`
  - `/` (root filesystem)
  - `/etc`, `/proc`, `/sys` (unless specifically required)

Example secure configuration:

```yaml
volumes:
  - /data/project:/app/data:ro
  - /tmp/workdir:/app/tmp:rw
```

Red flags:

- `docker.sock` mounted
- Bind mounts to sensitive host directories
- Privileged container mode
- Missing volume restrictions (default allows all)

### Dynamic (behavioral verification)

Test cases:

1. Attempt to read file outside mounted volumes → Should fail at OS level
2. Attempt to write to read-only mount → Should fail
3. Verify container cannot access host-only paths
4. Verify Docker socket operations fail if not needed

## Mappings

- **CWE:** CWE-250 (Execution with Unnecessary Privileges)
- **OWASP MCP Top 10:** Related to deployment security
- **OWASP ASVS:** V14.2 (Dependency)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**

- Server runs as native process without containerization AND achieves L3 with all other FS controls
- Deployment profile is LOCAL-DEV with single-user trusted environment

**Compensating controls:**

- chroot jails (less isolation than containers)
- VM-level isolation (stronger than containers)
- Mandatory Access Control (SELinux, AppArmor) restricting file access

## References

- [Docker Security Best Practices](https://docs.docker.com/engine/security/)
- [MCP Hardening Guide](https://modelcontextprotocol-security.io/hardening/)
