
{{#if metadata}}

## Architecture Metadata

{{#each metadata}}
- **{{@key}}:** {{.}}
{{/each}}
{{/if}}

## System Architecture
{{block-architecture}}

{{#if controls}}
## Architecture-Level Controls

{{#each controls}}
### {{@key}}

{{description}}

#### Requirements
{{#each requirements}}
- **Requirement:** {{requirement-url}}
{{#if config-url}}
- **Config:** {{config-url}}
{{/if}}
{{/each}}

{{/each}}
{{/if}}


## Architecture Statistics

- **Total Nodes:** {{nodes.length}}
- **Total Relationships:** {{relationships.length}}
{{#if flows}}
- **Total Flows:** {{flows.length}}
{{/if}}

## Components by Type

{{#each nodes}}
### {{name}}

**Type:** `{{node-type}}`  
**Unique ID:** `{{unique-id}}`

#### Description
{{description}}


{{#if interfaces}}
#### Interfaces

This component exposes the following interfaces:

{{#each interfaces}}
- **{{name}}** (`{{unique-id}}`)
  - **Protocol:** {{protocol}}{{#if port}} / **Port:** {{port}}{{/if}}
  - **Description:** {{description}}
  {{#if schema}}
  - **Schema:** {{schema.specification}}
    {{#if schema.operations}}
    - **Operations**
    {{#each schema.operations}}
      {{#if path}}
      - `{{method}} {{path}}` — {{description}}
      {{else}}
      - `{{event}}` ({{direction}}) — {{description}}
      {{/if}}
    {{/each}}
    {{/if}}
  {{/if}}
{{/each}}
{{/if}}

{{#if metadata}}
#### Metadata

{{#each metadata}}
- **{{@key}}:** {{.}}
{{/each}}
{{/if}}

{{#if controls}}
### Controls

This component has the following controls applied:

{{#each controls}}
- **{{@key}}:** {{description}}
{{/each}}
{{/if}}

---

{{/each}}


