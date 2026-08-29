# Control Creation Example

This directory demonstrates the `calm-control-requirement` agent skill, which converts a markdown-based control specification into a CALM control requirement JSON file (a JSON Schema document).

## Contents

```
control-creation/
├── control-spec/
│   └── control-spec-001.md          # Input: markdown control specification
└── generated-requirements/
    └── sample-control-requirement.json  # Output: generated CALM control requirement JSON
```

## The Skill

`calm-control-requirement` supports two modes:

- **Template Mode** — shows the markdown authoring template (`show template`) so you can draft a new spec.
- **Conversion Mode** — converts a filled-in markdown spec into CALM control requirement JSON, given a `spec_file` and an `output_dir`.

## Input: `control-spec/control-spec-001.md`

A markdown file describing a control named **Sample Control** (`CTRL-001`) with six properties covering each supported property type:

| Property | Type | Constraints |
|---|---|---|
| `time-out` | numeric (integer) | minimum: 1 |
| `latency-range` | numeric (float) | minimum: 0.5, maximum: 20.0 |
| `protection-level` | enum | low, medium, high |
| `information-level` | enum | not sensitive, sensitive, very sensitive |
| `control-string` | string | — |
| `control-flag` | boolean | — |

## Running the Skill

Invoke the skill with the spec file and an output directory:

```
/calm-control-requirement examples/control-creation/control-spec/control-spec-001.md examples/control-creation/generated-requirements
```

The skill reads the markdown spec, parses each property definition, and generates a JSON Schema document that:

- Composes with the CALM control-requirement meta-schema via `allOf`
- Represents `control-id`, `name`, and `description` as `const` values
- Maps numeric properties to `type: "integer"` or `type: "number"` with `minimum`/`maximum`
- Maps enum properties to `$ref`-linked definitions under `defs`
- Maps string and boolean properties directly

## Output: `generated-requirements/sample-control-requirement.json`

The generated file, saved using a kebab-case name derived from the control's `Name` field (`Sample Control` → `sample-control-requirement.json`).

## Using the Generated Requirement

To use this requirement in an architecture, reference it from a `controls` block and supply a `config` that satisfies its constraints:

```json
"controls": {
    "sample-control": {
        "description": "Applies the sample control to this component",
        "requirements": [
            {
                "requirement-url": "http://calm.finos.org/controls/sample/schema/sample-control.json",
                "config": {
                    "control-id": "CTRL-001",
                    "name": "Sample Control",
                    "description": "This demonstrates creating a CALM Control Requirement",
                    "time-out": 30,
                    "latency-range": 5.0,
                    "protection-level": "high",
                    "information-level": "sensitive",
                    "control-string": "example-value",
                    "control-flag": true
                }
            }
        ]
    }
}
```

Then validate the architecture with `calm validate -a <architecture-file>`, using `--url-to-local-file-mapping` if the requirement schema isn't published yet:

```bash
calm validate -a test-architecture.json -u url-mapping.json
```
