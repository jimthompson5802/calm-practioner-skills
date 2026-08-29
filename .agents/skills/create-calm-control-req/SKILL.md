---
name: create-calm-control-req
description: Convert CALM control requirements from markdown to JSON. Use when you have a markdown file with control metadata (control-id, name, description) and properties (enums, numeric with constraints, strings, booleans) that need to be converted to a CALM control requirement JSON file.
user-invocable: true
---

# CALM Control Requirement Converter

Convert markdown-based control specifications into valid CALM control requirement JSON format, or show the authoring template when the user asks for it.

First, determine which invocation mode applies from the incoming request, prompt, or call context:

- If the user clearly asks to show, display, view, or get the template in natural language, enter Template Mode.
- Otherwise, look for the parameters `spec_file` and `output_dir` and enter Conversion Mode when both are present.
- If neither mode is satisfied, ask a single concise clarifying question:
  `Provide either a request to show the template, or both spec_file and output_dir.`

## Overview

This skill helps you convert control requirement definitions written in markdown into structured CALM JSON. It handles:

- Control metadata (ID, name, description)
- Property definitions with type validation
- Numeric constraints (min, max, integer/float)
- Enum properties with allowed values
- String properties with optional constraints
- Boolean properties
- Saving the generated JSON to the requested output location using a kebab-case file name derived from the control name

## Workflow

### Invocation Modes

This skill supports two mutually exclusive entry paths:

1. **Template Mode**
   Trigger this mode when the user clearly asks to see the template in natural language, such as:
   - `show template`
   - `show me the template`
   - `display the markdown template`
   - `can you show the control requirement template?`

   In Template Mode:
   1. Read `assets/template.md`
   2. Render the file contents verbatim in the response
   3. Tell the user to save the template as a markdown file, fill it in, and reinvoke the skill with `spec_file` and `output_dir`
   4. Stop without attempting conversion

   If the user both asks for the template and provides `spec_file` and `output_dir`, Template Mode wins.

2. **Conversion Mode**
   Trigger this mode when both `spec_file` and `output_dir` are present.

   In Conversion Mode:
   - Read the markdown control definition from `spec_file`
   - Convert it into CALM control requirement JSON
   - Save the generated JSON into `output_dir`

Do not ask for `spec_file` or `output_dir` when Template Mode applies.

### Step 0: Enable the CALM Skill

Ensure the CALM skill is enabled in your agent. This skill depends on the CALM control requirement definitions.


### Step 1: Understand the Input Format

In Conversion Mode, read the markdown control definition from `spec_file`.

The markdown file should contain:

```markdown
# Control ID: [control-id]
## Name: [control-name]
## Description
[control description]

## Properties
### [Property Name]
- Type: [enum|numeric|string|boolean]
- Description: [description]
- [Type-specific fields]:
  - For numeric: Min: N, Max: N, Type: [integer|float]
  - For enum: Values: [value1, value2, ...]
```

### Step 2: Clarify the Control Definition

Before converting, ensure you have:
- [ ] Markdown specification file path (`spec_file`)
- [ ] Output directory for generated JSON (`output_dir`)
- [ ] Control ID (e.g., `CTL-001`)
- [ ] Control name and human-readable title
- [ ] Description of what the control enforces
- [ ] List of properties with their types and constraints

If `spec_file` or `output_dir` is missing, ask only for the missing value unless Template Mode applies.

If any required control fields are missing from the markdown content itself, ask the user to provide or fix them in the spec file.

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

**Boolean Properties:**
- Add `description`
- Generate `"type": "boolean"`

### Step 4: Generate JSON Structure

