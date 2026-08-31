# Control ID: [ENTER-CONTROL-ID]

## Name: [Enter Control Name]

## Description

[Enter a clear, concise description of what this control enforces. Describe the purpose, scope, and intended outcomes. This will be the main documentation of the control.]

---

## Properties

### [Property Name 1: camelCase or kebab-case]

- Type: numeric
- Description: [What does this property represent? What constraint does it enforce?]
- Value Type: integer
- Minimum: [optional number, e.g., 8]
- Maximum: [optional number, e.g., 128]

**Example**: For a password length requirement, you might have:
- Type: numeric
- Description: Minimum password length in characters
- Value Type: integer
- Minimum: 8
- Maximum: 128

---

### [Property Name 2: camelCase or kebab-case]

- Type: enum
- Description: [What are the allowed options? Why are these options valid?]
- Values: 
  - value-1
  - value-2
  - value-3
  - value-4

**Example**: For authentication methods, you might have:
- Type: enum
- Description: Approved authentication mechanisms
- Values:
  - mfa
  - oauth2
  - saml
  - ldap

---

### [Property Name 3: camelCase or kebab-case]

- Type: set
- Description: [What options can be selected together? Use this when one or more allowed values may be chosen.]
- Values:
  - value-1
  - value-2
  - value-3

**Example**: For approved deployment regions, you might have:
- Type: set
- Description: Approved regions where this workload may run
- Values:
  - us-east-1
  - us-west-2
  - eu-west-1

---

### [Property Name 4: camelCase or kebab-case]

- Type: string
- Description: [What is the purpose of this string property? Are there any format constraints?]

**Example**: For a policy reference, you might have:
- Type: string
- Description: Reference to the security policy document URL or identifier

---

### [Property Name 5: camelCase or kebab-case]

- Type: boolean
- Description: [What true/false decision does this property capture?]

**Example**: For an MFA toggle, you might have:
- Type: boolean
- Description: Whether multi-factor authentication is mandatory

---

## Template Instructions

**Before converting to JSON, verify:**

- [ ] Control ID follows your naming convention (e.g., CTL-001)
- [ ] Name is concise and descriptive
- [ ] Description is complete and unambiguous
- [ ] All properties are listed with their types
- [ ] Numeric properties have value-type, and constraints where needed
- [ ] Enum properties list all valid values
- [ ] Set properties list all valid values and are intended for multi-select use
- [ ] Boolean properties represent true/false behavior clearly
- [ ] Property names are consistent (kebab-case recommended)
- [ ] No typos or formatting issues

**Tips:**

- Use kebab-case for all property names (e.g., `min-password-length` not `minPasswordLength`)
- Be specific about constraints: don't use vague ranges
- For numeric properties, always specify if it's integer or float
- Use `enum` for exactly one allowed value and `set` for one or more allowed values
- For enums and sets, ensure values are meaningful and complete
- For boolean properties, describe what `true` and `false` mean in plain language
- Keep descriptions short but clear—one sentence is ideal, two is maximum
