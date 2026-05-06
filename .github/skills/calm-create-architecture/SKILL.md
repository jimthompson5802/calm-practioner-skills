---
name: calm-create-architecture
description: Creates a new CALM architecture file by adding known nodes and relationships.
user-invocable: true
---

# CALM Create Architecture Skill

Look for the parameter `arch_file` in the incoming request, prompt or call context.
If `arch_file` is missing or empty, ask a single concise clarifying question requesting the value for `arch_file`.

Once you have the value for `arch_file`, create a new CALM architecture file with that name.

The architecture file should include all known nodes and relationships that are currently known.

For nodes that have endpoint information include that CALM `interfaces` property with the information collected. Use the flexible version of the `interface-type` property to allow for different types of interfaces (e.g. REST, gRPC, websocket, etc.) and include the relevant details for each interface. Each entry in the `interfaces` property should include the following information:
- `interface-type`: The type of interface (e.g. REST, gRPC, websocket, etc.)
- `endpoint`: The endpoint URL or address for the interface.
- `methods`: A list of methods supported by the interface, if applicable (e.g., for REST interfaces, this could include GET, POST, etc.)
- `description`: A brief description of the interface and its purpose.
