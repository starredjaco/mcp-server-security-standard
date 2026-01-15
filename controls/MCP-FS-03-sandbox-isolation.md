# MCP-FS-03 — Filesystem Sandboxing

**Level:** L2  
**Domain:** FS  

## Requirement

MCP servers with filesystem tools SHOULD run in isolated environments (containers, VMs) with minimal filesystem mounts and read-only root filesystems where feasible.

## Rationale

Defense-in-depth: even if path validation fails, filesystem isolation limits attacker access to host resources.

## Scope

Applies to deployment configuration (Docker, Kubernetes, systemd).

## Required Evidence

1. Dockerfile or pod spec showing read-only root filesystem
2. Volume mounts limited to specific directories needed for operation
3. No `/var/run/docker.sock` or privileged mounts

## Verification Guidance

### Static
- Inspect Dockerfile: `readOnlyRootFilesystem: true`
- Check volume mounts are minimal and specific
- Verify no `--privileged` flag

### Dynamic
- From within running container, attempt to write to `/usr/bin` → expect failure

## Mappings

- OWASP MCP Top 10: MCP-09 (Shadow MCP Servers - relates to deployment security)
- Docker CIS Benchmark: 5.12 (Run containers with a read-only root filesystem)

## Exceptions & Compensating Controls

NOT_APPLICABLE: stdio transport on developer workstation without containerization may skip this if low-risk profile accepted.

## References

- Docker security best practices
- Trend Micro: "Using Containers to Secure MCP Infrastructure" (Sept 2024)
