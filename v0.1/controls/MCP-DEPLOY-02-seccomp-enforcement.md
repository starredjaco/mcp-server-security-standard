# MCP-DEPLOY-02 — System Call Filtering (seccomp/AppArmor)

**Level:** L3
**Domain:** DEPLOY

## Requirement

MCP servers running in containerized or Linux environments MUST implement system call filtering using seccomp, AppArmor, or equivalent kernel security modules to restrict available system calls to the minimum required set.

## Rationale

MCP servers with command execution capabilities represent high-value targets for exploitation. Research shows 43% of analyzed MCP servers contained command injection flaws (Equixly, 2025). When compromised, unrestricted system call access enables attackers to escalate privileges, establish persistence, and perform lateral movement. CVE-2025-53109 demonstrated how a filesystem MCP server vulnerability led to full system compromise via unfiltered system calls. Seccomp profiles reduce the kernel attack surface by 95%+ and have prevented exploitation in multiple documented MCP incidents. Docker's default seccomp profile blocks 44 out of 300+ system calls, preventing common attack techniques without impacting legitimate MCP operations.

## Scope

**Applies to:**
- Containerized MCP servers (Docker, Kubernetes)
- Linux-based MCP server deployments
- MCP servers with tool execution capabilities
- High-value production deployments (L3)

**Does NOT apply to:**
- Windows-based deployments (use Windows sandboxing instead)
- macOS deployments (use sandbox-exec instead)
- Development environments where debugging requires full syscall access

## Required Evidence

1. **Seccomp profile definition**: JSON/YAML profile or AppArmor policy file
2. **Profile enforcement**: Container or systemd configuration applying the profile
3. **Syscall allowlist**: Documentation of required system calls with justification
4. **Blocked syscall test**: Demonstration that dangerous syscalls fail
5. **Functional test**: MCP server operates correctly with profile enforced
6. **Audit logs**: Evidence of blocked syscall attempts in production

## Verification Guidance

### Static (code/config review)

**Docker Compose example:**
```yaml
services:
  mcp_server:
    image: mcp-server:latest
    security_opt:
      - seccomp=/path/to/mcp-seccomp.json
      - apparmor=mcp-server-profile
      - no-new-privileges:true
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE  # Only if needed
```

**Kubernetes example:**
```yaml
apiVersion: v1
kind: Pod
spec:
  securityContext:
    seccompProfile:
      type: RuntimeDefault  # Or custom profile
    runAsNonRoot: true
    runAsUser: 1000
    allowPrivilegeEscalation: false
  containers:
  - name: mcp-server
    securityContext:
      capabilities:
        drop: ["ALL"]
      readOnlyRootFilesystem: true
```

**Custom seccomp profile (mcp-seccomp.json):**
```json
{
  "defaultAction": "SCMP_ACT_ERRNO",
  "architectures": ["SCMP_ARCH_X86_64"],
  "syscalls": [
    {
      "names": [
        "read", "write", "open", "close", "stat",
        "fstat", "lstat", "poll", "mmap", "mprotect",
        "munmap", "brk", "rt_sigaction", "rt_sigprocmask",
        "ioctl", "access", "select", "exit_group"
      ],
      "action": "SCMP_ACT_ALLOW"
    },
    {
      "names": ["socket", "connect", "sendto", "recvfrom"],
      "action": "SCMP_ACT_ALLOW",
      "args": [
        {
          "index": 0,
          "value": 2,  // AF_INET
          "op": "SCMP_CMP_EQ"
        }
      ]
    }
  ]
}
```

**AppArmor profile example:**
```
#include <tunables/global>

profile mcp-server flags=(attach_disconnected,mediate_deleted) {
  #include <abstractions/base>

  # Network access
  network inet tcp,
  network inet udp,

  # File access (restricted)
  /usr/local/mcp/** r,
  /var/lib/mcp/** rw,
  /tmp/mcp-* rw,

  # Deny everything else
  deny /** w,
  deny /etc/** w,
  deny /home/** rw,
  deny /root/** rw,

  # Block dangerous capabilities
  deny capability setuid,
  deny capability setgid,
  deny capability sys_admin,
  deny capability sys_module,
}
```

### Dynamic (behavioral verification)

**Test blocked syscalls:**
```bash
# Inside container, attempt privileged operations
docker exec mcp-container sh -c "mount -t tmpfs none /mnt"
# Expected: Operation not permitted

# Check seccomp status
docker inspect mcp-container | jq '.[0].HostConfig.SecurityOpt'

# Monitor blocked syscalls (requires auditd)
ausearch -m SECCOMP -ts recent

# Verify with strace
strace -e trace=mount docker exec mcp-container mount
# Should show: mount(...) = -1 EPERM (Operation not permitted)
```

**Functional validation:**
1. Normal MCP operations work (tool execution, file access)
2. Privilege escalation attempts fail
3. Container escape techniques blocked
4. Network operations limited to allowed protocols

## Mappings

- **CWE**: CWE-265 (Privilege/Sandbox Issues), CWE-250 (Execution with Unnecessary Privileges)
- **OWASP Top 10**: A05:2021 – Security Misconfiguration, A08:2021 – Software and Data Integrity Failures
- **OWASP MCP Top 10**: MCP-05 (Command Injection & Execution)
- **OWASP ASVS**: V14.2.6 (The application uses operating system level sandboxing)
- **MITRE ATT&CK**: T1055 (Process Injection), T1611 (Escape to Host)
- **CIS Docker Benchmark**: 5.12 (Ensure that the Docker server is configured with appropriate seccomp profile)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Windows deployments (use Windows Sandbox or AppContainer)
- macOS deployments (use sandbox-exec profiles)
- Bare metal deployments where kernel features unavailable
- Development environments requiring debugger access

**Compensating controls:**
- SELinux policies providing similar MAC enforcement
- gVisor/Kata Containers providing stronger isolation
- Network segmentation limiting lateral movement
- Runtime monitoring (Falco, Sysdig) detecting anomalous syscalls

**Risk acceptance:**
- Development environments MAY disable for debugging
- Legacy systems MAY use monitoring-only mode initially
- Some MCP tools MAY require specific syscalls (document exceptions)

## References

- Equixly: "43% of MCP Servers Contain Command Injection" (March 2025)
- CVE-2025-53109: Anthropic Filesystem Server system compromise via unfiltered syscalls
- Docker Security: Default seccomp profiles - https://docs.docker.com/engine/security/seccomp/
- Kubernetes Security Standards: Pod Security Standards - https://kubernetes.io/docs/concepts/security/pod-security-standards/
- AppArmor Documentation: https://gitlab.com/apparmor/apparmor/-/wikis/Documentation
- "Sandboxing MCP Servers with seccomp-bpf" - Trail of Bits (July 2025)
