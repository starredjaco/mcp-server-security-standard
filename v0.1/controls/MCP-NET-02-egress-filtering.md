# MCP-NET-02 — Egress Traffic Filtering

**Level:** L3
**Domain:** NET

## Requirement

MCP servers MUST implement egress traffic filtering that restricts outbound connections to an explicit allowlist of destinations, protocols, and ports, with mandatory DNS resolution control, connection rate limiting, and comprehensive logging of all blocked attempts.

## Rationale

Unrestricted egress enables data exfiltration, command-and-control communication, and lateral movement in compromised environments. Research shows 80% of MCP servers allow unrestricted outbound connections, enabling attackers to exfiltrate data to arbitrary destinations. Documented incidents include cryptocurrency mining through compromised servers, data theft to attacker-controlled infrastructure, and botnet participation through unrestricted egress.

## Scope

**Applies to:**
- All outbound network connections (HTTP/HTTPS, TCP, UDP)
- DNS queries and resolutions
- Database and service connections
- Webhook and callback URLs
- File downloads and API calls
- All network interfaces and protocols

**Does NOT apply to:**
- Loopback connections (127.0.0.1, ::1)
- Unix domain sockets
- Internal service mesh communication (if properly segmented)

## Required Evidence

1. **Egress filtering configuration**: Allowlist of permitted destinations
   ```yaml
   egress_policy:
     default_action: DENY
     allowed_destinations:
       - host: api.company.com
         ports: [443]
         protocols: [https]
         purpose: "Internal API access"

       - host: "*.amazonaws.com"
         ports: [443]
         protocols: [https]
         purpose: "AWS service access"
         restrictions:
           regions: [us-east-1, eu-west-1]
           services: [s3, dynamodb]

       - host: database.internal
         ports: [5432]
         protocols: [tcp]
         purpose: "PostgreSQL database"

     denied_destinations:
       - host: "*.crypto-mining.com"
       - host: "*.torproject.org"
       - cidr: "10.0.0.0/8"  # Except explicitly allowed

     dns_policy:
       allowed_resolvers:
         - 10.0.0.53
         - 10.0.0.54
       block_doh: true  # Block DNS over HTTPS
       block_dot: true  # Block DNS over TLS

     rate_limits:
       per_destination:
         max_connections: 100
         time_window: 60  # seconds
       global:
         max_new_connections: 1000
         time_window: 60
   ```

2. **Enforcement implementation**: Code showing egress filtering
3. **Test results**:
   - Connection to allowed host succeeds
   - Connection to non-allowed host blocked
   - DNS resolution to unauthorized resolver blocked
   - Rate limit exceeded triggers blocking
   - Blocked attempts appear in logs

## Verification Guidance

### Static (code/config review)

