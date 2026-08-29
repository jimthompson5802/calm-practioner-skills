# Control ID: CTRL-001

## Name: Sample Control

## Description

This demonstrates creating a CALM Control Requirement

---

## Properties

### time-out

- Type: numeric
- Description: Time out value for the session
- Value Type: integer
- Minimum: 1

### latency-range
- type: numeric
- description: latency requirements for transaction
- value type: float
- minimum: 0.5
- maximum: 20.0

### protection-level

- Type: enum
- Description: specifies defined protection levels
- Values:
  - low
  - medium
  - high

### information-level
- Type: enum
- Description: level of informatoion confidentiality
- Values:
  - not sensitive
  - sensitive
  - very sensitive

### control-string

- Type: string
- Description: Arbitrary string the user can specify

### control-flag
- Type: boolean
- Description: indicates where the control flag is on or off

