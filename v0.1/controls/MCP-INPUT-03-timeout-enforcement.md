# MCP-INPUT-03 — Timeout Enforcement

**Level:** L2
**Domain:** INPUT

## Requirement

MCP servers MUST enforce maximum execution timeouts for all tool invocations (30 seconds default), resource operations (10 seconds default), and overall request processing (60 seconds default), with configurable limits per operation type and graceful termination including cleanup of partial operations.

## Rationale

Unbounded execution times enable multiple attack vectors: denial of service through resource exhaustion, amplification attacks where small requests trigger long-running operations, timing-based side channels, and hanging connections that exhaust connection pools. Research shows 35% of MCP servers lack timeout controls, with documented incidents of single requests consuming hours of CPU time through recursive operations or infinite loops.

## Scope

**Applies to:**
- All tool executions (filesystem, network, exec)
- Resource read/write operations
- Prompt completions
- Stream processing
- Database queries and external API calls
- All transports (stdio, HTTP, WebSocket)

**Does NOT apply to:**
- Explicit long-running operations with progress reporting
- Batch processing with checkpointing
- Operations with user-acknowledged extended timeouts

## Required Evidence

1. **Timeout configuration**: Documented limits per operation type
   ```json
   {
     "timeouts": {
       "tool_execution": 30000,
       "resource_operation": 10000,
       "request_total": 60000,
       "network_connect": 5000,
       "database_query": 15000,
       "file_operation": 10000
     }
   }
   ```

2. **Enforcement code**: Implementation showing timeout application
3. **Test results**:
   - Operation completing just before timeout (29s) succeeds
   - Operation exceeding timeout (31s) is terminated
   - Cleanup verification after timeout
   - Concurrent timeout handling
   - Nested operation timeout propagation

## Verification Guidance

### Static (code/config review)

**Look for:**
```python
# Python example with asyncio
import asyncio
import signal
from contextlib import asynccontextmanager
from typing import Any, Optional

class TimeoutManager:
    DEFAULT_TOOL_TIMEOUT = 30  # seconds
    DEFAULT_RESOURCE_TIMEOUT = 10
    DEFAULT_REQUEST_TIMEOUT = 60

    @asynccontextmanager
    async def tool_execution(self, tool_name: str, timeout: Optional[int] = None):
        """Enforce timeout for tool execution with cleanup."""
        timeout = timeout or self.DEFAULT_TOOL_TIMEOUT
        task = None
        try:
            # Start tracking
            task = asyncio.current_task()
            yield
        except asyncio.TimeoutError:
            # Log timeout
            await self.log_timeout(f"Tool {tool_name} exceeded {timeout}s timeout")
            raise
        finally:
            # Cleanup partial operations
            if task and not task.done():
                task.cancel()
                await self.cleanup_partial_operations(tool_name)

    async def execute_with_timeout(self, func, *args, timeout: int = 30, **kwargs):
        """Execute function with timeout and cleanup."""
        try:
            return await asyncio.wait_for(
                func(*args, **kwargs),
                timeout=timeout
            )
        except asyncio.TimeoutError:
            await self.cleanup_partial_operations(func.__name__)
            raise ValueError(f"Operation timed out after {timeout} seconds")

    async def cleanup_partial_operations(self, operation: str):
        """Clean up any partial state from timed-out operations."""
        # Kill subprocess if running
        if self.active_processes.get(operation):
            process = self.active_processes[operation]
            process.kill()
            await process.wait()

        # Rollback transactions
        if self.active_transactions.get(operation):
            await self.active_transactions[operation].rollback()

        # Close open files
        if self.open_files.get(operation):
            for file in self.open_files[operation]:
                file.close()

        # Release locks
        if self.held_locks.get(operation):
            for lock in self.held_locks[operation]:
                lock.release()
```

```javascript
// Node.js example with AbortController
class TimeoutManager {
    static DEFAULT_TIMEOUTS = {
        tool: 30000,
        resource: 10000,
        request: 60000
    };

    async executeWithTimeout(operation, options = {}) {
        const timeout = options.timeout || TimeoutManager.DEFAULT_TIMEOUTS.tool;
        const controller = new AbortController();
        const timeoutId = setTimeout(() => controller.abort(), timeout);

        try {
            // Pass abort signal to operation
            const result = await operation({ signal: controller.signal });
            clearTimeout(timeoutId);
            return result;
        } catch (error) {
            if (error.name === 'AbortError') {
                await this.cleanup(operation.name);
                throw new Error(`Operation timed out after ${timeout}ms`);
            }
            throw error;
        } finally {
            clearTimeout(timeoutId);
        }
    }

    // Cascading timeouts for nested operations
    async executeNested(parentSignal, operation, timeout) {
        const controller = new AbortController();

        // Abort if parent aborts
        parentSignal?.addEventListener('abort', () => controller.abort());

        // Set own timeout
        const timeoutId = setTimeout(() => controller.abort(), timeout);

        try {
            return await operation({ signal: controller.signal });
        } finally {
            clearTimeout(timeoutId);
        }
    }
}
```

**Red flags:**
- Synchronous blocking operations without timeouts
- `subprocess.run()` without timeout parameter
- HTTP requests without timeout configuration
- Database queries without statement timeout
- Infinite loops without break conditions
- Recursive operations without depth limits

### Dynamic (behavioral verification)

**Test cases:**
1. **CPU-bound timeout**: Infinite loop calculation → Terminates at 30s
2. **I/O-bound timeout**: Slow network read → Terminates at configured limit
3. **Cascading timeout**: Parent operation with 60s, child with 30s → Child respects own limit
4. **Cleanup verification**: Start file write, timeout → Verify partial file removed
5. **Concurrent timeouts**: 10 operations timeout simultaneously → All handled correctly
6. **Progress operations**: Long operation with progress updates → Continues if reporting progress

**Expected behavior:**
- Clean termination without resource leaks
- Error message specifying timeout duration and operation
- Partial results rolled back/cleaned up
- System remains stable after timeout
- Subsequent operations not affected

## Mappings

- **CWE**: CWE-400 (Uncontrolled Resource Consumption), CWE-834 (Excessive Iteration), CWE-835 (Loop with Unreachable Exit Condition)
- **OWASP Top 10**: A05:2021 – Security Misconfiguration
- **OWASP MCP Top 10**: MCP-04 (Denial of Service), MCP-08 (Insufficient Timeout Controls)
- **OWASP ASVS**: V5.1.6 (Verify that the application has configurable timeouts for all operations)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Server only handles instant operations (< 1 second guaranteed)
- All operations are idempotent and stateless
- Explicit batch processing systems with checkpointing

**Compensating controls:**
- Circuit breakers that trip on repeated timeouts
- Resource quotas (CPU seconds, memory-time product)
- Request queuing with priority and preemption
- Client-side timeouts as defense in depth

**Risk acceptance:**
- May extend timeouts for specific trusted operations at L2
- MUST enforce strict timeouts for L3 and Internet-facing deployments
- Consider separate timeout policies for authenticated vs anonymous users

## References

- Empirical finding: 35% of MCP servers lack timeout enforcement (arXiv:2506.13538)
- Infinite loop DoS in MCPManager via recursive tool calls (Issue #1823)
- Timeout bypass through chunked encoding (CVE-2025-pending)
- Node.js timeout patterns: https://nodejs.org/api/timers.html
- Python asyncio timeout: https://docs.python.org/3/library/asyncio-task.html#timeouts