**Look for:**
```python
# Python example with egress filtering
import socket
import ipaddress
from typing import Optional, Tuple, List, Dict
from dataclasses import dataclass
import dns.resolver
import time
from collections import defaultdict

@dataclass
class EgressRule:
    host_pattern: str
    allowed_ports: List[int]
    allowed_protocols: List[str]
    purpose: str
    rate_limit: Optional[int] = None

class EgressFilter:
    def __init__(self, default_deny: bool = True):
        self.default_deny = default_deny
        self.allowed_destinations: List[EgressRule] = []
        self.denied_cidrs: List[ipaddress.IPv4Network] = []
        self.allowed_dns_servers = ['10.0.0.53', '10.0.0.54']
        self.connection_counts = defaultdict(lambda: {'count': 0, 'reset_time': 0})

        # Monkey-patch socket to enforce filtering
        self._original_connect = socket.socket.connect
        socket.socket.connect = self._filtered_connect

    def add_allowed_destination(self, rule: EgressRule):
        """Add an allowed egress destination."""
        self.allowed_destinations.append(rule)

    def is_connection_allowed(self, host: str, port: int,
                             protocol: str = 'tcp') -> Tuple[bool, str]:
        """Check if outbound connection is allowed."""

        # Resolve hostname to IP
        try:
            ip = self._resolve_host(host)
        except Exception as e:
            return False, f"DNS resolution failed: {e}"

        # Check if IP is in denied ranges
        ip_obj = ipaddress.ip_address(ip)
        for denied_cidr in self.denied_cidrs:
            if ip_obj in denied_cidr:
                return False, f"IP {ip} in denied range {denied_cidr}"

        # Check against allowed rules
        for rule in self.allowed_destinations:
            if self._matches_rule(host, ip, port, protocol, rule):
                # Check rate limit
                if rule.rate_limit and not self._check_rate_limit(host, rule.rate_limit):
                    return False, f"Rate limit exceeded for {host}"
                return True, f"Allowed by rule: {rule.purpose}"

        # Default action
        if self.default_deny:
            return False, f"No matching allow rule for {host}:{port}"
        else:
            return True, "Default allow (not recommended)"

    def _filtered_connect(self, address):
        """Wrapper for socket.connect with filtering."""
        if isinstance(address, tuple):
            host, port = address[0], address[1]

            # Skip filtering for loopback
            if host in ['127.0.0.1', 'localhost', '::1']:
                return self._original_connect(address)

            # Check if connection is allowed
            allowed, reason = self.is_connection_allowed(host, port)

            if not allowed:
                self._log_blocked_connection(host, port, reason)
                raise ConnectionError(f"Egress blocked: {reason}")

            self._log_allowed_connection(host, port, reason)

        return self._original_connect(address)

    def _resolve_host(self, host: str) -> str:
        """Resolve hostname with DNS filtering."""
        # Configure resolver to use only allowed DNS servers
        resolver = dns.resolver.Resolver()
        resolver.nameservers = self.allowed_dns_servers

        try:
            # Block DNS over HTTPS/TLS
            if host.startswith('https://') or host.startswith('dns://'):
                raise ValueError("DNS over HTTPS/TLS not allowed")

            answers = resolver.resolve(host, 'A')
            return str(answers[0])
        except:
            # If already an IP, return as-is
            return host

    def _matches_rule(self, host: str, ip: str, port: int,
                     protocol: str, rule: EgressRule) -> bool:
        """Check if connection matches allow rule."""

        # Check port
        if port not in rule.allowed_ports:
            return False

        # Check protocol
        if protocol not in rule.allowed_protocols:
            return False

        # Check host pattern
        import fnmatch
        if not fnmatch.fnmatch(host, rule.host_pattern):
            # Also check IP match for cases where pattern is IP-based
            if not fnmatch.fnmatch(ip, rule.host_pattern):
                return False

        return True

    def _check_rate_limit(self, host: str, limit: int) -> bool:
        """Check if connection is within rate limit."""
        current_time = time.time()

        # Reset counter if window expired
        if current_time > self.connection_counts[host]['reset_time']:
            self.connection_counts[host] = {
                'count': 0,
                'reset_time': current_time + 60  # 1-minute window
            }

        # Check limit
        if self.connection_counts[host]['count'] >= limit:
            return False

        self.connection_counts[host]['count'] += 1
        return True

    def _log_blocked_connection(self, host: str, port: int, reason: str):
        """Log blocked egress attempt."""
        import logging
        logging.warning(f"EGRESS_BLOCKED: {host}:{port} - {reason}")
        # Send to SIEM
        self._send_to_siem({
            'event': 'egress_blocked',
            'host': host,
            'port': port,
            'reason': reason,
            'timestamp': time.time()
        })

    def _log_allowed_connection(self, host: str, port: int, reason: str):
        """Log allowed egress connection."""
        import logging
        logging.info(f"EGRESS_ALLOWED: {host}:{port} - {reason}")
```

