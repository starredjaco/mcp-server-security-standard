---
title: "MCP-INPUT-01 — JSON Schema Validation"
weight: 51
---

# MCP-INPUT-01 — JSON Schema Validation

**Level:** L1
**Domain:** INPUT

## Requirement

MCP servers MUST validate all tool arguments against a strict JSON Schema before execution, rejecting any input that does not conform to the defined schema with appropriate error messages.

## Rationale

Schema poisoning and tool poisoning attacks exploit unvalidated input to inject malicious instructions into tool parameters, default values, enums, and descriptions. Research shows that entire tool JSON schemas are processed by LLMs, making every field a potential attack vector. Strict validation prevents injection attacks, DoS via malformed input, and type confusion vulnerabilities.

## Scope

**Applies to:**
- All MCP tools accepting arguments
- Resource endpoints accepting parameters
- Prompt templates with variable substitution
- All transport types (stdio, HTTP, WebSocket)

**Does NOT apply to:**
- Static resources without parameters
- Tools with no arguments

## Required Evidence

1. **Schema definition**: JSON Schema for each tool with explicit type, format, and constraint definitions
2. **Validation implementation**: Code showing schema validation before tool execution
3. **Error handling**: Rejection of invalid input with specific error messages (not generic)
4. **Test results**:
   - Valid input accepted
   - Missing required field rejected
   - Wrong type rejected (string when expecting number)
   - Additional properties rejected when `additionalProperties: false`

## Verification Guidance

### Static (code/config review)

**Look for:**
```python
# Python example with jsonschema
from jsonschema import validate, ValidationError

TOOL_SCHEMA = {
    "type": "object",
    "properties": {
        "filename": {"type": "string", "maxLength": 255},
        "content": {"type": "string", "maxLength": 10000}
    },
    "required": ["filename"],
    "additionalProperties": False
}

def execute_tool(args):
    try:
        validate(instance=args, schema=TOOL_SCHEMA)
    except ValidationError as e:
        return {"error": f"Invalid arguments: {e.message}"}
    # Proceed with execution
```

**Red flags:**
- Direct use of arguments without validation
- Loose schemas with `additionalProperties: true` by default
- Missing `required` fields definition
- No `maxLength`, `maxItems`, or other bounds

### Dynamic (behavioral verification)

**Test cases:**
1. **Missing required field**: `{"optional": "value"}` → Expect rejection with "Missing required field: filename"
2. **Wrong type**: `{"filename": 123}` → Expect rejection with "Invalid type for filename"
3. **Extra properties**: `{"filename": "test", "malicious": "injection"}` → Expect rejection when `additionalProperties: false`
4. **Nested validation**: Complex objects validated recursively
5. **Enum validation**: Only allowed values accepted for enum fields

**Expected behavior:**
- Clear error messages identifying the validation failure
- No partial execution on validation failure
- No information leakage in error messages

## Mappings

- **CWE**: CWE-20 (Improper Input Validation), CWE-915 (Improperly Controlled Modification of Dynamically-Determined Object Attributes)
- **OWASP Top 10**: A03:2021 – Injection
- **OWASP MCP Top 10**: MCP-05 (Schema Poisoning)
- **OWASP ASVS**: V5.1.1 (Verify that the application has defenses against HTTP parameter pollution attacks)

## Exceptions & Compensating Controls

**NOT_APPLICABLE when:**
- Tool accepts no arguments (document tool list and confirm)
- Server only exposes static resources

**Compensating controls:**
- Runtime type checking in strongly-typed languages (partial mitigation)
- Input sanitization after validation (defense in depth, not replacement)

**Risk acceptance:**
- NOT acceptable for L1 or higher - schema validation is fundamental

## References

- CyberArk Labs: "Full Schema Poisoning" research demonstrating entire schema as attack vector
- Invariant Labs: Tool poisoning via hidden instructions in metadata
- MCPManager: "Rug Pull Attacks" showing tool definition changes post-installation (Dec 2025)
- JSON Schema Specification: https://json-schema.org/draft/2020-12/schema
- OWASP Input Validation Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html