Create a JSON Schema document with this structure:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "http://calm.finos.org/controls/[domain]/schema/[control-name].json",
  "title": "string",
  "type": "object",
  "allOf": [
    {
      "$ref": "https://calm.finos.org/release/1.2/meta/control-requirement.json"
    }
  ],
  "properties": {
    "control-id": {
      "const": "string"
    },
    "name": {
      "const": "string"
    },
    "description": {
      "const": "string"
    },
    "string-property": {
      "type": "string",
      "description": "string",
      "pattern": "regex (optional)",
      "minLength": 1,
      "maxLength": 255
    },
    "numeric-property": {
      "type": "integer",
      "description": "string",
      "minimum": 0,
      "maximum": 100
    },
    "enum-property": {
      "$ref": "#/defs/property-name"
    },
    "boolean-property": {
      "type": "boolean",
      "description": "string"
    }
  },
  "required": ["control-id", "name", "description", "string-property"],
  "defs": {
    "property-name": {
      "enum": ["value1", "value2"]
    }
  }
}
```

### Step 5: Validate

Verify the generated JSON:
- [ ] All required schema fields present (`$schema`, `$id`, `title`, `type`, `allOf`, `properties`, `required`)
- [ ] Control metadata is represented as `const` values for `control-id`, `name`, and `description`
- [ ] Each generated property is expressed as JSON Schema
- [ ] Numeric properties have valid min/max if specified (min ≤ max)
- [ ] Enum properties have at least one value
- [ ] Enum properties are defined in `defs` and referenced from `properties` when appropriate
- [ ] Boolean properties use `"type": "boolean"`
- [ ] JSON is syntactically valid
- [ ] IDs follow expected naming convention
- [ ] Output file name is derived from the control name and converted to kebab-case

### Step 6: Output and Save

1. Display the generated JSON for review
2. Derive the output file name from the markdown `Name` field by converting it to kebab-case
3. Save the JSON to `output_dir/<kebab-case-control-name>-requirement.json`
4. Display the full output path that was written
5. Optionally, validate against CALM control schema

## Quality Checklist

Before considering the conversion complete:

- [ ] `spec_file` was provided and read successfully
- [ ] `output_dir` was provided
- [ ] Control metadata is complete and clear
- [ ] All properties are accounted for
- [ ] Numeric constraints are logical (min < max)
- [ ] Enums have meaningful, distinct values
- [ ] JSON structure matches the schema
- [ ] File is saved in the specified output location with a kebab-case name based on the control name

## Example

**Template Mode Requests:**
```text
show template
show me the template
can you display the control requirement template?
```

**Template Mode Response:**
- Display the full contents of `assets/template.md`
- Instruct the user to save it as a markdown file, fill it in, and reinvoke the skill with `spec_file` and `output_dir`

**Input Markdown:**
```markdown
# Control ID: CTL-AUTH-001
## Name: Authentication Strength
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

### mfa-required
- Type: boolean
- Description: Whether multi-factor authentication is mandatory
```

**Output JSON:**
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "http://calm.finos.org/controls/security/schema/authentication-strength.json",
  "title": "Authentication Strength",
  "type": "object",
  "allOf": [
    {
      "$ref": "https://calm.finos.org/release/1.2/meta/control-requirement.json"
    }
  ],
  "properties": {
    "control-id": {
      "const": "CTL-AUTH-001"
    },
    "name": {
      "const": "Authentication Strength"
    },
    "description": {
      "const": "Enforces authentication mechanism requirements for system access."
    },
    "min-password-length": {
      "type": "integer",
      "description": "Minimum password length requirement",
      "minimum": 8,
      "maximum": 128
    },
    "allowed-auth-methods": {
      "description": "Approved authentication methods",
      "$ref": "#/defs/allowed-auth-methods"
    },
    "session-timeout-minutes": {
      "type": "integer",
      "description": "Maximum session duration in minutes",
      "minimum": 15,
      "maximum": 480
    },
    "mfa-required": {
      "type": "boolean",
      "description": "Whether multi-factor authentication is mandatory"
    }
  },
  "required": [
    "control-id",
    "name",
    "description",
    "min-password-length",
    "allowed-auth-methods",
    "session-timeout-minutes",
    "mfa-required"
  ],
  "defs": {
    "allowed-auth-methods": {
      "enum": ["mfa", "oauth2", "saml", "ldap"]
    }
  }
}
```

**Saved file path:**
```text
[output_dir]/authentication-strength-requirement.json
```

## Tips for Success

- **Be explicit about constraints**: Always specify integer vs. float, and include min/max when relevant
- **Use boolean for true/false controls**: Model on/off or required/not-required settings as `"type": "boolean"`
- **Use consistent naming**: Property names should be kebab-case (lowercase with hyphens)
- **Use a descriptive control name**: The generated JSON file name is derived from the control name after converting it to kebab-case
- **Validate early**: Check constraints are logical before proceeding to JSON generation
- **Preserve intent**: The JSON should capture the original control's meaning and enforcement logic
- **Document unclear values**: If a user's markdown is ambiguous, ask for clarification rather than guessing

## Related Skills

- `calm` – General CALM architecture work and validation
- `calm-create-architecture` – Create full CALM architecture models
- `calm-architecture-discovery` – Discover architecture from code