```javascript
// Node.js example with egress proxy
const net = require('net');
const dns = require('dns').promises;
const { URL } = require('url');

class EgressProxy {
    constructor(config) {
        this.allowedHosts = new Map();
        this.deniedCIDRs = [];
        this.allowedDNS = config.allowedDNS || ['10.0.0.53'];
        this.rateLimits = new Map();
        this.defaultDeny = config.defaultDeny !== false;

        // Intercept outbound connections
        this.interceptConnections();
    }

    addAllowedDestination(pattern, ports, protocols, options = {}) {
        this.allowedHosts.set(pattern, {
            ports: new Set(ports),
            protocols: new Set(protocols),
            rateLimit: options.rateLimit,
            purpose: options.purpose
        });
    }

    interceptConnections() {
        const originalConnect = net.Socket.prototype.connect;

        net.Socket.prototype.connect = function(...args) {
            const options = args[0];

            if (typeof options === 'object' && options.host) {
                const { host, port } = options;

                // Skip localhost
                if (['localhost', '127.0.0.1', '::1'].includes(host)) {
                    return originalConnect.apply(this, args);
                }

                // Check if allowed
                const allowed = this.checkEgress(host, port);
                if (!allowed.permitted) {
                    const error = new Error(`Egress blocked: ${allowed.reason}`);
                    error.code = 'EGRESS_DENIED';
                    throw error;
                }
            }

            return originalConnect.apply(this, args);
        }.bind(this);

        // Also intercept HTTP/HTTPS
        this.interceptHTTP();
    }

    interceptHTTP() {
        const http = require('http');
        const https = require('https');

        const wrapRequest = (module) => {
            const original = module.request;

            module.request = (options, callback) => {
                const url = typeof options === 'string' ? new URL(options) : options;

                const check = this.checkEgress(
                    url.hostname || url.host,
                    url.port || (url.protocol === 'https:' ? 443 : 80),
                    url.protocol.slice(0, -1)  // Remove trailing colon
                );

                if (!check.permitted) {
                    const error = new Error(`HTTP egress blocked: ${check.reason}`);
                    error.code = 'EGRESS_DENIED';

                    if (callback) {
                        process.nextTick(() => callback(error));
                        return { on: () => {}, end: () => {} };  // Mock request
                    }
                    throw error;
                }

                return original.call(module, options, callback);
            };
        };

        wrapRequest(http);
        wrapRequest(https);
    }

    checkEgress(host, port, protocol = 'tcp') {
        // Check rate limiting first
        if (!this.checkRateLimit(host)) {
            this.logBlocked(host, port, 'Rate limit exceeded');
            return { permitted: false, reason: 'Rate limit exceeded' };
        }

        // Check allowed hosts
        for (const [pattern, config] of this.allowedHosts) {
            if (this.matchesPattern(host, pattern)) {
                if (config.ports.has(port) &&
                    (config.protocols.has(protocol) || config.protocols.has('*'))) {
                    this.logAllowed(host, port, config.purpose);
                    return { permitted: true, reason: config.purpose };
                }
            }
        }

        // Default deny
        if (this.defaultDeny) {
            this.logBlocked(host, port, 'No matching allow rule');
            return { permitted: false, reason: 'Not in allowlist' };
        }

        return { permitted: true, reason: 'Default allow' };
    }

    checkRateLimit(host) {
        const now = Date.now();
        const window = 60000;  // 1 minute

        if (!this.rateLimits.has(host)) {
            this.rateLimits.set(host, []);
        }

        const hostLimits = this.rateLimits.get(host);

        // Clean old entries
        const recent = hostLimits.filter(time => now - time < window);
        this.rateLimits.set(host, recent);

        // Check limit (100 connections per minute)
        if (recent.length >= 100) {
            return false;
        }

        recent.push(now);
        return true;
    }

    matchesPattern(host, pattern) {
        // Support wildcards
        const regex = pattern
            .replace(/\./g, '\\.')
            .replace(/\*/g, '.*');
        return new RegExp(`^${regex}$`).test(host);
    }

    logBlocked(host, port, reason) {
        console.error(`[EGRESS_BLOCKED] ${host}:${port} - ${reason}`);
        // Send to SIEM/monitoring
    }

    logAllowed(host, port, purpose) {
        console.log(`[EGRESS_ALLOWED] ${host}:${port} - ${purpose}`);
    }
}
```

**Red flags:**
- No egress filtering at all
- Overly permissive rules (*.* allowed)
- DNS resolution without restrictions
- No rate limiting on connections
- Missing logging of blocked attempts
- Bypass mechanisms in code

### Dynamic (behavioral verification)

**Test cases:**
1. **Allowed connection**: Connect to api.company.com:443 → Success
2. **Blocked connection**: Connect to evil.com:443 → Connection refused
3. **DNS filtering**: Query unauthorized DNS server → Blocked
4. **Rate limiting**: 101 connections in 60 seconds → Last one blocked
5. **Data exfiltration**: Attempt to POST to pastebin.com → Blocked
6. **Protocol enforcement**: HTTP to HTTPS-only host → Blocked

**Expected behavior:**
- Only allowlisted destinations reachable
- Clear error messages for blocked connections
- All blocked attempts logged with details
- Rate limits enforced per destination
- No DNS leakage to unauthorized resolvers

## Mappings

- **CWE**: CWE-829 (Inclusion of Functionality from Untrusted Control Sphere), CWE-923 (Improper Restriction of Communication Channel)
- **OWASP Top 10**: A05:2021 – Security Misconfiguration, A08:2021 – Software and Data Integrity Failures
- **OWASP MCP Top 10**: MCP-09 (Unrestricted Egress), MCP-05 (Data Exfiltration)
- **OWASP ASVS**: V14.4.6 (Verify egress traffic filtering), V9.2.5 (Verify secure communication configuration)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Completely air-gapped system (no network)
- Local development with no sensitive data
- Read-only kiosk systems

**Compensating controls:**
- Network-level firewall rules (less granular)
- Data loss prevention (DLP) systems
- Proxy servers with content inspection
- Network segmentation and VLANs

**Risk acceptance:**
- May skip for L1 internal systems
- Recommended for L2 with sensitive data
- MANDATORY for L3 and Internet-facing systems

## References

- 80% of MCP servers lack egress filtering (arXiv:2506.13538)
- Data exfiltration via unrestricted egress (Incident Response Report 2024)
- NIST SP 800-41: Guidelines on Firewalls and Firewall Policy
- Zero Trust Architecture principles (NIST SP 800-207)
- Egress filtering best practices (SANS Institute)