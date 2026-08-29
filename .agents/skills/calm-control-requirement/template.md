# Control ID: [ENTER-CONTROL-ID]

## Name: [Enter Control Name]

## Section: [Enter Section/Category]

## Description

[Enter a clear, concise description of what this control enforces. Describe the purpose, scope, and intended outcomes. This will be the main documentation of the control.]

---

## Properties

### [Property Name 1: camelCase or kebab-case]

- **Type**: numeric
- **Description**: [What does this property represent? What constraint does it enforce?]
- **Value Type**: integer
- **Minimum**: [optional number, e.g., 8]
- **Maximum**: [optional number, e.g., 128]

**Example**: For a password length requirement, you might have:
- Type: numeric
- Description: Minimum password length in characters
- Value Type: integer
- Minimum: 8
- Maximum: 128

---

### [Property Name 2: camelCase or kebab-case]

- **Type**: enum
- **Description**: [What are the allowed options? Why are these options valid?]
- **Values**: 
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

- **Type**: string
- **Description**: [What is the purpose of this string property? Are there any format constraints?]

**Example**: For a policy reference, you might have:
- Type: string
- Description: Reference to the security policy document URL or identifier

---

## Template Instructions

**Before converting to JSON, verify:**

- [ ] Control ID follows your naming convention (e.g., CTL-SECTION-###)
- [ ] Name is concise and descriptive
- [ ] Section categorizes the control logically
- [ ] Description is complete and unambiguous
- [ ] All properties are listed with their types
- [ ] Numeric properties have value-type, and constraints where needed
- [ ] Enum properties list all valid values
- [ ] Property names are consistent (kebab-case recommended)
- [ ] No typos or formatting issues

**Tips:**

- Use kebab-case for all property names (e.g., `min-password-length` not `minPasswordLength`)
- Be specific about constraints: don't use vague ranges
- For numeric properties, always specify if it's integer or float
- For enums, ensure values are meaningful and complete
- Keep descriptions short but clear—one sentence is ideal, two is maximum
