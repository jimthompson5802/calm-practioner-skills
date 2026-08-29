---
name: calm-control-requirement
description: Convert CALM control requirements from markdown to JSON. Use when you have a markdown file with control metadata (control-id, name, description, section) and properties (enums, numeric with constraints, strings) that need to be converted to a CALM control requirement JSON file.
user-invocable: true
---

# CALM Control Requirement Converter

Convert markdown-based control specifications into valid CALM control requirement JSON format.

## Overview

This skill helps you convert control requirement definitions written in markdown into structured CALM JSON. It handles:

- Control metadata (ID, name, description, section)
- Property definitions with type validation
- Numeric constraints (min, max, integer/float)
- Enum properties with allowed values
- String properties with optional constraints

**Getting Started**: Use the [template.md](./template.md) as a starting point for your control definition.

## Workflow

### Step 1: Understand the Input Format

The markdown file should contain:

```markdown
# Control ID: [control-id]
## Name: [control-name]
## Section: [section-name]
## Description
[control description]

## Properties
### [Property Name]
- Type: [enum|numeric|string]
- Description: [description]
- [Type-specific fields]:
  - For numeric: Min: N, Max: N, Type: [integer|float]
  - For enum: Values: [value1, value2, ...]
```

### Step 2: Clarify the Control Definition

Before converting, ensure you have:
- [ ] Control ID (e.g., `CTL-001`)
- [ ] Control name and human-readable title
- [ ] Section/category this control belongs to
- [ ] Description of what the control enforces
- [ ] List of properties with their types and constraints

If any of these are missing, ask the user to provide them.

### Step 3: Parse Properties

For each property in the control:

**Numeric Properties:**
- Determine if it's `integer` or `float`
- Extract optional `minimum` constraint
- Extract optional `maximum` constraint
- Add `description`

**Enum Properties:**
- Extract all allowed `values`
- Add `description`

**String Properties:**
- Add `description`
- Optionally add `pattern` or length constraints if mentioned

### Step 4: Generate JSON Structure

Create a JSON object with this structure:

```json
{
  "control-id": "string",
  "control-name": "string",
  "section": "string",
  "description": "string",
  "properties": {
    "property-name": {
      "type": "string|numeric|enum",
      "description": "string",
      // Type-specific fields:
      // For numeric:
      "value-type": "integer|float",
      "minimum": number (optional),
      "maximum": number (optional),
      // For enum:
      "values": ["string"]
    }
  }
}
```

### Step 5: Validate

Verify the generated JSON:
- [ ] All required fields present (control-id, control-name, section, description, properties)
- [ ] Each property has a type, description
- [ ] Numeric properties have valid min/max if specified (min ≤ max)
- [ ] Enum properties have at least one value
- [ ] JSON is syntactically valid
- [ ] IDs follow expected naming convention

### Step 6: Output and Save

1. Display the generated JSON for review
2. Ask user to confirm before saving
3. Save to a file with naming pattern: `{control-id}-requirement.json`
4. Optionally, validate against CALM control schema

## Quality Checklist

Before considering the conversion complete:

- [ ] Control metadata is complete and clear
- [ ] All properties are accounted for
- [ ] Numeric constraints are logical (min < max)
- [ ] Enums have meaningful, distinct values
- [ ] JSON structure matches the schema
- [ ] File is saved in the expected location

## Example

**Input Markdown:**
```markdown
# Control ID: CTL-AUTH-001
## Name: Authentication Strength
## Section: Access Control
## Description
Enforces authentication mechanism requirements for system access.

## Properties
### min-password-length
- Type: numeric
- Description: Minimum password length requirement
- Type: integer
- Min: 8
- Max: 128

### allowed-auth-methods
- Type: enum
- Description: Approved authentication methods
- Values: [mfa, oauth2, saml, ldap]

### session-timeout-minutes
- Type: numeric
- Description: Maximum session duration in minutes
- Type: integer
- Min: 15
- Max: 480
```

**Output JSON:**
```json
{
  "control-id": "CTL-AUTH-001",
  "control-name": "Authentication Strength",
  "section": "Access Control",
  "description": "Enforces authentication mechanism requirements for system access.",
  "properties": {
    "min-password-length": {
      "type": "numeric",
      "description": "Minimum password length requirement",
      "value-type": "integer",
      "minimum": 8,
      "maximum": 128
    },
    "allowed-auth-methods": {
      "type": "enum",
      "description": "Approved authentication methods",
      "values": ["mfa", "oauth2", "saml", "ldap"]
    },
    "session-timeout-minutes": {
      "type": "numeric",
      "description": "Maximum session duration in minutes",
      "value-type": "integer",
      "minimum": 15,
      "maximum": 480
    }
  }
}
```

## Tips for Success

- **Be explicit about constraints**: Always specify integer vs. float, and include min/max when relevant
- **Use consistent naming**: Property names should be kebab-case (lowercase with hyphens)
- **Validate early**: Check constraints are logical before proceeding to JSON generation
- **Preserve intent**: The JSON should capture the original control's meaning and enforcement logic
- **Document unclear values**: If a user's markdown is ambiguous, ask for clarification rather than guessing

## Related Skills

- `calm` – General CALM architecture work and validation
- `calm-create-architecture` – Create full CALM architecture models
- `calm-architecture-discovery` – Discover architecture from code
